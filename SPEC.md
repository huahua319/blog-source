# SPEC

本文档是本仓库的**长期项目说明书**，面向后续维护者和 AI 编码代理（Claude Code 等）。
动手修改之前请先读完本文，再参照其他文档（`README.md` / `USAGE.md`）或直接操作。

所有陈述均基于当前仓库实际文件内容。若本文与 `README.md` / `USAGE.md` 有出入，**以本文 + 实际文件为准**。已知不一致之处在 §12 列出。

---

## 1. 项目定位

**这是什么**：一个由个人作者「花花」维护的静态博客**源码仓库**。博客基于 Hexo 生成，使用 NexT 主题，最终以静态页面形式发布在 GitHub Pages。

**核心目标**：

- 用 Markdown 承载内容，保留对原始内容的完全控制
- 内容永久可回溯（Git 历史）
- 发布流程可一条命令完成（`hexo deploy`）
- 对维护者友好（只需改 Markdown / YAML，不改主题源码）

**项目边界（不是什么）**：

- 不是通用 Hexo 模板 / 脚手架，不追求覆盖所有场景
- 不是动态站点，**没有后端、数据库、登录、用户系统**
- 不是主题仓库，**不承担对 NexT 主题本身的二次开发**
- 不承载与博客内容无关的代码（脚本、工具、实验项目请另建仓库）

任何扩展应先问：**「这件事是否属于内容或发布本身？」** 如果不是，考虑放到别处。

---

## 2. 仓库角色与发布关系

博客体系由**两个 GitHub 仓库**构成，职责完全不同：

| 仓库 | 内容 | 由谁写 | 何时更新 |
|---|---|---|---|
| `huahua319/blog-source`（**本仓库**） | Markdown 源文件、Hexo / NexT 配置、`scaffolds/`、`package.json` 等 | 作者本人 | 每次写作或改配置后 `git push` |
| `huahua319/huahua319.github.io` | 纯静态页面（`public/` 的构建产物） | `hexo-deployer-git` 自动推送 | 每次 `hexo deploy` 后 |

对应关系：

```
[本仓库 blog-source]
  ├── source/_posts/*.md        ─┐
  ├── source/about/index.md     ─┤
  ├── _config.yml               ─┼─► hexo generate ─► public/ ─► hexo deploy ─► [huahua319.github.io] ─► https://huahua319.github.io
  └── _config.next.yml          ─┘
```

**维护原则**：

- **所有写作 / 配置修改都在本仓库发生**
- **`huahua319.github.io` 仓库不应手动编辑**，它是构建产物的落地点，任何手改都会被下次 `hexo deploy` 覆盖
- 本仓库**不**跟踪 `public/`，那是构建产物
- 本仓库也**不**跟踪 `node_modules/`，依赖由 `package.json` + `package-lock.json` 恢复

---

## 3. 当前技术栈

基于 `package.json` 的实际声明（`^` 表示允许向后兼容更新）：

| 组件 | 声明版本 | 实际安装 / 锁定 | 用途 |
|---|---|---|---|
| Hexo | `^8.0.0` | `8.1.1`（`package.json` `hexo.version` 字段） | 静态站点生成器 |
| NexT 主题 | `^8.27.0` | 同上量级 | 当前使用的主题 |
| Node.js | `>=20`（`engines.node`） | 本地 `v24.14.0`（开发者机） | 运行时 |
| npm | 随 Node | `11.9.0`（开发者机） | 包管理 |

**已安装的 Hexo 相关依赖（全部来自 `package.json`）**：

生成与输出：
- `hexo-generator-index` — 首页
- `hexo-generator-archive` — 归档页 `/archives/`
- `hexo-generator-category` — 分类页 `/categories/<name>/`
- `hexo-generator-tag` — 标签页 `/tags/<name>/`
- `hexo-generator-feed` — RSS `/atom.xml`
- `hexo-generator-sitemap` — `sitemap.xml`
- `hexo-generator-search` — 本地搜索数据（NexT 的 `local_search` 依赖它）

渲染与服务：
- `hexo-renderer-ejs` — EJS 模板
- `hexo-renderer-marked` — Markdown 渲染
- `hexo-renderer-stylus` — Stylus CSS
- `hexo-server` — 本地预览服务

部署与统计：
- `hexo-deployer-git` — 推送 `public/` 到目标 Git 仓库
- `hexo-word-counter` — 字数/阅读时间（配合 NexT `symbols_count_time` 显示）

主题：
- `hexo-theme-next` — **当前使用**

**关键事实**：

