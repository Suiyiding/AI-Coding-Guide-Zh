# CX-02 Codex App 桌面工作流完整指南：把 App 当主控台

定位说明：本篇是整个 Codex 系列的主轴。后续 Commands、MCP、Skills、Plugins、Automations、Review、Cloud 都围绕这里展开。

主要来源：OpenAI Codex App Features、App Commands、Settings、Review、Automations、MCP、Skills、Plugins、CLI Slash Commands 官方文档。

---

> **课程信息**
>
> - **作者**：老金
> - **GitHub**：https://github.com/KimYx0207
> - **公众号**：老金带你玩AI
> - **X（Twitter）**：老金带你玩AI
> - **个人博客**：https://aiking.dev
> - **预计学时**：3-4小时
> - **难度等级**：⭐ 零基础入门
> - **更新日期**：2026年5月30日
> - **信息来源**：OpenAI Codex App Features、Settings、Review、Automations、MCP、Skills、Plugins 官方文档
> - **前置要求**：已完成 [CX-01 Codex App 安装与认证](./CX-01-Codex-App安装与认证完整指南.md)

---

## 📚 本课学习目标

完成本课学习后，你将能够：

1. **理解App的核心模型**：掌握Thread、Worktree、Review、Terminal、Settings等核心概念
2. **区分三种工作方式**：清楚Local thread、Worktree thread、Cloud handoff各自的适用场景
3. **写好任务描述**：用目标-范围-约束-验证-交付五要素模板描述任务
4. **掌握Review工作流**：在App中查看diff、审批命令、合并改动
5. **理解Settings配置**：知道模型、审批、沙盒、插件、MCP在App中的配置入口
6. **建立App全局视角**：知道Commands、MCP、Skills、Plugins、Automations在App中各自的位置和关系
7. **避免常见翻车场景**：不盲目给写权限、不跳过Review、不用Cloud替代本地App
8. **跑通完整的App改动流程**：从任务描述到diff检查到合并提交的全流程

---

## 🗺️ 学习路径导航（先看这里！）

> 💡 **根据你的情况选择学习路径**：这是一篇长教程，不用全看！

### 路径A：快速上手（⏱️ 20分钟）

**适合人群**：装好了App，想快速知道怎么用

**只看这些章节**（其他跳过）：

```
✅ 第1部分：App的核心模型（5分钟）
✅ 第2部分：三种工作方式（5分钟）
✅ 第3部分：任务描述模板（5分钟）
✅ 第6部分：Review工作流（5分钟）
```

**20分钟后你能达到**：能用App描述任务、看懂diff、做基本操作

---

### 路径B：完整学习（⏱️ 3-4小时）

**适合人群**：想系统掌握Codex App的全部工作流

**学习顺序**：从头到尾所有章节

---

### 路径C：问题排查（⏱️ 5分钟）

**适合人群**：使用App时遇到问题

**直接跳到**：

```
🔧 常见问题
🔧 第15部分：课程交叉验证口径
```

---

## 1. App 的核心模型


Codex App 可以理解为一个桌面开发主控台。它把这些能力放到同一个工作流里：

| 能力 | App 中的意义 |
|---|---|
| Thread | 一条任务线。每个线程都有上下文、历史和状态 |
| Local workspace | 直接围绕当前本地目录工作 |
| Worktree | 给任务创建隔离分支/目录，降低互相覆盖风险 |
| Review | 查看 diff、文件变更、行内反馈、stage / unstage |
| Terminal | 让 Codex 在受控环境中运行命令 |
| Settings | 模型、审批、沙盒、插件、MCP、外观、Git 等配置 |
| Automations | 周期性或后台检查任务 |
| Plugins / Connectors | 连接 GitHub、Gmail、Drive、Slack 等外部上下文 |
| Appshots | 把截图、页面状态或视觉反馈带入线程，用于 UI 复核和问题定位 |

本系列后续功能都要回到这个模型里解释。

### 1.1 App 主控台不是“聊天窗口”

