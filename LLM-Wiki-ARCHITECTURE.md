# LLM Wiki 架构说明

> **本文档可直接发给 AI，让它为你重建完整的 LLM Wiki 系统。**

---

## 一、核心理念

**LLM Wiki** 是基于 Andrej Karpathy 的理念设计的个人知识库系统。

与传统 RAG 每次从原始文档重新检索不同，LLM Wiki 让 AI **增量构建和维护一个持久的 wiki**——知识会累积、交叉引用会自动维护、矛盾会被标记。

**人类的工作**：策展来源、提出好问题、指导分析方向
**AI 的工作**：阅读、摘要、交叉引用、维护一致性——所有繁琐的"记账"工作

---

## 二、目录结构

```
LLM-Wiki/
├── raw/                          # 原始材料（人类输入，AI 只读不改）
│   ├── assets/                   # 图片、PDF 等附件
│   └── *.md                      # 文章、笔记（Markdown 格式）
├── wiki/                         # 知识库（AI 维护的结构化输出）
│   ├── concepts/                 # 概念页面
│   ├── entities/                 # 实体页面（人物、组织、项目）
│   ├── sources/                  # 来源摘要
│   ├── analyses/                 # 比较与分析
│   ├── templates/                # 页面模板
│   ├── index.md                  # 内容索引（查询入口）
│   └── log.md                    # 操作日志
├── SCHEMA.md                     # AI 指令文件（核心）
├── README.md                     # 用户使用指南
└── Dashboard.md                  # Dataview 仪表盘（可选）
```

---

## 三、核心文件内容

### 3.1 SCHEMA.md（AI 指令文件）

```markdown
# LLM Wiki Schema & 维护指南

**这是给 LLM (AI 助手) 的系统级指令。**
当用户要求你"摄入(ingest)"文档、"查询(query)"信息或"维护(lint)"Wiki 时，请严格遵循此文档的规则。

## 目录结构

LLM-Wiki/
├── raw/                  # 原始来源（只读，LLM 绝不修改）
│   ├── assets/           # 图片、PDF 等附件
│   └── *.md              # 原始文章/笔记
├── wiki/                 # 知识库（LLM 完全拥有并维护）
│   ├── concepts/         # 概念页面
│   ├── entities/         # 实体页面（人物、组织、项目等）
│   ├── sources/          # 来源摘要页面
│   ├── analyses/         # 比较、分析、综合页面
│   ├── templates/        # 页面模板
│   ├── index.md          # 内容索引
│   └── log.md            # 操作日志
├── SCHEMA.md             # 本文件（LLM 指令）
└── README.md             # 用户使用指南

## 页面命名规范

| 分类 | 命名格式 | 示例 |
|------|----------|------|
| 来源摘要 | `sources/Source_简短标题.md` | `sources/Source_LLM-Wiki理念.md` |
| 概念 | `concepts/Concept_概念名.md` | `concepts/Concept_向量数据库.md` |
| 实体 | `entities/Entity_名称.md` | `entities/Entity_Karpathy.md` |
| 分析 | `analyses/Analysis_主题.md` | `analyses/Analysis_RAG与LLM-Wiki对比.md` |

**规则：**
- 文件名使用中文或英文均可，但同一概念保持一致
- 避免特殊字符，用 `-` 连接多个词
- 页面标题（H1）与文件名保持一致

## Frontmatter 模板

每个 Wiki 页面 **必须** 包含以下 YAML frontmatter：

```yaml
---
title: "页面标题"
type: source | concept | entity | analysis
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources:
  - "[[Source_xxx]]"
tags:
  - 标签1
  - 标签2
status: active | outdated | contradicted
---
```

**字段说明：**
- `type`: 页面类型，决定放入哪个子目录
- `sources`: 该页面信息的来源引用（Obsidian 双链格式）
- `status`:
  - `active` — 当前有效
  - `outdated` — 已被新来源取代，保留但标记
  - `contradicted` — 存在矛盾信息，待解决

## 标签体系

使用 `tags` 字段进行分类，遵循以下层级结构：

```
# 一级标签：领域
AI, 编程, 运维, 安全, 架构, 个人

# 二级标签：子领域（用 / 分隔）
AI/LLM, AI/向量数据库, AI/RAG
编程/Python, 编程/Go, 编程/前端
运维/Docker, 运维/Nginx, 运维/Caddy