- 主题来源是 `node_modules/hexo-theme-next/`，**不是** `themes/` 目录。本仓库 `themes/` 仅含 `.gitkeep` 占位
- `.github/dependabot.yml` 已启用：每周检查 npm 依赖，最多同时开 20 个 PR。作者可选择合并或关闭该自动化

---

## 4. 目录结构说明

以下为当前仓库的**真实**结构（Git 跟踪内容；`node_modules/`、`public/`、`db.json` 等被 `.gitignore` 排除）：

```
blog/
├── .github/
│   └── dependabot.yml          # GitHub 依赖自动更新（每周，上限 20 PR）
├── .gitignore                   # 排除构建产物、依赖、临时文件
├── AGENTS.md                    # 跨 AI 工具起手指引（Codex / Cursor / Claude Code 等均读）
├── CLAUDE.md                    # Claude Code 特有补充（指向 AGENTS.md）
├── README.md                    # 面向读者的项目说明
├── SPEC.md                      # 本文件，面向维护者 / AI 的工程文档
├── USAGE.md                     # 日常操作速查手册
├── _config.next.yml             # NexT 主题配置（Alternate Config 模式）
├── _config.yml                  # Hexo 站点主配置
├── package.json                 # npm 依赖清单 + scripts
├── package-lock.json            # 依赖锁定
├── scaffolds/                   # hexo new 使用的模板
│   ├── draft.md                 # 草稿模板：title + tags（无 date）
│   ├── page.md                  # 独立页面模板：title + date
│   └── post.md                  # 文章模板：title + date + tags（无 categories）
├── source/_data/                # NexT 覆盖配置
│   ├── styles.styl              # 自定义 CSS 样式（主色调、圆角等）
│   └── variables.styl           # 覆盖 NexT 布局宽度变量
├── source/                      # 所有内容源（全部是 Markdown）
│   ├── _posts/                  # 已发布文章（文件名 = slug）
│   │   ├── 开始写博客的理由.md  # 随笔示例
│   │   └── 我的第一篇技术笔记.md # 技术示例
│   ├── 404.md                   # 404 页面（permalink: /404.html）
│   ├── about/index.md           # 关于页
│   ├── categories/index.md      # 分类索引页（front-matter 带 type: categories）
│   └── tags/index.md            # 标签索引页（front-matter 带 type: tags）
│   ├── images/                  # 图片及静态资源
│   │   └── avatar.jpg           # 用户头像
└── themes/
    └── .gitkeep                 # 占位；NexT 主题实际在 node_modules/ 下
```

**几个需要特别说明的点**：

- `source/_drafts/` 目录**当前不存在**。它只在首次执行 `hexo new draft <名>` 时由 Hexo 自动创建。默认 `render_drafts: false`，草稿不会进入 `public/`
- `.gitignore` 中 `.deploy*/` 对应 `hexo-deployer-git` 的临时目录 `.deploy_git/`

---

## 5. 配置层级与权威来源

本仓库存在多处配置，定位清晰：

| 层 | 文件 | 影响范围 |
|---|---|---|
| 站点层 | `_config.yml` | 标题、URL、permalink、语言、部署目标、启用的插件行为 |
| 主题层 | `_config.next.yml` | 外观、菜单、侧栏、搜索开关、代码高亮主题、暗黑模式 |
| 内容层 | `source/**/*.md` | 实际呈现的文字、front-matter 中单篇行为覆盖 |
| 依赖层 | `package.json` / `package-lock.json` | 所有插件和主题版本 |
| 模板层 | `scaffolds/*.md` | `hexo new` 生成文件的初始 front-matter |
| 忽略层 | `.gitignore` | 哪些文件不进入 Git |

### 5.1 典型行为的权威文件速查

想改或排查某个行为时，按下表找对应文件，**不要搞反**。