Codex App 的学习难点不在提示词，而在你是否把每个动作放到正确位置：

| 你看到的东西 | 你真正要判断的事 | 错误用法 |
|---|---|---|
| Thread 列表 | 哪条任务线承载当前上下文 | 同一个线程里混多个目标 |
| Prompt 输入框 | 任务是否有目标、范围、验证和交付 | 只写“帮我优化一下” |
| Terminal / 命令输出 | 命令是否按预期运行、有没有失败 | 只看 Codex 总结，不看输出 |
| Review 面板 | 文件差异是否可接受 | 跳过 diff 直接提交 |
| Settings | 当前模型、审批、沙盒、工具是否匹配任务风险 | 所有项目都用同一套高权限配置 |
| Apps / Connectors | 外部上下文是否真的需要 | 为了方便授权过多服务 |

一句话：App 负责把“任务、文件、命令、权限、审查、外部上下文”放在同一个桌面流程里。你要学的是流程控制，不是只学几个命令。

### 1.2 新手每天都该用的三条安全线

1. 第一次进陌生项目，先发只读任务，不让 Codex 写文件。
2. 第一次允许写文件，只改低风险文档或单个小范围文件。
3. 每次写完都进 Review 面板看 diff，不把“Codex 说完成了”当成验收。

这三条比任何高级插件都重要。只要你守住它们，App 工作流会比纯 CLI 更容易控制。

## 2. 三种工作方式


### 2.1 Local thread

适合小任务和只读任务：

```text
阅读这个项目并总结启动命令、测试命令、主要目录。不要修改文件。
```

优点：快，直接使用当前工作区。

风险：如果让 Codex 写文件，改动会进入当前工作区。学习阶段要盯住 Review 面板。

### 2.2 Worktree thread

适合功能开发、重构、风险较高的改动：

```text
在新的 worktree 里实现登录页移动端布局修复。改完运行相关测试，最后给我 diff 摘要。
```

优点：隔离改动，多个任务可以并行。

注意：worktree 仍然是真实 Git 工作区。合并前要 Review。

### 2.3 Cloud / Web handoff

适合云端仓库任务、长时间后台任务和 PR 工作流。App 是本地主控台，Cloud 是远程执行环境。不要把 Cloud 当成本地 App 的替代品。

### 2.4 三种方式怎么选

| 任务 | 推荐方式 | 为什么 |
|---|---|---|
| 读项目、找启动命令、解释报错 | Local thread 只读 | 快，风险低 |
| 改一个文件或少量文档 | Local thread + Review | 改动小，容易检查 |
| 新功能、重构、修多个文件 | Worktree thread | 隔离改动，便于撤回 |
| 盯 PR、修 CI、长时间后台任务 | Cloud / Web handoff | 不占用本地环境 |
| 依赖本机浏览器、私有服务、桌面 App | Local thread | 云端通常拿不到本机状态 |

判断标准不是“哪个功能更酷”，而是：执行环境能不能看到需要的文件、命令和服务；失败时你能不能快速撤销。

## 3. App 任务描述模板


好任务要包含五件事：

```text
目标：修复用户资料页加载失败。
范围：只改 src/pages/profile.tsx 和 src/api/user.ts。
约束：不要改数据库 schema，不要引入新依赖。
验证：运行 npm test -- profile。
交付：展示 diff 摘要和测试结果，不要自动提交。
```

不要只写：

```text
帮我修一下。
```

因为 Codex 不知道范围、验证方式和停止条件。

### 3.1 三类高质量任务模板

**只读理解任务**

```text
阅读这个仓库。只总结：技术栈、启动命令、测试命令、主要目录、当前风险。不要修改文件，不要安装依赖。
```

**小范围修复任务**

```text
目标：修复 docs/install.md 中 Windows 安装命令不一致的问题。
范围：只改 docs/install.md。
约束：不要改 README，不要新增图片，不要提交。
验证：检查文内命令前后一致。
交付：给出 diff 摘要和你检查过的命令列表。
```

