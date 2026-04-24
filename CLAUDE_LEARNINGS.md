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
