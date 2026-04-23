# 花花的blog

> 遇到困难睡觉觉

个人博客源码，基于 [Hexo](https://hexo.io) + [NexT](https://theme-next.js.org) 主题，发布在 GitHub Pages。

- **线上站点**：<https://huahua319.github.io>
- **源码仓库**（本仓库）：<https://github.com/huahua319/blog-source>
- **静态站点仓库**：<https://github.com/huahua319/huahua319.github.io>

## 相关文档

- [USAGE.md](USAGE.md) — 日常操作速查手册
- [SPEC.md](SPEC.md) — 工程说明（面向维护者 / AI）
- [AGENTS.md](AGENTS.md) — 跨 AI 工具的起手指引

## 技术栈

| 组件 | 版本 | 说明 |
|---|---|---|
| Hexo | 8.x | 静态站点生成器 |
| NexT | 8.27 | 主题（Gemini 双栏配色） |
| Node.js | ≥ 20 | 运行环境 |

已启用插件：本地搜索、RSS、sitemap、字数统计。

## 两个仓库的分工

```
                   ┌─────────────────────┐
  写作 (本仓库)    │   blog-source       │
                   │  Markdown / 配置    │
                   └──────────┬──────────┘
                              │ hexo generate
                              ▼
                   ┌─────────────────────┐
  发布             │  public/ (本地生成) │
                   └──────────┬──────────┘
                              │ hexo deploy
                              ▼
                   ┌─────────────────────┐
  线上             │ huahua319.github.io │───► https://huahua319.github.io
                   │   静态页面          │
                   └─────────────────────┘
```

- **blog-source**：日常写作改配置的地方，全部源文件在此（本仓库）
- **huahua319.github.io**：只存放构建后的静态页，由 `hexo deploy` 自动推送，**不需要手工操作**

## 目录结构

```
blog/
├── _config.yml              # 站点主配置（标题、作者、URL、部署）
├── _config.next.yml         # NexT 主题配置（外观、菜单、侧栏、搜索）
├── package.json             # npm 依赖清单
├── package-lock.json        # 依赖锁定
├── .gitignore               # 排除 node_modules/、public/、.deploy_git/、db.json
├── .github/
│   └── dependabot.yml       # GitHub 依赖自动更新配置
├── scaffolds/               # hexo new 时的模板
│   ├── post.md              #   新建文章 (hexo new post)
│   ├── page.md              #   新建页面 (hexo new page)
│   └── draft.md             #   新建草稿 (hexo new draft)
├── source/                  # 内容源（全部为 Markdown）
│   ├── _posts/              #   已发布文章
│   ├── _drafts/             #   草稿（默认不渲染，按需生成）
│   ├── about/index.md       #   关于页
│   ├── categories/index.md  #   分类索引页（type: categories）
│   └── tags/index.md        #   标签索引页（type: tags）
└── themes/                  # 主题目录（NexT 已通过 npm 安装，此处为空）
```

> **生成产物 `public/` 和依赖目录 `node_modules/` 不纳入版本控制**，首次克隆后用 `npm install` 恢复。

## 本地开发

### 首次克隆

```bash
git clone git@github.com:huahua319/blog-source.git blog
cd blog
npm install          # 还原所有依赖（约 30 秒）
```

### 预览

```bash
hexo clean           # 清除缓存（修改配置或主题后必须）
hexo generate        # 生成静态文件到 public/
hexo server          # 启动本地服务，访问 http://localhost:4000
```

合并写法：`hexo clean && hexo g && hexo s`

## 写作流程

### 1. 新建文章

```bash
hexo new post "文章标题"
# → 生成 source/_posts/文章标题.md
```

### 2. 填写 front-matter 并写正文

```yaml
---
title: 标题
date: 2026-04-22 10:00:00
categories:
  - 技术          # 或「随笔」
tags:
  - 具体标签
  - 第二个标签
---

正文从这里开始……
```

**目前使用的分类约定：**

| 分类 | 用途 |
|---|---|
| `技术` | 技术文章、学习笔记、踩坑记录 |
| `随笔` | 生活随笔、日记、想法 |

### 3. 本地预览

```bash
hexo clean && hexo g && hexo s
```

打开 http://localhost:4000 确认效果。

### 4. 发布

```bash
# a. 构建并推送到 GitHub Pages（线上站点立即更新）
hexo deploy

# b. 备份源码到本仓库（建议每次发布后都执行）
git add .
git commit -m "新增：文章标题"
git push
```

## 关键配置文件

### `_config.yml` —— 站点配置

常改字段：

```yaml
title: 花花的blog
subtitle: 遇到困难睡觉觉
description: 记录技术与生活
author: 花花
url: https://huahua319.github.io
theme: next
deploy:
  type: git
  repo: git@github.com:huahua319/huahua319.github.io.git
  branch: main
```

完整说明：<https://hexo.io/docs/configuration.html>

### `_config.next.yml` —— 主题配置

常改字段：

```yaml
scheme: Gemini            # 配色方案：Muse / Mist / Pisces / Gemini
menu:                     # 顶部导航
  home: / || fa fa-home
  about: /about/ || fa fa-user
  tags: /tags/ || fa fa-tags
  categories: /categories/ || fa fa-th
  archives: /archives/ || fa fa-archive
local_search:
  enable: true            # 本地搜索已启用
```

完整说明：<https://theme-next.js.org/docs/>

**升级主题**：`npm update hexo-theme-next`，随后把 `node_modules/hexo-theme-next/_config.yml` 和本地 `_config.next.yml` 对比合并新增字段。

## 常用命令

| 命令 | 作用 | npm 等价 |
|---|---|---|
| `hexo new post "<标题>"` | 新建文章 | — |
| `hexo new page <名>` | 新建独立页面（如 about） | — |
| `hexo new draft "<标题>"` | 新建草稿（不会发布） | — |
| `hexo publish "<草稿名>"` | 把草稿转为正式文章 | — |
| `hexo clean` | 清除缓存和 `public/` | `npm run clean` |
| `hexo g` | 生成静态文件 | `npm run build` |
| `hexo s` | 本地预览（默认 4000） | `npm run server` <br> `npm run dev` (带热重载) |
| `hexo d` | 部署到 GitHub Pages | `npm run deploy` |
| `hexo list post` | 列出所有文章 | — |

## 环境前置

- Node.js ≥ 20、npm、Git
- GitHub SSH 认证已配置（`~/.ssh/id_ed25519` 对应公钥已加到 GitHub 账号）
- **国内网络**：GitHub SSH 22 端口常被重置，在 `~/.ssh/config` 加入：

  ```
  Host github.com
    Hostname ssh.github.com
    Port 443
    User git
  ```

## 后续可扩展

- **评论系统**：[Giscus](https://giscus.app)（基于 GitHub Discussions，免维护）或 [Waline](https://waline.js.org)
- **自定义域名**：在 GitHub Pages `Settings → Pages → Custom domain` 绑定，并在 `source/CNAME` 文件中写入域名
- **图床**：[PicGo](https://picgo.github.io/PicGo-Doc/) + GitHub 仓库做图床
- **访问统计**：不蒜子 / Umami / Cloudflare Web Analytics
- **SEO**：已启用 `sitemap.xml` 和 `atom.xml`（RSS），可提交到搜索引擎站长平台
- **数学公式**：在 `_config.next.yml` 中启用 KaTeX 或 MathJax
- **图表**：开启 Mermaid 支持写流程图

## 常见问题

**Q：`hexo deploy` 报 `Connection reset by ... port 22`？**
A：GitHub SSH 22 端口国内不稳定，按上面「环境前置」配置 SSH 走 443 端口。

**Q：修改主题配置后预览没变化？**
A：先 `hexo clean` 再 `hexo g && hexo s`。

**Q：文章里的图片放哪？**
A：放 `source/images/` 下，文章中引用 `![](/images/xxx.png)`。若要每篇文章独立资源目录，在 `_config.yml` 设 `post_asset_folder: true`。

**Q：误提交了 `node_modules/` 或 `public/`？**
A：这两个目录已在 `.gitignore` 中，正常情况不会被追踪。如果被误加过，执行 `git rm -r --cached node_modules public` 后重新 commit。

## License

源代码采用 MIT 授权；`source/_posts/` 下的文章内容版权归作者所有（花花），未经许可请勿转载。
