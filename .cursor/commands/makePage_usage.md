# /makePage 命令使用说明

## 🚀 命令格式

```
/makePage @[网页URL] [文件夹名字]
```

## 📋 参数说明

| 参数 | 说明 | 示例 | 必填 |
|------|------|------|------|
| `@[网页URL]` | 目标网页地址，必须以@开头 | `@https://example.com/article` | ✅ |
| `[文件夹名字]` | 在docs目录下创建的文件夹名 | `tech_article` | ✅ |

## 📁 输出结构

```
docs/[文件夹名字]/
├── page.md          # 文章内容（Markdown格式）
└── image/           # 图片文件夹
    ├── image_1.png
    ├── image_2.jpeg
    └── ...
```

## 💡 使用示例

### 基本用法
```
/makePage @https://example.com/article tech_article
```
→ 创建 `docs/tech_article/page.md` 和 `docs/tech_article/image/`

### 中文文件夹名
```
/makePage @https://example.com/article 技术文章
```
→ 创建 `docs/技术文章/page.md` 和 `docs/技术文章/image/`

### 使用别名
```
/makePage 网页转Markdown @https://example.com/article blockchain_news
```
→ 创建 `docs/blockchain_news/page.md` 和 `docs/blockchain_news/image/`

## ⚡ 快速提示词

当您使用 `/makePage` 命令时，系统会自动转换为以下提示词：

```
使用网页内容提取工作流程，访问"[网页URL]"，并在docs目录下创建"[文件夹名字]"文件夹，将其中的文字存到[文件夹名字]文件夹下的page.md中，在[文件夹名字]文件夹下要创建一个"image"文件夹，将其中的图片存在这里，请正确匹配page.md中图片链接改成本地image中的链接
```

## 🔧 功能特性

- ✅ 自动提取网页文字内容
- ✅ 下载并保存所有图片
- ✅ 转换为Markdown格式
- ✅ 图片链接本地化
- ✅ 支持多种图片格式（PNG、JPEG、GIF、WebP、SVG）
- ✅ 支持Base64编码图片
- ✅ 自动文件命名和路径管理

## 📝 注意事项

- 确保网页URL可访问
- 文件夹名字不能包含特殊字符
- 图片下载需要网络连接
- 大文件可能需要较长时间处理
- 遵守网站robots.txt规则

## 🆘 常见问题

**Q: 网页无法访问怎么办？**
A: 检查URL是否正确，确保网络连接正常

**Q: 图片下载失败怎么办？**
A: 系统会自动重试，失败时会记录错误信息

**Q: 可以提取动态加载的内容吗？**
A: 目前主要提取静态HTML内容，动态内容可能需要特殊处理

**Q: 支持哪些图片格式？**
A: 支持PNG、JPEG、GIF、WebP、SVG等常见格式

## 📚 相关文档

- 完整工作流程：`webpage_to_markdown.md`
- 命令规范：`makePage.md`
- 提示词总结：`webpage_prompts.md`