# 特殊标签
重要, 待验证, 已过期
```

## 核心操作规范

### 1. 摄入 (Ingest)

当用户提供新文档或指向 `raw/` 中的新文件时：

1. **阅读**：仔细阅读源文档全文。
2. **创建摘要**：在 `wiki/sources/` 下创建来源摘要页面，包含：
   - frontmatter（含完整元数据）
   - 一段话总结（不超过 3 句）
   - 关键要点（bullet list）
   - 值得注意的引用（blockquote）
   - 相关概念链接
3. **更新概念/实体**：
   - 查阅 `index.md`，检查是否有已存在的概念/实体页面
   - 已存在 → 更新页面内容，追加新来源引用，更新 `updated` 日期
   - 不存在但重要 → 创建新页面
4. **处理矛盾**：如果新信息与现有 Wiki 内容冲突：
   ```markdown
   > [!warning] 矛盾
   > **来源 A** ([[Source_xxx]]) 认为：...
   > **来源 B** ([[Source_yyy]]) 认为：...
   > **待解决**：需要进一步验证。
   ```
   **绝不静默覆盖！** 同时将相关页面 `status` 标记为 `contradicted`。
5. **更新索引**：将新页面添加到 `wiki/index.md` 对应分类下，格式：
   ```markdown
   - [[页面名称]] — 一句话摘要 (YYYY-MM-DD, N个来源)
   ```
6. **记录日志**：在 `wiki/log.md` 末尾追加，格式：
   ```markdown
   ## [YYYY-MM-DD] ingest | 源文档名称
   - 创建：[[Source_xxx]], [[Concept_yyy]]
   - 更新：[[Entity_zzz]]
   - 标签：#AI/LLM
   ```

### 2. 查询 (Query)

当用户提出问题时：

1. **搜索**：读取 `wiki/index.md` → 定位相关页面 → 读取页面内容。
2. **综合回答**：基于 Wiki 内容回答，**必须附带 `[[双链]]` 引用**。
3. **知识沉淀**：如果回答包含有价值的比较或分析：
   - 在 `wiki/analyses/` 下创建新页面
   - 更新 `index.md`
4. **记录日志**：
   ```markdown
   ## [YYYY-MM-DD] query | 用户的核心问题
   - 参考页面：[[Concept_xxx]], [[Source_yyy]]
   - 是否沉淀：是/否
   ```

### 3. 维护 (Lint)

当用户要求健康检查时，逐项执行：

| 检查项 | 判定标准 | 处理方式 |
|--------|----------|----------|
| **矛盾排查** | 存在 `[!warning] 矛盾` 标记的页面 | 尝试通过网络搜索或逻辑推理解决，无法解决则保留标记并报告 |
| **孤立页面** | `wiki/` 下的页面既不在 `index.md` 中，也未被任何其他页面 `[[链接]]` | 添加到 index 或建立关联；如确无价值则建议删除 |
| **概念补全** | 某个术语在 ≥3 个不同页面中被提及但无独立页面 | 创建概念页面 |
| **过期内容** | `status: outdated` 或 `updated` 超过 90 天 | 标记提醒用户，建议更新或确认 |
| **缺失引用** | 页面的 `sources` 字段为空 | 补充来源或标记 `#待验证` |
| **索引完整性** | `wiki/` 子目录下的页面未出现在 `index.md` | 补充到 index |

日志格式：
```markdown
## [YYYY-MM-DD] lint | 维护执行摘要
- 矛盾：N 处（已解决 M 处）
- 孤立页面：N 个
- 新建概念页：N 个
- 过期警告：N 个
```

## 格式约定

- 使用标准的 **Obsidian Markdown** 语法
- 内部链接使用双括号：`[[页面名称]]`
- 图片引用：`![[raw/assets/图片名.png]]`
- 矛盾标记：`> [!warning] 矛盾`
- 重要提示：`> [!tip]` 或 `> [!note]`
- 页面过长（>500 行）时，拆分为子页面并建立链接
- 所有日期格式统一为 `YYYY-MM-DD`
```

---

### 3.2 wiki/index.md（内容索引）

```markdown
# LLM Wiki 索引 (Index)

> 这是 LLM Wiki 的主目录。LLM 在回答问题或摄入新文档时，应**首先查阅此文件**以寻找相关页面。
> 索引按类型分类，每个条目格式：`[[页面名]] — 摘要 (日期, 来源数)`

---

## 📚 来源摘要 (Sources)

_摄入的原始文档摘要_

<!-- 示例：- [[Source_xxx]] — 来源描述 (YYYY-MM-DD, N个来源) -->

## 💡 概念 (Concepts)

_核心概念、技术术语、方法论_

<!-- 示例：- [[Concept_xxx]] — 概念描述 (YYYY-MM-DD, N个来源) -->

## 👤 实体 (Entities)

_人物、组织、项目、产品_

<!-- 示例：- [[Entity_xxx]] — 实体描述 (YYYY-MM-DD, N个来源) -->

## 🔍 比较与分析 (Analyses)

_跨来源的对比、综合分析、洞察_

<!-- 暂无条目 — 随来源增多会自动生成 -->

## 📅 时间线与事件 (Timeline)

_按时间排列的重要事件、里程碑_

<!-- 暂无条目 — 随来源增多会自动生成 -->
```

---

### 3.3 wiki/log.md（操作日志）

```markdown
# LLM Wiki 操作日志 (Log)

此文件记录了 LLM Wiki 的所有重要操作（如摄入文档、回答查询、执行维护 Lint）。
请使用追加 (Append) 方式更新此文件，格式如下：
`## [YYYY-MM-DD] action | Title/Description`

---

## [YYYY-MM-DD] init | 初始化 LLM Wiki 基础结构
```

---

### 3.4 页面模板

**来源摘要模板** (`wiki/templates/Template_Source.md`)：

```markdown
---
title: "{{title}}"
type: source
created: {{date}}
updated: {{date}}
sources:
  - "原始文档路径或URL"
