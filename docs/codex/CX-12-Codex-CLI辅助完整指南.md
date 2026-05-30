# CX-12 Codex CLI 辅助指南：App 用户什么时候需要终端

主要来源：OpenAI Codex CLI、CLI Slash Commands、Config、MCP、Review 官方文档。

---

> **课程信息**
>
> - **作者**：老金
> - **GitHub**：https://github.com/KimYx0207
> - **公众号**：老金带你玩AI
> - **X（Twitter）**：老金带你玩AI
> - **个人博客**：https://aiking.dev
> - **预计学时**：2-3小时
> - **难度等级**：⭐⭐ 入门级
> - **更新日期**：2026年5月30日
> - **信息来源**：OpenAI Codex CLI、CLI Slash Commands、Config、MCP、Review 官方文档
> - **前置要求**：已完成 [CX-01 安装](./CX-01-Codex-App安装与认证完整指南.md)

---

## 📚 本课学习目标

完成本课学习后，你将能够：

1. **理解CLI的定位**：CLI是App的辅助工具，不是普通用户的第一入口
2. **掌握CLI常用命令**：`codex --help`、`codex exec`、`codex review`、`codex mcp list`
3. **理解审批与沙盒参数**：`--ask-for-approval`和`--sandbox`的组合使用
4. **使用`codex exec`无头执行**：在CI和脚本中运行一次性只读任务
5. **使用`codex review`终端审查**：快速检查当前diff，与App Review交叉验证
6. **掌握CLI slash commands**：`/status`、`/permissions`、`/plan`、`/compact`等常用命令
7. **用CLI管理MCP和Plugins**：排查工具配置、统一团队安装
8. **理解CI安全原则**：最小权限、外部沙箱、不输出密钥、明确范围、人工合并

---

## 🗺️ 学习路径导航（先看这里！）

> 💡 **根据你的情况选择学习路径**：不用全看！

### 路径A：快速上手（⏱️ 10分钟）

**适合人群**：想知道CLI能干什么

**只看这些章节**：

```
✅ 第1-2部分：CLI定位 + 常用命令（5分钟）
✅ 第4部分：codex exec（5分钟）
```

---

### 路径B：完整学习（⏱️ 2-3小时）

**适合人群**：需要用CLI做CI或排查

**学习顺序**：从头到尾所有章节

---

## 1. CLI 的定位


> **v0.135.0 CLI 基线**：`codex remote-control` 前台化并报告 readiness；goals 默认启用；permission profiles 增加 list API、继承、managed `requirements.toml`、named profiles 与 Windows sandbox 强化；plugin discovery 更容易检查；extension lifecycle 可观察 subagent start/stop、tool execution、turn metadata 和 async approval / turn processing。v0.134.0 / v0.135.0 还补充了本地会话历史搜索、`--profile` 主选择器、MCP per-server environment / OAuth、read-only MCP 并发、`codex doctor` 丰富诊断、远程 `/status`、Vim text object、Python SDK `Sandbox` presets 和 `CODEX_NON_INTERACTIVE=1` 非交互安装。安装方式以官方 CLI 文档为准，不再只写 npm。

CLI 是 App 的辅助工具，适合：

- 排查配置。
- 管理 MCP。
- 在 CI 中无头执行。
- 终端中快速 review。
- 用脚本批处理。
- Linux 或远程服务器环境。

不适合：

- 作为普通 App 用户的第一入口。
- 替代 App Review 面板。
- 让新手先背所有参数。

## 2. 常用命令

```bash
codex --help
codex
codex app
codex exec "summarize this repo"
codex review
codex mcp list
codex plugin --help
codex features
```

App 用户学习 CLI 的顺序：

1. 先会用 `codex --help` 看本机支持项。
2. 再会用 `codex app` 从终端打开或配合 App。
3. 需要无头任务时再学 `codex exec`。
4. 需要审查时再学 `codex review`。
5. 需要排查外部工具时再学 `codex mcp` 和 plugin 命令。

不要先背参数表。CLI 版本变化快，本机 `--help` 比教程里的静态列表可靠。

### 2.1 v0.129.0 到 v0.135.0：先用本机命令确认差量

这几版变化集中在 CLI 可观测性、远程执行、权限配置和 SDK，而不是“多背几个参数”。升级后先做四个确认：

```bash
codex --version
codex doctor
codex remote-control --help
codex plugin --help
```

重点看这些能力是否已在你本机暴露：

