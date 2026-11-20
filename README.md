# whitdust.com 的网站文件

这里是 `whitdust.com` 的全部内容。**整个文件夹就是要上传的东西**——上传之后每个人都有网可看。

> 2026-11-20：名字从 `whidust` 改为 `whitdust`（whit 古英语「一点」+ dust，一点微尘；又与 wit 同音）。域名 whitdust.com 已启用；旧域名 whidust.com 已做 301 跳转，到期不续。

## 文件结构

```
A网站/
  index.html     ← 整站就这一个页面（2026-11-20 合并：主页/工具/小游戏/个人/文档都在里面）
  files/         ← 要给人下载的东西（单个 HTML、pdf、zip 都行）
  README.md      ← 这份说明，不用上传也行
```

**五个栏目是同一个文件里的五段**，靠顶部按钮切换：地址栏是 `whitdust.com/#tools` 这种，`#` 后面那段叫锚点，它不发给服务器，只告诉浏览器显示哪一段。刷新会停在原来那一栏。

- **加一栏**：在 `<nav>` 里加一个 `<a href="#新名字">`，再在 `<main class="panel">` 里加一段 `<section class="page" id="新名字">`，最后把 `新名字` 加进脚本最上面那行 `var pages = [...]`。三处都要加，缺一处就不显示。
- 样式不再单独一个 `style.css`，全在 `index.html` 的 `<style>` 里 —— 上传时少一个文件，也不会出现样式丢了的情况。

## 文字归主人写，我只动结构

这个站的文案是主人自己写的——「孩子不懂事写着玩的」「我是谁？这是一个好问题」「唯一正经的东西」，都是他的原话。

**不替他改措辞。** 我只碰结构：加栏目、挪条目、修链接、调样式、补占位。要动文字，先问他要不要改、怎么改；他写下的句子（包括语气词、自嘲、省略号）一律原样留着，我觉得写得不好也不动。

理由：这些句子是他这个人露在外面的部分，我润一句，就变成我写的了。上一版我写的「这里放白尘的东西」被他换掉，是对的——那句话放在任何个人主页上都成立，等于没说。

## 改内容

- **文字**：找到对应栏目的 `<section>`，里面的中文直接改。
- **配色**：`index.html` 开头 `<style>` 里的变量。`--bg-top` / `--bg-bottom` 是淡蓝背景的渐变两头，`--panel` 是卡片的浅白，`--accent` 是当前栏目按钮的深蓝。
- **条目列表**：用 `<ul class="item-list">`，一件一条 `<li>`，样式已经写好了。
- **加文件**：丢进 `files/`，链接写成 `files/你的文件名`。
- **预览**：双击 `DSH工作区\网页预览\index.html`。它和 `A网站\index.html` 是**同一个文件的两个名字**（硬链接），改哪边都是改同一份，不存在“预览是新的、传上去是旧的”。改动后强刷浏览器（Ctrl+F5）。
  - 如果哪天用某个编辑器“另存为”保存，可能把这个链接弄断，那时预览里的改动就不会进网站了。发现两边内容不一样时告诉我，一条命令能重建。


## 线上是怎么部署的

**不是 Pages，是 Worker + 静态资源。** 以下是 2026-11-20 实测确认过的现状，改动前先看这一节。

| 项 | 值 |
|---|---|
| 平台 | Cloudflare **Workers**（不是 Pages） |
| 项目名 | `whitdust`（2026-11-20 从 whidust 改名） |
| 默认地址 | `whitdust.a13303330333.workers.dev` —— 国内大多打不开，它只是默认占位地址，不用管 |
| 正式入口 | `https://whitdust.com`（Worker 自定义域） |
| 证书 | Cloudflare 自动签（签发方 Google Trust Services），已生效 |
| HTTP 跳转 | 已开 Always Use HTTPS，`http://` 一律 301 到 `https://` |
| www | **没配**，`www.whitdust.com` 解析不到 |
| 额度 | 静态资源请求免费且不限次；只有命中 Worker 脚本的请求才计入 10 万次/天 |

### 域名迁移记录（2026-11-20，已完成）

