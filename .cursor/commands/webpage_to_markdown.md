# 网页内容提取和本地化工作流程

## 概述
将网页内容（文字+图片）提取并保存为本地Markdown文件，图片下载到本地并更新链接。

## 工具和操作步骤

### 1. 浏览器访问网页
```bash
# 使用Browser MCP工具访问目标网页
mcp_browsermcp_browser_navigate
- url: "目标网页URL"
```

### 2. 创建文件夹结构
```bash
# 创建必要的目录结构
mkdir -p /path/to/docs/try/image
```

### 3. 下载网页HTML源码
```bash
# 使用curl下载完整HTML
curl -s "目标网页URL" > /tmp/page.html
```

### 4. 创建Python内容提取脚本
创建 `/tmp/extract_content.py`：

```python
#!/usr/bin/env python3
import re
import html
import os
import base64
from urllib.parse import urljoin, urlparse

def extract_images(html_content, base_url):
    """提取HTML中的图片链接"""
    img_pattern = r'<img[^>]*src=[\"\\']([^\"\\']*)[\"\\'][^>]*>'
    images = []
    
    for match in re.finditer(img_pattern, html_content, re.IGNORECASE):
        img_src = match.group(1)
        # 转换相对URL为绝对URL
        if img_src.startswith('http'):
            full_url = img_src
        else:
            full_url = urljoin(base_url, img_src)
        
        # 获取文件名
        parsed_url = urlparse(full_url)
        filename = os.path.basename(parsed_url.path)
        if not filename or '.' not in filename:
            filename = f"image_{len(images)+1}.png"
        
        images.append({
            'url': full_url,
            'filename': filename,
            'original_src': img_src
        })
    
    return images

def extract_base64_images(html_content):
    """提取base64编码的图片"""
    base64_pattern = r'<img[^>]*src=[\"\\']data:image/([^;]+);base64,([^\"\\']+)[\"\\'][^>]*>'
    images = []
    
    for match in re.finditer(base64_pattern, html_content, re.IGNORECASE):
        img_format = match.group(1)
        img_data = match.group(2)
        
        # 确定文件扩展名
        ext_map = {
            'jpeg': 'jpeg',
            'jpg': 'jpeg', 
            'png': 'png',
            'gif': 'gif',
            'webp': 'webp',
            'svg+xml': 'svg'
        }
        ext = ext_map.get(img_format, 'png')
        
        filename = f"image_{len(images)+1}.{ext}"
        
        images.append({
            'data': img_data,
            'filename': filename,
            'format': img_format
        })
    
    return images

def extract_text_content(html_content):
    """提取文本内容并转换为Markdown格式"""
    # 提取标题
    title_match = re.search(r'<title[^>]*>(.*?)</title>', html_content, re.DOTALL | re.IGNORECASE)
    title = title_match.group(1).strip() if title_match else 'Untitled'
    
    # 提取主要内容
    content_patterns = [
        r'<main[^>]*>(.*?)</main>',
        r'<article[^>]*>(.*?)</article>',
        r'<div[^>]*class=[\"\\'][^\"\\']*content[^\"\\']*[\"\\'][^>]*>(.*?)</div>',
        r'<div[^>]*class=[\"\\'][^\"\\']*post[^\"\\']*[\"\\'][^>]*>(.*?)</div>'
    ]
    
    article_content = ''
    for pattern in content_patterns:
        match = re.search(pattern, html_content, re.DOTALL | re.IGNORECASE)
        if match:
            article_content = match.group(1)
            break
    
    if not article_content:
        body_match = re.search(r'<body[^>]*>(.*?)</body>', html_content, re.DOTALL | re.IGNORECASE)
        if body_match:
            article_content = body_match.group(1)
    
    # 简单的HTML到Markdown转换
    markdown = article_content
    
    # 转换标题
    markdown = re.sub(r'<h1[^>]*>(.*?)</h1>', r'# \1\n', markdown, flags=re.DOTALL | re.IGNORECASE)
    markdown = re.sub(r'<h2[^>]*>(.*?)</h2>', r'## \1\n', markdown, flags=re.DOTALL | re.IGNORECASE)
    markdown = re.sub(r'<h3[^>]*>(.*?)</h3>', r'### \1\n', markdown, flags=re.DOTALL | re.IGNORECASE)
    
    # 转换段落
    markdown = re.sub(r'<p[^>]*>(.*?)</p>', r'\1\n\n', markdown, flags=re.DOTALL | re.IGNORECASE)
    
    # 转换粗体和斜体
    markdown = re.sub(r'<strong[^>]*>(.*?)</strong>', r'**\1**', markdown, flags=re.DOTALL | re.IGNORECASE)
    markdown = re.sub(r'<b[^>]*>(.*?)</b>', r'**\1**', markdown, flags=re.DOTALL | re.IGNORECASE)
    markdown = re.sub(r'<em[^>]*>(.*?)</em>', r'*\1*', markdown, flags=re.DOTALL | re.IGNORECASE)
    markdown = re.sub(r'<i[^>]*>(.*?)</i>', r'*\1*', markdown, flags=re.DOTALL | re.IGNORECASE)
    
    # 转换链接
    markdown = re.sub(r'<a[^>]*href=[\"\\']([^\"\\']*)[\"\\'][^>]*>(.*?)</a>', r'[\2](\1)', markdown, flags=re.DOTALL | re.IGNORECASE)
    
    # 转换列表
    markdown = re.sub(r'<ul[^>]*>', '', markdown, flags=re.IGNORECASE)
    markdown = re.sub(r'</ul>', '\n', markdown, flags=re.IGNORECASE)
    markdown = re.sub(r'<li[^>]*>(.*?)</li>', r'- \1\n', markdown, flags=re.DOTALL | re.IGNORECASE)
    
    # 清理HTML标签
    markdown = re.sub(r'<[^>]+>', '', markdown)
    
    # 解码HTML实体
    markdown = html.unescape(markdown)
    
    # 清理多余空白
    markdown = re.sub(r'\n\s*\n\s*\n', '\n\n', markdown)
    markdown = markdown.strip()
    
    return f"# {title}\n\n{markdown}"

def main():
    base_url = "https://vitalik.eth.limo/general/2025/09/24/openness_and_verifiability.html"
    output_dir = "/Users/ruoyan/Desktop/WorkSpace/ETHPanda_OPS_SOP/docs/try"
    image_dir = os.path.join(output_dir, "image")
    
    # 读取HTML文件
    with open('/tmp/page.html', 'r', encoding='utf-8') as f:
        html_content = f.read()
    
    # 提取文本内容
    markdown_content = extract_text_content(html_content)
    
    # 提取图片
    images = extract_images(html_content, base_url)
    base64_images = extract_base64_images(html_content)
    
    print(f"找到 {len(images)} 个网络图片")
    print(f"找到 {len(base64_images)} 个base64图片")
    
    # 保存Markdown文件
    with open(os.path.join(output_dir, 'page.md'), 'w', encoding='utf-8') as f:
        f.write(markdown_content)
    
    # 下载网络图片
    import requests
    for img in images:
        try:
            response = requests.get(img['url'])
            if response.status_code == 200:
                img_path = os.path.join(image_dir, img['filename'])
                with open(img_path, 'wb') as f:
                    f.write(response.content)
                print(f"下载图片: {img['filename']}")
        except Exception as e:
            print(f"下载图片失败 {img['filename']}: {e}")
    
    # 保存base64图片
    for img in base64_images:
        try:
            img_data = base64.b64decode(img['data'])
            img_path = os.path.join(image_dir, img['filename'])
            with open(img_path, 'wb') as f:
                f.write(img_data)
            print(f"保存base64图片: {img['filename']}")
        except Exception as e:
            print(f"保存base64图片失败 {img['filename']}: {e}")
    
    print("内容提取完成！")

if __name__ == "__main__":
    main()
```