| 版本段 | 你应该确认什么 | 教程里的使用方式 |
|---|---|---|
| v0.129.0 | `/vim` composer、默认 Vim mode、`/keymap debug`、workspace plugin sharing、`/hooks` 浏览与开关 | 输入体验、插件共享和 hooks 排查都先用内置入口确认 |
| v0.130.0 | `codex remote-control` 顶层入口、plugin share metadata / discoverability、live thread config refresh、thread pagination、Windows sandbox runtime cache | 远程控制、插件分享和 App server 排查时先看 readiness / config 是否刷新 |
| v0.131.0 | TUI session controls、`@` mentions 搜索文件 / 目录 / plugins / skills、`codex doctor` | 交互模式先用 `/help` 和 `@` 试搜索，不要手写路径 |
| v0.132.0 | Python SDK 认证、`codex exec resume --output-schema`、remote executor 标准认证、图片 fidelity、session picker | 自动化任务需要结构化输出时优先用 `exec resume --output-schema` |
| v0.133.0 | Goals 默认启用、permission profiles list / inheritance / managed `requirements.toml`、plugin discovery、extension lifecycle events | 把 `/goal`、权限 profiles、插件排查当成稳定教学入口 |
| v0.134.0 | 本地会话历史搜索、`--profile` 主选择器、MCP per-server environment / streamable HTTP OAuth、read-only MCP 并发、extension / hook 上下文增强 | 迁移旧 profile 配置，排查 MCP 时先看 server environment 和 OAuth 选项 |
| v0.135.0 | `codex doctor` 环境/Git/终端/app-server/thread 诊断、远程 `/status`、named permission profiles、Vim text object、Python SDK `Sandbox` presets、非交互安装 | 支持工单排查、CI 安装和 SDK 自动化时优先用新版诊断和 preset |

v0.135.0 也补了几类“看起来小、实际影响学习体验”的 TUI 修复：Markdown 表格和多行列表更容易读，macOS / Zellij 下输出不再容易覆盖 composer，slash command 补全会保留已有草稿，resume flow 可以按需包含 non-interactive exec sessions，App mentions 会过滤不可访问或已禁用的 App。写教程和排障时，这些不用当成新命令背，但可以解释“为什么升级后终端界面突然更稳了”。

## 3. 审批与沙盒


CLI 常见参数：

```bash
codex --ask-for-approval on-request --sandbox workspace-write
codex --ask-for-approval never --sandbox read-only
```

危险模式只用于外部已经隔离的一次性环境：

```bash
codex --dangerously-bypass-approvals-and-sandbox
```

不要把危险模式写成团队默认推荐。

如果你不知道该选什么，App 用户默认做法是：

| 场景 | 建议 |
|---|---|
| 只读分析 | read-only / 需要审批 |
| 小范围改 docs | workspace-write / 明确目录 |
| 跑测试 | 允许普通命令，危险命令仍审批 |
| CI runner | 只给 PR diff 或目标目录 |
| 一次性隔离环境 | 才考虑无审批无沙盒 |

## 4. `codex exec`

无头执行一次任务：

```bash
codex exec "read the repo and summarize test commands"
```

适合：

- CI。
- 脚本化只读分析。
- 生成报告。

不适合：

- 需要频繁交互的大改。

一个更清楚的无头任务示例：

```bash
codex exec "Read package.json and AGENTS.md. Report the install, test, lint, and build commands. Do not modify files."
```

### 4.1 恢复会话并保持结构化输出

v0.132.0 以后，`codex exec resume` 可配合 `--output-schema` 使用。它适合“上一次自动化任务已有上下文，这次继续处理，但输出仍必须是 JSON”的场景。

```bash
codex exec resume <session-id> \
  --output-schema ./schemas/audit-result.schema.json \
  "Continue the dependency audit and return only schema-valid JSON."
```

不要把它当成普通聊天续写。它的价值是让 CI、定时任务或批处理在保留上下文时仍能拿到可校验产物。

## 5. `codex review`


终端审查当前改动：

```bash
codex review
```

适合：

- CI PR 审查。
- 本地提交前快速检查。
- 与 App Review 交叉验证。

## 6. CLI slash commands

终端交互模式中可以输入 `/help` 查看当前 slash commands。不要把命令表写死；以当前 CLI 为准。

App 用户重点掌握：

- `/status`
- `/permissions`
- `/plan`
- `/goal`
- `/review`
- `/mcp`
- `/plugins`
- `/compact`
- `/resume`

如果这些命令没有出现在你本机 `/help`，以本机输出为准，不要按教程硬敲。

### 6.0 `/vim` 和键盘排查

v0.129.0 起，CLI TUI composer 支持 `/vim`。如果你习惯 Vim，可以在当前会话里开启 modal editing；团队教程不要默认要求所有人开 Vim mode，因为新手会被 Normal / Insert 模式卡住。

