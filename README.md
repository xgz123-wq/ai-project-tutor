# 🎓 开源 AI 项目吃透助手（自学 + 求职版）

> 帮一个**没有带教导师**的人，几天内吃透一个开源 Python/AI 应用项目（FastAPI / LangChain / RAG / Agent / Celery / 向量数据库），并把理解变成**面试弹药、SDD 规格文档、二次开发能力**。

这份 skill 改造自一个腾讯系 Go 后端实习带教 skill：把 Go/tRPC 全部换成了 Python/AI 技术栈，并新增了求职者最需要、原版完全没有的三块能力——**面试弹药、倒推 SDD、二次开发选题**。

---

## 📁 包含什么

```
ai-project-tutor/
├── README.md            # 你正在看的这个
├── SKILL.md             # 主文件（元数据 + 工作流 + 输出模板）
└── references/          # 按需加载的资源（用到才读，省上下文）
    ├── explainPatterns.md       # 术语翻译库（含 Python/FastAPI/AI 专属表）
    ├── debugPlaybook.md         # 排错 SOP（含 Docker/Ubuntu/Python/AI 速查）
    ├── architectureChecklist.md # 架构讲解 13 项检查清单
    ├── businessFlowTemplate.md  # 业务流程理解 + 业务-代码映射
    ├── learningPathTemplate.md  # 5 天求职导向学习计划
    ├── interviewAmmo.md         # 🆕 把理解转成面试话术 + 模拟面试
    ├── reverseSDD.md            # 🆕 从代码倒推 PRD / Spec
    └── secondaryDev.md          # 🆕 二次开发选题 + git 工作流
```

---

## 🚀 使用步骤

### 方式一：在 Claude Code 里用（推荐）

1. **解压**这个压缩包，得到 `ai-project-tutor/` 文件夹。
2. 把整个文件夹**放进你项目仓库的 skill 目录**：
   ```
   你的项目/
   └── .claude/
       └── skills/
           └── ai-project-tutor/   ← 放这里（SKILL.md 在这一层）
   ```
   如果没有 `.claude/skills/` 目录，自己新建即可。
3. 在项目根目录**打开 Claude Code**（`claude` 命令）。
4. **直接用自然语言提需求**，skill 会自动触发，例如：
   - "我想吃透这个项目，从 0 给我讲起，先画个全景图"
   - "这个项目的岗位匹配 Agent 是怎么实现的？顺便整理成面试能讲的话术"
   - "帮我倒推这个项目的 PRD 和 Spec"
   - "给我几个能写进简历的二次开发点"
5. **模型选择建议**（Claude Code 里用 `/model` 切换）：
   - 梳理全景图、啃 Agent/RAG、生成 PRD/Spec、终面压力面 → 用 **Opus**（质量优先）
   - 逐文件读、写测试代码、常规改动、平时多刷模拟面试 → 用 **Sonnet**（快且省）

### 方式二：在 Claude 网页/App 里用

1. 解压后，把 `SKILL.md` 的内容**复制**进对话（或作为附件上传）。
2. 需要某个资源时（比如要倒推 SDD），再把对应的 `references/xxx.md` 内容贴进去。
3. 然后正常提需求即可。
   > 网页版没有自动按需加载，所以用到哪个 reference 就手动贴哪个。

---

## 🗺️ 推荐使用节奏（配合 5 天计划）

| 阶段 | 你说什么 | skill 会用到的资源 |
|------|---------|------------------|
| Day 0 跑通 | "我在 Ubuntu 上跑这个项目，报了这个错…" | `debugPlaybook.md` |
| Day 1 全景 | "从 0 给我讲，画全景图和核心主线" | `architectureChecklist.md` |
| Day 2 AI 专项 | "深入讲 RAG / Agent，整理面试话术" | `interviewAmmo.md` |
| Day 3 倒推 | "帮我反推 PRD 和 Spec" | `reverseSDD.md` |
| Day 4 二开 | "给我二次开发选题和 git 流程" | `secondaryDev.md` |
| Day 5 面试 | "用我的项目文档模拟面试，连环追问我" | `interviewAmmo.md` |

---

## 💡 设计原则（它为什么这么干）

- **先跑通 → 主线 → 输出**：不追求读懂每一行，只求能讲清几条主线 + 做出几个改动
- **诚实路径**：始终引导"基于开源深入学习并二次开发"的讲法，而不是"完全自己开发"（fork 关系一查就穿帮，且二开能力本身就是公司看重的）
- **求职导向**：每讲一块都想着"这能怎么变成面试能讲的一句话"
- **不给压力**：永远不让你觉得"问了个蠢问题"

---

## ⚠️ 小提示

- 用的时候**项目代码要在当前工作区**，否则 skill 没法扫描代码
- skill 会引导你产出 `specs/`（反推的 PRD/Spec）和 `docs/`（学习笔记）两个目录
- 敏感信息（API key、密码）注意别提交进 git
- 这套方法不止适用于这一个项目——以后接手任何陌生的 Python/AI 项目都能用

---

**技能名**：`ai-project-tutor` ｜ **适用**：Python/AI 后端项目自学、面试准备、二次开发