| 想改 / 排查的行为 | 权威文件 | 关键字段 / 说明 |
|---|---|---|
| 站点标题、副标题、描述、作者 | `_config.yml` | `title` / `subtitle` / `description` / `author` |
| 站点 URL、永久链接格式 | `_config.yml` | `url` / `permalink`（现为 `:year/:month/:day/:title/`） |
| 部署目标仓库 | `_config.yml` | `deploy.repo` / `deploy.branch`（当前 `main`） |
| 语言与时区 | `_config.yml` | `language: zh-CN` / `timezone: Asia/Shanghai` |
| 首页每页文章数 | `_config.yml` | `index_generator.per_page`（现为 10） |
| 草稿是否渲染 | `_config.yml` | `render_drafts: false` |
| 图片资源策略 | `_config.yml` | `post_asset_folder`（现为 `false`） |
| 代码高亮后端 | `_config.yml` | `syntax_highlighter: highlight.js` |
| 当前主题 | `_config.yml` | `theme: next` |
| 配色方案 | `_config.next.yml` | `scheme`（现为 `Pisces`） |
| 暗黑模式 | `_config.next.yml` | `darkmode: true` |
| 顶部菜单 | `_config.next.yml` | `menu`（现 5 项：home/about/tags/categories/archives） |
| 侧栏位置 / 宽度 / 社交图标 | `_config.next.yml` | `sidebar` / `social` / `avatar` |
| 文章侧栏目录 | `_config.next.yml` | `toc.enable: true`，`max_depth: 6` |
| 本地搜索 | `_config.next.yml` | `local_search.enable: true` |
| 回到顶部 | `_config.next.yml` | `back2top.enable: true` |
| 字数与阅读时间 | `_config.next.yml` | `symbols_count_time`（依赖 `hexo-word-counter`） |
| 代码块高亮主题 / 复制按钮 / 折叠 | `_config.next.yml` | `codeblock`（当前复制按钮与折叠均 `false`） |
| 评论系统 | `_config.next.yml` | `comments` + 对应子节点（**当前全部未启用**） |
| 统计 / 分析 | `_config.next.yml` | `google_analytics` / `umami` / `busuanzi_count` 等（**当前全部未启用**） |
| 数学公式 | `_config.next.yml` | `math.mathjax.enable` / `math.katex.enable`（**当前均未启用**） |
| 文章内容、front-matter | `source/_posts/*.md` | 直接改 md 文件 |
| 单篇文章禁用评论 / 打赏 / TOC | 文章 front-matter | `comments: false` / `reward: false` / `toc: false` |
| 新建文章的默认字段 | `scaffolds/post.md` | 当前**不含** `categories` |

### 5.2 排查顺序

当某个行为异常（例如"侧栏不显示"、"搜索坏了"、"暗黑模式没效果"）时，按以下顺序定位：

1. 单篇文章的 **front-matter** 是否有覆盖（如 `sidebar: false`、`toc: false`）
2. **`_config.next.yml`** 中对应开关是否开启、参数是否合法
3. **`_config.yml`** 中是否设置了矛盾项（少见但可能，如 `theme` 未指向 `next`）
4. 是否遗漏了 `hexo clean`（**配置改动必须清缓存**）
5. 依赖是否装全：`npm ls <包名>`
6. 最后看主题源码：`node_modules/hexo-theme-next/`（只读，不要修改）

---

## 6. 内容维护约定

### 6.1 文件位置

| 内容类型 | 路径 | 生成命令 |
|---|---|---|
| 博客文章 | `source/_posts/<slug>.md` | `hexo new post "标题"` |
| 草稿（不渲染） | `source/_drafts/<slug>.md` | `hexo new draft "标题"`（会自动创建 `_drafts/`） |
| 独立页面 | `source/<名>/index.md` | `hexo new page <名>` |
| 图片等资源 | 默认 `source/images/`（需手动创建） | — |

### 6.2 文件命名

- Hexo `new_post_name: :title.md`：**文件名直接用标题**，支持中文
- 文件名同时是 URL 中的 `:title`。当前 permalink 是 `:year/:month/:day/:title/`，所以文章 URL 会包含中文字符（经 URL 编码）
- 如希望 URL 使用英文 slug，可在 front-matter 中显式指定 `slug: english-name`，不影响文件名和展示标题

### 6.3 front-matter 字段约定

当前使用的字段规范：

```yaml
---
title: 文章标题              # 必填
date: 2026-04-22 10:00:00    # 必填，hexo new 自动写入
categories:                  # 建议填（当前两类约定：技术 / 随笔）
  - 技术
tags:                        # 建议填
  - 标签1
  - 标签2
# 以下字段可选，按需加
# description: 摘要（用于 OG 标签和首页显示，NexT 已启用 excerpt_description）
# sticky: 100                # 置顶（数字越大越前）
# comments: false            # 关闭该篇评论（评论未启用时无影响）
# toc: false                 # 关闭侧栏目录
# mathjax: true              # 按需加载数学公式（启用 math 后才有意义）
---
```

### 6.4 分类约定

当前站点**仅使用两个一级分类**，请保持一致（以避免分类页膨胀）：

| 分类 | 定位 |
|---|---|
| `技术` | 技术文章、学习笔记、踩坑记录 |
| `随笔` | 生活随笔、日记、想法 |

新增第三种分类前，先问自己："这是一个长期会持续产出的新主线吗？" 如果只是偶尔一两篇，优先用 `tags` 区分。

**`hexo-generator-category` 支持多级分类**，如需二级可写 `categories: [技术, 前端]`。当前所有已有文章均为单级。

### 6.5 内容层 vs 站点层

