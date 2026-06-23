# JunhanX Blog

基于 [Hugo](https://gohugo.io/) + [PaperMod](https://github.com/adityatelange/hugo-PaperMod) 主题搭建的个人技术博客，部署在 GitHub Pages。

## 本地开发

```bash
# 克隆仓库（包含主题子模块）
git clone --recurse-submodules https://github.com/junhanX/junhanx.github.io.git
cd junhanx.github.io

# 启动本地服务器（含草稿）
hugo server -D

# 构建
hugo
```

## 部署

Push 到 `main` 分支后，GitHub Actions 自动构建并部署到 GitHub Pages。

访问地址：[https://junhanx.github.io/](https://junhanx.github.io/)

## 新建文章

```bash
hugo new posts/my-new-post.md
```
