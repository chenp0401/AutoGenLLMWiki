# AutoGenLLMWiki

> 让 AI 为你自动构建、维护一个持久的、交叉链接的个人知识库。

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Made with Obsidian](https://img.shields.io/badge/Made%20with-Obsidian-7c3aed.svg)](https://obsidian.md/)
[![AI Powered](https://img.shields.io/badge/AI-Powered-blue.svg)]()

## 📖 项目简介

**AutoGenLLMWiki** 是基于 [Andrej Karpathy](https://karpathy.ai/) 的 **LLM Wiki** 理念设计的个人知识库系统。

与传统 RAG 每次从原始文档重新检索不同，AutoGenLLMWiki 让 AI **增量构建和维护一个持久的 wiki**——知识会累积、交叉引用会自动维护、矛盾会被标记。

### 🎯 核心理念

> 传统 RAG 每次都从头检索，没有累积性。AutoGenLLMWiki 让 AI 增量构建一个**持久的、交叉链接的知识库**——每加一篇文章，每问一个问题，它都会变得更丰富。

- **人类的工作**：策展来源、提出好问题、指导分析方向
- **AI 的工作**：阅读、摘要、交叉引用、维护一致性——所有繁琐的"记账"工作

---

## ✨ 核心特性

- 🧠 **累积式知识库**：不是临时缓存，是永久沉淀的结构化知识
- 🔗 **自动交叉引用**：使用 Obsidian 双链 `[[]]` 自动维护关联
- ⚠️ **矛盾可见**：冲突信息不静默覆盖，而是标记 `> [!warning] 矛盾` 待解决
- 📋 **统一规范**：标准化的命名、frontmatter 元数据、标签体系
- 🔍 **可追溯性**：每个知识点都有来源引用，绝不"幻觉"
- 🤖 **AI 友好**：`SCHEMA.md` 让任何支持 Markdown 的 AI 都能正确维护

---

## 📂 目录结构

```
AutoGenLLMWiki/
├── raw/                          # 📁 原始材料（人类输入，AI 只读不改）
│   ├── assets/                   #    图片、PDF 等附件
│   └── *.md                      #    文章、笔记（Markdown 格式）
├── wiki/                         # 📁 知识库（AI 维护的结构化输出）
│   ├── concepts/                 #    概念页面
│   ├── entities/                 #    实体页面（人物、组织、项目）
│   ├── sources/                  #    来源摘要
│   ├── analyses/                 #    比较与分析
│   ├── templates/                #    页面模板
│   ├── index.md                  #    📋 内容索引（查询入口）
│   └── log.md                    #    📝 操作日志
├── SCHEMA.md                     # 🤖 AI 指令文件（核心）
├── LLM-Wiki-ARCHITECTURE.md      # 📐 完整架构说明
├── LICENSE                       # MIT 许可证
└── README.md                     # 📖 本文件
```

---

## 🚀 快速开始

### 前置要求

- [Obsidian](https://obsidian.md/)（必装，用于可视化知识图谱）
- **obsidian-skill**（必装，需先安装 Obsidian 后再安装此 Skill 插件）

> 💡 **Skill 获取推荐**：
> - 通过 [SkillHub](https://skillhub.cn/skills/obsidian) 获取 Obsidian 相关 Skill
> - 通过 [ClawHub](https://clawhub.ai/steipete/obsidian) 获取 steipete/obsidian Skill

- **AI 辅助知识构建工具**（必装，以下任选其一即可）：
  - [Openclaw](https://github.com/openclaw/openclaw)
    > 💰 **部署推荐**（以下任选其一）：
    > - **CVM 一键部署**：通过 [腾讯云市场 OpenClaw 一键部署镜像](https://app.cloud.tencent.com/detail/SPU_BHGJGAFIIJ7195) 在 CVM 上快速启动，免去手动环境配置。
    > - **轻量云部署**：购买 [腾讯云轻量应用服务器 2核4G](https://curl.qcloud.com/ypKBExWz) 来部署 OpenClaw，性价比高、开箱即用。
  - [WorkBuddy](https://www.codebuddy.cn/work/)

> 🛠️ **配套工具推荐**：如果你在 Linux 环境下使用 Obsidian，并希望和你的 Mac 同步 Obsidian vault，欢迎试试我个人开发的另一款工具 [obsidian2linux](https://github.com/chenp0401/obsidian2linux)，帮助你在 Linux 和 Mac 上更顺畅地使用 Obsidian。结合 iCloud，可免费实现 **iPhone / Mac / Linux 多终端同步**。

### 初始化步骤

> 📌 **重要说明**：[`LLM-Wiki-ARCHITECTURE.md`](./LLM-Wiki-ARCHITECTURE.md) 是**专门给 AI 阅读的搭建手册**，而非给人类阅读的文档。你只需把它交给 AI，AI 会根据其中的架构规范自动完成整个知识库的搭建。

**只需一步：让 AI 自动搭建**

1. 将 [`LLM-Wiki-ARCHITECTURE.md`](./LLM-Wiki-ARCHITECTURE.md) 完整内容发送给 AI
2. 对 AI 说：

   > "请根据上述架构说明，在我的 Obsidian vault 中创建完整的 LLM Wiki 目录结构和核心文件。"

3. AI 将自动创建：
   - 完整的目录结构
   - `SCHEMA.md`、`index.md`、`log.md`
   - 页面模板文件
   - `.gitkeep` 占位文件

---

## 🧩 三大核心操作

### 1️⃣ 摄入 (Ingest) — 向 Wiki 注入新知识

将 Markdown 文件放入 `raw/` 目录，然后告诉 AI：

> "请将 `raw/` 目录下的 `新文章.md` 摄入到 LLM Wiki。"

或直接给一个 URL：

> "请将这篇文章摄入到 LLM Wiki：https://example.com/article"

AI 会自动：
- 阅读源文档 → 创建来源摘要页面
- 提取概念/实体 → 创建或更新对应页面
- 维护交叉引用 → 更新 `index.md`
- 检测矛盾 → 标记待解决
- 记录操作 → 追加到 `log.md`

### 2️⃣ 查询 (Query) — 从 Wiki 获取知识

> "根据 LLM Wiki，[你的问题]？"

AI 会基于累积的 Wiki 内容回答，**必附带 `[[双链]]` 引用**，并在有价值时自动沉淀为新的分析页面。

### 3️⃣ 维护 (Lint) — 健康检查

> "请对 LLM Wiki 执行健康检查。"

AI 会逐项检查：

| 检查项 | 处理方式 |
|--------|----------|
| 矛盾排查 | 尝试解决或保留标记 |
| 孤立页面 | 建立关联或建议删除 |
| 概念补全 | 创建缺失的概念页 |
| 过期内容 | 提醒更新（>90 天） |
| 缺失引用 | 补充来源或标记 `#待验证` |
| 索引完整性 | 补全 `index.md` |

---

## 📐 页面命名规范

| 分类 | 命名格式 | 示例 |
|------|----------|------|
| 来源摘要 | `sources/Source_简短标题.md` | `sources/Source_LLM-Wiki理念.md` |
| 概念 | `concepts/Concept_概念名.md` | `concepts/Concept_向量数据库.md` |
| 实体 | `entities/Entity_名称.md` | `entities/Entity_Karpathy.md` |
| 分析 | `analyses/Analysis_主题.md` | `analyses/Analysis_RAG与LLM-Wiki对比.md` |

---

## 🏷️ 标签体系

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

---

## 🔌 推荐 Obsidian 插件

| 插件 | 用途 | 推荐度 |
|------|------|--------|
| **Dataview** | 对 frontmatter 运行查询 | ⭐⭐⭐ 必装 |
| **Obsidian Web Clipper** | 浏览器扩展，一键剪藏网页 | ⭐⭐⭐ 必装 |
| **Graph View** | 可视化知识图谱 | ⭐⭐ 推荐 |
| **Templater** | 增强的模板支持 | ⭐⭐ 推荐 |

---

## 🎨 设计原则

1. **持久性**：Wiki 是累积的产物，不是临时缓存
2. **结构化**：统一的命名规范、frontmatter、标签体系
3. **可追溯**：每个知识点都有来源引用
4. **矛盾可见**：冲突信息不静默覆盖，而是标记待解决
5. **AI 友好**：`SCHEMA.md` 让任何 AI 都能正确维护这个系统

---

## 📚 详细文档

完整的架构说明、Schema 规范、页面模板请参阅：

👉 [LLM-Wiki-ARCHITECTURE.md](./LLM-Wiki-ARCHITECTURE.md)

---

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

如果你对架构有改进建议、发现了新的最佳实践，或者想分享你的使用心得，都欢迎参与讨论。

---

## 📄 许可证

本项目采用 [MIT 许可证](./LICENSE)。

---

## 🙏 致谢

- 灵感来自 [Andrej Karpathy](https://karpathy.ai/) 提出的 LLM Wiki 理念
- 基于 [Obsidian](https://obsidian.md/) 的 Markdown 生态构建

---

**版本**: 1.0
**创建日期**: 2026-04-18
