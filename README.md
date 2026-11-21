# 简·应用站

张文军（jerryzwj）的个人应用发布页：安卓应用、飞牛 NAS 应用、服务与工具统一归类，每个应用都有介绍、下载入口和项目地址。

- 线上：<https://apps.wenjun.eu.org>
- 仓库：<https://github.com/jerryzwj/jian-apps>
- 托管：Cloudflare Pages，直接吃仓库里的静态文件，没有构建步骤

## 目录

```
jian-apps/
├─ index.html                     单页站点（样式、数据、交互全部内联）
├─ README.md
└─ .github/workflows/deploy.yml   可选：用 Actions 推 Cloudflare Pages
```

## 本地预览

双击 `index.html` 就能看，不用起服务。

## 新增 / 修改应用

打开 `index.html`，找到 `const APPS = [...]`，照已有对象加一条即可，卡片、筛选、搜索、详情弹窗都会自动出现：

```js
{
  id:'myapp',                 // 唯一标识
  cat:'android',              // android | nas | server
  icon:'note',                // ICONS 里的键：bug tv note pulse book network merge dl term tunnel rss sync moon video box
  name:'简·某某',
  tagline:'一句话说明',
  version:'v1.0.0',
  size:'3.2 MB',
  platforms:['Android'],
  desc:'两三句完整介绍',
  feats:['要点一','要点二'],
  download:{label:'下载 APK', url:'https://github.com/.../app.apk'},
  repo:'https://github.com/jerryzwj/xxx',
  changelog:'https://github.com/jerryzwj/xxx/releases',
  live:{label:'打开网页版', url:'https://xxx.wenjun.eu.org'},
  cmd:'docker run ...',       // 可选，弹窗里显示一行命令
  private:true,               // 可选，显示「私有 · 不公开」标签
  note:'可选提示语'
}
```

分类文案在 `CATS` 里改，页脚邮箱、GitHub、域名在 `<footer>` 里改。

## 部署到 Cloudflare Pages

### 方式 A：控制台连仓库（推荐，无需密钥）

1. Cloudflare 控制台 → Workers & Pages → Create → Pages → Connect to Git
2. 选 `jerryzwj/jian-apps`，分支 `main`
3. 构建命令留空（Framework preset 选 None），输出目录填 `/`
4. Save and Deploy，几十秒后拿到 `jian-apps.pages.dev`

首次部署后绑定自定义域名：

5. 项目 → Custom domains → Set up a custom domain → 填 `apps.wenjun.eu.org`
6. 域名在同一个 Cloudflare 账号下时 CNAME 会自动创建，等状态变成 Active 即可
7. 之后每次 `git push` 到 `main` 自动重新部署

### 方式 B：GitHub Actions 推送（需要 API Token）

适合想要部署日志、或仓库不在同一个 CF 账号下：

1. 建一个 Cloudflare API Token：Profile → API Tokens → Create Token，模板选 **Edit Cloudflare Workers**，权限至少包含 `Account → Cloudflare Pages → Edit`
2. 在 Cloudflare 控制台右侧或域名概览页抄下 **Account ID**
3. 仓库 Settings → Secrets and variables → Actions 新建两个 Secret：
   - `CLOUDFLARE_API_TOKEN`
   - `CLOUDFLARE_ACCOUNT_ID`
4. 推一次 `main`，或者在 Actions 里手动 Run workflow

工作流做的事：`wrangler pages deploy . --project-name=jian-apps --branch=main`。首次跑会自动创建 Pages 项目。

## 域名后缀

站点固定用 `apps.wenjun.eu.org`。想换子域（比如 `app` 或 `jian`），改两处：

- Cloudflare Pages 项目的 Custom domains
- `index.html` 里「关于」段落和页脚的文案

## 说明

- 纯静态，不接统计脚本，不收集访问数据
- 深浅色跟随系统，手动切换会记在 localStorage
- 应用下载全部指向 GitHub Releases 或 Docker Hub，不在本仓库放二进制