以下区分有助于判断"改哪里"：

- **内容层修改**：新增 / 修改 / 删除 `source/` 下的 Markdown 文件 — **不需要** `hexo clean`，只 `hexo g && hexo s` 即可
- **站点层修改**：改 `_config.yml`、`_config.next.yml`、`package.json`、`scaffolds/` — **必须** `hexo clean` 后再 `hexo g && hexo s`

### 6.6 草稿流程

- 写到一半的长文建议先 `hexo new draft "标题"`，写在 `source/_drafts/`
- 草稿默认不进入 `public/`，可以安心 `git push` 备份
- 完成后 `hexo publish "标题"`，Hexo 自动把 md 从 `_drafts/` 移到 `_posts/` 并更新 date

---

## 7. 主题与样式修改约定

### 7.1 当前主题方案

- 使用 **NexT 8.27**，通过 `npm install hexo-theme-next` 安装，实际文件在 `node_modules/hexo-theme-next/`
- 使用 **Alternate Config 模式**：项目根的 `_config.next.yml` 独立于 `node_modules/hexo-theme-next/_config.yml`，Hexo 会合并加载
- 当前 scheme：`Pisces`（双栏，侧栏固定在左，桌面侧栏宽 240px）

### 7.2 修改优先级：配置 → 主题覆盖 → 源码

改动请按以下顺序尝试，**越靠前越优先**：

1. **改 `_config.next.yml`**：绝大多数外观 / 功能开关都在这里（菜单、侧栏、搜索、暗黑模式、代码块主题、动画、字体等）
2. **覆盖主题的部分模板或样式**：在 `source/_data/` 下建对应文件，再到 `_config.next.yml` 的 `custom_file_path` 里取消注释对应项。当前 `custom_file_path` 全部注释，**`source/_data/` 目录不存在**。如需深度自定义再建
3. **写自定义样式**：通过 `custom_file_path.style` 指向 `source/_data/styles.styl` 添加 Stylus 片段（不改主题源码）
4. **最后才考虑**直接改 `node_modules/hexo-theme-next/` 源码 — **强烈不推荐**，会被下次 `npm install` 覆盖

### 7.3 当前 NexT 已启用 / 未启用功能清单

基于 `_config.next.yml` 实际状态（便于后续维护者快速了解现状，不必重读 915 行配置）。

**已启用**：

- `scheme: Pisces`
- `darkmode: true`（暗黑模式）
- 顶部菜单：home / about / tags / categories / archives（带图标）
- 侧栏：左侧，双栏模式（自定义为 85% 宽屏）
- `site_state: true`（侧栏显示文章/分类/标签计数）
- `toc.enable: true`（自动目录，max_depth: 6，自动编号）
- `back2top.enable: true`
- `motion.enable: true`（页面元素动画）
- `local_search.enable: true`（本地搜索，依赖 `hexo-generator-search`）
- `symbols_count_time`（字数 + 阅读时间，依赖 `hexo-word-counter`）
- `post_meta`（创建时间、更新时间、分类全部显示）
- `post_navigation: left`（上一篇/下一篇）
- `open_graph.enable: true`（社交卡片元信息）
- `excerpt_description: true` + `read_more_btn: true`（首页摘要与"阅读全文"按钮）
- `codeblock.theme`：亮色 `default`、暗色 `stackoverflow-dark`；prism 对应 `prism` / `prism-dark`
- `codeblock.copy_button.enable: true`（代码块复制按钮，样式 `default`）
- `pangu: true`（中英文之间自动加空格，提升中文排版）
- `social`：侧栏已启用 `GitHub`（`https://github.com/huahua319`）

**未启用（需要才打开）**：

- 所有评论系统：`disqus` / `disqusjs` / `gitalk` / `utterances` / `isso` / `livere` 均 `enable: false` 或留空
- 所有统计：`google_analytics` / `baidu_analytics` / `cloudflare_analytics` / `clarity_analytics` / `matomo` / `umami` / `plausible` / `firestore` / `busuanzi_count` 均未启用
- 数学公式：`math.mathjax.enable` / `math.katex.enable` 都是 `false`（开启需同时安装对应的 `hexo-filter-*` 依赖）
- 图表：`mermaid.enable` / `wavedrom.enable` / `pdf.enable` 均 `false`
- 外部字体：`font.enable: false`
- 其他动效：`pjax` / `fancybox` / `mediumzoom` / `lazyload` / `quicklink` / `pace` / `canvas_ribbon` 均关闭
- 打赏：`reward_settings.enable: false`
- `github_banner` / `reading_progress` / `bookmark` / `follow_me` / `related_posts` / `post_edit` 均关闭
- `footer.beian.enable: false`（无 ICP / 公安备案信息）
- `links`（友链）全部注释未填

