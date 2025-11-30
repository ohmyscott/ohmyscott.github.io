---
title: "我怎么搭建的博客？"
date: 2025-11-28
draft: false
slug: "hugo-papermod-github-pages-blog"
tags: ["博客", "Hugo", "PaperMod", "GitHub Pages"]
categories: ["技术"]
description: "使用 Hugo 和 PaperMod 主题配合 GitHub Pages 搭建个人博客的完整指南"
ShowToc: true
---


欢迎来到我的博客！这篇文章记录了我使用 **Hugo + PaperMod + GitHub Pages** 搭建个人博客的完整过程。

## 为什么选择这个技术栈？

### Hugo 静态网站生成器

选择 Hugo 是因为它：

- 🚀 **构建速度超快** - 世界上最快的静态网站生成器之一
- 📝 **Markdown 写作** - 专注于内容创作，无需关心样式
- 🎨 **丰富的主题生态** - 社区活跃，主题选择多样
- 🔧 **配置简单灵活** - 单文件配置，易于维护
- 💰 **免费开源** - 完全免费，MIT 许可证

### PaperMod 主题

[PaperMod](https://github.com/adityatelange/hugo-PaperMod) 是一个功能强大且设计简洁的 Hugo 主题，提供了丰富的功能特性：

#### 核心功能特性

- 🎨 **三种显示模式**：
  - Regular Mode（常规模式）
  - Home-Info Mode（首页信息模式）
  - Profile Mode（个人资料模式）

- 📋 **目录生成** - 基于 Markdown 标题自动生成 TOC
- 📚 **文章归档** - 自动按时间归档所有文章
- 🔗 **社交图标** - 支持多种社交媒体平台的图标链接
- 📤 **分享按钮** - 文章页面内置社交媒体分享功能
- 🧭 **菜单位置指示器** - 清晰显示当前页面位置
- 🌍 **多语言支持** - 内置语言选择器，支持国际化
- 🏷️ **分类系统** - 支持文章分类和标签管理
- 🖼️ **封面图片** - 每篇文章可设置封面图，支持响应式
- 🌓 **主题切换** - 支持深色/浅色主题，自动检测浏览器偏好
- 🔍 **SEO 优化** - 对搜索引擎友好
- 👥 **多作者支持** - 支持多作者博客
- 🔎 **搜索功能** - 内置基于 Fuse.js 的搜索页面
- 📖 **相关文章推荐** - 文章底部自动推荐相关内容
- 🧭 **面包屑导航** - 清晰的页面层级导航
- 📋 **代码复制** - 代码块一键复制功能
- 🎨 **语法高亮** - 集成 Hugo Chroma 语法高亮器
- ⚡ **零依赖** - 无需 webpack、nodejs 等额外依赖

### GitHub Pages

选择 GitHub Pages 是因为：

- 🆓 **完全免费** - 无需支付服务器费用
- 🚀 **自动部署** - 通过 GitHub Actions 实现自动构建和部署
- 🔒 **HTTPS 支持** - 自动配置 SSL 证书
- 📈 **全球 CDN** - GitHub 提供的全球内容分发网络
- 🔧 **版本控制** - 博客内容和配置完全版本化管理

## 搭建流程概览

```bash
# 1. 安装 Hugo
brew install hugo  # macOS
# 或访问 https://gohugo.io/installation/ 获取其他平台安装方式

# 2. 创建新站点
hugo new site my-blog

# 3. 添加 PaperMod 主题
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod

# 4. 配置主题（在 hugo.toml 中）
theme = 'PaperMod'

# 5. 创建第一篇文章
hugo new posts/my-first-post.md

# 6. 本地预览
hugo server -D

# 7. 构建生产版本
hugo
```

## 配置要点

在 `hugo.toml` 中的关键配置：

```toml
baseURL = 'https://your-username.github.io/'
languageCode = 'zh-cn'
title = 'Your Blog Title'
theme = 'PaperMod'

[params]
env = "production"
defaultTheme = "auto"
ShowReadingTime = true
ShowShareButtons = true
ShowPostNavLinks = true
ShowBreadCrumbs = true
ShowCodeCopyButtons = true

[params.homeInfoParams]
Title = "Hi there 👋"
Content = "Welcome to my blog"
```

## 总结

使用 Hugo + PaperMod + GitHub Pages 的组合，我们能够：

- ✅ **快速搭建** - 几分钟内完成基础配置
- ✅ **高性能** - 静态网站，加载速度快
- ✅ **零成本** - 完全免费的托管方案
- ✅ **易维护** - Markdown 写作，Git 版本控制
- ✅ **功能丰富** - PaperMod 主题提供现代化功能

## 额外推荐：使用 Claude Code 加速博客撰写

在搭建和运营博客的过程中，我发现了一个强大的工具来提升写作效率 - **[Claude Code](https://github.com/anthropics/claude-code)**！

### 为什么推荐 Claude Code？

- 🚀 **智能内容创作** - 辅助构思文章结构和内容组织
- 📝 **Markdown 优化** - 自动格式化和技术术语润色
- 🔍 **SEO 优化建议** - 提供标题优化和关键词建议
- 💡 **代码示例生成** - 自动生成和验证代码片段
- 🌐 **多语言支持** - 中英文混排内容处理

## 相关资源

- [Hugo 官方文档](https://gohugo.io/documentation/)
- [PaperMod 主题 GitHub](https://github.com/adityatelange/hugo-PaperMod)
- [PaperMod 主题文档](https://github.com/adityatelange/hugo-PaperMod/wiki)
- [GitHub Pages 官方文档](https://docs.github.com/en/pages)

感谢你的访问！如果你有任何问题或建议，欢迎通过 Email 或社交媒体与我联系。
