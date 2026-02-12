# Superpowers

<p align="left">
  <a href="README.md">简体中文</a> |
  <a href="README.en.md">English</a>
</p>

Superpowers 是一套面向 coding agents 的完整软件开发工作流，基于一组可组合的「技能（skills）」以及若干初始指令，确保你的 agent 会正确使用它们。

## 工作原理

从你启动 coding agent 的那一刻就开始了。当它发现你在构建某样东西时，它*不会*立刻动手写代码，而是先退一步，问你真正想达成什么。

一旦从对话中梳理出需求规格，它会分块呈现给你，每块都短到可以真正阅读和消化。

在你认可设计之后，agent 会整理出一份实现计划，清晰到足以让一位热情但缺乏品味、没有判断力、不了解项目背景、还不爱写测试的初级工程师照着做。计划强调真正的红/绿 TDD、YAGNI（You Aren't Gonna Need It）和 DRY。

接下来，一旦你说「开始」，它会启动 *子智能体驱动开发（subagent-driven-development）* 流程：由多个 agent 逐项完成工程任务、检查并评审产出，然后继续推进。Claude 常常可以按你们一起定下的计划自主工作数小时而不跑偏。

除此之外还有很多细节，但这就是系统的核心。而且因为技能会自动触发，你不需要做任何特别操作——你的 coding agent 只是拥有了 Superpowers。

## 赞助

