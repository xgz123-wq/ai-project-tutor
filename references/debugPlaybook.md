## 新手排错 SOP

当用户遇到报错时，按此流程引导排查，不要直接甩答案。

### 五步排错法
1. **看现象**：完整报错信息是什么？什么操作触发的？能复现吗？
2. **定位置**：报错指向哪个文件、哪一行？是启动时、请求时还是 AI 调用时？是本地还是容器内？
3. **查上下文**：这个文件/函数做什么？最近改过什么？有相关 git diff 吗？
4. **找根因**：语法错？类型错？依赖缺失？环境/配置问题？还是 AI 返回格式问题？
5. **验证并复盘**：加 `print`/日志确认变量值 → 注释可疑代码看是否消失 → 查文档/搜报错 → 修复后跑一遍 → 总结"为什么错 + 怎么避免"

---

## 部署环境报错速查（Docker / Ubuntu，跑通项目阶段最常撞）

| 报错关键词 | 可能原因 | 排查方向 |
|-----------|---------|---------|
| `docker compose: command not found` | Ubuntu 20.04 默认是 compose v1（带横杠 `docker-compose`） | 按 Docker 官网装新版 Engine + `docker-compose-plugin`，别用 `apt install docker.io` |
| `permission denied while trying to connect to Docker daemon` | 当前用户不在 docker 组 | `sudo usermod -aG docker $USER` 后重新登录，或临时加 `sudo` |
| `port is already allocated` | 端口被占（如 8006/5434/6382） | `lsof -i:端口` 找进程，或改 compose 里的端口映射 |
| `Cannot connect to the Docker daemon` | Docker 服务没启动 | `sudo systemctl start docker` |
| 容器起来但 `app` 反复重启 | 多半是 `.env` 没配好或连不上 DB | `docker logs 容器名` 看真实报错 |
| `relation "xxx" does not exist` | 表还没建（迁移没跑） | 进 app 容器执行 `alembic upgrade head` |
| `extension "vector" is not available` | 用了普通 postgres 镜像 | 确认镜像是 `pgvector/pgvector:pg16` |
| 拉镜像/装包巨慢或超时 | 网络问题 | 配国内镜像源（Docker registry mirror、pip 国内源） |
| `.env` 改了不生效 | 容器用的是旧环境 | `docker compose down` 再 `up`，别只 restart |

---

## Python / FastAPI 运行期报错速查

| 报错关键词 | 可能原因 | 排查方向 |
|-----------|---------|---------|
| `ModuleNotFoundError: No module named xxx` | 依赖没装 / 装在别的环境 | `pip install -r requirements.txt`，确认在对的虚拟环境/容器里 |
| `ImportError: cannot import name` | 循环导入 或 改了模块结构 | 检查 import 路径、是否互相 import |
| `AttributeError: 'NoneType' object has no attribute` | 拿到了 None 还往下用 | 往上找哪个查询/调用返回了 None，加判空 |
| `pydantic.ValidationError` | 传入数据不符合 Schema | 看缺哪个字段/类型错，对比 schema 定义 |
| `RuntimeError: ... attached to a different loop` | 异步 session 用错了事件循环 | 检查 async session 是否在请求外被复用（Agent 工具里常踩） |
| `sqlalchemy ... MissingGreenlet` | 在同步上下文里用了异步查询 | 确认 `await` 了、用的是 async session |
| `401 / 403` | 鉴权失败 | 检查 token、登录态、依赖里的鉴权逻辑 |
| `422 Unprocessable Entity` | 请求体没过 Schema 校验 | 看返回的 detail，缺字段还是类型错 |
| `500 Internal Server Error` | 后端代码崩了 | 看后端日志栈，定位真实异常 |

---

## AI / LLM 调用报错速查（这个项目的高频坑）

| 现象 | 可能原因 | 排查方向 |
|------|---------|---------|
| `AuthenticationError` / 401 from API | API key 没配或错了 | 检查 `.env` 里 `DEEPSEEK_API_KEY` / DashScope key |
| `insufficient balance` / 余额不足 | DeepSeek 账户没钱 | 充值（很便宜），或先用免费额度的服务测 |
| `RateLimitError` / 429 | 调太快超频 | 加重试/退避，降低并发 |
| AI 返回解析成 JSON 失败 | 模型没吐纯 JSON（带了 ```json 或解释文字） | 看项目里 `_extract_json` 怎么三级兜底的，这是设计点也是面试点 |
| Embedding 维度报错 | query 向量和库里向量维度不一致 | 确认用的是同一个 embedding 模型/维度 |
| 召回结果总是空 | 题库没向量化 / position_tag 过滤太严 | 确认入库时向量化跑了；看有没有"放宽条件二次召回"兜底 |
| Agent 转圈不停 / 超时 | 工具报错被吞、prompt 没约束好 | 看 `verbose` 输出的中间步骤，确认 `max_iterations` 和错误透传 |

---

## 排错工具箱（Python 项目）

| 工具/方法 | 用途 |
|-----------|------|
| `print(f"{var=}")` / `logging` | 在可疑位置打变量值（最朴素也最有效） |
| `docker logs -f 容器名` | 看容器实时日志（部署期排错主力） |
| `docker exec -it 容器名 bash` | 进容器里手动跑命令、跑迁移 |
| `pytest -v -k 关键词` | 跑单个测试验证某函数行为 |
| `python -m py_compile 文件` | 快速查语法错 |
| `git diff` / `git log --oneline -10` | 看改了什么、最近谁改的 |
| `grep -rn "关键词" app/` | 全局搜某函数/变量在哪用 |
| 浏览器 F12 Network | 前端报错时看请求/响应到底发了什么、返回啥 |

---

## 通用 Web 报错速查（调前后端联调时）

| 报错关键词 | 可能原因 | 排查方向 |
|-----------|---------|---------|
| `CORS error` | 跨域被拦 | 检查后端 CORS 配置、前端请求地址 |
| `404 Not Found` | 接口地址错或服务没起 | 核对 URL、确认后端在跑 |
| `Network Error` / 请求挂起 | 后端没起 / 端口不对 / 代理配置错 | 先确认后端能 curl 通，再查前端代理 |
| `Cannot read property of undefined` | 前端拿到的数据结构和预期不符 | F12 看接口真实返回 |