**资源加载**：`vendors.internal: local`（NexT 内部脚本走本地），`vendors.plugins: cdnjs`（第三方插件走 CDN）

### 7.4 改菜单 / 改导航的正确做法

- 新增菜单项：在 `_config.next.yml` 的 `menu:` 下加一行 `key: /path/ || fa fa-xxx`
- 若链接指向自建页面（如 `/friends/`），**必须同时**在 `source/` 下创建对应内容
- Font Awesome 图标名可在 <https://fontawesome.com/icons> 查
- **Key 是区分大小写的**，翻译依赖 NexT 语言包 `zh-CN`；找不到翻译时直接使用 Key 原文

### 7.5 改头像 / 社交图标

- 头像：把图放 `source/images/avatar.xxx`，在 `_config.next.yml` `avatar.url` 填 `/images/avatar.xxx`
- 社交图标：`_config.next.yml` 的 `social:` 取消注释并填写

### 7.6 属于"需要谨慎"的主题修改

以下改动可能破坏后续升级：

- 手改 `node_modules/hexo-theme-next/` 源码
- 在 `_config.next.yml` 里改动 `cache` / `vendors.internal` / 文件路径相关字段
- 启用 `pjax`（与部分其他插件不兼容）
- 同时启用 `fancybox` 和 `mediumzoom`（NexT 配置里已注明冲突）

---

## 8. 开发与预览流程

### 8.1 环境前置

- Node.js ≥ 20，npm（随 Node 安装）
- Git，并配置好 GitHub SSH（`~/.ssh/id_ed25519` 及其公钥已加到 GitHub 账户）
- 国内网络下，`~/.ssh/config` 应让 GitHub 走 443 端口：

  ```
  Host github.com
    Hostname ssh.github.com
    Port 443
    User git
  ```

  此配置**在仓库外**，换机器需重新设。

### 8.2 首次克隆

```bash
git clone git@github.com:huahua319/blog-source.git blog
cd blog
npm install                # 恢复 package.json 声明的所有依赖
npm install -g hexo-cli    # 仅首次，若全局未装 hexo CLI
```

### 8.3 常用命令

Hexo CLI 和 npm scripts 两种方式等效（`package.json` 已声明 scripts）：

| 动作 | Hexo 直接命令 | npm script |
|---|---|---|
| 清理缓存和 `public/` | `hexo clean` | `npm run clean` |
| 生成静态文件 | `hexo generate` / `hexo g` | `npm run build` |
| 启动本地预览（4000 端口） | `hexo server` / `hexo s` | `npm run server` |
| 启动本地预览（热重载配置/样式）| — | `npm run dev` |
| 部署到 GitHub Pages | `hexo deploy` / `hexo d` | `npm run deploy` |
| 新建文章 | `hexo new post "标题"` | — |
| 新建草稿 | `hexo new draft "标题"` | — |
| 新建页面 | `hexo new page <名>` | — |
| 草稿转正 | `hexo publish "标题"` | — |
| 列出所有文章 | `hexo list post` | — |

### 8.4 典型开发循环

```bash
# 1. 写作 / 改配置
hexo new post "标题"        # 或直接编辑 source/ / _config.*.yml

# 2. 本地验证
hexo clean && hexo g && hexo s
# 浏览器打开 http://localhost:4000，确认后 Ctrl+C 停

# 3. 发布到线上
hexo deploy

# 4. 备份源码到本仓库
git add .
git commit -m "简短描述"
git push
```

**注意**：`hexo clean` 只在改过配置 / 主题 / 插件后必需；仅新增文章时可省略（但加上也无害）。

### 8.5 AI 代理的本地预览行为

本节规则**只对 AI 编码代理**（Claude Code 等）生效，不约束作者手动操作。设立动机：让作者可以在代理完成改动后立即在浏览器看到效果，而不必每次都手动敲 `hexo clean && hexo g && hexo s`。

**规则**：

1. **改动完成后自动启动本地预览**。无论是站点层（`_config.yml` / `_config.next.yml` / `source/_data/*.styl` / `scaffolds/` / `package.json` / 主题相关）还是内容层（`source/**/*.md`）改动完成后，代理必须在后台启动 `npm run dev`（基于 nodemon，自动监听配置 / Stylus / njk 变动并执行 `hexo clean && hexo g && hexo s`）。
2. **已运行则不重启**。若 `npm run dev`（或 `hexo server`）已在后台运行，代理应复用，不要重复启动多个实例。后续的 yml/styl/njk 改动由 nodemon 自动触发重建。
3. **只启动本地预览，不自动部署**。`hexo deploy` / `npm run deploy` **永远不自动执行**。仅在用户明确表达"发布 / 部署 / deploy / 上线 / 推到线上"等意图时，代理才可执行，且执行前应复述一次动作供用户确认。
4. **报告预览信息**。启动后告诉用户预览地址（默认 `http://localhost:4000`）和"浏览器 Ctrl+F5 刷新查看"；若首次构建报错，附带错误摘要。
5. **会话结束前不主动关闭**。后台的 `npm run dev` 默认保留运行，除非用户要求停止。

