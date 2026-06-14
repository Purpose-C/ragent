# Codex 执行报告

## 执行结果

| 项目 | 结果 | 说明 |
|---|---|---|
| Git 状态检查 | 已完成 | 执行前已有 `learning_notes/CODEX_LEARNING_TASK.md` 和 `.DS_Store`，未改动 |
| 扫描目录 | 已完成 | 根文档、四个 Maven 模块、frontend、resources、docs、scripts |
| 生成文档 | 已完成 | `learning-notes/` 下 17 篇 Markdown |
| 业务源码修改 | 无 | 未修改 Java、TS/TSX、SQL、YAML、POM |
| commit/push | 未执行 | 符合任务约束 |
| 真实密钥写入 | 无 | 仅说明环境变量和占位规则 |

## 扫描范围

- `README.md`、根 `pom.xml`、各模块 `pom.xml`。
- `bootstrap` 的启动、Controller、Service、RAG core、ingestion、knowledge、user、配置和 Prompt。
- `framework` 的统一模型、SSE、上下文、幂等、MQ 等结构。
- `infra-ai` 的 Chat、Embedding、Rerank、模型选择与健康状态。
- `mcp-server` 的启动、配置和天气/工单/销售工具。
- `resources/database`、`resources/docker`、`resources/docs`。
- `frontend` 的路由、页面、services、stores 和 SSE Hook。
- `docs` 的架构、检索和 PDF Pipeline 示例。

## 已根据源码确认

1. Java 17、Spring Boot 3.5.7 和四 Maven 模块。
2. 主服务 9090、context path `/api/ragent`，MCP 服务 9099。
3. 当前默认向量存储是 PostgreSQL pgvector，Milvus 可选。
4. RAG 主链由 `StreamChatPipeline` 编排。
5. 入库由 `IngestionEngine` 和六类节点编排。
6. 模型具有候选优先级、三态熔断和失败降级。
7. MCP 客户端通过 Streamable HTTP 发现并调用远端工具。
8. 前端使用 fetch 处理带认证和取消能力的 SSE。

## 需要进一步确认

- 当前机器中 PostgreSQL、Redis、RocketMQ、RustFS、Milvus 的实际安装和版本。
- 初始化用户的可用密码及真实登录结果。
- 各外部模型候选在当前网络和账号下是否可用。
- 完整 Docker 启动组合。仓库没有发现覆盖全部依赖的一键 Compose。
- 入库任务失败后的通用自动重试策略，源码扫描未确认统一实现。
- 所有 Mermaid 图建议在实际 Markdown 渲染器中再做视觉检查。

## 风险与不一致

| 风险 | 说明 |
|---|---|
| 配置风险 | `application.yaml` 含本地默认密码，部署时必须改为环境化配置 |
| 维度风险 | pgvector 为 1536，必须与实际 Embedding 输出一致 |
| 外部服务风险 | AI、MCP、对象存储和 MQ 任一不可用会影响对应链路 |
| 文档不一致 | `docs/ragent-architecture.md` 部分仍写 MySQL，当前代码实际用 PostgreSQL |
| 前端环境风险 | package 未明确 Node 最低版本，需要实际安装验证 |

## 建议优先阅读

1. `learning-notes/00-阅读顺序与使用说明.md`
2. `learning-notes/01-项目总览.md`
3. `learning-notes/02-本地启动指南.md`

第一天建议先读上述三篇，在 IDE 找到 `RagentApplication`、`StreamChatPipeline`、`IngestionEngine`、`ModelRoutingExecutor`，然后手画一张架构图，不急着读每个方法。

## 修改范围确认

本次只新增了 `learning-notes/` 目录下 Markdown 文档。没有修改、删除、移动或重命名原项目文件；没有运行 Git commit 或 push。

## 本章复习问题

1. 哪些结论已经由当前代码确认？
2. 哪些启动条件仍需要本机实测？
3. 项目文档中最重要的不一致是什么？

## 下一步建议

按照 `11-两周学习计划.md` 从第 1 天开始，并把首次实际启动结果补充为自己的学习记录。
