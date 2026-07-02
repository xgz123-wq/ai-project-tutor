# 🎓 开源 AI 项目吃透助手（自学 + 求职版）

> 帮一个**没有带教导师**的人，几天内吃透一个开源 Python/AI 应用项目（FastAPI / LangChain / RAG / Agent / Celery / 向量数据库），并把理解变成**面试弹药、SDD 规格文档、二次开发能力**。

这份 skill 改造自一个腾讯系 Go 后端实习带教 skill（把 Go/tRPC 换成 Python/AI 技术栈，新增面试弹药、倒推 SDD、二次开发选题三块能力），并在一个真实项目上**完整实战了一轮 5 天流程**，把实战经验沉淀成了「以终为始工作流」SOP 和 few-shot 样例。

---

## 📁 包含什么

```
ai-project-tutor/
├── README.md            # 你正在看的这个
├── SKILL.md             # 主文件（元数据 + 工作流 + 输出模板 + 事实核实纪律）
├── references/          # 按需加载的资源（用到才读，省上下文）
│   ├── endToEndWorkflow.md      # ⭐ 以终为始工作流（自包含 SOP：产物地图 + 每份资料的
│   │                            #    生成 prompt + 验收标准 + 弱模型适配，可单独复制给任何 AI）
│   ├── explainPatterns.md       # 术语翻译库（含 Python/FastAPI/AI 专属表）
│   ├── debugPlaybook.md         # 排错 SOP（含 Docker/Ubuntu/Python/AI 速查）
│   ├── architectureChecklist.md # 架构讲解 13 项检查清单
│   ├── businessFlowTemplate.md  # 业务流程理解 + 业务-代码映射
│   ├── learningPathTemplate.md  # 5 天求职导向学习计划（含以终为始变体）
│   ├── interviewAmmo.md         # 把理解转成面试话术 + 模拟面试
│   ├── reverseSDD.md            # 从代码倒推 PRD / Spec
│   └── secondaryDev.md          # 二次开发选题 + git 工作流
└── examples/            # 实战产物精选节选（few-shot 样例，弱模型生成前必读）
    ├── interviewScriptSample.md # 口语逐字稿 + 追问链 + 避雷 的写法范式
    ├── specModuleSample.md      # 复现级 Spec 的颗粒度范式
    └── resumeAmmoSample.md      # 面试要点「一句话讲法 + 深挖支撑」双栏范式
```

---

## 🚀 使用步骤

### 方式一：全局安装（推荐，一次安装所有项目可用）

把整个 `ai-project-tutor/` 文件夹放进用户级 skill 目录：

```
C:\Users\<你>\.claude\skills\ai-project-tutor\     （Windows）
~/.claude/skills/ai-project-tutor/                  （macOS/Linux）
```

之后在**任何项目**打开 Claude Code 直接说需求即可触发，不用逐项目拷贝。

### 方式二：装进单个项目

放进项目仓库的 `.claude/skills/ai-project-tutor/`（SKILL.md 在这一层）。适合想把 skill 跟着项目 git 留痕的场景。
⚠️ 如果两处都装了：**改动不会自动联动**，升级后用 `robocopy 项目副本 全局副本 /E /XD .git` 手动同步。

### 方式三：脱离 Claude Code（网页版 / 其他 AI 工具）

`references/endToEndWorkflow.md` 是**自包含**的：把它（或其中某一节的 prompt）直接贴给任何 AI（Sonnet / DeepSeek / ChatGPT），再按其 §6 手动贴上「必读文件清单」里的源文件内容即可。生成 05 / Spec / 面试要点前，先把 `examples/` 对应样例也贴上（few-shot，弱模型质量提升最大）。

### 两种使用模式

| 模式 | 你说什么 | 适合谁 |
|------|---------|--------|
| ⭐ 以终为始（实战验证） | "以终为始，把学习资料全部生成" | 想先建认知再看代码的人；弱模型下质量最稳（逐份生成、每份有必读清单和验收标准） |
| 边问边讲（互动式） | "讲讲这个项目的 RAG 模块" | 已有基础、只想啃某一块的人 |

---

## 🗺️ 推荐使用节奏（配合 5 天计划）

| 阶段 | 你说什么 | skill 会用到的资源 |
|------|---------|------------------|
| Day 0 跑通 | "我在 Ubuntu 上跑这个项目，报了这个错…" | `debugPlaybook.md` |
| Day 1 全景 | "从 0 给我讲，画全景图和核心主线"（或此时说"以终为始"一次性生成全套） | `architectureChecklist.md` / `endToEndWorkflow.md` |
| Day 2 专项 | "深入讲 RAG / Agent，整理面试话术" | `interviewAmmo.md` |
| Day 3 倒推 | "帮我反推 PRD 和 Spec" | `reverseSDD.md` + `examples/specModuleSample.md` |
| Day 4 二开 | "给我二次开发选题和 git 流程" | `secondaryDev.md` |
| Day 5 面试 | "用我的项目文档模拟面试，连环追问我" | `interviewAmmo.md` + `examples/interviewScriptSample.md` |

---

## 💡 设计原则（它为什么这么干）

- **先跑通 → 主线 → 输出**：不追求读懂每一行，只求能讲清几条主线 + 做出几个改动
- **以终为始**：学习资料全部预先生成，学习 = 读资料 → 自测 → 对照源码 → 卡住再问
- **模型无关（三根柱子，都编码在文档里而不是靠模型自觉）**：
  1. checklist 代替自由发挥——弱模型照单执行比强模型即兴更稳
  2. few-shot 样例代替长指令——`examples/` 照着仿写
  3. 事实核实护栏——任何数字写入前必须先读源文件，防自信编造
- **维护策略**：用当时能用到的**最强模型**改编/升级本 skill（一次性投入），日常执行交给任何可用模型
- **诚实路径**：始终引导"基于开源深入学习并二次开发"的讲法，而不是"完全自己开发"（fork 关系一查就穿帮，且二开能力本身就是公司看重的）
- **求职导向**：每讲一块都想着"这能怎么变成面试能讲的一句话"
- **不给压力**：永远不让你觉得"问了个蠢问题"

---

## ⚠️ 小提示

- 用的时候**项目代码要在当前工作区**，否则 skill 没法扫描代码
- skill 会引导你产出 `docs/`（学习资料）、`specs/`（反推的 PRD/Spec）和根目录 `面试要点.md`，完整产物地图见 `endToEndWorkflow.md` §2
- 敏感信息（API key、密码）注意别提交进 git
- 每学完一个新项目，把新踩的坑、更好的 prompt 写法**回写进本 skill**（§7 第 10 步）——工作流靠迭代变值钱

---

**技能名**：`ai-project-tutor` ｜ **适用**：Python/AI 后端项目自学、面试准备、二次开发