**Windows 下端口冲突恢复**（已知异常路径，详见 [CLAUDE_LEARNINGS.md](CLAUDE_LEARNINGS.md) §6）：nodemon 重启时若旧 `hexo server` 未被 kill 干净（Windows 信号机制限制），新进程会因 4000 端口已被占用而 crash，nodemon 输出 `app crashed - waiting for file changes`。代理遇到时按以下步骤恢复：

1. `TaskStop` 停掉 nodemon 任务（或终端 Ctrl+C）
2. `netstat -ano | grep -E ":4000\s"` 找占用 PID
3. `taskkill //PID <pid> //F`（Git Bash 下双斜杠转义）
4. `npm run dev` 重启

**与 §8.4 典型开发循环的关系**：§8.4 描述作者本人的手动流程；本节描述 AI 代理协作时的自动化流程。两者不冲突——代理自动做前两步（生成 + 本地预览），第三步（`hexo deploy`）始终由作者发指令。

---

## 9. 修改原则

基于本项目的实际情况（个人博客、静态站、小规模、长期维护），请遵循以下原则。

1. **优先小步修改**。一次 commit 做一件事（例如"只加一篇文章"、"只改一个配置开关"），不要把"加文章 + 调主题 + 升级依赖"塞进同一次提交。

2. **配置优先于源码**。想改外观、想开 / 关功能，第一反应是到 `_config.next.yml` 搜关键字，而不是去 `node_modules/hexo-theme-next/` 或自写模板。

3. **内容稳定性 > 花哨特效**。本项目面向"长期记录"，静态页本身简洁干净。在考虑引入动画、花式字体、粒子背景前，先问："这会影响阅读吗？"

4. **不随意引入重依赖**。新增一个插件前，确认：
   - 它是否真的被需要（不是"看起来酷"）
   - 是否有被广泛使用（NexT 官方文档 / awesome-next 列表里出现）
   - 是否有替代的 `_config.next.yml` 原生配置能达到 80% 效果
   - 体积与维护状态（末次提交近一年内）

5. **不随意改变已有依赖结构**。新增或删除依赖前先评估：是否确认无任何隐式引用、无 starter 兼容负担、无未来合理切回的可能。小博客长期维护的首要价值是稳定性，不是依赖列表的整洁度。确认完毕后再动手。

6. **永远以实际文件为准**。`README.md` / `USAGE.md` 可能过时，改动前若发现与实际不符，**先信配置再信文档**，并顺手更新文档（或在 commit 信息里记一笔）。

7. **发布前本地必过预览**。`hexo deploy` 之前至少 `hexo s` 看一眼，发现问题比线上回滚便宜得多。

8. **静态站点仓库不手动编辑**。`huahua319.github.io` 仓库只接收 `hexo deploy` 的推送，任何手动提交会被下次部署覆盖。

9. **重要改动后必须同步文档**。凡是修改了以下任一内容，提交前都必须检查并在必要时同步更新 `README.md`、`USAGE.md`、`SPEC.md`：
   - 用户可见功能
   - 配置方式
   - 安装或使用命令
   - 发布流程
   - 目录结构
   - 依赖列表
   - 主题方案或 scheme
   - 写作 / 维护约定（如分类约定）

   判断标准：
   - 会影响"读者如何使用这个项目"的，优先更新 `README.md`
   - 会影响"作者日常如何操作"的，优先更新 `USAGE.md`
   - 会影响"维护者 / AI 应如何理解和修改项目"的，优先更新 `SPEC.md`

10. **保留草稿隐私意识**。`source/_drafts/` 虽然不渲染进 `public/`，但会随 `git push` 进入本仓库。若某些草稿涉及隐私，考虑不 push，或用本地另一分支。

11. **AI 代理负责本地预览自动化，但不自动部署**。代理完成改动后应在后台启动 `npm run dev`（若未运行），方便作者立即在浏览器刷新查看；`hexo deploy` / `npm run deploy` 仅在作者明确指示时才执行。详见 §8.5。

---

## 10. 验收与检查清单

以下清单按修改类型组织。**发布前至少过一遍对应项**。

