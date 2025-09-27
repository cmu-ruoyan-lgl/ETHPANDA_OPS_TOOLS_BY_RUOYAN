# 网页内容提取提示词总结

## 🚀 快速使用提示词

### 标准版（推荐）
```
使用网页内容提取工作流程，访问"[网页URL]"，并在docs目录下创建"[文件夹名字]"文件夹，将其中的文字存到[文件夹名字]文件夹下的page.md中，在[文件夹名字]文件夹下要创建一个"image"文件夹，将其中的图片存在这里，请正确匹配page.md中图片链接改成本地image中的链接
```

### 简化版
```
按照webpage_to_markdown工作流程，提取[网页URL]的内容到docs/[文件夹名字]
```

### 超简版
```
网页转Markdown：[网页URL] → docs/[文件夹名字]
```

## 📝 使用示例

### 基本用法
```
使用网页内容提取工作流程，访问"https://example.com/article"，并在docs目录下创建"article"文件夹，将其中的文字存到article文件夹下的page.md中，在article文件夹下要创建一个"image"文件夹，将其中的图片存在这里，请正确匹配page.md中图片链接改成本地image中的链接
```

### 指定文件夹名
```
按照webpage_to_markdown工作流程，提取https://example.com/article到docs/tech
```

### 自定义文件夹名
```
使用网页内容提取工作流程，访问"https://example.com/article"，并在docs目录下创建"tech_blog"文件夹，将其中的文字存到tech_blog文件夹下的page.md中，在tech_blog文件夹下要创建一个"image"文件夹，将其中的图片存在这里，请正确匹配page.md中图片链接改成本地image中的链接
```

## 🎯 参数说明

| 参数 | 说明 | 示例 | 默认值 |
|------|------|------|--------|
| `[网页URL]` | 目标网页地址 | `https://example.com/article` | 必填 |
| `[文件夹名字]` | 文章文件夹名 | `tech_article` | 必填 |
| 目标目录 | 固定为docs目录 | `docs` | 固定 |

## ⚡ 快速参考

### 最常用提示词
```
使用网页内容提取工作流程，访问"[URL]"，并在docs目录下创建"[文件夹名字]"文件夹，将其中的文字存到[文件夹名字]文件夹下的page.md中，在[文件夹名字]文件夹下要创建一个"image"文件夹，将其中的图片存在这里，请正确匹配page.md中图片链接改成本地image中的链接
```

### 批量处理
```
使用网页内容提取工作流程，批量处理以下网页：
1. https://example.com/article1 → docs/article1
2. https://example.com/article2 → docs/article2
3. https://example.com/article3 → docs/article3
```

### 错误处理
```
使用网页内容提取工作流程，访问"https://example.com/article"，如果无法访问请告诉我原因，并在docs目录下创建"article"文件夹...
```

## 🔧 高级用法

### 自定义输出格式
```
使用网页内容提取工作流程，访问"https://example.com/article"，保存到docs/article，请：
- 保持原始HTML格式
- 图片保存为PNG格式
- 添加文章元数据（标题、作者、日期）
```

### 特定内容提取
```
使用网页内容提取工作流程，访问"https://example.com/article"，保存到docs/article，请只提取：
- 文章正文内容
- 相关图片
- 忽略广告和侧边栏内容
```

## 📋 检查清单

使用前确认：
- [ ] 网页URL可访问
- [ ] 目标目录存在
- [ ] 网络连接正常
- [ ] 磁盘空间充足

使用后检查：
- [ ] page.md文件生成
- [ ] image文件夹创建
- [ ] 图片下载完整
- [ ] 链接更新正确

## 🚨 注意事项

- 遵守网站robots.txt规则
- 注意版权和知识产权问题
- 不要过度频繁请求同一网站
- 检查提取内容是否完整
- 大文件下载需要时间

## 📚 相关文档

- 完整工作流程：`webpage_to_markdown.md`
- 配置命令：`makePage.md`
- 使用示例：本文档
