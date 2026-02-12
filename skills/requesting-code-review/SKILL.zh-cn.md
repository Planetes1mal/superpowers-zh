---
name: requesting-code-review
description: 在完成任务、实现主要功能或合并前验证工作是否符合要求时使用
---

# 请求代码审查 (Requesting Code Review)

派遣 superpowers:code-reviewer subagent 在问题蔓延之前捕获它们。

**核心原则：** 尽早审查，频繁审查。

## 何时请求审查

**必须：**
- 在 subagent-driven development 的每个任务之后
- 在完成主要功能后
- 在合并到 main 之前

**可选但有价值：**
- 遇到困难时（新视角）
- 在重构之前（基线检查）
- 在修复复杂 bug 后

## 如何请求

**1. 获取 git SHA：**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # 或 origin/main
HEAD_SHA=$(git rev-parse HEAD)
```

**2. 派遣 code-reviewer subagent：**

使用 Task 工具，类型为 superpowers:code-reviewer，填写 `code-reviewer.md` 中的模板

**占位符：**
- `{WHAT_WAS_IMPLEMENTED}` - 你刚构建的内容
- `{PLAN_OR_REQUIREMENTS}` - 它应该做什么
- `{BASE_SHA}` - 起始提交
- `{HEAD_SHA}` - 结束提交
- `{DESCRIPTION}` - 简要摘要

**3. 根据反馈行动：**
- 立即修复 Critical 问题
- 在继续之前修复 Important 问题
- 记录 Minor 问题稍后处理
- 如果审查者错了就反驳（给出理由）

## 示例

```
[刚刚完成任务 2：添加验证功能]

你：让我在继续之前请求代码审查。

BASE_SHA=$(git log --oneline | grep "Task 1" | head -1 | awk '{print $1}')
HEAD_SHA=$(git rev-parse HEAD)

[派遣 superpowers:code-reviewer subagent]
  WHAT_WAS_IMPLEMENTED: conversation index 的验证和修复功能
  PLAN_OR_REQUIREMENTS: docs/plans/deployment-plan.md 中的任务 2
  BASE_SHA: a7981ec
  HEAD_SHA: 3df7661
  DESCRIPTION: 添加了 verifyIndex() 和 repairIndex()，支持 4 种问题类型

[Subagent 返回]：
  Strengths: 清晰的架构，真实的测试
  Issues:
    Important: 缺少进度指示器
    Minor: 报告间隔的 magic number (100)
  Assessment: 可以继续

你：[修复进度指示器]
[继续到任务 3]
```

## 与工作流集成

**Subagent-Driven Development：**
- 每个任务后审查
- 在问题累积之前捕获它们
- 在移动到下一个任务之前修复

**Executing Plans：**
- 每批（3 个任务）后审查
- 获取反馈，应用，继续

**Ad-Hoc Development：**
- 在合并前审查
- 遇到困难时审查

## 警告标志

**绝不：**
- 因为"很简单"而跳过审查
- 忽略 Critical 问题
- 带着未修复的 Important 问题继续
- 与有效的技术反馈争论

**如果审查者错了：**
- 用技术理由反驳
- 展示证明有效的代码/测试
- 请求澄清

查看模板：requesting-code-review/code-reviewer.md
