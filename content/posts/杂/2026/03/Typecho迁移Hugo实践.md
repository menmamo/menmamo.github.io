---
title: "Typecho迁移Hugo实践"
author: "mona"
date: 2026-03-06
categories: ["杂"]
tags: []

---

## 导出文章

因为插件只支持mysql，而我的typecho使用sqlite3作为数据库，所以直接使用gemini写了个导出脚本 并按`文章分类/年/月/文章名.md`的格式导出

```python
import sqlite3
import os
import datetime
import re
import json

# 配置信息
DB_PATH = 'typecho.db'
OUTPUT_DIR = os.path.expanduser('~/blog_source/content/posts') # 直接指向你的 Hugo 目录
FIXED_AUTHOR = "mona"  # 统一作者名为 mona

def clean_path_name(name):
    """过滤文件夹或文件名中的非法字符"""
    if not name:
        return "default"
    # 替换 Windows/Linux 路径中的非法字符
    name = re.sub(r'[\\/:*?"<>|\r\n]', '_', name).strip()
    return name if name else "default"

def migrate():
    if not os.path.exists(DB_PATH):
        print(f"错误：未找到数据库文件 {DB_PATH}")
        return

    conn = sqlite3.connect(DB_PATH)
    cursor = conn.cursor()

    # 查询文章：cid, title, created, text
    query = "SELECT cid, title, created, text FROM typecho_contents WHERE type='post' AND status='publish'"
    cursor.execute(query)
    posts = cursor.fetchall()

    if not os.path.exists(OUTPUT_DIR):
        os.makedirs(OUTPUT_DIR)

    print(f"开始迁移并清理标签：共 {len(posts)} 篇文章...")

    for row in posts:
        curr_cid, original_title, curr_created, curr_text = row
        
        # 1. 解析日期
        dt = datetime.datetime.fromtimestamp(curr_created)
        year_str = dt.strftime('%Y')
        month_str = dt.strftime('%m')
        date_iso = dt.strftime('%Y-%m-%dT%H:%M:%S+08:00')

        # 2. 获取分类和标签
        meta_query = """
        SELECT m.name, m.type 
        FROM typecho_metas m
        INNER JOIN typecho_relationships r ON m.mid = r.mid
        WHERE r.cid = ?
        """
        cursor.execute(meta_query, (curr_cid,))
        metas = cursor.fetchall()
        
        categories = [m[0] for m in metas if m[1] == 'category']
        tags = [m[0] for m in metas if m[1] == 'tag']

        # 3. 核心：合并你的清理逻辑
        # 在写入前直接替换掉所有<!--markdown-->标签
        content = curr_text.replace('<!--markdown-->', '')

        # 4. 创建多层级目录 (分类/年/月)
        main_category = categories[0] if categories else "uncategorized"
        target_dir = os.path.join(OUTPUT_DIR, clean_path_name(main_category), year_str, month_str)
        
        if not os.path.exists(target_dir):
            os.makedirs(target_dir)

        # 5. 确定文件路径
        safe_filename = clean_path_name(original_title)
        file_path = os.path.join(target_dir, f"{safe_filename}.md")

        # 6. 写入 Hugo 格式文件
        try:
            with open(file_path, 'w', encoding='utf-8') as f:
                f.write("---\n")
                f.write(f'title: "{original_title}"\n')
                f.write(f'author: "{FIXED_AUTHOR}"\n')
                f.write(f"date: {date_iso}\n")
                f.write(f"categories: {json.dumps(categories, ensure_ascii=False)}\n")
                f.write(f"tags: {json.dumps(tags, ensure_ascii=False)}\n")
                f.write("---\n\n")
                f.write(content)
            print(f"成功导出并清理: {file_path}")
        except Exception as e:
            print(f"处理失败 {original_title}: {e}")

    conn.close()
    print(f"\n迁移完成！所有文件已按分类和日期存入: {OUTPUT_DIR}")

if __name__ == "__main__":
    migrate()
```

## 导入文章

1. 把导出的文件夹 放入`/content/posts`目录下
2. 将`usr`文件夹直接放进`static`目录 

至此文章数据导入完毕

------

## 配置hugo

下载一个喜欢的主题，按文档配置就好了~

------

## 部署到GithubPages

### 创建`.gitignore`文件

在项目根目录下创建`.gitignore`文件

```
public/
resources/_gen/
.hugo_build.log
```

------

### 在 GitHub 上创建仓库

仓库名必须填`<你的用户名>.github.io`

------

### 将本地项目关联到仓库

在项目的根目录下执行git

```shell
# 初始化 git
git init
# 添加所有文件
git add .
#提交
git commit -m "Initial commit"
# 关联远程仓库 (替换为你自己的地址)
git branch -M main
git remote add origin https://github.com/<你的用户名>/<仓库名>.git
# 推送源代码
git push -u origin main
```

------

#### 配置 GitHub Actions 自动化部署

1. 在项目根目录下创建目录结构：`.github/workflows/`。
2. 在该文件夹下新建一个文件，命名为 `hugo.yaml`。

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: 'latest'
          extended: true
      - name: Build with Hugo
        run: hugo --minify
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

**在 GitHub 网页端开启服务**

1. 回到 GitHub 仓库页面，点击 **Settings** -> **Pages**。
2. 在 **Build and deployment** 下的 **Source** 选项中，将默认的 "Deploy from a branch" 改为 **"GitHub Actions"**。
3. 提交刚才创建的 `.github/workflows/hugo.yaml` 文件到 GitHub：

```shell
git add .
git commit -m "Add GitHub Actions workflow"
git push
```

点击仓库顶部的 **Actions** 选项卡，如果配置正确会有一个名为"Deploy Hugo site to Pages" 的任务正在运行，等他变成绿色，就可以看到博客了。

------

### **修改域名**

1. 打开 GitHub 仓库：`menmamo` -> **Settings** -> **Pages**。
2. 修改 **Custom domain**为自己的域名
3. 在CloudFlare中为域名添加cname记录为`用户名.github.io`
4. 等dns生效后勾选 **Enforce HTTPS**。
5. 修改 Hugo 的 `baseURL` 

------

## 修改/更新博客

至此自动化部署环境已经搭建完成，提交修改（无论是写新文章、修改配置还是更换主题设置）只需要在本地终端执行简单的 **Git 三部曲**。

1. 检查状态（可选）

```shell
git status
```

2. 将修改添加到暂存区

```shell
git add .
```

或者只添加特定的文件：

```shell
git add content/posts/my-article.md
```

3. 提交到本地仓库

```shell
git commit -m "feat: 新增关于 Hugo 部署的文章"
```

4. 推送到 GitHub

```shell
git push
```

如果在 GitHub 网页端直接改过文件，本地推送前需要先拉取：

```shell
git pull origin main
```

