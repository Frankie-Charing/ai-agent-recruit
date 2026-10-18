# AI Agent 学习笔记

> 🤖 AI 使用说明：本部分使用 Codex（OpenAI 的 Coding Agent）协助完成，最终修改由本人通过 `git diff` 检查并验证。

## 任务 1：领养并试用一个 Agent

### 我使用了什么 Agent

**Codex** —— OpenAI 推出的 Coding Agent。它能阅读项目文件、修改代码和文档、执行终端命令、搜索整个项目，并根据程序运行结果继续工作。

### 安装和配置过程

- 在 Codex 桌面应用中直接使用（也支持 CLI 形式）。
- 无需额外安装运行时；在项目目录中打开即可开始。
- 配置了 GitHub 凭据，使 Agent 能把代码推送到远程仓库。

### 我交给了它什么任务

1. 阅读 `linux-shell-recruit` 仓库，完成 8 道主线 Shell 题和 1 道附加题。
2. 阅读 `make-cmake-recurit` 仓库，补全 `Makefile` 和 `CMakeLists.txt`。
3. 撰写这份学习笔记。

### Agent 做了哪些修改

以 `linux-shell-recruit` 为例：

- 生成 `output/` 下 10 个答案文件；
- 补全 `scripts/analyze.sh`（参数校验、文件校验、统计 ERROR 数量与最高频 code）；
- 修复 `scripts/batch-copy.sh`（给 `$@`、`$file` 等加引号，解决带空格文件名复制失败的问题）；
- 完成 `answers/02.md`、`answers/08.md` 的文字回答；
- 撰写 `note.md` 学习笔记。

以 `make-cmake-recurit` 为例：

- 补全 `make-task/Makefile` 的编译、链接与 `clean` 规则；
- 补全 `cmake-task/CMakeLists.txt` 的 `add_executable` 与 `target_include_directories`；
- 完成 `answers/task1.md`、`answers/task4.md`。

### git diff 中我看到了什么

用 `git diff` 逐一检查了每次改动：

- 确认 `analyze.sh` 的三处校验逻辑是必要且正确的；
- 确认 `batch-copy.sh` 的引号修复确实解决了“文件名带空格”的问题；
- 确认 `Makefile` 的依赖关系与增量构建逻辑正确；
- 确认 `CMakeLists.txt` 的 include 目录与源码列表正确。

### 最终结果是否符合预期

符合预期。两个仓库的本地判题脚本都通过：

```text
Linux & Shell: Main progress: 8 / 8（附加题 09 也 PASS）
Make & CMake:  2 passed, 0 failed
```

## 任务 2：五个问题

### 1. Agent 为什么能读取文件、修改代码，而普通聊天 AI 通常不能？

因为 Agent 除了大模型，还接入了若干 **Tool**（读取文件、写入文件、搜索、执行命令等）。Agent 会把大模型的意图翻译成 Tool 调用，由运行环境真正执行，再把 Tool 的返回结果送回模型继续推理。普通聊天 AI 只有对话能力，没有执行这些动作的通道。

### 2. Tool 在 Agent 中起到了什么作用？

Tool 是 Agent 与外部世界交互的“手和脚”。它让模型不再只是“说”，而是能“做”：读取真实文件、修改代码、运行程序、访问服务等。模型负责判断何时调用哪个 Tool，并根据 Tool 的返回结果继续完成任务。

### 3. 为什么项目需要给 Agent 配置一份类似“员工手册”的规则？

Agent 第一次来到项目时并不知道：这个项目是干什么的、文件该怎么放、有什么开发规范、改完要不要测试、哪些东西不能动。项目级 Instructions / Rules（如 `CLAUDE.md`、`AGENTS.md`）能把这些上下文固化下来，让 Agent 每次工作都遵守同样的约定，减少乱改和踩坑。

### 4. Agent 为什么可能“忘记”之前说过的内容？额度怎么样计算？

大模型一次能处理的上下文（Context）是有限的。对话、代码、Tool 返回结果、项目规则等内容都会占用上下文；聊得久了，较早的信息可能被截断或“挤掉”。额度通常按 **Token** 计算：输入（prompt + 上下文 + 工具结果）和输出都要计数，不同产品还会有每日/每轮次数限制，超限后无法继续。

### 5. 如果一个 Agent 可以随便执行任何终端命令，会有什么风险？

可能误删文件、覆盖数据、安装恶意软件、泄露密钥，甚至影响系统或他人数据。所以应限制权限、对危险操作要求确认、不把密码/Token/API Key 交给它，并且人始终要为最终结果负责。

## 任务 3（可选）：MCP / Skill / Tools

- **MCP**：Codex 中接入了 GitHub MCP，让 Agent 能直接读写仓库、创建 Pull Request 等。
- **Skill**：Codex 提供 Skill 机制（如文档、表格、演示文稿等技能），Agent 可以按专门的工作流处理特定任务。
- **Tools**：Agent 通过内置工具执行 `git`、运行脚本、读写文件等。

配置过程：在 Codex 应用内启用相应插件 / 连接，并在任务中按需调用，无需手写复杂配置文件。这让“读仓库 → 改代码 → 运行 → 提交”这类重复流程可以交给 Agent，自己专注于审查最终结果。