**UI / 页面反馈任务**

```text
目标：修复设置页按钮在 375px 宽度下文字溢出。
范围：只改 SettingsPage 相关组件和样式。
验证：启动本地页面，分别检查桌面和移动宽度；如能使用 App 内浏览器或截图，请把观察结果写清楚。
交付：展示 diff、截图观察结论和未覆盖风险。
```

### 3.2 不要写进任务里的东西

- 不要写 API key、cookie、token、私有账号。
- 不要让 Codex “顺手优化所有代码”。
- 不要把“自动提交、自动推送、自动发 PR”设成默认动作。
- 不要让它在没说明原因时安装新依赖。
- 不要把不确定的命令当成事实；让它先从项目文件里找证据。

## 4. App Review 工作流

每次 Codex 修改文件后，你都应该看 Review：

1. 看文件列表：确认只改了目标范围。
2. 看 diff：确认行为变化合理。
3. 看测试输出：确认验证真的跑过。
4. 行内指出问题：让 Codex 继续修。
5. 需要提交时再 stage / commit。

典型提示：

```text
Review 这次 diff。重点检查是否改了范围外文件、是否缺测试、是否引入兼容性风险。
```

### 4.1 Review 面板逐项检查法

| 检查项 | 看什么 | 不通过时怎么处理 |
|---|---|---|
| 文件列表 | 是否只改了任务范围内文件 | 让 Codex 解释范围外改动，必要时撤掉 |
| 单文件 diff | 是否有无关格式化、删除、重命名 | 行内指出具体片段重改 |
| 命令记录 | 是否真的运行验证命令 | 要求运行相关命令，或说明不能运行原因 |
| 生成文件 | 是否出现 lockfile、build output、缓存 | 确认是否必要，不必要就删除 |
| 安全项 | 是否碰到 `.env`、token、权限配置 | 立即停止并人工审查 |
| Git 操作 | 是否 stage / commit / push 到正确目标 | 学习阶段让 Codex 只草拟，不自动执行 |

### 4.2 App 里继续修的正确说法

不要开新线程说“你刚才改错了”。在同一线程里引用具体 diff：

```text
Review 面板里 src/pages/login.tsx 第 42 行的条件判断不对。请只改这一处，并说明为什么不会影响桌面端。
```

这样 Codex 能保留上下文，改动也更容易收敛。

## 5. App 中的 Commands

Commands 是 App 里最快的工作流入口。你可以在输入框里输入 `/` 查看当前可用命令。

常见类型：

| 类型 | 例子 | 用途 |
|---|---|---|
| 会话 | `/new`、`/resume`、`/compact` | 管理线程和上下文 |
| 状态 | `/status` | 查看模型、工作区、审批、上下文状态 |
| 工作流 | `/plan`、`/review` | 计划、审查 |
| 工具 | `/mcp`、Apps / Plugins 入口 | 外部能力入口 |
| 反馈 | `/feedback` | 向产品反馈问题 |

不同版本、实验开关和插件会改变命令列表。App 用户先输入 `/` 看当前列表；`/goal`、`/loop` 等长目标/循环入口如果当前 App 显示，再按 CX-03 的实验能力写法使用。完整讲解见 CX-03。

### 5.1 App 命令的正确学习方式

App 命令会随版本、账号、实验开关、插件和连接器变化。本教程只把 `/status`、`/plan`、`/review`、`/mcp` 这类稳定工作流当作教学主线；其他命令必须以当前 App 输入 `/` 后的列表为准。

如果你想核对一个命令是否可用，按这个顺序：

1. 在 App 输入 `/` 看当前列表。
2. 看命令弹出的说明文字。
3. 再查官方 App Commands 文档。
4. CLI 里的命令只能辅助理解，不能反向证明 App 一定有同名入口。

## 6. App 中的项目指令

`AGENTS.md` 是 Codex 理解项目规则的核心文件。它应该写：

- 项目是什么。
- 常用命令是什么。
- 代码风格和测试要求。
- 哪些文件不要动。
- 修改后如何验证。

