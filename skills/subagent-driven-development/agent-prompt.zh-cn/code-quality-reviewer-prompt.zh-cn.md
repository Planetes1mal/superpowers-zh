# 代码质量审查者提示模板 (Code Quality Reviewer Prompt Template)

在派遣代码质量审查者 subagent 时使用此模板。

**目的：** 验证实现构建良好（干净、经过测试、可维护）

**仅在规范合规性审查通过后派遣。**

```
Task tool (superpowers:code-reviewer):
  Use template at requesting-code-review/code-reviewer.md

  WHAT_WAS_IMPLEMENTED: [from implementer's report]
  PLAN_OR_REQUIREMENTS: Task N from [plan-file]
  BASE_SHA: [commit before task]
  HEAD_SHA: [current commit]
  DESCRIPTION: [task summary]
```

**代码审查者返回：** Strengths, Issues (Critical/Important/Minor), Assessment
