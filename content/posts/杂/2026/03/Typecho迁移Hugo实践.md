---
title: "Typecho迁移Hugo实践"
author: "mona"
date: '2026-03-08T12:15:45+08:00'
categories: ["杂"]
tags: []
---

## 数据准备

### 导出文章md

我的typecho使用sqlite3作为数据库，没有找到合适的插件，于是直接让gemini写个导出脚本。

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

------

### 安装hugo

直接使用winget安装。`--version`可以指定版本。

```shell
winget install Hugo.Hugo --version 0.145.0
```

------

### 导入文章

1. `hugo new site my-blog`创建站点
2. 文章md放入站点目录下的`/content/posts`。
3. `usr`文件夹放入`static`目录。

-----

### 配置hugo.toml

找一个喜欢的主题放入`themes`文件夹，我这里使用了`LoveIt`主题，根据文档配置如下：

```toml
baseURL = 'https://menma.top/'
languageCode = 'zh-CN'
title = 'MenmaTop'
theme = 'LoveIt'
languageName = "简体中文"
ignoreErrors = ["error-remote-getjson", "error-missing-instagram-accesstoken"]
# 分页配置
[pagination]
  disableAliases = false
  pagerSize = 20
  path = "page"
#菜单配置
[menu]
  [[menu.main]]
    weight = 1
    identifier = "posts"
    pre = ""
    post = ""
    name = "所有文章"
    url = "/posts/"
    title = ""
  [[menu.main]]
    weight = 2
    identifier = "tags"
    pre = ""
    post = ""
    name = "标签"
    url = "/tags/"
    title = ""
  [[menu.main]]
    weight = 3
    identifier = "categories"
    pre = ""
    post = ""
    name = "分类"
    url = "/categories/"
    title = ""
[params]
  defaultTheme = "auto"
  title = "MenmaTop"
  images = ["/info/favicon/favicon.svg"]
    [params.author]
        name = "mona"
        email = ""
        link = "https://menma.top"
    [params.header]
        desktopMode = "fixed"
        mobileMode = "auto"
        [params.header.title]
            logo = "/info/favicon/favicon.svg"
            name = "MenmaTop"
    [params.footer]
        enable = true
        hugo = false
        copyright = false
        author = false
    [params.section]
        paginate = 20
        rss = 10
    [params.list]
        paginate = 20
        rss = 10
    [params.app]
        title = "MenmaTop"
        noFavicon = false
        svgFavicon = "/info/favicon/favicon.svg"
        # Android 浏览器主题色
        themeColor = "#ffffff"
        # Safari 图标颜色
        iconColor = "#5bbad5"
        # Windows v8-10磁贴颜色
        tileColor = "#da532c"
    # 搜索配置
    [params.search]
        enable = true
        type = "algolia"
        contentLength = 4000
        placeholder = ""
        maxResultLength = 10
        snippetLength = 50
        highlightTag = "em"
        absoluteURL = false
        [params.search.algolia]
            index = ""
            appID = ""
            searchKey = ""
    #主页配置
    [params.home]
        rss = 10
        [params.home.profile]
            enable = false
        [params.home.posts]
            enable = true
            paginate = 20
    [params.page]
        [params.page.code]
            copy = true
            maxShownLines = 100
        [params.page.math]
            enable = true
        [params.page.share]
            enable = false
    [params.analytics]
         enable = false
[markup]
  [markup.highlight]
  noClasses = false
    [markup.goldmark]
      [markup.goldmark.renderer]
        unsafe = true
      [markup.goldmark.extensions]
        [markup.goldmark.extensions.passthrough]
            enable = true
            [markup.goldmark.extensions.passthrough.delimiters]
            block = [['$$', '$$'], ['\[', '\]']]
            inline = [['$', '$'], ['\(', '\)']]
[outputs]
    home = ["HTML", "RSS", "JSON"]
    page = ["HTML"]
    section = ["HTML", "RSS"]
    taxonomy = ["HTML", "RSS"]
```

最后，使用`hugo server`命令来预览效果，没有问题就可以进行下一步操作。

------

## 部署到GithubPages

### 创建`.gitignore`文件

因为使用`Github Action`来生成文章，所以需要在项目根目录下创建`.gitignore`文件 排除一些不必要的目录和文件

```
public/
resources/_gen/
.hugo_build.log
```

------

### 在 GitHub 上创建仓库

仓库名必须填`<用户名>.github.io`

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

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: '0.145.0'
          extended: false

      - name: Build with Hugo
        env:
          HUGO_PARAMS_SEARCH_ALGOLIA_SEARCHKEY: ${{ secrets.HUGO_SEARCH_KEY }}
          HUGO_PARAMS_SEARCH_ALGOLIA_APPID: ${{ secrets.APPLICATION_ID }}
          HUGO_PARAMS_SEARCH_ALGOLIA_INDEX: ${{ secrets.INDEX_NAME }}
        run: hugo --minify

      - name: Upload Algolia Indexes
        uses: iChochy/Algolia-Upload-Records@main
        env:
          APPLICATION_ID: ${{ secrets.APPLICATION_ID }}
          ADMIN_API_KEY: ${{ secrets.ADMIN_API_KEY }}
          INDEX_NAME: ${{ secrets.INDEX_NAME }}
          FILE_PATH: "public/index.json"
      
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
2. 在 **Build and deployment** 下的 **Source** 选项中，将默认的 "**Deploy from a branch**" 改为 **"GitHub Actions"**。
3. 提交刚才创建的 `.github/workflows/hugo.yaml` 文件到 GitHub：

```shell
git add .
git commit -m "Add GitHub Actions workflow"
git push
```

点击仓库顶部的 **Actions** 选项卡，如果配置正确会有一个名为"Deploy Hugo site to Pages" 的任务正在运行，等他变成绿色，就可以看到博客了。

------

### **修改域名**

1. 打开 GitHub 仓库：**Settings** -> **Pages**。
2. 修改 **Custom domain**为自己的域名
3. 在CloudFlare中为域名添加cname记录为`用户名.github.io`
4. 等dns生效后勾选 **Enforce HTTPS**。
5. 修改 Hugo 的 `baseURL` 

------

## 修改/更新博客

至此自动化部署环境已经搭建完成，提交修改（无论是写新文章、修改配置还是更换主题设置）只需要在本地终端执行简单的 **Git 三部曲**。

**创建文章**

```shell
hugo new 文章
```

**提交修改**

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
