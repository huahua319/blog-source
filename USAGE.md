# 博客使用速查手册

> 我自己的博客操作速查表，以后忘记怎么做时翻这里。

**线上站点**：<https://huahua319.github.io>
**本地目录**：`C:\Users\hua\blog`

---

## 万能起手式

做任何事之前，先进博客目录：

```bash
cd ~/blog
```

后面所有命令都假定已经在这个目录。

---

## 我要……

### 写一篇新技术文章

```bash
hexo new post "文章标题"
# 打开 source/_posts/文章标题.md 开始写
# 写完用「发布流程」小节的命令发上去
```

front-matter 填：

```yaml
---
title: 文章标题
date: 2026-04-22 10:00:00
categories:
  - 技术
tags:
  - 标签1
  - 标签2
---
```

### 写一篇生活随笔 / 日记

```bash
hexo new post "随笔标题"
```

front-matter 填：

```yaml
---
title: 随笔标题
date: 2026-04-22 10:00:00
categories:
  - 随笔
tags:
  - 日记
---
```

### 修改已经发布的文章

1. 打开 `source/_posts/文章名.md` 直接改
2. 走「发布流程」

### 先写个草稿（暂不发布）

```bash
hexo new draft "想法标题"
# 草稿在 source/_drafts/，默认不会被渲染到线上
```

可以大胆 `git push` 备份，草稿不会泄露到线上站点。

### 把草稿发布出去

```bash
hexo publish "想法标题"
# 自动从 _drafts/ 移到 _posts/，时间戳更新为当前
# 然后走「发布流程」
```

### 改博客标题 / 副标题 / 作者

打开 `_config.yml`，改这几行：

```yaml
title: 花花的blog
subtitle: 遇到困难睡觉觉
description: 记录技术与生活
author: 花花
```

改完走「发布流程」（**必须 `hexo clean`**）。

### 改侧栏 / 顶部菜单 / 配色

打开 `_config.next.yml`，常改项：

```yaml
scheme: Pisces          # 换配色：Muse / Mist / Pisces / Gemini
menu:                   # 顶部菜单项增减
  home: / || fa fa-home
  about: /about/ || fa fa-user
  ...
```

改完走「发布流程」（**必须 `hexo clean`**）。

### 改"关于"页

打开 `source/about/index.md`，整篇就是 Markdown 正文，可随意改。走「发布流程」。

### 往文章里加图片

1. 把图片放到 `source/images/` 下（自己建这个目录）
2. 在文章里用：

   ```markdown
   ![描述](/images/图片名.png)
   ```

3. 走「发布流程」

### 只在本地看看效果、不发布

```bash
hexo clean && hexo g && hexo s
# 浏览器打开 http://localhost:4000
# 看完 Ctrl+C 停掉
```

### 发布到线上

见下面「发布流程」。

### 只备份源码（不发布到线上）

```bash
git add .
git commit -m "备份：xxx"
git push
```

---

## 发布流程（核心四步）

```bash
# 1. 清缓存（改过配置/主题必须跑，只加文章可省略但建议跑）
hexo clean

# 2. 本地预览，确认没问题
hexo g && hexo s
# 访问 http://localhost:4000，满意后 Ctrl+C 停掉

# 3. 发布到线上（1-2 分钟后 https://huahua319.github.io 更新）
hexo deploy

# 4. 备份源码到 blog-source 仓库
git add .
git commit -m "新增/修改：xxx"
git push
```

**一行流**（改动简单时）：

```bash
hexo clean && hexo g && hexo s
# Ctrl+C 后：
hexo d && git add . && git commit -m "xxx" && git push
```

---

## 文件速查：我要改 X 就打开 Y

| 想改什么 | 打开哪个文件 |
|---|---|
| 站点标题、副标题、作者、URL | `_config.yml` |
| 主题配色、菜单、侧栏、搜索 | `_config.next.yml` |
| "关于"页内容 | `source/about/index.md` |
| 某篇文章 | `source/_posts/<标题>.md` |
| 新文章的默认模板 | `scaffolds/post.md` |
| 部署地址（github 仓库） | `_config.yml` 最下面 `deploy:` |

---

## 命令速查表

