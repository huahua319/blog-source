# AGENTS.md

Hexo + NexT 个人博客源码。面向所有 AI 编码代理的起手指引（跨工具通用：Codex / Cursor / Copilot CLI / Claude Code / Gemini CLI 等）。

## 阅读顺序

1. 本文件（快速约束）
2. [SPEC.md](SPEC.md) §1–§9（完整工程说明，**必读**）
3. 按任务深入：
   - 写 / 改文章 → SPEC §6 + [USAGE.md](USAGE.md)
   - 改配置 / 主题 → SPEC §5 + §7
   - 动依赖 / 构建 → SPEC §3 + §8
   - 任何修改前 → SPEC §9（10 条修改原则）

## 硬约束（SPEC 已详述，此处仅提醒）

- 分类仅两类：`技术` / `随笔`（§6.4）
- 改站点 / 主题配置必须先 `hexo clean`（§6.5）
- 不要手动编辑 `huahua319.github.io` 仓库（§2）
- `themes/` 是占位目录，NexT 实体在 `node_modules/hexo-theme-next/`（§7.1）
- 改动后必须同步 `README.md` / `USAGE.md` / `SPEC.md`（§9 第 9 条）
- 文档与实际不一致时，**以实际文件为准**

## 环境

- Shell：bash（非 PowerShell，命令用 `&&` / `/dev/null` 等 Unix 语法）
- Node.js ≥ 20（`engines.node` 已声明）
- OS：Windows 11
- 作者：花花，站点 <https://huahua319.github.io>

## 提交规范

- **不主动 commit / push**，除非用户明确要求
- 一次 commit 做一件事（SPEC §9 第 1 条）
- 提交信息使用中文，风格参考 `git log` 近期 commit
- **不加 AI 署名标记**（不加 "Generated with ..." 或 "Co-Authored-By: ..."）
- 禁止 `--force` 推送到 main / master
- 敏感文件（`.env` / `credentials.*`）绝不提交

## 回复风格

- 所有回复使用**简体中文**
- 不使用表情符号（emoji）
- 简洁；不要无意义复述用户指令
