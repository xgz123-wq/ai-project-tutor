# 样例：复现级 Spec 的颗粒度范式

> **来自**：一个 FastAPI + LangChain + pgvector 的 AI 面试项目的 `specs/Spec-技术规格.md` 节选（核心模块设计 §8.3 + 复现验收清单 §12 + 瑕疵记录 §13）。
> **对应产物**：`specs/Spec-技术规格.md`（生成规范见 references/endToEndWorkflow.md §3.5）。
> **仿写时替换**：函数名、SQL、参数值全部换成目标项目的真实实现；**保留的是颗粒度标准**——伪代码精确到分支条件与默认值、输出有数据契约、验收清单可实际执行、瑕疵诚实记录。

---

## 节选一：核心模块详细设计（伪代码级）

### 8.3 RAG 出题（interview_service._generate_questions_with_rag，系统核心）

```python
# ① query 构造
query = f"{target_position} {简历skills前若干项}"           # _build_retrieval_query

# ② 召回（question_bank_service.retrieve_questions）
recall_k = total_questions * RECALL_FACTOR(2)
distance_threshold = 1.0 - min_score(0.7)
SELECT id, ..., embedding <=> :qvec AS distance FROM question_bank
WHERE is_active AND embedding IS NOT NULL
  AND embedding <=> :qvec <= :threshold
  [AND position_tag LIKE %:tag%] [AND difficulty = :d]
ORDER BY distance LIMIT :recall_k                          # <=> 即 cosine_distance
# 返回项：{...题字段..., similarity=1-distance, source="from_bank"}

# ③ 检索层兜底：len(candidates) < total 时去掉 position_tag 再召回一次，按 id 去重合并

# ④ 生成层三分支（cnt = len(candidates)）
cnt >= total  → AIService.select_and_adapt_questions(candidates,...)   # AI 从候选挑 total 道可微调措辞
0 < cnt < total → AIService.generate_with_seeds(candidates,...)        # 候选全保留 + AI 仿风格补差额
cnt == 0      → AIService.generate_questions(...)                      # 纯生成；补 source="ai_fallback", bank_id=None

# ⑤ 收尾：被选题库题 use_count+1；questions_data 快照入 interviews；写第一条 interviewer 消息
```

三个 AI 方法的输出契约统一为题目数组：`[{question, bank_id|null, reference_answer|null, key_points|null, source}]`。

## 节选二：复现验收清单（按序执行，全过 = 复现成功）

| # | 验证 | 预期 |
|---|------|------|
| 1 | `alembic current` | head=最新且 13 张表齐 |
| 2 | 管理端建岗位 python_backend + 导入 10 道对应题 | 列表可见，has_embedding 陆续变 true |
| 3 | 检索测试 query="python 后端 GIL 并发" | 返回按 similarity 降序的相关题，分数≥0.7 |
| 4 | 用户端传一份 Python 简历 | 15s 内返回结构化解析+诊断 |
| 5 | 岗位匹配 | Top1=python_backend，含命中/缺失技能与理由；过程有中间步骤可视化 |
| 6 | 开始面试（题库充分场景） | 全部题 source=from_bank |
| 7 | 停用全部题再开一场 | 仍正常出题，source=ai_fallback（三级兜底生效） |
| 8 | 提交答案 | 首 chunk ≤2s，评语流式，done 带 score，界面无 JSON 闪现 |
| 9 | 答完最后一题 | done 标记结束；报告页有总分/优劣势/逐题得分 |
| 10 | 全程数据检查 | interview_messages 逐题有 score；question_bank.use_count 增长 |

## 节选三：反推过程中发现的实现瑕疵（诚实记录，亦是二开素材）

| 瑕疵 | 位置 | 影响 | 建议 |
|------|------|------|------|
| `delete_interview` 路由重复定义两遍 | api/client/v1/interview.py 尾部 | FastAPI 后者覆盖前者，功能无碍但属死代码 | 删除重复段 |

---

## 这个样例展示的颗粒度标准（仿写自查）

1. 伪代码不是流程图叙述——**分支条件、参数默认值（RECALL_FACTOR=2、min_score=0.7）、SQL 关键子句**都在场，且全部来自源码而非记忆。
2. 每个模块给出**输出数据契约**（字段级），下游怎么消费一目了然。
3. 验收清单每条都是**可执行的动作 + 可判定的预期**，不是"功能正常"这种空话；含反向用例（第 7 条停用题库验证兜底）。
4. 发现原作者的死代码/不一致**如实记录**，不回避——这是"真反推"区别于"抄 README"的证据，也是现成的面试素材和二开选题。