tags:
  -
status: active
---

# {{title}}

## 一句话总结

<!-- 用不超过 3 句话概括这篇文章的核心内容 -->

## 关键要点

-
-
-

## 值得注意的引用

>

## 相关概念

- [[Concept_]]
- [[Entity_]]

## 元信息

- **原始来源**：
- **作者**：
- **发布日期**：
```

**概念模板** (`wiki/templates/Template_Concept.md`)：

```markdown
---
title: "{{title}}"
type: concept
created: {{date}}
updated: {{date}}
sources:
  - "[[Source_]]"
tags:
  -
status: active
---

# {{title}}

## 定义

<!-- 用简洁的语言定义这个概念 -->

## 核心特征

-
-
-

## 应用场景

-

## 与其他概念的关系

- **相关概念**：[[Concept_]]
- **对比概念**：[[Concept_]]
- **上位概念**：[[Concept_]]

## 来源与参考

- [[Source_]]
```

**实体模板** (`wiki/templates/Template_Entity.md`)：

```markdown
---
title: "{{title}}"
type: entity
created: {{date}}
updated: {{date}}
sources:
  - "[[Source_]]"
tags:
  -
status: active
---

# {{title}}

## 基本信息

- **类型**：人物 / 组织 / 项目 / 产品
- **领域**：
- **相关链接**：

## 简介

<!-- 2-3 句话介绍 -->

## 关键事实

-
-

## 相关实体

- [[Entity_]]

## 涉及的概念

- [[Concept_]]

## 来源与参考

- [[Source_]]
```

---

### 3.5 README.md（用户指南）

```markdown
# LLM Wiki 使用指南

欢迎使用基于 Andrej Karpathy 理念构建的 LLM Wiki。

## 核心理念

> 传统 RAG 每次都从头检索，没有累积性。LLM Wiki 让 AI 增量构建一个**持久的、交叉链接的知识库**——每加一篇文章，每问一个问题，它都会变得更丰富。

**你的工作**：策展来源、提出好问题、指导分析方向
**AI 的工作**：阅读、摘要、交叉引用、维护一致性——所有繁琐的"记账"工作

## 目录结构

LLM-Wiki/
├── raw/                  # 📁 原始材料（你的输入，AI 只读不改）
│   ├── assets/           #    图片、PDF 等附件
│   └── *.md              #    文章、笔记（Markdown 格式最佳）
├── wiki/                 # 📁 知识库（AI 维护的结构化输出）
│   ├── concepts/         #    概念页面
│   ├── entities/         #    实体页面（人物、组织、项目）
│   ├── sources/          #    来源摘要
│   ├── analyses/         #    比较与分析
│   ├── templates/        #    页面模板
│   ├── index.md          #    📋 内容索引（查询入口）
│   └── log.md            #    📝 操作日志
├── SCHEMA.md             # 🤖 AI 指令文件
└── README.md             # 📖 本文件

## 快速开始

### 1. 摄入新知识 (Ingest)

> "请将 `raw/` 目录下的 `新文章.md` 摄入到 LLM Wiki。"

或者直接给我一个 URL：

> "请将这篇文章摄入到 LLM Wiki：https://example.com/article"

### 2. 查询知识 (Query)

> "根据 LLM Wiki，[你的问题]？"

### 3. 维护知识库 (Lint)

> "请对 LLM Wiki 执行健康检查。"

## 推荐 Obsidian 插件

| 插件 | 用途 | 推荐度 |
|------|------|--------|
| **Dataview** | 对 frontmatter 运行查询 | ⭐⭐⭐ 必装 |
| **Obsidian Web Clipper** | 浏览器扩展，一键剪藏网页 | ⭐⭐⭐ 必装 |
| **Graph View** | 可视化知识图谱 | ⭐⭐ 推荐 |
```

---

## 四、使用方法

### 给 AI 的初始化指令

将本文档完整发送给 AI，然后说：

> "请根据上述架构说明，在我的 Obsidian vault 中创建完整的 LLM Wiki 目录结构和核心文件。"

AI 将会：
1. 创建 `LLM-Wiki/` 目录及所有子目录
2. 创建 `SCHEMA.md`、`index.md`、`log.md`、`README.md`
3. 创建页面模板文件
4. 创建 `.gitkeep` 占位文件确保空目录被同步

### 日常使用

1. **添加文章**：将 Markdown 文件放入 `raw/` 目录
2. **触发摄入**：告诉 AI "请摄入 raw 目录下的新文章"
3. **查询知识**：问 AI "根据 LLM Wiki，..."

---

## 五、设计原则

1. **持久性**：Wiki 是累积的产物，不是临时缓存
2. **结构化**：统一的命名规范、frontmatter、标签体系
3. **可追溯**：每个知识点都有来源引用
4. **矛盾可见**：冲突信息不静默覆盖，而是标记待解决
5. **AI 友好**：SCHEMA.md 让任何 AI 都能正确维护这个系统

---

**版本**: 1.0
**创建日期**: 2026-04-12
**基于**: Andrej Karpathy 的 LLM Wiki 理念
