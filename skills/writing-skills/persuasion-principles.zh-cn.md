# Skill 设计的说服原则

## 概述

LLMs 对与人类相同的说服原则做出响应。理解这种心理学有助于你设计更有效的 skills - 不是为了操纵，而是为了确保即使在压力下也能遵循关键实践。

**研究基础：** Meincke 等人 (2025) 用 N=28,000 次 AI 对话测试了 7 种说服原则。说服技术使合规率提高了一倍以上（33% → 72%，p < .001）。

## 七项原则

### 1. 权威 (Authority)
**它是什么：** 对专业知识、证书或官方来源的顺从。

**在 skills 中如何运作：**
- 命令式语言："YOU MUST", "Never", "Always"
- 不可协商的框架："No exceptions"
- 消除决策疲劳和合理化

**何时使用：**
- 强制执行纪律的 skills（TDD、验证要求）
- 安全关键实践
- 已建立的最佳实践

**示例：**
```markdown
✅ 在测试之前写代码？删除它。重新开始。没有例外。
❌ 在可行时考虑先写测试。
```

### 2. 承诺 (Commitment)
**它是什么：** 与先前行动、声明或公开声明保持一致。

**在 skills 中如何运作：**
- 要求宣布："Announce skill usage"
- 强制明确选择："Choose A, B, or C"
- 使用跟踪：TodoWrite 用于检查清单

**何时使用：**
- 确保 skills 实际被遵循
- 多步骤过程
- 问责机制

**示例：**
```markdown
✅ 当你找到 skill 时，你必须宣布："I'm using [Skill Name]"
❌ 考虑让你的伙伴知道你在使用哪个 skill。
```

### 3. 稀缺 (Scarcity)
**它是什么：** 来自时间限制或有限可用性的紧迫感。

**在 skills 中如何运作：**
- 时间限制要求："Before proceeding"
- 顺序依赖："Immediately after X"
- 防止拖延

**何时使用：**
- 立即验证要求
- 时间敏感工作流
- 防止"我稍后会做"

**示例：**
```markdown
✅ 完成任务后，在继续之前立即请求代码审查。
❌ 你可以在方便时审查代码。
```

### 4. 社会认同 (Social Proof)
**它是什么：** 顺从他人所做或被认为正常的事情。

**在 skills 中如何运作：**
- 通用模式："Every time", "Always"
- 失败模式："X without Y = failure"
- 建立规范

**何时使用：**
- 记录通用实践
- 警告常见失败
- 强化标准

**示例：**
```markdown
✅ 没有 TodoWrite 跟踪的检查清单 = 步骤被跳过。每次都这样。
❌ 有些人发现 TodoWrite 对检查清单有帮助。
```

### 5. 团结 (Unity)
**它是什么：** 共享身份，"我们感"，群内归属感。

**在 skills 中如何运作：**
- 协作语言："our codebase", "we're colleagues"
- 共享目标："we both want quality"

**何时使用：**
- 协作工作流
- 建立团队文化
- 非层级实践

**示例：**
```markdown
✅ 我们是同事，一起工作。我需要你诚实的技术判断。
❌ 如果我错了，你可能应该告诉我。
```

### 6. 互惠 (Reciprocity)
**它是什么：** 回报所获得的好处的义务。

**如何运作：**
- 谨慎使用 - 可能感觉操纵
- 在 skills 中很少需要

**何时避免：**
- 几乎总是（其他原则更有效）

### 7. 喜爱 (Liking)
**它是什么：** 偏好与我们喜欢的人合作。

**如何运作：**
- **不要用于合规**
- 与诚实反馈文化冲突
- 创造谄媚

**何时避免：**
- 始终用于纪律执行

## 按 Skill 类型的原则组合

| Skill 类型 | 使用 | 避免 |
|------------|-----|-------|
| 强制执行纪律 | Authority + Commitment + Social Proof | Liking, Reciprocity |
| 指导/技术 | Moderate Authority + Unity | Heavy authority |
| 协作 | Unity + Commitment | Authority, Liking |
| 参考 | 仅清晰 | 所有说服 |

## 为什么有效：心理学

**明线规则减少合理化：**
- "YOU MUST" 移除决策疲劳
- 绝对语言消除"这是例外吗？"问题
- 明确的反合理化针对特定漏洞

**实施意图创造自动行为：**
- 清晰的触发器 + 要求的动作 = 自动执行
- "When X, do Y" 比"generally do Y"更有效
- 降低合规的认知负荷

**LLMs 是类人智能体 (parahuman)：**
- 在包含这些模式的人类文本上训练
- 训练数据中权威语言先于合规
- 承诺序列（声明 → 动作）经常被建模
- 社会认同模式（每个人都做 X）建立规范

## 伦理使用

**合法：**
- 确保关键实践被遵循
- 创建有效文档
- 防止可预测的失败

**非法：**
- 为个人利益操纵
- 创造虚假紧迫感
- 基于内疚的合规

**测试：** 如果他们完全理解，这种技术会服务于用户的真正利益吗？

## 研究引用

**Cialdini, R. B. (2021).** *Influence: The Psychology of Persuasion (New and Expanded).* Harper Business.
- 七项说服原则
- 影响力研究的实证基础

**Meincke, L., Shapiro, D., Duckworth, A. L., Mollick, E., Mollick, L., & Cialdini, R. (2025).** Call Me A Jerk: Persuading AI to Comply with Objectionable Requests. University of Pennsylvania.
- 用 N=28,000 次 LLM 对话测试 7 项原则
- 使用说服技术，合规率从 33% → 72%
- Authority、commitment、scarcity 最有效
- 验证 LLM 行为的类人智能体模型

## 快速参考

设计 skill 时，询问：

1. **它是什么类型？**（纪律 vs. 指导 vs. 参考）
2. **我试图改变什么行为？**
3. **哪些原则适用？**（通常 authority + commitment 用于纪律）
4. **我是否组合太多？**（不要使用全部七个）
5. **这合乎道德吗？**（服务于用户的真正利益？）
