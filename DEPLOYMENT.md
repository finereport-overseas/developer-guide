# 部署说明（HonKit + Node 24 + GitHub Actions + GitHub Pages）

本文档说明本仓库当前的文档构建与发布方案：使用 **HonKit** 在 **Node.js 24** 环境下构建静态站点，并通过 **GitHub Actions** 将构建产物发布到 `gh-pages` 分支，再由 **GitHub Pages** 自动对外提供访问。

## 方案概览

- **源文件分支**：`master`
- **构建工具**：HonKit（兼容 GitBook Legacy 配置）
- **运行时版本**：Node.js 24
- **构建产物目录**：`_book/`
- **发布分支**：`gh-pages`（由 CI 推送静态文件）
- **触发方式**：
  - push 到 `master` 自动触发
  - 支持在 GitHub Actions 页面手动触发（`workflow_dispatch`）

相关文件：

- **工作流**：`.github/workflows/honkit-gh-pages.yml`
- **依赖与脚本**：`package.json`、`package-lock.json`
- **书籍配置**：`book.json`
- **忽略规则**：`.gitignore`（忽略 `node_modules/`、`_book/`）

## 本地运行

在仓库根目录（`book.json` 所在目录）执行：

```bash
npm ci
npm run build
```

本地预览（启动本地服务）：

```bash
npm run serve
```

> 说明：CI 使用 `npm ci`，本地建议也用 `npm ci` 以保证与 `package-lock.json` 一致；如需更新依赖再使用 `npm install` 并提交新的 lockfile。

## GitHub Actions（CI 构建与发布）

工作流 `.github/workflows/honkit-gh-pages.yml` 的核心逻辑：

- checkout 仓库
- 安装 Node.js 24，并启用 npm 缓存
- `npm ci` 安装依赖（包含 HonKit 与 GitBook 插件包）
- `npm run build` 生成 `_book/`
- 使用 `peaceiris/actions-gh-pages@v4` 将 `_book/` 推送到 `gh-pages`

### 必要权限

该方案使用仓库内置的 `GITHUB_TOKEN` 推送到 `gh-pages`，因此需要在 GitHub 仓库设置里启用写权限：

- **Settings → Actions → General → Workflow permissions**
  - 选择 **Read and write permissions**

若组织策略禁止 `GITHUB_TOKEN` 写入仓库，需要改为使用 PAT（Personal Access Token）并存入 `Secrets`，再在工作流中引用（此为备用方案，默认不需要）。

## GitHub Pages（对外发布）

本方案属于 **Static HTML**（静态文件托管）：CI 将构建后的 HTML/CSS/JS 推到 `gh-pages`，Pages 从该分支直接发布。

在 GitHub 仓库中设置：

- **Settings → Pages**
  - **Source**：Deploy from a branch
  - **Branch**：`gh-pages`
  - **Folder**：`/ (root)`

设置完成后：

- 每次 `gh-pages` 有新提交，GitHub Pages 会 **自动发布**（通常有几十秒到几分钟延迟）。

## 插件与依赖管理

HonKit 不再使用历史上的 `gitbook install` 拉取插件，而是通过 npm 依赖来解析插件包。因此：

- `book.json` 中的插件名（如 `anchor-navigation-ex`、`mygitalk`）需要在 `package.json` 中以 `gitbook-plugin-xxx` 的形式声明并安装。
- 当前依赖已写入 `package.json`，并生成 `package-lock.json` 以供 `npm ci` 使用。

## 常见问题（Troubleshooting）

### 1) Actions 里 `npm ci` 失败

常见原因：

- 未提交 `package-lock.json`
- `package.json` 与 `package-lock.json` 不一致

处理方式：

- 本地执行 `npm install` 更新 lockfile 后提交
- 或保持依赖不变，确保 `package-lock.json` 已在仓库中

### 2) 部署步骤报权限错误（无法 push 到 `gh-pages`）

检查：

- **Settings → Actions → General → Workflow permissions** 是否为 **Read and write**
- 仓库是否受组织策略限制 `GITHUB_TOKEN` 写权限

### 3) Pages 页面不更新

检查：

- **Settings → Pages** 是否确实从 `gh-pages` 分支发布（而不是 “GitHub Actions” 方式）
- Actions 是否成功完成并推送了 `gh-pages`

### 4) 安全注意事项：`book.json` 中的 `clientSecret`

当前 `book.json` 的 `pluginsConfig.mygitalk.clientSecret` 为明文。

- 若仓库为公开仓库，建议迁移为更安全的方案（例如使用不需要 `clientSecret` 的方式，或将敏感信息迁移到构建期注入并避免进入源码仓库）。
- 该优化会涉及插件实现/模板注入方式调整，属于后续增强项。

## 维护建议

- **主分支名称**：若未来将 `master` 改为 `main`，需同步更新 `.github/workflows/honkit-gh-pages.yml` 的触发分支。
- **Node 版本**：当前锁定为 Node 24；如需升级，建议先在本地跑通 `npm ci && npm run build` 再调整 CI。

