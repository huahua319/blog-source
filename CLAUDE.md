# CLAUDE.md

先读 [AGENTS.md](AGENTS.md) — 本项目跨工具通用的 AI 指引在那里。本文件只补充 Claude Code 特有内容。

## Claude Code 特有

- `.claude/` 目录存项目级 Claude Code 设置（权限白名单、skills、agents 等）。本仓库选择纳入 git 以便跨机器同步，里面不含敏感信息
- Memory 系统若已积累作者偏好，会在同 cwd 的会话中自动加载（无需手动调用）
