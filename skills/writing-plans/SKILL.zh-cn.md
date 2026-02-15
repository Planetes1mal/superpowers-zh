---
name: writing-plans
description: 当你有一个多步骤任务的规格或需求时，在接触代码之前使用
---

# 编写计划 (Writing Plans)

## 概述

编写全面的实施计划，假设工程师对我们的代码库一无所知且品味有问题。记录他们需要知道的一切：每个任务要接触哪些文件、代码、测试、他们可能需要检查的文档、如何测试它。将整个计划作为一口大小的任务给他们。DRY。YAGNI。TDD。频繁提交。

假设他们是熟练的开发人员，但几乎不了解我们的工具集或问题领域。假设他们不太了解好的测试设计。

**开始时宣布：** "我正在使用 writing-plans skill 来创建实施计划。"

**上下文：** 这应该在专用 worktree 中运行（由 brainstorming skill 创建）。

**保存计划到：** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## 一口大小任务的粒度

**每个步骤是一个动作（2-5 分钟）：**
- "编写失败测试" - 步骤
- "运行它确保失败" - 步骤
- "实施最小代码让测试通过" - 步骤
- "运行测试确保通过" - 步骤
- "提交" - 步骤

## 计划文档头部

**每个计划必须以此头部开头：**

```markdown
# [功能名称] 实施计划

> **For Claude:** 必需子技能：使用 superpowers:executing-plans 逐个任务实施此计划。

**目标：** [一句话描述构建什么]

**架构：** [2-3 句话关于方法]

**技术栈：** [关键技术/库]

---
```

## 任务结构

````markdown
### 任务 N: [组件名称]

**文件：**
- 创建: `exact/path/to/file.py`
- 修改: `exact/path/to/existing.py:123-145`
- 测试: `tests/exact/path/to/test.py`

**步骤 1: 编写失败测试**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**步骤 2: 运行测试验证失败**

运行: `pytest tests/path/test.py::test_name -v`
预期: FAIL with "function not defined"

**步骤 3: 编写最小实现**

```python
def function(input):
    return expected
```

**步骤 4: 运行测试验证通过**

运行: `pytest tests/path/test.py::test_name -v`
预期: PASS

**步骤 5: 提交**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
````

## 记住
- 始终精确文件路径
- 计划中完整代码（不是"添加验证"）
- 带预期输出的精确命令
- 使用 @ 语法引用相关 skills
- DRY、YAGNI、TDD、频繁提交

## 执行交接

保存计划后，提供执行选择：

**"计划完成并保存到 `docs/plans/<filename>.md`。两种执行选项：**

**1. Subagent-Driven（本会话）** - 我为每个任务派遣新的 subagent，任务之间审查，快速迭代

**2. Parallel Session（单独）** - 在新会话中打开 executing-plans，批量执行带检查点

**哪种方法？"**

**如果选择 Subagent-Driven：**
- **必需子技能：** 使用 superpowers:subagent-driven-development
- 留在本会话
- 每个任务新 subagent + 代码审查

**如果选择 Parallel Session：**
- 引导他们在 worktree 中打开新会话
- **必需子技能：** 新会话使用 superpowers:executing-plans
