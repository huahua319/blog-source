# NexT 主题定制踩坑记录与经验总结 (2026-04-24)

在为本博客定制前端样式（头像位置、圆角、宽度）的过程中，我们遇到了一些坑。为了防止后续维护者（或者 AI 代理）再次踩坑，特总结如下：

## 1. 尝试修改“头像与导航栏的上下顺序”失败
- **目标**：在 Gemini / Pisces 布局下，让头像显示在“首页、关于”等导航菜单的正上方。
- **踩坑 1 (纯 CSS Flex 覆盖)**：尝试用 `order` 属性将底部 sidebar 里的头像提上来。失败原因：NexT 的 HTML 结构将导航和侧边栏分装在两个独立的卡片/列中，跨卡片调节顺序在复杂响应式场景下表现极其不稳定，甚至会导致头像消失。
- **踩坑 2 (覆盖 Nunjucks 模板)**：尝试使用 NexT 的 `custom_file_path` 覆盖 `header.njk` 和 `sidebar.njk`，硬改 DOM 结构。失败原因：本地直接放入覆盖文件会导致 Hexo 渲染引擎报错（`Unable to call '__'`），因为自定义数据夹缺少 Hexo 全局翻译函数的上下文环境。
- **结论**：**不要尝试改变 NexT 默认组件的大结构顺序**。接受 Pisces 布局原生的“导航在上，头像在下方卡片”设定，这是最稳妥的。

## 2. 修改页面最大宽度失败
- **目标**：在大屏幕下，压缩两侧留白，让页面有效内容更宽（占屏幕 85%）。
- **踩坑 (普通 CSS 覆盖)**：直接在 `styles.styl` 中写 `@media` 覆盖 `.main-inner` 宽度。失败原因：NexT 主题默认的宽度限制层级非常深，有专门的 Stylus 变量控制。强行覆盖会被底层样式冲掉。
- **正确做法**：必须通过启用 `custom_file_path.variable`，在 `source/_data/variables.styl` 中覆盖原生的布局变量：
  ```stylus
  $content-desktop = 85%
  $content-desktop-large = 85%
  $content-desktop-largest = 85%
  ```

## 3. Hexo 本地服务更新盲区
- **现象**：修改了 `_config.yml`、`_config.next.yml` 或 `styles.styl` 后，浏览器刷新无效。
- **原因**：`hexo server` 默认只热重载 Markdown 文章。深层配置和自定义 Stylus 有强缓存。
- **解决方案**：必须 `hexo clean && hexo g && hexo s`。为此，我们在 `package.json` 中引入了 `nodemon`，封装了 `npm run dev` 命令，自动监听配置文件变动并执行全套重载。

## 4. GitHub Pages 缓存问题
- **现象**：本地确认正常，`hexo deploy` 提示成功，但线上始终是老页面。
- **原因**：如果没有修改实质内容（仅修改了某些不触发 Git Diff 的底层逻辑），或者由于浏览器/CDN 强缓存，即使 deploy 成功页面也不会立刻刷新。
- **应对方案**：稍微修改某篇 Markdown 文件的内容（加一个空格或改一个字）来强制破坏缓存，并使用 `Ctrl+F5` 或无痕模式访问。

## 5. CSS 样式未能生效（编译后丢失）
- **现象**：本地在 `source/_data/styles.styl` 中写了样式，Hexo 编译部署后，线上页面毫无变化，生成的 `main.css` 里也搜不到自定义的代码。
- **原因**：在频繁使用 `sed` 修改 `_config.next.yml` 启用 `custom_file_path.style` 的过程中，破坏了 YAML 文件的严格缩进格式，或者意外产生了多行重复的 `style:` 键，导致主题在编译时无法正确挂载自定义的 Stylus 文件。另外，如果不加 `!important`，很多样式会被主题原有的高权重选择器覆盖。
- **结论**：
  1. 修改 YAML 配置文件时必须格外注意缩进层级。
  2. 怀疑样式没生效时，第一反应应是使用 `grep` 检查生成的 `public/css/main.css` 中是否包含所写代码。
  3. 为了保险起见，覆写现有组件（如圆角、主色调）的 CSS，最后都带上 `!important` 权重。

## 6. Windows 下 nodemon 重启时 hexo server 残留导致 4000 端口冲突（高频，重点）

