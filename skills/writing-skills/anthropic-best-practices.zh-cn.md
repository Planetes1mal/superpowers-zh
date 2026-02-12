# Skill 编写最佳实践

> 了解如何编写高效的 Skills，让 Claude 能够发现并成功使用它们。

优秀的 Skills 应当简洁、结构清晰，并经过实际使用测试。本指南提供实用的编写建议，帮助你写出 Claude 能够发现并有效使用的 Skills。

关于 Skills 工作原理的概念背景，请参阅 [Skills 概述](/en/docs/agents-and-tools/agent-skills/overview)。

## 核心原则

### 简洁是关键

[上下文窗口](https://platform.claude.com/docs/en/build-with-claude/context-windows)是一种公共资源。你的 Skill 与 Claude 需要了解的所有其他内容共享上下文窗口，包括：

* 系统提示词
* 对话历史
* 其他 Skills 的元数据
* 你的实际请求

并非 Skill 中的每个 token 都有即时成本。在启动时，只有所有 Skills 的元数据（名称和描述）会被预加载。Claude 仅在 Skill 变得相关时才读取 SKILL.md，并在需要时才读取其他文件。然而，保持 SKILL.md 的简洁仍然很重要：一旦 Claude 加载了它，每个 token 都在与对话历史和其他上下文竞争。

**默认假设**：Claude 本身已经非常聪明

只添加 Claude 尚不具备的上下文。对每一条信息进行审视：

* "Claude 真的需要这个解释吗？"
* "我能假设 Claude 已经知道这些吗？"
* "这段内容是否值得占用这些 token？"

**正面示例：简洁**（约 50 个 token）：

````markdown  theme={null}
## 提取 PDF 文本

使用 pdfplumber 进行文本提取：

```python
import pdfplumber

with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```
````

**反面示例：过于冗长**（约 150 个 token）：

```markdown  theme={null}
## 提取 PDF 文本

PDF（便携式文档格式）是一种常见的文件格式，包含文本、图像和其他内容。
要从 PDF 中提取文本，你需要使用一个库。有很多可用于 PDF 处理的库，
但我们推荐 pdfplumber，因为它易于使用且能处理大多数情况。
首先，你需要通过 pip 安装它。然后可以使用下面的代码……
```

简洁版本假设 Claude 知道什么是 PDF 以及库是如何工作的。

### 设置适当的自由度

根据任务的脆弱性和变化性来匹配具体程度。

**高自由度**（基于文本的指令）：

适用场景：

* 多种方法都是有效的
* 决策取决于上下文
* 用启发式方法指导处理方式

示例：

```markdown  theme={null}
## 代码审查流程

1. 分析代码结构和组织方式
2. 检查潜在的 bug 或边界情况
3. 提出可读性和可维护性方面的改进建议
4. 验证是否遵守项目约定
```

**中等自由度**（伪代码或带参数的脚本）：

适用场景：

* 存在首选模式
* 允许一定程度的变化
* 配置会影响行为

示例：

````markdown  theme={null}
## 生成报告

使用此模板并根据需要进行自定义：

```python
def generate_report(data, format="markdown", include_charts=True):
    # 处理数据
    # 以指定格式生成输出
    # 可选地包含可视化内容
```
````

**低自由度**（具体脚本，少量或没有参数）：

适用场景：

* 操作脆弱且容易出错
* 一致性至关重要
* 必须遵循特定的执行顺序

示例：

````markdown  theme={null}
## 数据库迁移

严格执行此脚本：

```bash
python scripts/migrate.py --verify --backup
```

不要修改命令或添加额外的标志。
````

**类比**：把 Claude 想象成一个在路上探索的机器人：

* **两侧都是悬崖的窄桥**：只有一条安全的前进路线。提供具体的护栏和精确指令（低自由度）。示例：必须按照精确顺序运行的数据库迁移。
* **没有危险的开阔场地**：很多条路都能成功到达目的地。给出大致方向，信任 Claude 找到最佳路线（高自由度）。示例：需要根据上下文决定最佳方法的代码审查。

### 在所有计划使用的模型上进行测试

Skills 是模型的增强，因此效果取决于底层模型。在你计划使用的所有模型上测试你的 Skill。

**按模型的测试考量**：

* **Claude Haiku**（快速、经济）：Skill 是否提供了足够的指导？
* **Claude Sonnet**（均衡）：Skill 是否清晰且高效？
* **Claude Opus**（强大的推理能力）：Skill 是否避免了过度解释？

对 Opus 完美适用的内容可能需要为 Haiku 增加更多细节。如果你计划在多个模型上使用 Skill，应瞄准在所有模型上都能良好运作的指令。

## Skill 结构

<Note>
  **YAML 前置元数据（Frontmatter）**：SKILL.md 的前置元数据支持两个字段：

  * `name` — 可读的 Skill 名称（最多 64 个字符）
  * `description` — 一行描述，说明 Skill 的功能和使用时机（最多 1024 个字符）

  有关完整的 Skill 结构详情，请参阅 [Skills 概述](/en/docs/agents-and-tools/agent-skills/overview#skill-structure)。
</Note>

### 命名规范

使用一致的命名模式，便于引用和讨论 Skills。我们建议使用**动名词形式**（动词 + -ing）来命名 Skills，因为这能清楚地描述 Skill 提供的活动或能力。

**良好的命名示例（动名词形式）**：

* "Processing PDFs"
* "Analyzing spreadsheets"
* "Managing databases"
* "Testing code"
* "Writing documentation"

**可接受的替代方案**：

* 名词短语："PDF Processing"、"Spreadsheet Analysis"
* 动作导向："Process PDFs"、"Analyze Spreadsheets"

**避免**：

* 模糊的名称："Helper"、"Utils"、"Tools"
* 过于笼统的名称："Documents"、"Data"、"Files"
* Skill 集合中不一致的命名模式

一致的命名可以更方便地：

* 在文档和对话中引用 Skills
* 一眼了解 Skill 的功能
* 组织和搜索多个 Skills
* 维护专业且一致的 Skill 库

### 编写有效的描述

`description` 字段用于 Skill 的发现，应包含 Skill 的功能和使用时机。

<Warning>
  **始终使用第三人称编写**。描述会被注入到系统提示词中，不一致的人称视角可能导致发现问题。

  * **正确：** "Processes Excel files and generates reports"
  * **避免：** "I can help you process Excel files"
  * **避免：** "You can use this to process Excel files"
</Warning>

**要具体并包含关键词**。同时说明 Skill 的功能以及使用的具体触发条件/上下文。

每个 Skill 只有一个 description 字段。描述对 Skill 选择至关重要：Claude 使用它从可能多达 100 多个可用 Skills 中选择合适的 Skill。你的描述必须提供足够的细节，让 Claude 知道何时选择此 Skill，而 SKILL.md 的其余部分则提供实现细节。

有效示例：

**PDF 处理 Skill：**

```yaml  theme={null}
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
```

**Excel 分析 Skill：**

```yaml  theme={null}
description: Analyze Excel spreadsheets, create pivot tables, generate charts. Use when analyzing Excel files, spreadsheets, tabular data, or .xlsx files.
```

**Git Commit 辅助 Skill：**

```yaml  theme={null}
description: Generate descriptive commit messages by analyzing git diffs. Use when the user asks for help writing commit messages or reviewing staged changes.
```

避免模糊的描述，例如：

```yaml  theme={null}
description: Helps with documents
```

```yaml  theme={null}
description: Processes data
```

```yaml  theme={null}
description: Does stuff with files
```

### 渐进式披露模式

SKILL.md 作为概览，在需要时将 Claude 指向详细材料，就像入职指南中的目录。有关渐进式披露工作原理的解释，请参阅概述中的 [Skills 工作原理](/en/docs/agents-and-tools/agent-skills/overview#how-skills-work)。

**实用指导：**

* 将 SKILL.md 正文控制在 500 行以内以获得最佳性能
* 当接近此限制时，将内容拆分到单独的文件中
* 使用以下模式有效地组织指令、代码和资源

#### 可视化概览：从简单到复杂

一个基本的 Skill 仅从包含元数据和指令的 SKILL.md 文件开始：

<img src="https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-simple-file.png?fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=87782ff239b297d9a9e8e1b72ed72db9" alt="Simple SKILL.md file showing YAML frontmatter and markdown body" data-og-width="2048" width="2048" data-og-height="1153" height="1153" data-path="images/agent-skills-simple-file.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-simple-file.png?w=280&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=c61cc33b6f5855809907f7fda94cd80e 280w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-simple-file.png?w=560&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=90d2c0c1c76b36e8d485f49e0810dbfd 560w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-simple-file.png?w=840&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=ad17d231ac7b0bea7e5b4d58fb4aeabb 840w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-simple-file.png?w=1100&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=f5d0a7a3c668435bb0aee9a3a8f8c329 1100w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-simple-file.png?w=1650&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=0e927c1af9de5799cfe557d12249f6e6 1650w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-simple-file.png?w=2500&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=46bbb1a51dd4c8202a470ac8c80a893d 2500w" />

随着 Skill 的增长，你可以捆绑 Claude 仅在需要时才加载的额外内容：

<img src="https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-bundling-content.png?fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=a5e0aa41e3d53985a7e3e43668a33ea3" alt="Bundling additional reference files like reference.md and forms.md." data-og-width="2048" width="2048" data-og-height="1327" height="1327" data-path="images/agent-skills-bundling-content.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-bundling-content.png?w=280&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=f8a0e73783e99b4a643d79eac86b70a2 280w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-bundling-content.png?w=560&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=dc510a2a9d3f14359416b706f067904a 560w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-bundling-content.png?w=840&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=82cd6286c966303f7dd914c28170e385 840w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-bundling-content.png?w=1100&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=56f3be36c77e4fe4b523df209a6824c6 1100w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-bundling-content.png?w=1650&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=d22b5161b2075656417d56f41a74f3dd 1650w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-bundling-content.png?w=2500&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=3dd4bdd6850ffcc96c6c45fcb0acd6eb 2500w" />

完整的 Skill 目录结构可能如下所示：

```
pdf/
├── SKILL.md              # 主要指令（触发时加载）
├── FORMS.md              # 表单填写指南（按需加载）
├── reference.md          # API 参考（按需加载）
├── examples.md           # 使用示例（按需加载）
└── scripts/
    ├── analyze_form.py   # 实用脚本（执行，不加载到上下文）
    ├── fill_form.py      # 表单填写脚本
    └── validate.py       # 验证脚本
```

#### 模式 1：高层指南与参考文件

````markdown  theme={null}
---
name: PDF Processing
description: Extracts text and tables from PDF files, fills forms, and merges documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
---

# PDF Processing

## 快速开始

使用 pdfplumber 提取文本：
```python
import pdfplumber
with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```

## 高级功能

**表单填写**：参见 [FORMS.md](FORMS.md) 获取完整指南
**API 参考**：参见 [REFERENCE.md](REFERENCE.md) 获取所有方法
**示例**：参见 [EXAMPLES.md](EXAMPLES.md) 获取常见模式
````

Claude 仅在需要时才加载 FORMS.md、REFERENCE.md 或 EXAMPLES.md。

#### 模式 2：按领域组织

对于涉及多个领域的 Skills，按领域组织内容以避免加载无关的上下文。当用户询问销售指标时，Claude 只需要读取与销售相关的 schema，而不是财务或市场营销数据。这使 token 使用量保持在较低水平，上下文保持集中。

```
bigquery-skill/
├── SKILL.md（概览和导航）
└── reference/
    ├── finance.md（收入、计费指标）
    ├── sales.md（商机、销售管道）
    ├── product.md（API 使用、功能）
    └── marketing.md（营销活动、归因分析）
```

````markdown SKILL.md theme={null}
# BigQuery 数据分析

## 可用数据集

**财务**：收入、ARR、计费 → 参见 [reference/finance.md](reference/finance.md)
**销售**：商机、管道、客户 → 参见 [reference/sales.md](reference/sales.md)
**产品**：API 使用、功能、采用率 → 参见 [reference/product.md](reference/product.md)
**市场营销**：营销活动、归因、邮件 → 参见 [reference/marketing.md](reference/marketing.md)

## 快速搜索

使用 grep 查找特定指标：

```bash
grep -i "revenue" reference/finance.md
grep -i "pipeline" reference/sales.md
grep -i "api usage" reference/product.md
```
````

#### 模式 3：条件性详情

展示基本内容，链接到高级内容：

```markdown  theme={null}
# DOCX 处理

## 创建文档

使用 docx-js 创建新文档。参见 [DOCX-JS.md](DOCX-JS.md)。

## 编辑文档

对于简单编辑，直接修改 XML。

**对于修订跟踪**：参见 [REDLINING.md](REDLINING.md)
**对于 OOXML 详情**：参见 [OOXML.md](OOXML.md)
```

Claude 仅在用户需要这些功能时才读取 REDLINING.md 或 OOXML.md。

### 避免深层嵌套引用

当文件从其他被引用的文件中再被引用时，Claude 可能只会部分读取。遇到嵌套引用时，Claude 可能会使用 `head -100` 等命令来预览内容而非读取完整文件，导致信息不完整。

**保持从 SKILL.md 出发只有一层深度的引用**。所有参考文件应直接从 SKILL.md 链接，以确保 Claude 在需要时能读取完整文件。

**反面示例：嵌套太深**：

```markdown  theme={null}
# SKILL.md
参见 [advanced.md](advanced.md)...

# advanced.md
参见 [details.md](details.md)...

# details.md
这里才是实际信息...
```

**正面示例：一层深度**：

```markdown  theme={null}
# SKILL.md

**基本用法**：[SKILL.md 中的指令]
**高级功能**：参见 [advanced.md](advanced.md)
**API 参考**：参见 [reference.md](reference.md)
**示例**：参见 [examples.md](examples.md)
```

### 为较长的参考文件添加目录结构

对于超过 100 行的参考文件，在顶部包含目录。这确保 Claude 即使在部分读取预览时也能看到可用信息的完整范围。

**示例**：

```markdown  theme={null}
# API 参考

## 目录
- 身份验证与设置
- 核心方法（创建、读取、更新、删除）
- 高级功能（批量操作、Webhooks）
- 错误处理模式
- 代码示例

## 身份验证与设置
...

## 核心方法
...
```

Claude 随后可以读取完整文件或根据需要跳转到特定章节。

有关此基于文件系统的架构如何实现渐进式披露的详情，请参阅下方高级部分中的[运行时环境](#运行时环境)章节。

## 工作流与反馈循环

### 对复杂任务使用工作流

将复杂操作拆分为清晰的顺序步骤。对于特别复杂的工作流，提供一个 Claude 可以复制到其响应中并逐项勾选的检查清单。

**示例 1：研究综述工作流**（用于不包含代码的 Skills）：

````markdown  theme={null}
## 研究综述工作流

复制此检查清单并跟踪你的进度：

```
研究进度：
- [ ] 步骤 1：阅读所有源文档
- [ ] 步骤 2：识别关键主题
- [ ] 步骤 3：交叉引用论述
- [ ] 步骤 4：创建结构化摘要
- [ ] 步骤 5：验证引用
```

**步骤 1：阅读所有源文档**

审阅 `sources/` 目录中的每份文档。记录主要论点和支持证据。

**步骤 2：识别关键主题**

寻找各文档之间的模式。哪些主题反复出现？各文档在哪些方面一致或存在分歧？

**步骤 3：交叉引用论述**

对于每个主要论述，验证它是否出现在源材料中。记录哪个来源支持每个观点。

**步骤 4：创建结构化摘要**

按主题组织发现。包括：
- 主要论述
- 来自各来源的支持证据
- 相矛盾的观点（如有）

**步骤 5：验证引用**

检查每个论述是否引用了正确的源文档。如果引用不完整，返回步骤 3。
````

此示例展示了工作流如何应用于不需要代码的分析任务。检查清单模式适用于任何复杂的多步骤流程。

**示例 2：PDF 表单填写工作流**（用于包含代码的 Skills）：

````markdown  theme={null}
## PDF 表单填写工作流

复制此检查清单并在完成时逐项勾选：

```
任务进度：
- [ ] 步骤 1：分析表单（运行 analyze_form.py）
- [ ] 步骤 2：创建字段映射（编辑 fields.json）
- [ ] 步骤 3：验证映射（运行 validate_fields.py）
- [ ] 步骤 4：填写表单（运行 fill_form.py）
- [ ] 步骤 5：验证输出（运行 verify_output.py）
```

**步骤 1：分析表单**

运行：`python scripts/analyze_form.py input.pdf`

这会提取表单字段及其位置，保存到 `fields.json`。

**步骤 2：创建字段映射**

编辑 `fields.json` 为每个字段添加值。

**步骤 3：验证映射**

运行：`python scripts/validate_fields.py fields.json`

在继续之前修复所有验证错误。

**步骤 4：填写表单**

运行：`python scripts/fill_form.py input.pdf fields.json output.pdf`

**步骤 5：验证输出**

运行：`python scripts/verify_output.py output.pdf`

如果验证失败，返回步骤 2。
````

清晰的步骤防止 Claude 跳过关键的验证环节。检查清单帮助 Claude 和你在多步骤工作流中跟踪进度。

### 实施反馈循环

**常见模式**：运行验证器 → 修复错误 → 重复

这种模式能极大提升输出质量。

**示例 1：风格指南合规性**（用于不包含代码的 Skills）：

```markdown  theme={null}
## 内容审查流程

1. 按照 STYLE_GUIDE.md 中的指南起草内容
2. 对照检查清单审查：
   - 检查术语一致性
   - 验证示例是否遵循标准格式
   - 确认所有必需章节都已包含
3. 如果发现问题：
   - 记录每个问题及具体的章节引用
   - 修改内容
   - 再次审查检查清单
4. 仅在满足所有要求后才继续
5. 完成并保存文档
```

这展示了使用参考文档而非脚本的验证循环模式。"验证器"是 STYLE\_GUIDE.md，Claude 通过阅读和比较来执行检查。

**示例 2：文档编辑流程**（用于包含代码的 Skills）：

```markdown  theme={null}
## 文档编辑流程

1. 对 `word/document.xml` 进行编辑
2. **立即验证**：`python ooxml/scripts/validate.py unpacked_dir/`
3. 如果验证失败：
   - 仔细审查错误信息
   - 修复 XML 中的问题
   - 再次运行验证
4. **仅在验证通过后才继续**
5. 重新打包：`python ooxml/scripts/pack.py unpacked_dir/ output.docx`
6. 测试输出文档
```

验证循环能及早捕获错误。

## 内容指南

### 避免时效性信息

不要包含会过时的信息：

**反面示例：时效性信息**（会变得不正确）：

```markdown  theme={null}
如果你在 2025 年 8 月之前执行此操作，使用旧 API。
2025 年 8 月之后，使用新 API。
```

**正面示例**（使用"旧模式"章节）：

```markdown  theme={null}
## 当前方法

使用 v2 API 端点：`api.example.com/v2/messages`

## 旧模式

<details>
<summary>Legacy v1 API（2025-08 已弃用）</summary>

v1 API 使用：`api.example.com/v1/messages`

此端点已不再支持。
</details>
```

旧模式章节提供历史上下文，而不会使主要内容杂乱。

### 使用一致的术语

选择一个术语并在整个 Skill 中统一使用：

**正面示例 - 一致**：

* 始终使用 "API endpoint"
* 始终使用 "field"
* 始终使用 "extract"

**反面示例 - 不一致**：

* 混用 "API endpoint"、"URL"、"API route"、"path"
* 混用 "field"、"box"、"element"、"control"
* 混用 "extract"、"pull"、"get"、"retrieve"

一致性帮助 Claude 理解和遵循指令。

## 常见模式

### 模板模式

为输出格式提供模板。根据你的需求匹配严格程度。

**对于严格要求**（如 API 响应或数据格式）：

````markdown  theme={null}
## 报告结构

始终使用此精确的模板结构：

```markdown
# [分析标题]

## 执行摘要
[关键发现的一段话概述]

## 主要发现
- 发现 1 及支持数据
- 发现 2 及支持数据
- 发现 3 及支持数据

## 建议
1. 具体可操作的建议
2. 具体可操作的建议
```
````

**对于灵活指导**（当需要灵活调整时）：

````markdown  theme={null}
## 报告结构

以下是合理的默认格式，但请根据分析情况自行判断：

```markdown
# [分析标题]

## 执行摘要
[概述]

## 主要发现
[根据发现内容调整章节]

## 建议
[根据具体情况进行调整]
```

根据具体分析类型按需调整章节。
````

### 示例模式

对于输出质量依赖于查看示例的 Skills，提供输入/输出对，就像在常规提示词工程中一样：

````markdown  theme={null}
## 提交信息格式

按照以下示例生成提交信息：

**示例 1：**
输入：添加了基于 JWT 令牌的用户认证
输出：
```
feat(auth): implement JWT-based authentication

Add login endpoint and token validation middleware
```

**示例 2：**
输入：修复了报告中日期显示不正确的 bug
输出：
```
fix(reports): correct date formatting in timezone conversion

Use UTC timestamps consistently across report generation
```

**示例 3：**
输入：更新了依赖并重构了错误处理
输出：
```
chore: update dependencies and refactor error handling

- Upgrade lodash to 4.17.21
- Standardize error response format across endpoints
```

遵循此风格：type(scope): 简要描述，然后是详细说明。
````

示例比单纯的描述更能帮助 Claude 理解期望的风格和详细程度。

### 条件工作流模式

引导 Claude 通过决策节点：

```markdown  theme={null}
## 文档修改工作流

1. 确定修改类型：

   **创建新内容？** → 遵循下方"创建工作流"
   **编辑现有内容？** → 遵循下方"编辑工作流"

2. 创建工作流：
   - 使用 docx-js 库
   - 从头构建文档
   - 导出为 .docx 格式

3. 编辑工作流：
   - 解包现有文档
   - 直接修改 XML
   - 每次更改后进行验证
   - 完成后重新打包
```

<Tip>
  如果工作流变得庞大或复杂且包含许多步骤，考虑将它们推送到单独的文件中，并告诉 Claude 根据当前任务读取相应的文件。
</Tip>

## 评估与迭代

### 先建立评估体系

**在编写大量文档之前先创建评估体系。** 这确保你的 Skill 解决的是实际问题，而非记录想象中的问题。

**评估驱动的开发：**

1. **识别差距**：在没有 Skill 的情况下让 Claude 执行代表性任务。记录具体的失败情况或缺失的上下文
2. **创建评估**：构建三个测试这些差距的场景
3. **建立基线**：衡量没有 Skill 时 Claude 的表现
4. **编写最少量的指令**：只创建足以弥补差距并通过评估的内容
5. **迭代**：执行评估，与基线比较，并持续改进

这种方法确保你解决的是实际问题，而不是预设可能永远不会出现的需求。

**评估结构**：

```json  theme={null}
{
  "skills": ["pdf-processing"],
  "query": "Extract all text from this PDF file and save it to output.txt",
  "files": ["test-files/document.pdf"],
  "expected_behavior": [
    "Successfully reads the PDF file using an appropriate PDF processing library or command-line tool",
    "Extracts text content from all pages in the document without missing any pages",
    "Saves the extracted text to a file named output.txt in a clear, readable format"
  ]
}
```

<Note>
  此示例展示了一个带有简单测试标准的数据驱动评估。我们目前不提供运行这些评估的内置方式。用户可以创建自己的评估系统。评估是衡量 Skill 有效性的可靠依据。
</Note>

### 与 Claude 协作迭代开发 Skills

最有效的 Skill 开发流程涉及 Claude 本身。与一个 Claude 实例（"Claude A"）合作创建一个将被其他实例（"Claude B"）使用的 Skill。Claude A 帮助你设计和完善指令，而 Claude B 在实际任务中测试它们。这之所以有效，是因为 Claude 模型既理解如何编写高效的 agent 指令，也了解 agent 需要什么信息。

**创建新的 Skill：**

1. **在没有 Skill 的情况下完成一个任务**：使用普通提示词与 Claude A 一起解决问题。在工作过程中，你会自然地提供上下文、解释偏好和分享流程知识。注意你反复提供了哪些信息。

2. **识别可复用的模式**：完成任务后，识别你提供的哪些上下文对未来类似任务有用。

   **示例**：如果你完成了一个 BigQuery 分析，你可能提供了表名、字段定义、过滤规则（如"始终排除测试账户"）和常见查询模式。

3. **让 Claude A 创建一个 Skill**："创建一个 Skill，捕获我们刚才使用的 BigQuery 分析模式。包括表 schema、命名规范和关于过滤测试账户的规则。"

   <Tip>
     Claude 模型原生理解 Skill 的格式和结构。你不需要特殊的系统提示词或"编写 Skills 的 Skill"来让 Claude 帮助创建 Skills。只需让 Claude 创建一个 Skill，它就会生成带有适当前置元数据和正文内容的结构化 SKILL.md。
   </Tip>

4. **审查简洁性**：检查 Claude A 是否添加了不必要的解释。问："删除关于什么是赢率的解释——Claude 已经知道了。"

5. **改进信息架构**：让 Claude A 更有效地组织内容。例如："将表 schema 组织到单独的参考文件中。我们以后可能会添加更多表。"

6. **在类似任务上测试**：在相关用例上使用带有该 Skill 的 Claude B（加载了 Skill 的全新实例）。观察 Claude B 是否找到了正确的信息、正确应用了规则并成功处理了任务。

7. **基于观察进行迭代**：如果 Claude B 遇到困难或遗漏了什么，带着具体信息返回 Claude A："当 Claude 使用这个 Skill 时，它忘记按 Q4 的日期过滤。我们是否应该添加关于日期过滤模式的章节？"

**迭代改进现有 Skills：**

相同的层次化模式在改进 Skills 时继续适用。你在以下三者之间交替：

* **与 Claude A 合作**（帮助完善 Skill 的专家）
* **使用 Claude B 测试**（使用 Skill 执行实际工作的 agent）
* **观察 Claude B 的行为**，并将洞察反馈给 Claude A

1. **在实际工作流中使用 Skill**：给 Claude B（加载了 Skill）分配实际任务，而非测试场景

2. **观察 Claude B 的行为**：注意它在哪里遇到困难、在哪里成功，或做出了意外的选择

   **观察示例**："当我让 Claude B 生成区域销售报告时，它写了查询但忘记过滤测试账户，尽管 Skill 中提到了这条规则。"

3. **返回 Claude A 进行改进**：分享当前的 SKILL.md 并描述你观察到的情况。问："我注意到 Claude B 在我要求区域报告时忘记过滤测试账户。Skill 中提到了过滤，但可能不够突出？"

4. **审查 Claude A 的建议**：Claude A 可能建议重新组织以使规则更突出，使用更强的措辞如"MUST filter"而非"always filter"，或重构工作流章节。

5. **应用并测试更改**：使用 Claude A 的改进方案更新 Skill，然后在类似请求上再次使用 Claude B 测试

6. **基于使用情况重复**：随着你遇到新场景，继续这个观察-改进-测试的循环。每次迭代都基于实际 agent 行为而非假设来改进 Skill。

**收集团队反馈：**

1. 与团队成员分享 Skills 并观察他们的使用情况
2. 询问：Skill 是否在预期时被激活？指令是否清晰？缺少什么？
3. 纳入反馈以弥补你自己使用模式中的盲点

**为什么这种方法有效**：Claude A 理解 agent 的需求，你提供领域专业知识，Claude B 通过实际使用揭示差距，而迭代改进基于观察到的行为而非假设来提升 Skills。

### 观察 Claude 如何导航 Skills

在迭代改进 Skills 时，关注 Claude 在实际使用中是如何操作它们的。注意以下情况：

* **意外的探索路径**：Claude 是否以你未预料到的顺序读取文件？这可能表明你的结构不够直观
* **遗漏的关联**：Claude 是否未能跟随重要文件的引用？你的链接可能需要更明确或更突出
* **过度依赖某些章节**：如果 Claude 反复读取同一个文件，考虑该内容是否应放在主 SKILL.md 中
* **被忽略的内容**：如果 Claude 从未访问某个捆绑文件，它可能是不必要的，或在主指令中的信号不够明确

基于这些观察而非假设进行迭代。Skill 元数据中的 `name` 和 `description` 尤为关键。Claude 在决定是否针对当前任务触发 Skill 时会使用这些字段。确保它们清楚地描述 Skill 的功能和使用时机。

## 应避免的反模式

### 避免 Windows 风格的路径

始终在文件路径中使用正斜杠，即使在 Windows 上也是如此：

* ✓ **正确**：`scripts/helper.py`、`reference/guide.md`
* ✗ **避免**：`scripts\helper.py`、`reference\guide.md`

Unix 风格的路径在所有平台上都能工作，而 Windows 风格的路径在 Unix 系统上会导致错误。

### 避免提供过多选项

除非必要，不要呈现多种方法：

````markdown  theme={null}
**反面示例：选择过多**（令人困惑）：
"你可以使用 pypdf，或 pdfplumber，或 PyMuPDF，或 pdf2image，或……"

**正面示例：提供默认方案**（带有备选方案）：
"使用 pdfplumber 进行文本提取：
```python
import pdfplumber
```

对于需要 OCR 的扫描 PDF，改用 pdf2image 配合 pytesseract。"
````

## 高级：包含可执行代码的 Skills

以下章节专注于包含可执行脚本的 Skills。如果你的 Skill 仅使用 markdown 指令，请跳至[高效 Skills 检查清单](#高效-skills-检查清单)。

### 解决问题，而非推卸

编写 Skill 脚本时，应处理错误条件而非推给 Claude。

**正面示例：显式处理错误**：

```python  theme={null}
def process_file(path):
    """Process a file, creating it if it doesn't exist."""
    try:
        with open(path) as f:
            return f.read()
    except FileNotFoundError:
        # Create file with default content instead of failing
        print(f"File {path} not found, creating default")
        with open(path, 'w') as f:
            f.write('')
        return ''
    except PermissionError:
        # Provide alternative instead of failing
        print(f"Cannot access {path}, using default")
        return ''
```

**反面示例：推给 Claude**：

```python  theme={null}
def process_file(path):
    # Just fail and let Claude figure it out
    return open(path).read()
```

配置参数也应有理有据且有文档说明，以避免"巫毒常量"（Ousterhout 定律）。如果你不知道正确的值，Claude 又如何确定呢？

**正面示例：自文档化**：

```python  theme={null}
# HTTP requests typically complete within 30 seconds
# Longer timeout accounts for slow connections
REQUEST_TIMEOUT = 30

# Three retries balances reliability vs speed
# Most intermittent failures resolve by the second retry
MAX_RETRIES = 3
```

**反面示例：魔法数字**：

```python  theme={null}
TIMEOUT = 47  # Why 47?
RETRIES = 5   # Why 5?
```

### 提供实用脚本

即使 Claude 能自己编写脚本，预制脚本也有其优势：

**实用脚本的优势**：

* 比生成的代码更可靠
* 节省 token（无需在上下文中包含代码）
* 节省时间（无需生成代码）
* 确保跨使用的一致性

<img src="https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-executable-scripts.png?fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=4bbc45f2c2e0bee9f2f0d5da669bad00" alt="Bundling executable scripts alongside instruction files" data-og-width="2048" width="2048" data-og-height="1154" height="1154" data-path="images/agent-skills-executable-scripts.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-executable-scripts.png?w=280&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=9a04e6535a8467bfeea492e517de389f 280w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-executable-scripts.png?w=560&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=e49333ad90141af17c0d7651cca7216b 560w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-executable-scripts.png?w=840&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=954265a5df52223d6572b6214168c428 840w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-executable-scripts.png?w=1100&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=2ff7a2d8f2a83ee8af132b29f10150fd 1100w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-executable-scripts.png?w=1650&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=48ab96245e04077f4d15e9170e081cfb 1650w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-executable-scripts.png?w=2500&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=0301a6c8b3ee879497cc5b5483177c90 2500w" />

上图展示了可执行脚本如何与指令文件配合工作。指令文件（forms.md）引用脚本，Claude 可以执行它而无需将其内容加载到上下文中。

**重要区分**：在指令中明确说明 Claude 应该：

* **执行脚本**（最常见）："运行 `analyze_form.py` 来提取字段"
* **作为参考阅读**（用于复杂逻辑）："参见 `analyze_form.py` 了解字段提取算法"

对于大多数实用脚本，执行是首选方式，因为它更可靠且更高效。有关脚本执行工作原理的详情，请参阅下方的[运行时环境](#运行时环境)章节。

**示例**：

````markdown  theme={null}
## 实用脚本

**analyze_form.py**：从 PDF 中提取所有表单字段

```bash
python scripts/analyze_form.py input.pdf > fields.json
```

输出格式：
```json
{
  "field_name": {"type": "text", "x": 100, "y": 200},
  "signature": {"type": "sig", "x": 150, "y": 500}
}
```

**validate_boxes.py**：检查重叠的边界框

```bash
python scripts/validate_boxes.py fields.json
# 返回："OK" 或列出冲突
```

**fill_form.py**：将字段值应用到 PDF

```bash
python scripts/fill_form.py input.pdf fields.json output.pdf
```
````

### 使用视觉分析

当输入内容可以渲染为图像时，让 Claude 分析它们：

````markdown  theme={null}
## 表单布局分析

1. 将 PDF 转换为图像：
   ```bash
   python scripts/pdf_to_images.py form.pdf
   ```

2. 分析每个页面图像以识别表单字段
3. Claude 可以直观地看到字段位置和类型
````

<Note>
  在此示例中，你需要编写 `pdf_to_images.py` 脚本。
</Note>

Claude 的视觉能力有助于理解布局和结构。

### 创建可验证的中间输出

当 Claude 执行复杂的开放性任务时，可能会出错。"计划-验证-执行"模式通过让 Claude 先以结构化格式创建计划，然后用脚本验证该计划再执行，从而及早发现错误。

**示例**：假设你要求 Claude 根据电子表格更新 PDF 中的 50 个表单字段。没有验证的话，Claude 可能会引用不存在的字段、创建冲突的值、遗漏必填字段或错误地应用更新。

**解决方案**：使用上面展示的工作流模式（PDF 表单填写），但添加一个中间的 `changes.json` 文件，在应用更改之前进行验证。工作流变为：分析 → **创建计划文件** → **验证计划** → 执行 → 验证。

**为什么这种模式有效：**

* **及早发现错误**：验证在更改应用之前发现问题
* **机器可验证**：脚本提供客观的验证
* **可逆的规划**：Claude 可以在不触及原始文件的情况下迭代计划
* **清晰的调试**：错误信息指向具体问题

**何时使用**：批量操作、破坏性更改、复杂验证规则、高风险操作。

**实现提示**：使验证脚本输出详细的错误信息，如 "Field 'signature\_date' not found. Available fields: customer\_name, order\_total, signature\_date\_signed"，以帮助 Claude 修复问题。

### 打包依赖

Skills 在代码执行环境中运行，受平台特定限制：

* **claude.ai**：可以从 npm 和 PyPI 安装包，也可以从 GitHub 仓库拉取
* **Anthropic API**：没有网络访问权限，无法在运行时安装包

在 SKILL.md 中列出所需的包，并在[代码执行工具文档](/en/docs/agents-and-tools/tool-use/code-execution-tool)中验证其可用性。

### 运行时环境

Skills 在具有文件系统访问、bash 命令和代码执行能力的代码执行环境中运行。有关此架构的概念性解释，请参阅概述中的 [Skills 架构](/en/docs/agents-and-tools/agent-skills/overview#the-skills-architecture)。

**这对编写有何影响：**

**Claude 如何访问 Skills：**

1. **元数据预加载**：启动时，所有 Skills 的 YAML 前置元数据中的名称和描述会被加载到系统提示词中
2. **文件按需读取**：Claude 在需要时使用 bash Read 工具从文件系统访问 SKILL.md 和其他文件
3. **脚本高效执行**：实用脚本可以通过 bash 执行而无需将其完整内容加载到上下文中。只有脚本的输出消耗 token
4. **大文件无上下文开销**：参考文件、数据或文档在实际被读取之前不消耗上下文 token

* **文件路径很重要**：Claude 像导航文件系统一样导航你的 Skill 目录。使用正斜杠（`reference/guide.md`），而非反斜杠
* **描述性地命名文件**：使用表明内容的名称：`form_validation_rules.md`，而非 `doc2.md`
* **组织以便发现**：按领域或功能组织目录结构
  * 正确：`reference/finance.md`、`reference/sales.md`
  * 错误：`docs/file1.md`、`docs/file2.md`
* **捆绑全面的资源**：包括完整的 API 文档、大量示例、大型数据集——在被访问之前没有上下文开销
* **对确定性操作优先使用脚本**：编写 `validate_form.py` 而不是要求 Claude 生成验证代码
* **明确执行意图**：
  * "运行 `analyze_form.py` 来提取字段"（执行）
  * "参见 `analyze_form.py` 了解提取算法"（作为参考阅读）
* **测试文件访问模式**：通过实际请求验证 Claude 能够导航你的目录结构

**示例：**

```
bigquery-skill/
├── SKILL.md（概览，指向参考文件）
└── reference/
    ├── finance.md（收入指标）
    ├── sales.md（销售管道数据）
    └── product.md（使用分析）
```

当用户询问收入相关问题时，Claude 读取 SKILL.md，看到对 `reference/finance.md` 的引用，然后调用 bash 仅读取该文件。sales.md 和 product.md 文件留在文件系统上，在被需要之前消耗零上下文 token。这种基于文件系统的模型正是渐进式披露得以实现的原因。Claude 可以导航并有选择性地加载每个任务所需的确切内容。

有关技术架构的完整详情，请参阅 Skills 概述中的 [Skills 工作原理](/en/docs/agents-and-tools/agent-skills/overview#how-skills-work)。

### MCP 工具引用

如果你的 Skill 使用 MCP（Model Context Protocol）工具，始终使用完全限定的工具名称以避免"tool not found"错误。

**格式**：`ServerName:tool_name`

**示例**：

```markdown  theme={null}
Use the BigQuery:bigquery_schema tool to retrieve table schemas.
Use the GitHub:create_issue tool to create issues.
```

其中：

* `BigQuery` 和 `GitHub` 是 MCP 服务器名称
* `bigquery_schema` 和 `create_issue` 是这些服务器中的工具名称

如果没有服务器前缀，Claude 可能无法找到工具，尤其是当有多个 MCP 服务器可用时。

### 避免假设工具已安装

不要假设包已可用：

````markdown  theme={null}
**反面示例：假设已安装**：
"使用 pdf 库来处理文件。"

**正面示例：明确说明依赖**：
"安装所需的包：`pip install pypdf`

然后使用它：
```python
from pypdf import PdfReader
reader = PdfReader("file.pdf")
```"
````

## 技术说明

### YAML 前置元数据要求

SKILL.md 的前置元数据只包含 `name`（最多 64 个字符）和 `description`（最多 1024 个字符）字段。有关完整结构详情，请参阅 [Skills 概述](/en/docs/agents-and-tools/agent-skills/overview#skill-structure)。

### Token 预算

将 SKILL.md 正文保持在 500 行以内以获得最佳性能。如果内容超过此限制，使用前面描述的渐进式披露模式将其拆分到单独的文件中。有关架构详情，请参阅 [Skills 概述](/en/docs/agents-and-tools/agent-skills/overview#how-skills-work)。

## 高效 Skills 检查清单

在分享 Skill 之前，请验证：

### 核心质量

* [ ] 描述具体且包含关键词
* [ ] 描述包含 Skill 的功能和使用时机
* [ ] SKILL.md 正文不超过 500 行
* [ ] 额外的细节放在单独的文件中（如需要）
* [ ] 没有时效性信息（或放在"旧模式"章节中）
* [ ] 全文术语一致
* [ ] 示例具体，而非抽象
* [ ] 文件引用保持一层深度
* [ ] 适当使用渐进式披露
* [ ] 工作流步骤清晰

### 代码与脚本

* [ ] 脚本自行解决问题，而非推给 Claude
* [ ] 错误处理明确且有帮助
* [ ] 没有"巫毒常量"（所有值都有理由）
* [ ] 所需的包在指令中列出并已验证可用
* [ ] 脚本有清晰的文档
* [ ] 没有 Windows 风格的路径（全部使用正斜杠）
* [ ] 关键操作有验证/确认步骤
* [ ] 对质量要求高的任务包含反馈循环

### 测试

* [ ] 至少创建了三个评估
* [ ] 在 Haiku、Sonnet 和 Opus 上测试过
* [ ] 使用真实使用场景测试过
* [ ] 纳入了团队反馈（如适用）

## 后续步骤

<CardGroup cols={2}>
  <Card title="开始使用 Agent Skills" icon="rocket" href="/en/docs/agents-and-tools/agent-skills/quickstart">
    创建你的第一个 Skill
  </Card>

  <Card title="在 Claude Code 中使用 Skills" icon="terminal" href="/en/docs/claude-code/skills">
    在 Claude Code 中创建和管理 Skills
  </Card>

  <Card title="通过 API 使用 Skills" icon="code" href="/en/api/skills-guide">
    通过编程方式上传和使用 Skills
  </Card>
</CardGroup>