### Hexo 命令

| 命令 | 作用 |
|---|---|
| `hexo new post "标题"` | 新建文章 |
| `hexo new page <名>` | 新建独立页面（如 about） |
| `hexo new draft "标题"` | 新建草稿 |
| `hexo publish "草稿名"` | 草稿转正式文章 |
| `hexo list post` | 列出所有文章 |
| `hexo clean` | 清缓存和 `public/` |
| `hexo g` 或 `hexo generate` | 生成静态文件 |
| `hexo s` 或 `hexo server` | 本地预览（默认 4000 端口） |
| `hexo d` 或 `hexo deploy` | 发布到 GitHub Pages |

### Git 命令（本仓库用）

| 命令 | 作用 |
|---|---|
| `git status` | 看看改了什么 |
| `git add .` | 把所有改动加入暂存 |
| `git commit -m "消息"` | 提交 |
| `git push` | 推到 GitHub 备份 |
| `git log --oneline` | 看历史提交 |
| `git diff` | 看未暂存的改动 |

---

## front-matter 模板（复制即用）

### 技术文章

```yaml
---
title:
date: 2026-04-22 10:00:00
categories:
  - 技术
tags:
  -
---
```

### 随笔日记

```yaml
---
title:
date: 2026-04-22 10:00:00
categories:
  - 随笔
tags:
  - 日记
---
```

### 置顶文章（可选）

在 front-matter 加：

```yaml
sticky: 100    # 数字越大越靠前
```

### 关闭某篇文章的评论/打赏（未来加了评论系统时用）

```yaml
comments: false
reward: false
```

---

## 常见问题排查

### 改了 `_config.yml` 或 `_config.next.yml`，预览没变

**原因**：Hexo 有缓存。
**解决**：

```bash
hexo clean && hexo g && hexo s
```

### `hexo deploy` 报 `Connection reset by ... port 22`

**原因**：国内访问 GitHub SSH 22 端口不稳定。
**解决**：确认 `~/.ssh/config` 中有：

```
Host github.com
  Hostname ssh.github.com
  Port 443
  User git
```

（初次搭建时已经配好，若哪天丢了照抄回去。）

### 端口 4000 被占用（`hexo s` 失败）

换一个端口：

```bash
hexo s -p 5000
```

### 图片在本地能看但线上看不到

检查图片路径：
- 正确：`![](/images/a.png)` — 绝对路径
- 错误：`![](./images/a.png)` 或 `![](images/a.png)` — 相对路径不稳

以及确认图片已在 `source/images/` 下且 `hexo clean && hexo g` 过。

### `git push` 报权限错误

先测试 SSH 是否通：

```bash
ssh -T git@github.com
# 期望输出：Hi huahua319! You've successfully authenticated...
```

不通就照「hexo deploy 报 Connection reset」处理。

### 换电脑后怎么继续写博客

```bash
# 新电脑先装 Node.js 20+、Git，配置 GitHub SSH key
git clone git@github.com:huahua319/blog-source.git blog
cd blog
npm install
npm install -g hexo-cli
# 搞定，开写
```

### 误把 node_modules 或 public 提交了

```bash
git rm -r --cached node_modules public
git commit -m "移除误提交的目录"
git push
```

（`.gitignore` 已经排除它们，正常不会发生。）

---

## 重要地址

| 用途 | 地址 |
|---|---|
| 线上站点 | <https://huahua319.github.io> |
| 源码仓库（写作用） | <https://github.com/huahua319/blog-source> |
| 静态站点仓库（自动推送，不用管） | <https://github.com/huahua319/huahua319.github.io> |
| Hexo 官方文档 | <https://hexo.io/docs/> |
| NexT 主题文档 | <https://theme-next.js.org/> |
| Markdown 语法参考 | <https://www.markdownguide.org/basic-syntax/> |

---

## 一周一次的建议

- 养成习惯：哪怕只改了几个字也 `git push` 一次，丢失成本几乎为零
- 每次 `hexo deploy` 成功后打开 <https://huahua319.github.io> 确认一下（大约等 1 分钟）
- 长文建议先 `hexo new draft` 写草稿，改稳了再 `hexo publish`