1. whitdust.com 在 Spaceship 购买 → Cloudflare 添加站点 → 域名的域名服务器改成 `janet` / `zeus.ns.cloudflare.com`（与旧域名同一对）
2. Worker「自定义域」加 `whitdust.com`，子域名留空，启用对象选「生产」；证书自动签发
3. 新域名 zone 里单独开了「始终使用 HTTPS」（这个设置每个域名各管各的）
4. 旧域名 `whidust.com`：先解绑 Worker 的自定义域，再在 zone 里加一条 A 记录占位（`192.0.2.1`，橙云），然后加一条重定向规则
   - 请求 URL `https://whidust.com/*` → 目标 `https://whitdust.com/${1}`，301，保留查询字符串
   - 注意：带 `#锚点` 的旧链接跳过去会落在首页（锚点不发给服务器，搬不过来）
5. Worker 项目名从 `whidust` 改成 `whitdust`（只影响 workers.dev 默认地址）
6. VPS 隧道主机名换成 `panel.` / `sub.whitdust.com`，服务地址仍是 `localhost:13330` / `2096`，服务器上没动任何东西
7. 旧域名到期不续

**踩过的坑**：给 zone 加第一条记录前 Cloudflare 不给激活；Worker 自定义域遇到「externally managed DNS records」要先删占位记录；新主机名的 DNS 有负缓存，最长 30 分钟才全球生效。

### 更新一次内容要做什么（2026-11-20 起：自动部署）

推送即发布，不用再拖文件夹。

```powershell
cd "C:\Users\eric0\Desktop\DSH工作区\大肥鱼\A网站"
git add -A
git commit -m "这次改了什么"
git push
```

推完等一分钟左右，Cloudflare 自动拉代码、执行 `npx wrangler deploy`。打开 https://whitdust.com 强刷即可。

- 仓库：https://github.com/whidust/whitdust-pub（公开）
- 接法：Workers → `whitdust` → 设置 → 构建 → 连接 Git 仓库；分支 `main`；构建命令留空；部署命令 `npx wrangler deploy`
- 仓库里的 `wrangler.jsonc` 说明“这是一个纯静态 Worker，当前目录就是资源目录”
- `.assetsignore` 把 `.git/`、`wrangler.jsonc`、`.assetsignore` 排除在部署之外（已实测：`/.git/config` 和 `/wrangler.jsonc` 都返回 404）
- 回退：在 GitHub 上找到要回退的提交，或者本地 `git revert` 后再推

（本机不需要装 wrangler——构建时 Cloudflare 会在它自己的环境里跑。）

### 踩过的两个坑

- **「路由」不等于「自定义域」。** 路由只告诉 Cloudflare「这个域名的请求交给这个 Worker」，**不会替你建 DNS 记录**，所以加完路由照样打不开。要的是「+ 添加域名」，它会自己补 DNS 和证书。
- **workers.dev 那个地址在国内基本打不开。** 它通不通跟你没关系，验证一律走绑好的正式域名。

### 为什么不用 Pages

Pages 的唯一实质优势是「请求和带宽都不限」，而 Worker 静态资源的请求同样免费不限次，差别基本抹平了。迁到 Pages 要先把自定义域从 Worker 解绑、重建项目、重新等证书，没有收益。真到哪天被额度卡住（只有存在 Worker 脚本时才可能），再迁也不迟——文件都在本地，迁过去就是重新上传一次。

### 待确认（没查过，别当已知）

- 这个项目里到底有没有 Worker 脚本，还是纯静态资源。有脚本的话，「没命中静态文件的请求」会走脚本、消耗那 10 万次额度；纯静态就不消耗。面板里看一眼有没有代码入口就能确定。

## 注意事项

- **单文件最大 25 MB**（Worker 静态资源上限）。更大的文件（视频、大压缩包）得放 R2 或者别处。
- 只能放静态的东西：HTML / CSS / JS / 图片 / 文档。要跑后端程序得另外找服务器，或者在 Worker 里写脚本。
- 域名和这个项目的绑定在 Cloudflare 里，跟 VPS 无关；VPS 挂了这站也不受影响。
- **本地和线上现在不一致**：本地 `files/` 里已经有四个独立 HTML（`多K练习器v3.html`、`代码换行处理工具-压缩-展开.html`、`映射表.html`、`替换器.html`），都还没传上去；线上跑的还是初版，`files/example.txt` 在那儿。首页目前是空白壳，只有右上角三个按钮，作品列表还没写。