### 10.1 修改文章 / 新增文章

- [ ] front-matter 完整：`title` / `date` / `categories` / `tags` 都填了
- [ ] 分类在约定范围内（`技术` / `随笔`）
- [ ] 图片链接用绝对路径 `/images/xxx`（或已启用 `post_asset_folder`）
- [ ] 本地 `hexo g && hexo s` 打开文章确认：
  - [ ] 标题、正文、代码块、图片显示正常
  - [ ] 侧栏目录（TOC）结构合理
  - [ ] 字数统计与阅读时间显示
  - [ ] 分类 / 标签链接能跳转
- [ ] `hexo deploy` 后访问 <https://huahua319.github.io/YYYY/MM/DD/标题/> 确认（1–2 分钟后生效）

### 10.2 修改 `_config.yml`（站点层）

- [ ] YAML 语法正确（缩进、冒号后空格）
- [ ] **执行 `hexo clean`** 后再 `hexo g && hexo s`
- [ ] 首页、关于页、分类页、标签页、归档页都打开看一眼
- [ ] 若改了 `url` / `permalink`：确认 RSS / sitemap 中的链接也正确（访问 `/atom.xml` 和 `/sitemap.xml`）
- [ ] 若改了 `deploy`：确认目标仓库存在且有写权限

### 10.3 修改 `_config.next.yml`（主题层）

- [ ] 同样必须 `hexo clean`
- [ ] 覆盖以下场景各至少打开一次：
  - [ ] 首页（看 motion、摘要、阅读全文按钮）
  - [ ] 任意一篇文章（看 TOC、字数、导航、目录侧栏）
  - [ ] `/categories/` 和 `/tags/`
  - [ ] 暗黑模式切换
- [ ] 检查浏览器控制台无 JS 报错
- [ ] 若启用了新功能（评论 / 统计 / 数学 / 图表），**必须**确认对应依赖已装：
  - 评论（gitalk/utterances 等）— 通常仅需 NexT 配置即可，但需要在 GitHub 配对应 OAuth / 仓库
  - 数学 — 额外装 `hexo-filter-mathjax` 或 `hexo-filter-katex`
  - mermaid — 额外装 `hexo-filter-mermaid-diagrams`

### 10.4 新增 / 升级 npm 依赖

- [ ] 执行 `npm install <包>` 后：
  - [ ] `package.json` 和 `package-lock.json` 都有变更
  - [ ] `hexo clean && hexo g` 能跑通，无报错
  - [ ] 本地预览功能正常
- [ ] 提交时同时带上 `package.json` 和 `package-lock.json`

### 10.5 发布前最终一遍

- [ ] 本地 `hexo s` 最后确认一次
- [ ] 没有遗留的 `TODO` / 占位 `Lorem ipsum` / 未填空的 front-matter
- [ ] `git status` 无意外文件（特别是不该被跟踪的 `public/` / `node_modules/`）
- [ ] `hexo deploy` 成功，无 `Connection reset` / 权限错误
- [ ] 线上 1–2 分钟后访问 <https://huahua319.github.io>，确认更新到位
- [ ] 最后 `git push` 把源码推到 `blog-source`，完成闭环

---

## 11. 后续可扩展方向

按"与当前项目兼容且有真实价值"排序。每项都写明需要改动的文件，不写空话。

### 11.1 接入评论（高价值）

推荐 **Giscus**（基于 GitHub Discussions，零运维，本人已在 GitHub）：

1. 在 <https://giscus.app> 按指引选本仓库或另开一个仓库
2. NexT 原生无 Giscus 配置，需装 `hexo-next-giscus` 或改为启用 `utterances`（NexT 内置）
3. 若选 utterances：在 `_config.next.yml` `utterances.enable: true`，填 `repo`
4. 不想给所有页面启用可在 front-matter 加 `comments: false`

### 11.2 自动部署（中价值）

当前 `hexo deploy` 在本地执行。可改为 GitHub Actions：

- 新建 `.github/workflows/deploy.yml`，在每次推 `main` 分支时跑 `npm ci && hexo g && hexo d`
- 需要配置 `DEPLOY_KEY`（能写 `huahua319.github.io` 仓库的 SSH 私钥）或 `GH_TOKEN`
- **代价**：作者写一篇文章需 push 才能发布（不能再只 `hexo d` 不 push）
- **收益**：换电脑 / 手机 Web 编辑都能直接发布
- 不必着急引入，本人工作流够用时保持现状

### 11.3 自定义域名（低难度）

- 买域名后，在 `source/CNAME` 写入一行域名（例：`blog.example.com`）
- GitHub 仓库 Settings → Pages → Custom domain 填同样域名并勾选 Enforce HTTPS
- DNS 按 GitHub 官方指引配 A 记录或 CNAME

