---
title: "Typecho迁移Hugo实践"
author: "mona"
date: 2026-03-06
categories: ["杂"]
tags: []

---

## 导出文章

开始考虑使用插件导出，但插件似乎不支持sqlite作为数据库导出

故让gemini写了导出脚本 按`文章分类/年/月/文章名.md`的格式导出

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

把导出的文件夹 放入`/content/posts`目录下

将`usr`文件夹直接放进`static`目录 就可以正常显示图片了

至此文章数据导入完毕

------

## 配置hugo

下载一个喜欢的主题，按文档配置就好了~

------

## 配置cloudflare缓存

因为是静态博客可以直接配置cloudflare全站缓存，博客更新时再清理缓存就行了

这里还是让gemini帮忙写了个更新脚本

```bash
#!/bin/bash

# --- 配置部分 ---
ZONE_ID="ZONE_ID"
API_TOKEN="API_TOKEN"

# 1. 进入目录并构建
cd ~/blog_source || { echo "目录不存在"; exit 1; }
hugo --minify --destination 网站输出目录

# 2. 检查 Hugo 是否构建成功
if [ $? -eq 0 ]; then
    echo "Hugo 静态文件生成成功！"

    echo "正在请求 Cloudflare 清除缓存..."

    # 3. 调用 Cloudflare API (清理所有文件)
    # 使用 API Token 的标准方式：
    curl -X POST "https://api.cloudflare.com/client/v4/zones/$ZONE_ID/purge_cache" \
         -H "Authorization: Bearer $API_TOKEN" \
         -H "Content-Type: application/json" \
         --data '{"purge_everything":true}'

    echo -e "\nDeploy & Cache Clear Complete!"
else
    echo "Hugo 构建失败，未触发缓存清理。"
    exit 1
fi
```

------

至此我完成了Typecho向hugo的迁移。