如果 Superpowers 帮你做了能赚钱的事，并且你愿意的话，我会非常感激你考虑 [赞助我的开源工作](https://github.com/sponsors/obra)。

谢谢！

- Jesse

## 安装

**说明：** 不同平台安装方式不同。Claude Code 有内置插件系统；Codex 和 OpenCode 需要手动配置。

### Claude Code（通过插件市场）

在 Claude Code 中，先注册市场：

```bash
/plugin marketplace add obra/superpowers-marketplace
```

然后从该市场安装插件：

```bash
/plugin install superpowers@superpowers-marketplace
```

### 验证安装

新建一个会话，向 Claude 提出会触发某技能的问题（例如「帮我规划这个功能」或「我们来排查这个问题」）。Claude 应会自动调用对应的 superpowers 技能。

### Codex

对 Codex 说：

```
Fetch and follow instructions from https://raw.githubusercontent.com/obra/superpowers/refs/heads/main/.codex/INSTALL.md
```

**详细文档：** [docs/README.codex.md](docs/README.codex.md)

### OpenCode

对 OpenCode 说：

```
Fetch and follow instructions from https://raw.githubusercontent.com/obra/superpowers/refs/heads/main/.opencode/INSTALL.md
```

**详细文档：** [docs/README.opencode.md](docs/README.opencode.md)

## 基本工作流

1. **brainstorming（头脑风暴）** - 在写代码前触发。通过提问细化粗略想法、探索替代方案，分节呈现设计供你确认，并保存设计文档。

2. **using-git-worktrees（使用 Git 工作树）** - 在设计获准后触发。在新分支上创建隔离工作区，执行项目初始化，验证测试基线干净。

3. **writing-plans（编写计划）** - 在设计获准后触发。将工作拆成小块任务（每项约 2–5 分钟），每项任务包含确切文件路径、完整代码与验证步骤。

4. **subagent-driven-development（子智能体驱动开发）** 或 **executing-plans（执行计划）** - 在计划就绪后触发。按任务派发新的 subagent，进行两阶段评审（先看是否符合规格，再看代码质量）；或分批执行并设置人工检查点。

5. **test-driven-development（测试驱动开发）** - 在实现过程中触发。强制 RED-GREEN-REFACTOR：先写失败测试、确认失败，再写最少代码、确认通过，然后提交；会删除在测试之前写下的代码。

6. **requesting-code-review（请求代码评审）** - 在任务之间触发。对照计划做评审，按严重程度报告问题；严重问题会阻塞进展。

7. **finishing-a-development-branch（完成开发分支）** - 在任务全部完成时触发。验证测试、给出选项（合并/PR/保留/丢弃），并清理 worktree。

**Agent 会在执行任何任务前检查相关技能。** 这些是强制工作流，而非建议。

## 内容概览

### 技能库

**测试**

| 技能 | 描述 | 文件 |
|------|------|------|
| [test-driven-development](skills/test-driven-development/SKILL.zh-cn.md) | 在实现任何功能或修复 bug 时使用，在编写实现代码之前 | [SKILL.md](skills/test-driven-development/SKILL.md) · [中文](skills/test-driven-development/SKILL.zh-cn.md) <br />[testing-anti-patterns.md](skills/test-driven-development/testing-anti-patterns.md) · [中文](skills/test-driven-development/testing-anti-patterns.zh-cn.md) |

**调试**

| 技能 | 描述 | 文件 |
|------|------|------|
| [systematic-debugging](skills/systematic-debugging/SKILL.zh-cn.md) | 在遇到任何 bug、测试失败或意外行为时使用，在提出修复之前 | [SKILL.md](skills/systematic-debugging/SKILL.md) · [中文](skills/systematic-debugging/SKILL.zh-cn.md)<br />[root-cause-tracing.md](skills/systematic-debugging/root-cause-tracing.md) · [中文](skills/systematic-debugging/root-cause-tracing.zh-cn.md)<br />[defense-in-depth.md](skills/systematic-debugging/defense-in-depth.md) · [中文](skills/systematic-debugging/defense-in-depth.zh-cn.md)<br />[condition-based-waiting.md](skills/systematic-debugging/condition-based-waiting.md) · [中文](skills/systematic-debugging/condition-based-waiting.zh-cn.md)<br />[CREATION-LOG.md](skills/systematic-debugging/CREATION-LOG.md) · [中文](skills/systematic-debugging/CREATION-LOG.zh-cn.md) <br />[test-academic.md](skills/systematic-debugging/test-academic.md) · [中文](skills/systematic-debugging/test-academic.zh-cn.md) <br />[test-pressure-1.md](skills/systematic-debugging/test-pressure-1.md) · [中文](skills/systematic-debugging/test-pressure-1.zh-cn.md) <br />[test-pressure-2.md](skills/systematic-debugging/test-pressure-2.md) · [中文](skills/systematic-debugging/test-pressure-2.zh-cn.md) <br />[test-pressure-3.md](skills/systematic-debugging/test-pressure-3.md) · [中文](skills/systematic-debugging/test-pressure-3.zh-cn.md) |
| [verification-before-completion](skills/verification-before-completion/SKILL.zh-cn.md) | 在即将声称工作完成、修复或通过时，在提交或创建 PR 之前使用 | [SKILL.md](skills/verification-before-completion/SKILL.md) · [中文](skills/verification-before-completion/SKILL.zh-cn.md) |

**协作**

| 技能 | 描述 | 文件 |
|------|------|------|
| [brainstorming](skills/brainstorming/SKILL.zh-cn.md) | 在进行任何创意工作之前必须使用——包括创建功能、构建组件、添加功能或修改行为 | [SKILL.md](skills/brainstorming/SKILL.md) · [中文](skills/brainstorming/SKILL.zh-cn.md) |
| [writing-plans](skills/writing-plans/SKILL.zh-cn.md) | 当你有一个多步骤任务的规格或需求时，在接触代码之前使用 | [SKILL.md](skills/writing-plans/SKILL.md) · [中文](skills/writing-plans/SKILL.zh-cn.md) |
| [executing-plans](skills/executing-plans/SKILL.zh-cn.md) | 当你有一个需要在独立会话中执行的书面实施计划，并带有审查检查点时使用 | [SKILL.md](skills/executing-plans/SKILL.md) · [中文](skills/executing-plans/SKILL.zh-cn.md) |
| [dispatching-parallel-agents](skills/dispatching-parallel-agents/SKILL.zh-cn.md) | 当面对 2 个或更多可以独立工作、无需共享状态或顺序依赖的任务时使用 | [SKILL.md](skills/dispatching-parallel-agents/SKILL.md) · [中文](skills/dispatching-parallel-agents/SKILL.zh-cn.md) |
| [subagent-driven-development](skills/subagent-driven-development/SKILL.zh-cn.md) | 在当前会话中执行具有独立任务的实现计划时使用 | [SKILL.md](skills/subagent-driven-development/SKILL.md) · [中文](skills/subagent-driven-development/SKILL.zh-cn.md) <br />[implementer-prompt.md](skills/subagent-driven-development/implementer-prompt.md) · [中文](skills/subagent-driven-development/agent-prompt.zh-cn/implementer-prompt.zh-cn.md) <br />[spec-reviewer-prompt.md](skills/subagent-driven-development/spec-reviewer-prompt.md) · [中文](skills/subagent-driven-development/agent-prompt.zh-cn/spec-reviewer-prompt.zh-cn.md) <br />[code-quality-reviewer-prompt.md](skills/subagent-driven-development/code-quality-reviewer-prompt.md) · [中文](skills/subagent-driven-development/agent-prompt.zh-cn/code-quality-reviewer-prompt.zh-cn.md) |
| [requesting-code-review](skills/requesting-code-review/SKILL.zh-cn.md) | 在完成任务、实现主要功能或合并前验证工作是否符合要求时使用 | [SKILL.md](skills/requesting-code-review/SKILL.md) · [中文](skills/requesting-code-review/SKILL.zh-cn.md) <br />[code-reviewer.md](skills/requesting-code-review/code-reviewer.md) · [中文](skills/requesting-code-review/agent-prompt.zh-cn/code-reviewer.zh-cn.md) |
| [receiving-code-review](skills/receiving-code-review/SKILL.zh-cn.md) | 在收到代码审查反馈时使用，在实施建议之前 | [SKILL.md](skills/receiving-code-review/SKILL.md) · [中文](skills/receiving-code-review/SKILL.zh-cn.md) |
| [using-git-worktrees](skills/using-git-worktrees/SKILL.zh-cn.md) | 在开始需要与当前工作区隔离的功能工作，或在执行实现计划之前使用 | [SKILL.md](skills/using-git-worktrees/SKILL.md) · [中文](skills/using-git-worktrees/SKILL.zh-cn.md) |
| [finishing-a-development-branch](skills/finishing-a-development-branch/SKILL.zh-cn.md) | 在实现完成、所有测试通过，且需要决定如何集成工作时使用 | [SKILL.md](skills/finishing-a-development-branch/SKILL.md) · [中文](skills/finishing-a-development-branch/SKILL.zh-cn.md) |

**元技能**

| 技能 | 描述 | 文件 |
|------|------|------|
| [writing-skills](skills/writing-skills/SKILL.zh-cn.md) | 在创建新 skills、编辑现有 skills 或在部署前验证 skills 工作时使用 | [SKILL.md](skills/writing-skills/SKILL.md) · [中文](skills/writing-skills/SKILL.zh-cn.md) <br />[anthropic-best-practices.md](skills/writing-skills/anthropic-best-practices.md) · [中文](skills/writing-skills/anthropic-best-practices.zh-cn.md) <br />[persuasion-principles.md](skills/writing-skills/persuasion-principles.md) · [中文](skills/writing-skills/persuasion-principles.zh-cn.md) <br />[testing-skills-with-subagents.md](skills/writing-skills/testing-skills-with-subagents.md) · [中文](skills/writing-skills/testing-skills-with-subagents.zh-cn.md) <br />[examples/CLAUDE_MD_TESTING.md](skills/writing-skills/examples/CLAUDE_MD_TESTING.md) · [中文](skills/writing-skills/examples/CLAUDE_MD_TESTING.zh-cn.md) |
| [using-superpowers](skills/using-superpowers/SKILL.zh-cn.md) | 在启动任何对话时使用——建立如何查找和使用 skills | [SKILL.md](skills/using-superpowers/SKILL.md) · [中文](skills/using-superpowers/SKILL.zh-cn.md) |

## 理念

- **测试驱动开发** - 始终先写测试
- **系统化而非临时** - 流程优先于猜测
- **降低复杂度** - 以简单为首要目标
- **证据优于断言** - 在宣布成功前先验证

延伸阅读：[Superpowers for Claude Code](https://blog.fsck.com/2025/10/09/superpowers/)

## 参与贡献

技能直接存放在本仓库中。贡献步骤：

1. Fork 本仓库
2. 为你的技能创建分支
3. 按 `writing-skills` 技能的要求创建并测试新技能
4. 提交 PR

完整指南见 `skills/writing-skills/SKILL.md`。

## 更新

更新插件后，技能会自动更新：

```bash
/plugin update superpowers
```

## 许可证

MIT License - 详见 LICENSE 文件

## 支持

- **Issues**：https://github.com/obra/superpowers/issues
- **Marketplace**：https://github.com/obra/superpowers-marketplace