### 11.4 补全站点元信息（低成本，推荐）

当前 `_config.next.yml` 以下项空着，填上后对阅读体验有直接帮助：

- `avatar.url`：放一张 200×200 头像到 `source/images/avatar.png`
- `social`：至少填 GitHub
- `favicon`：当前用 NexT 默认；可换成自己的 16x16 / 32x32 / apple-touch
- 首页或"关于"里补一段个人简介（现在"关于"只有三行）

### 11.5 图片策略升级（内容规模上来再做）

当文章里图片多起来后：

- 方案 A：在 `_config.yml` 设 `post_asset_folder: true`，每篇文章有独立资源目录
- 方案 B：接入图床（PicGo + 七牛云 / 阿里云 OSS / Cloudflare R2），URL 直接用外链
- 当前 `source/images/` 存放了头像资源

### 11.6 数学公式 / 图表（按需）

若后续写算法 / 机器学习类文章：

- 装 `hexo-filter-mathjax`，启用 `_config.next.yml` `math.mathjax.enable: true`
- 文章顶部 `mathjax: true`（避免全局加载影响性能）

若写架构图 / 流程图：

- 装 `hexo-filter-mermaid-diagrams`，启用 `_config.next.yml` `mermaid.enable: true`

### 11.7 访问统计（可选）

免运维优先：

- Cloudflare Web Analytics（免费、无 Cookie、一行代码）
- 或 Umami Cloud

国内友好：

- 不蒜子（`busuanzi_count.enable: true`，零接入成本但数据简单）

### 11.8 明确不推荐的扩展

- **粒子 / 背景音乐 / 鼠标拖尾**：干扰阅读，违反 §9 第 3 条
- **重度 CMS 化（Strapi / Sanity 后台）**：违反 §1 项目边界
- **多语言站**：当前只有中文作者，维护成本极高
- **商业化（广告 / 会员）**：违反 §1 项目定位

---

## 12. 已知不一致与待确认

### 12.1 `README.md` 与实际文件的不一致

历史上曾存在以下不一致，已于 **2026-04-22** 同步修正（README 已更新）：

| 项 | 曾经的不一致 | 修正方式 |
|---|---|---|
| Hexo 版本 | README 写"Hexo 7.x"，实际 `^8.0.0` / 锁定 `8.1.1` | README 改为 "Hexo 8.x" |
| `source/_drafts/` | README 目录树直接列出，实际按需生成 | 目录树注释加"（按需生成）" |
| `USAGE.md` | README 未提及 | README 顶部新增"相关文档"小节，链到 `USAGE.md` 与 `SPEC.md` |
| npm scripts | README 未提及 `build/clean/deploy/server` | README 命令表新增"npm 等价"列 |

当前**仍存在且有意保留**的不一致：_（当前无）_

维护策略：README 面向读者、SPEC 面向维护者，允许各有侧重。发现不一致时按以下原则处理：

- **事实性错误**（版本号、目录缺失、命令写错等）：同步修正 README
- **工程细节**（未启用的依赖、构建产物路径、内部约定等）：保留在 SPEC，不必回灌 README
- 不要为了"对齐 SPEC"而大改 README

### 12.2 待确认项

已确认处理：

- **Node.js 最低版本**（2026-04-22 处理）：已在 `package.json` 补 `"engines": { "node": ">=20" }`，与文档建议一致。非 strict 模式下低版本安装会给警告。
- **`hexo-theme-landscape` 清理**（2026-04-22 处理）：已 `npm uninstall hexo-theme-landscape` 并删除 `_config.landscape.yml`。确认无任何引用；若未来需要切回 landscape，一行 `npm install` 即可还原。
- **`dependabot.yml` 策略**（2026-04-22 处理）：检查频率从 `daily` 改为 `weekly`，`open-pull-requests-limit: 20` 保持不变。个人博客场景下每周批处理更合理。

_（当前无待确认项）_

---

## 附录：关键外部链接

| 资源 | 地址 |
|---|---|
| 线上站点 | <https://huahua319.github.io> |
| 源码仓库（本仓库） | <https://github.com/huahua319/blog-source> |
| 静态站点仓库 | <https://github.com/huahua319/huahua319.github.io> |
| Hexo 官方文档 | <https://hexo.io/docs/> |
| Hexo 配置文档 | <https://hexo.io/docs/configuration.html> |
| NexT 官方文档 | <https://theme-next.js.org/> |
| NexT 主题仓库 | <https://github.com/next-theme/hexo-theme-next> |
| Font Awesome（菜单图标） | <https://fontawesome.com/icons> |