- **现象**：在 Windows 下 `npm run dev`，每次保存 `_config.next.yml` / `source/_data/*.styl` / `*.njk` 触发 nodemon restart 时，新一轮 `hexo server` 报 `Error: listen EADDRINUSE: address already in use :::4000`，nodemon 输出 `[nodemon] app crashed - waiting for file changes before starting...`。**本次单一会话中遇到 3 次**。
- **根本原因**：
  - `package.json` 的 `dev` 脚本是 `nodemon ... --exec "hexo clean && hexo generate && hexo server"`，是 shell `&&` 链
  - nodemon 重启时给子 shell 发信号，但 **Windows 没有 POSIX 信号机制**，shell 不一定把信号转发给最深层的孙进程 `hexo server` (node.exe)
  - 老 `hexo server` 进程留着没退出，4000 端口未释放，新启动失败
- **应急恢复流程**（已多次验证）：
  1. `TaskStop`（Claude Code 后台任务）或终端里 `Ctrl+C` 停掉 nodemon 任务
  2. `netstat -ano | grep -E ":4000\s"` 找占用端口的 PID（取最后一列）
  3. `taskkill //PID <pid> //F` 强制终止（**Git Bash 下必须双斜杠 `//`**，避免被路径转换成单斜杠）
  4. `cd C:/Users/hua/blog && npm run dev` 重启（前台或 `run_in_background: true`）
- **快速诊断技巧**：
  - 改完 yml/styl 后浏览器 Ctrl+F5 没变化 → 先 `curl -s -o /dev/null -w "%{http_code}\n" http://localhost:4000/`
  - HTTP 200 但内容陈旧 → 大概率 nodemon 崩了，旧 `hexo server` 还在跑老 in-memory router
  - 此时也可以 `cat` 后台任务的输出看末尾是否有 `app crashed - waiting for file changes` 字样
  - `curl http://localhost:4000/中文路径/` 会触发 `URIError: URI malformed`（hexo-server 默认 `decodeURIComponent` 不容错），改用 Python `urllib.parse.quote` 先编码再请求
- **未实施的根治建议**（属于站点层改动，需作者明确决策再做）：
  - 改 `dev` 脚本为 `concurrently` + `kill-port`：每次启动前先 `kill-port 4000` 再启 nodemon
  - 或引入 `tree-kill`（npm 包），让 nodemon 重启时终止整个进程树
  - 现状是手动恢复，频率低于每天 1 次时尚可接受；高频出现时再升级

## 7. Pisces 布局下"白底大卡片"是 `.main-inner` 而非 `.post-block`

- **现象**：想给主页文章加 `border-radius` 圆角，覆盖 `.post-block` 后视觉无变化
- **原因**：Pisces / Gemini scheme 默认结构是**所有文章共用一个外层 `.main-inner` 大卡片**（带白底 / padding / 阴影），每篇 `.post-block` 本身没有独立背景，只是内部分节
- **正确做法**：
  - 想给主白底加圆角 → 选择器 `.main-inner { border-radius: ... !important }`
  - 想做"卡片流"（每篇独立卡片） → 给 `.main-inner.posts-expand` 设 `background: transparent !important`，再给 `.posts-expand > .post-block` 加白底 + padding + 阴影 + margin
- **首页 scope 技巧**：首页的 `.main-inner` HTML 同时带 `.index .posts-expand` 两个类，可用 `.main-inner.posts-expand` 或 `.posts-expand > .post-block` 精准命中首页文章列表，避免影响文章详情页

## 8. NexT 代码块复制按钮由前端 JS 注入，HTML 静态源里看不到

- **现象**：开启 `codeblock.copy_button.enable: true` 后，`grep` `public/*.html` 找不到 `<button>` 或 `copy-btn` 元素，第一反应是"没生效"
- **原因**：复制按钮由 `js/utils.js` 在浏览器加载时动态注入到每个 `<figure class="highlight">` 元素的右上角，服务端渲染的 HTML 不携带按钮
- **判断方法**：
  - 看 `public/js/utils.js` 是否含 `clipboard` / `copyCode` 关键字（NexT 8.x 有约 8 处）
  - 或浏览器实际查看代码块右上角
  - 排查时不要被静态 HTML 里的 `<div class="copyright">`（页脚版权块）误判为 "copy 元素"
