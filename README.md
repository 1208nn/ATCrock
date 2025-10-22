# ATCrock

基于 Hexo 的博客项目，使用 Arknights 主题。

## GitHub Actions 自动化

本项目已配置 GitHub Actions 自动构建和部署：

### 自动部署 (Deploy Workflow)

- **触发条件**: 当代码推送到 `main` 分支时
- **功能**:
  - 自动初始化 Git 子模块（Arknights 主题）
  - 安装依赖
  - 构建 Hexo 静态网站
  - 自动部署到 `gh-pages` 分支
- **文件**: `.github/workflows/deploy.yml`

### 持续集成检查 (CI Workflow)

- **触发条件**: 当创建或更新 Pull Request 到 `main` 分支时
- **功能**:
  - 验证代码能否成功构建
  - 确保生成的静态文件完整
- **文件**: `.github/workflows/ci.yml`

## 本地开发

```bash
# 安装依赖
yarn install

# 初始化主题子模块
git submodule update --init --recursive

# 启动开发服务器
yarn server

# 构建静态文件
yarn build

# 清理构建文件
yarn clean
```

## 部署

代码推送到 `main` 分支后，GitHub Actions 会自动构建并部署到 GitHub Pages。

无需手动运行 `hexo deploy` 命令。