不要把临时任务、密钥、个人路径写进去。详见 CX-04。

## 7. App 中的 MCP

MCP 是外部工具协议。App 通过 MCP 使用浏览器、数据库、文档源、内部系统等工具。

App 用户应该先理解：

- MCP 不是 prompt。
- MCP server 可能带权限和数据风险。
- 连接后要在 App 或 `/mcp` 中确认工具是否可用。
- CLI 管理 MCP 只是辅助，不是主线。

详见 CX-05。

## 8. App 中的 Skills

Skills 是可复用工作流。适合把“每次都要重复说的步骤”沉淀下来。

例如：

- 代码审查流程。
- 发布前检查。
- 文档核对流程。
- 特定团队的写作规范。

App 中可以通过自然语言或 `$skill-name` 点名触发。详见 CX-06。

## 9. App 中的 Plugins / Connectors

Plugins 是能力包，可能包含 Skills、MCP、Apps、配置等。Connectors 是连接外部服务的入口，例如 GitHub、Google Drive、Slack、Gmail 等。

App 用户要记住：

- 安装插件不等于自动授权所有外部数据。
- 连接器需要单独登录和授权。
- 外部服务权限要按最小范围配置。

详见 CX-07。

## 10. App 中的 Subagents

Subagents 适合并行分析、分工改代码、独立审查。不要滥用。

适合：

- 一个 agent 查 API 文档，一个 agent 看代码实现。
- 一个 agent 改前端，一个 agent 改测试。
- 一个 agent 做实现，一个 agent 做 review。

不适合：

- 单个错别字。
- 线性的一步任务。
- 你还没定义清楚范围的大任务。

详见 CX-08。

## 11. App 中的 Automations

Automations 用来让 Codex 周期性或后台执行任务：

- 每天检查测试是否失败。
- 每周总结依赖更新。
- 定期检查文档是否和代码漂移。
- 监控某个 PR 或部署状态。

创建自动化前要明确：

- 运行频率。
- 工作目录。
- 是否允许写文件。
- 找到问题后通知还是自动修。

详见 CX-09。

## 12. App 与 GitHub / PR

App 的 Git 工作流通常是：

1. Codex 修改本地文件。
2. 你在 Review 面板检查。
3. 运行测试。
4. stage / commit。
5. 推送分支。
6. 创建 PR。
7. 必要时交给 Cloud / Web 继续处理。

GitHub / PR 详见 CX-10。

## 13. App 用户什么时候看 CLI / Web

| 你要做什么 | 看哪里 |
|---|---|
| 只用桌面 App 开发 | CX-01 到 CX-10 |
| 排查 slash / MCP / config | CX-12 CLI |
| CI 或无头任务 | CX-12 CLI |
| 云端仓库任务 / 长跑 PR | CX-11 Web / Cloud |
| 内部平台接 Codex | SDK / App Server 官方文档 |

## 14. 从零到 PR 的完整 App 实战

这一节是把前面的概念串起来。建议你拿一个非生产仓库练一遍。

### 14.1 第一步：只读建模

```text
阅读这个项目，只输出：技术栈、启动命令、测试命令、主要目录、可能需要写进 AGENTS.md 的规则。不要修改文件。
```

通过标准：

- Codex 没有写文件。
- 输出里能指出命令来自哪个文件，例如 `package.json`、README、Makefile。
- 没有把不存在的命令编出来。

### 14.2 第二步：小范围修改

```text
目标：修正 README 中一处过时命令。
范围：只改 README.md。
约束：不要改版本号，不要重排整篇文档，不要提交。
验证：确认 README 中同类命令写法一致。
交付：展示 diff 摘要。
```

通过标准：

- Review 里只有 README。
- 没有格式化整篇文件。
- Codex 能说明为什么改这一行。

### 14.3 第三步：让 Codex 自审一次

```text
/review 重点检查这次改动是否超出范围、是否引入错误命令、是否需要更新其他文档。
```