键盘快捷键异常时先用：

```text
/keymap debug
```

它适合排查终端是否正确上报组合键，不适合写进普通工作流步骤。

### 6.1 `/goal`、`@` mentions 和 session picker

v0.133.0 起 Goals 默认启用，并有专用存储追踪 active turns 之间的进度。写 `/goal` 时要给明确停止条件：

```text
/goal Finish the docs link audit. Stop when every broken local link has either been fixed or listed with a reason.
```

v0.131.0 起，`@` mentions 搜索范围更宽，能覆盖文件、目录、plugins 和 skills。路径不确定时，优先在 TUI 里输入 `@` 搜索，而不是复制一长串容易出错的路径。

session picker 在 v0.132.0 后更适合恢复旧线程：重命名线程会显示 `name (thread-id)`，粘贴文本也能用于搜索。找不到旧任务时，先用 picker 搜标题关键词，再决定是否用 `codex exec resume`。

## 7. CLI 管理 MCP / Plugins

当 App 中看不到工具或需要团队统一配置时：

```bash
codex mcp --help
codex plugin --help
```

v0.133.0 改进了 plugin discovery 输出。排查插件时不要只问“装没装”，还要看它来自哪个 marketplace、当前安装版本、远程 collection 是否可见：

```bash
codex plugin list
codex plugin marketplace --help
codex plugin --help
```

CLI 是管理和排查路径，不是普通用户调用外部工具的唯一方式。

CLI 改完 MCP 或插件后，仍要回 App 验收：

1. App 里能否看到工具或插件。
2. 线程中能否调用。
3. 权限提示是否符合预期。
4. 是否能撤销或禁用。

## 8. `codex remote-control` 最小排查流程

v0.130.0 新增顶层 `codex remote-control` 入口，v0.133.0 又把它前台化并增加 readiness 反馈。排查时不要只看“命令有没有返回”，按这个顺序：

```bash
codex remote-control --help
codex remote-control
```

启动后确认三件事：

1. 终端显示 ready 或 machine status。
2. App / 远端客户端能看到同一台机器。
3. 退出前明确停止前台进程，避免误以为它是后台 daemon。

如果你需要长期后台服务，优先查当前 App server / Remote Connections 官方文档，不要把 `remote-control` 当成无监督系统服务。

## 8. CI 安全原则

| 原则 | 说明 |
|---|---|
| 最小权限 | CI token 只给需要的仓库权限 |
| 外部沙箱 | 无沙盒模式只在 runner 自身可信时使用 |
| 不输出密钥 | 日志中不得打印 secret |
| 明确范围 | 只审查 PR diff 或指定路径 |
| 人工合并 | 自动评论可以，自动合并要慎重 |

## 常见问题

### Q1：App 用户不装 CLI 可以吗？

可以，但排查和 CI 会不方便。建议安装，但不先学。

### Q2：CLI 和 App 的配置完全共享吗？

不要做绝对假设。看当前版本和官方文档。能通过 App 设置解决的，优先用 App。

### Q3：CLI 能替代 Automations 吗？

不能完全替代。CLI 适合脚本和 CI；App Automations 是 App 内的后台/周期任务体验。

---

## 📝 总结与检查清单

完成本课后，请确认以下所有项：

- [ ] 理解CLI是App的辅助，不是主线
- [ ] 掌握`codex exec`无头执行和`codex review`终端审查
- [ ] 知道审批和沙盒参数的组合使用
- [ ] 不把CLI教程写成Codex主线
- [ ] CI中遵循最小权限、不输出密钥、人工合并原则

**如果以上全部勾选，恭喜你掌握Codex CLI辅助！**

---

## 附录

### A. CLI命令速查

| 命令 | 用途 |
|------|------|
| `codex --help` | 查看帮助 |
| `codex` | 交互模式 |
| `codex app` | 打开或配合App |
| `codex exec "任务"` | 无头执行 |
| `codex review` | 审查当前改动 |
| `codex mcp list` | 列出MCP工具 |
| `codex plugin --help` | 插件管理帮助 |

### B. 推荐学习资源

- **Codex CLI 官方文档**：https://developers.openai.com/codex/cli
- **本系列上一篇**：[CX-11 Web / Cloud 辅助](./CX-11-Codex-Web-Cloud辅助指南.md)
- **本系列下一篇**：[CX-13 安全与企业](./CX-13-Codex安全企业完整指南.md)

---

**课程制作**：老金
**最后更新**：2026年5月30日
**许可**：本课程采用CC BY-NC-SA 4.0许可

---

## 下一步

下一篇：[CX-13 安全与企业基线](./CX-13-Codex安全企业完整指南.md)。