### 5. 运行内容提取脚本
```bash
python3 /tmp/extract_content.py
```

### 6. 创建图片链接修复脚本（如果需要）
创建 `/tmp/fix_images.py`：

```python
#!/usr/bin/env python3
import re

def main():
    # 读取Markdown文件
    with open('/path/to/docs/try/page.md', 'r', encoding='utf-8') as f:
        content = f.read()
    
    # 在适当位置插入图片链接
    images_to_add = [
        "![图片1](image/image_1.png)\n\n",
        "![图片2](image/image_2.png)\n\n", 
        # ... 更多图片
    ]
    
    # 在文档末尾添加所有图片
    content += "\n\n## 文章图片\n\n"
    for img_link in images_to_add:
        content += img_link
    
    # 保存修改后的文件
    with open('/path/to/docs/try/page.md', 'w', encoding='utf-8') as f:
        f.write(content)
    
    print("图片链接已添加到Markdown文件中")

if __name__ == "__main__":
    main()
```

### 7. 运行图片链接修复脚本
```bash
python3 /tmp/fix_images.py
```

### 8. 清理临时文件
```bash
rm /tmp/page.html /tmp/extract_content.py /tmp/fix_images.py
```

## 关键成功要素

1. **使用Browser MCP工具**：确保能正确访问网页
2. **HTML源码下载**：使用curl获取完整HTML内容
3. **Python脚本处理**：使用正则表达式提取内容和图片
4. **支持多种图片格式**：包括网络图片和base64编码图片
5. **Markdown转换**：将HTML转换为可读的Markdown格式
6. **本地化链接**：将图片链接更新为本地相对路径

## 适用场景

- 博客文章保存
- 技术文档备份
- 网页内容归档
- 离线阅读准备

## 注意事项

- 确保目标网页允许爬取
- 检查图片版权问题
- 大文件下载可能需要时间
- 某些动态加载的内容可能需要特殊处理
