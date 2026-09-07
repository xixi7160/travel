# 一路向西 · 滇西十二日 — Cloudflare Pages 部署指南

## 🎯 总览

- **平台**：Cloudflare Pages（免费版 100k 请求/日 + 无限带宽 + 自动 HTTPS）
- **触发**：推送到 GitHub `main` → Cloudflare 自动 build → **约 30s 全网生效**
- **域名**：默认 `*.pages.dev`，可绑定自定义域名
- **改起来**：本地改 `index.html` → `git push` → 自动上线（详见文末）

---

## 📋 前置准备（5 项）

- [ ] 1. **Cloudflare 账号**（免费，dash.cloudflare.com 注册）
- [ ] 2. **GitHub 账号**（已有则跳过）
- [ ] 3. 一个空 GitHub 仓库（例：`mangshi-2026`）
- [ ] 4. `index.html`（即您当前这份，已经做好了）
- [ ] 5. git 命令行 / GitHub Desktop / GitHub 网页拖拽（三选一）

---

## 🚀 部署步骤（约 5 分钟）

### Step 1 · GitHub 建空仓库

1. 登录 GitHub → 右上角 `+` → `New repository`
2. 仓库名：`mangshi-2026`（或您喜欢，最终域名会带这个名字）
3. Public/Private 任意（**Pages 支持私有仓库**，代码看不到）
4. ⚠️ **不要**勾选 "Add a README file" / "Add .gitignore" / "Choose a license"（保持空仓库最干净）
5. 点 `Create repository` → 复制页面顶部那个 HTTPS 地址：
   `https://github.com/您的用户名/mangshi-2026.git`

### Step 2 · 上传 `index.html`

**方案 A · 命令行（推荐程序员）**

桌面新建文件夹 `mangshi-2026`，把 `index.html` 放进去，然后：

```bash
cd mangshi-2026
git init -b main
git add .
git commit -m "首发：滇西十二日"
git remote add origin https://github.com/您的用户名/mangshi-2026.git
git push -u origin main
```

需要 GitHub 用户名密码（或 Personal Access Token，2024 后强制）。

**方案 B · GitHub 网页拖拽（推荐非程序员，30s）**

1. 进入刚创建的仓库页面
2. 看到一个 `uploading an existing file` 链接，点进去
3. 把 `index.html` 拖到上传区
4. 底部 Commit message 留默认或写"首发"
5. 点 `Commit changes`

### Step 3 · Cloudflare 接仓库

1. 登录 [dash.cloudflare.com](https://dash.cloudflare.com)
2. 左侧菜单 → **`Workers & Pages`** → 右上 **`Create application`**
3. 选 **`Pages`** 标签卡 → 点 **`Connect to Git`**

### Step 4 · 授权 + 选仓库

1. 选 `GitHub` 图标 → 弹窗授权（首次需要，OAuth 流程，1 分钟）
2. 选 `Only select repositories` → 选您刚才建的 `mangshi-2026`
3. 点 `Install & Authorize`

### Step 5 · 配置构建（关键）

回到 Cloudflare 后看到配置页：

| 字段 | 值 |
|---|---|
| **Project name** | `mangshi-2026`（最终域名 = `mangshi-2026.pages.dev`） |
| **Production branch** | `main` |
| **Framework preset** | `None` |
| **Build command** | **留空** |
| **Build output directory** | `/`（或 `.`，二选一即根目录） |

### Step 6 · Save and Deploy

- 点 `Save and Deploy`
- 等 30s – 2min，看右侧 Build log 是否通过
- 顶部出现 🎉 部署成功

### Step 7 · 访问 + 测试

- 部署成功后 Cloudflare 会显示 URL（形如 `https://mangshi-2026.pages.dev`）
- 用手机打开测试：倒计时、地图、SVG、导航跳转是否都正常
- 把这个 URL 发给同行 3 人，所有人都看同一份

---

## ✏️ 后续怎么改（无限循环）

任何时候、任何地方、任何设备：

1. **本机**：用任意编辑器改 `index.html`
2. **提交**：
   ```bash
   git add . && git commit -m "调整 D2 螃蟹时间" && git push
   ```
3. **自动上线**（30s 后所有人看到新版本）

### 手机上也能改

1. 在手机上装 GitHub App
2. 进 `mangshi-2026` 仓库 → 选 `index.html` → 右上铅笔 → 改 → Commit → 自动部署

**iOS Safari 也能用**：github.com 网页直接 edit → commit，30s 全网生效

### 改坏了回滚

Cloudflare Dashboard → `Workers & Pages` → `mangshi-2026` → `Deployments` 标签 → 找到历史版本 → `Rollback to this deploy`（30s 回到旧版）

git 上也有完整历史，`git log` + `git revert` 都能用。

---

## ❓ 常见问题

**Q：您说 Workers，为什么这里是 Pages？**
A：Pages 和 Workers 在 Cloudflare 控制台里就在同一个菜单（`Workers & Pages`）。Pages 底层就是 Workers 引擎（实际就是 Functions + Assets），但对静态文件 UX 更友好——一键 Connect to Git、无需 wrangler、无需 Secrets。**您原话"Workers + GitHub 自动发布"在 Cloudflare 内部就是 Pages 的标准用法**。

**Q：会收费吗？**
A：不会。Cloudflare Pages 免费版 = 5000 次部署/月 + 无限带宽 + 自定义域名 + 自动 HTTPS + 全球 CDN。您这份网页一年访问量大概率 < 5k，免费额度覆盖到天荒地老。

**Q：GitHub 私有仓库可以吗？**
A：可以。Cloudflare Pages 支持私有仓库连接，代码对访问者不可见，但 CDN 公开。这是免费功能。

**Q：自定义域名麻烦吗？**
A：不麻烦。Cloudflare Pages → Custom domains → 输入域名 → 按提示加 2 条 DNS 记录（自动给值）→ 等 5 分钟生效。前提是域名已经在 Cloudflare 托管（如果不在，先把 DNS 切到 Cloudflare，免费）。

**Q：能否让用户登录 / 加私有页 / 多个用户看不同内容？**
A：可以。但这是 Cloudflare Access（付费），而且您说"不要确认号、不要登录"，所以不需要。

---

## 🆘 卡住了怎么办

| 现象 | 排查 |
|---|---|
| Build log 报错 | 看具体错误，多半是 path 错。`Build output directory` 应该填 `/` 或 `.` |
| 404 Not Found | 确认 `index.html` 就在仓库根目录，不在子文件夹里 |
| 网页能开但样式崩 | 检查 `<head>` 里 CSS 是不是还在，inline 还是 link 标签 |
| 改完没生效 | 看 `Deployments` 标签的 latest commit hash 是不是您刚 push 的 |
| 授权失败 | GitHub 那边取消授权（github.com/settings/applications）后重试 |

---

## 🔄 等价方案对比（仅供了解，您不需要操作）

| 方案 | 复杂度 | 适合场景 |
|---|---|---|
| **Cloudflare Pages（您选）** | ⭐ 最简，5 分钟 | 静态 HTML / SPA / Jekyll |
| Cloudflare Workers + wrangler | 中等，10–15 分钟 | 有服务端逻辑（API、边缘计算） |
| Netlify | 类似 Pages | 备选，CF 国内访问更快 |
| Vercel | 类似 Pages | Next.js 友好 |
| 自建 VPS | 最麻烦 | 不推荐，电车自驾路线上没法运维 |

您选的 Pages 是性价比最高的方案。