如果当前 App 没有 `/review`，就用自然语言：

```text
请审查当前 diff。只报告问题和风险，不要继续修改。
```

### 14.4 第四步：人工决定 Git 操作

学习阶段建议你让 Codex 只草拟：

```text
根据当前 diff 写一个 commit message 和 PR 描述。不要执行 git commit，不要 push。
```

你确认无误后再决定是否提交。这样可以保留人的最后控制权。

## 15. Codex App 课程交叉验证口径

为了避免 Codex 系列比 Claude Code 系列弱，本系列以后每次更新都按同一张表验收：

| 维度 | 必须满足 |
|---|---|
| 官方来源 | 每篇至少列出对应官方文档或 release 来源 |
| App 主线 | 普通用户先学 App；CLI / Cloud / SDK 是辅助 |
| 操作步骤 | 关键章节必须有可复制提示词或命令 |
| 风险边界 | 写清权限、沙盒、外部连接器、Git 操作边界 |
| Review | 涉及写文件时必须回到 Review / diff |
| 可视化素材 | 如需配图，必须逐篇核对语义位置和来源，不做批量插图 |
| 版本口径 | 固定版本只作验证快照，最终以当前 App 和官方文档为准 |

## 常见问题

### Q1：App 是不是只能聊天？

不是。App 是本地开发主控台，核心是线程、文件改动、Review、终端、插件、自动化。

### Q2：所有功能都要学吗？

不需要。先掌握 Thread、Review、Commands、AGENTS.md，再按需要学 MCP、Skills、Plugins、Automations。

### Q3：App 中看到的命令和 CLI 一样吗？

不一定。以当前 App 的 `/help`、命令补全和官方文档为准。CLI 能帮助核对，但不是 App 的替代品。

---

## 📝 总结与检查清单

完成本课后，请确认以下所有项：

- [ ] 理解Local thread、Worktree thread、Cloud handoff三种工作方式的区别
- [ ] 能用五要素模板（目标-范围-约束-验证-交付）描述任务
- [ ] 知道如何在App中查看diff和审批命令
- [ ] 理解Settings中模型、审批、沙盒等配置入口
- [ ] 知道Commands、MCP、Skills、Plugins、Automations在App中的位置
- [ ] 不跳过Review直接合并
- [ ] 陌生项目先用只读模式

**如果以上全部勾选，恭喜你掌握Codex App核心工作流！**

---

## 附录

### A. App核心概念速查

| 概念 | 一句话解释 |
|------|-----------|
| Thread | 一条任务线，包含上下文、历史和状态 |
| Local thread | 直接在当前工作区执行的任务线 |
| Worktree thread | 在隔离Git分支/目录中执行的任务线 |
| Cloud handoff | 将任务交给云端执行的接力模式 |
| Review面板 | 查看diff、文件变更、行内反馈的关口 |
| Settings | 模型、审批、沙盒、插件、MCP等配置入口 |

### B. 任务描述模板

```text
目标：[你要达到什么效果]
范围：[只改哪些文件/目录]
约束：[不能做什么]
验证：[怎么确认改对了]
交付：[期望的输出形式]
```

### C. 推荐学习资源

- **Codex App 官方文档**：https://developers.openai.com/codex
- **Codex App Review 官方文档**：https://developers.openai.com/codex/app/review
- **Codex App Automations 官方文档**：https://developers.openai.com/codex/app/automations
- **本系列上一篇**：[CX-01 Codex App 安装与认证](./CX-01-Codex-App安装与认证完整指南.md)
- **本系列下一篇**：[CX-03 Commands 工作流入口](./CX-03-Codex-Commands工作流入口完整指南.md)

---

**课程制作**：老金
**最后更新**：2026年5月30日
**许可**：本课程采用CC BY-NC-SA 4.0许可

---

## 下一步

下一篇：[CX-03 Commands：App 里的 slash commands 与工作流入口](./CX-03-Codex-Commands工作流入口完整指南.md)。
