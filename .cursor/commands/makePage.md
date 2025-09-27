# /makePage 命令规范

## 触发短语
- `/makePage @[网页URL] [文件夹名字]`
- `/makePage 网页转Markdown @[网页URL] [文件夹名字]`
- `/makePage 提取网页内容 @[网页URL] [文件夹名字]`

## 输入参数
- 必填：`@[网页URL]`（目标网页地址，必须以@开头）
- 必填：`[文件夹名字]`（在docs目录下创建的文件夹名称）
- 固定：目标目录始终为 `docs` 目录

## 标准提示词模板

### 🎯 完整版提示词
```
使用网页内容提取工作流程，访问"[网页URL]"，并在docs目录下创建"[文件夹名字]"文件夹，将其中的文字存到[文件夹名字]文件夹下的page.md中，在[文件夹名字]文件夹下要创建一个"image"文件夹，将其中的图片存在这里，请正确匹配page.md中图片链接改成本地image中的链接
```

### ⚡ 简化版提示词
```
按照webpage_to_markdown工作流程，提取[网页URL]的内容到docs/[文件夹名字]
```

### 🔧 快速版提示词
```
网页转Markdown：[网页URL] → docs/[文件夹名字]
```

## 执行流程
1. **网页访问**：使用Browser MCP工具访问目标网页
2. **目录创建**：在指定目录下创建文件夹结构
3. **内容下载**：使用curl下载完整HTML源码
4. **内容提取**：运行Python脚本提取文字和图片
5. **图片处理**：下载网络图片，解码base64图片
6. **格式转换**：将HTML转换为Markdown格式
7. **链接更新**：将图片链接更新为本地相对路径
8. **文件保存**：保存page.md和图片文件
9. **清理工作**：删除临时文件

## 输出结构
```
docs/[文件夹名字]/
├── page.md          # 文章内容（Markdown格式）
└── image/           # 图片文件夹
    ├── image_1.png
    ├── image_2.jpeg
    └── ...
```

## 支持的功能
- ✅ 文字内容提取和Markdown转换
- ✅ 网络图片下载
- ✅ Base64编码图片解码
- ✅ 图片链接本地化
- ✅ 多种图片格式支持（PNG、JPEG、GIF、WebP、SVG）
- ✅ HTML到Markdown智能转换
- ✅ 自动文件命名和路径管理

## 使用示例

### 示例1：基本用法
```
/makePage @https://example.com/article tech_article
```
输出：`docs/tech_article/page.md` + 图片文件夹

### 示例2：使用别名
```
/makePage 网页转Markdown @https://example.com/article blockchain_news
```
输出：`docs/blockchain_news/page.md` + 图片文件夹

### 示例3：完整参数
```
/makePage 提取网页内容 @https://example.com/article ai_research
```
输出：`docs/ai_research/page.md` + 图片文件夹

### 示例4：中文文件夹名
```
/makePage @https://example.com/article 技术文章
```
输出：`docs/技术文章/page.md` + 图片文件夹

## 环境要求
- Python 3.x
- requests库 (`pip install requests`)
- 网络连接
- 足够的磁盘空间

## 错误处理
- 网页无法访问：提供错误原因和解决建议
- 图片下载失败：重试或跳过，记录失败信息
- 内容提取失败：尝试备用提取方法
- 格式转换问题：提供原始HTML作为备选

## 最佳实践
- 使用描述性的文件夹名称
- 定期清理临时文件
- 检查生成的内容质量
- 遵守网站robots.txt规则
- 注意版权和知识产权问题

## 校验清单
- [ ] 网页成功访问
- [ ] 文件夹结构正确创建
- [ ] 文字内容完整提取
- [ ] 图片成功下载和保存
- [ ] Markdown格式正确
- [ ] 图片链接本地化完成
- [ ] 临时文件已清理
- [ ] 输出文件可正常打开

## 相关文件
- 工作流程文档：`/Users/ruoyan/Desktop/WorkSpace/ETHPanda_OPS_SOP/.cursor/commands/webpage_to_markdown.md`
- Python提取脚本：运行时自动生成
- 临时HTML文件：`/tmp/page.html`（处理完成后自动删除）

## 注意事项
- 确保目标网页允许爬取
- 检查图片版权问题
- 大文件下载可能需要时间
- 某些动态加载的内容可能需要特殊处理
- 建议在非高峰时段进行批量处理
