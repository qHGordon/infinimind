# 无界思维 InfiniMind · 网页版（Google Drive）

部署后：

- 在 drive.google.com 右键任意无界思维 `.html` → **打开方式 → 无界思维**，网页里打开的就是最新工程，修改约 2 秒自动存回 Drive。
- Drive 左上角 **新建 → 更多 → 无界思维**：在当前文件夹新建一个工程。
- iPad / 其他电脑：直接打开网址，或用右下角「复制本工程链接」。在 Safari 里可以「添加到主屏幕」。
- 电脑上在 Finder 里双击同一个 `.html` 也照样能用（离线、自动写回本地文件，再由 Drive 桌面版同步）。网页版和本地打开改的是同一份文件，互相之间有版本号和冲突提示。

全程不需要服务器，也不需要上架 Marketplace。下面 `<用户名>` 换成你的 GitHub 用户名。

---

## 文件清单

| 文件 | 作用 |
|---|---|
| `index.html` | 应用本体（数据区是空的，网页版从 Drive 读写数据） |
| `config.js` | **唯一需要你改的文件**：填 Google OAuth 客户端 ID |
| `sw.js`、`manifest.webmanifest`、`icons/` | 离线缓存、主屏幕图标 |
| `privacy.html` | 隐私说明（OAuth 同意屏幕可以填它的地址） |

---

## 一、发布到 GitHub Pages（约 5 分钟）

1. GitHub 新建一个 **Public** 仓库，名字建议 `infinimind`。
2. 仓库页面 **Add file → Upload files**，把这个文件夹里的所有文件（包括 `icons` 文件夹）拖进去，Commit。
3. **Settings → Pages**：Source 选 **Deploy from a branch**，Branch 选 `main`、目录 `/ (root)`，Save。
4. 一两分钟后网址生效：`https://<用户名>.github.io/infinimind/`。打开能看到画布、右下角显示「未配置 Google 登录」就说明部署成功。

## 二、Google Cloud 设置（约 10 分钟，都免费）

打开 https://console.cloud.google.com ，用你的 Google 账号登录。

**A. 建项目、开 Drive API**

1. 顶部项目下拉 → **新建项目**，名字填 `InfiniMind`，创建后切换到这个项目。
2. 左侧菜单 **API 和服务 → 库**，搜 **Google Drive API** → **启用**。

**B. OAuth 同意屏幕**（菜单里叫 **Google Auth Platform** 或「OAuth 同意屏幕」）

1. **品牌 / Branding**：应用名称 `无界思维`，用户支持邮箱选你自己；应用首页填 `https://<用户名>.github.io/infinimind/`，隐私权政策填 `https://<用户名>.github.io/infinimind/privacy.html`（可选）。
2. **目标对象 / Audience**：用户类型选 **外部 (External)**，发布状态保持 **测试 (Testing)**，在 **测试用户** 里加上你自己的 Gmail。
   - 自己用，保持「测试」就够了，不需要任何审核。授权时会看到「Google 尚未验证此应用」，点「继续」即可。
3. **数据访问 / Data Access**：**添加或移除范围**，勾选（或在下方手动粘贴）：
   - `https://www.googleapis.com/auth/drive.file`
   - `https://www.googleapis.com/auth/drive.install`

   这两个都是「非敏感」范围。

**C. 创建客户端 ID → 填进 config.js**

1. **客户端 / Clients**（或 **API 和服务 → 凭据 → 创建凭据 → OAuth 客户端 ID**）。
2. 应用类型 **Web 应用**；**已获授权的 JavaScript 来源** 添加：`https://<用户名>.github.io`（只写到域名，不带 `/infinimind`）。重定向 URI 不用填。
3. 创建后复制 **客户端 ID**（形如 `1234567890-xxxx.apps.googleusercontent.com`）。
4. 回到 GitHub 仓库，点开 `config.js` → 右上角铅笔编辑 → 把 ID 粘进引号里 → Commit。

**D. 接入 Drive 的「打开方式」和「新建」**

1. **API 和服务 → 已启用的 API 和服务**，页面下方点 **Google Drive API**，切到 **Drive UI integration**（云端硬盘界面集成）标签页。
2. 按顺序填写：
   - Application name：`无界思维`
   - Short description：`无边际画布 × 思维导图`
   - Application icons：上传 `icons/icon-128.png`、`icons/icon-256.png`（以及其他尺寸，可选）
   - **Open URL**：`https://<用户名>.github.io/infinimind/`
   - Default MIME types / Default file extensions：留空
   - **Secondary MIME types**：`text/html`
   - **Secondary file extensions**：`html`
   - 勾选 **Creating files**，**New URL** 填 `https://<用户名>.github.io/infinimind/`，Document name 留空
   - 不要勾「Automatic OAuth 2.0 consent screen」之类的旧选项
3. **Submit**。

## 三、第一次使用

1. 打开 `https://<用户名>.github.io/infinimind/`，点右下角 **连接 Google Drive**，在弹出的小窗里两项权限都允许。**这一步之后「打开方式」里才会出现无界思维。**
2. 到 drive.google.com，右键你的 `无界思维.html` → **打开方式 → 无界思维**。第一次会再让你点一下「连接 Google Drive」。
3. 之后右下角显示「已保存 时:分」就说明在自动存回 Drive。点它可以：复制本工程链接（发到 iPad 用）、打开其他工程、新建工程。
4. iPad：Safari 打开上面的链接 → 分享 → **添加到主屏幕**，以后点图标就进到上次的工程。

## 常见问题

- **「打开方式」里没有无界思维**：先完成「第一次使用」第 1 步（授权里包含 `drive.install`）；刷新 Drive 页面。新上传的图标最多要 24 小时才显示。
- **隔一阵右下角变成「Drive 连接已过期」**：Google 的网页授权大约 1 小时过期。正常情况下你下一次点击画布时会自动续上（小窗一闪即关）；如果没续上，点一下角标即可。期间的修改都存在浏览器里，不会丢。
- **网页版只列出部分文件**：出于隐私，应用只能看到「用无界思维打开或新建过」的文件。别的 `.html` 用一次「打开方式」就能加进来。
- **两台设备同时改了**：保存时会弹窗让你选保留哪一份，没被选的那份会自动下载成 `.json`。
- **改了网址或换了域名**：同时更新 C 步的 JavaScript 来源和 D 步的 Open URL / New URL。
- **以后要给别人用**：在 Audience 里把发布状态改为「正式版 (In production)」，按提示提交品牌验证（需要自己的域名、隐私政策页 `privacy.html`）；要出现在 Workspace Marketplace 里还需要另外提交 Marketplace 审核。
