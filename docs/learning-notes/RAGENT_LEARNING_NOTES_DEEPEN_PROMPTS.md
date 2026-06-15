# Ragent 学习文档深度扩写：Codex 分模块提示词合集

> 适用仓库：`https://github.com/Purpose-C/ragent`  
> 目标目录：`docs/learning-notes/`  
> 使用方式：不要一次性把所有提示词丢给 Codex。建议按模块逐条执行，每次只扩写 1～2 篇文档，完成后先 `git diff` 检查，再进入下一模块。

---

## 0. 当前学习文档的问题诊断

你当前已经生成了完整的学习笔记目录，这是好事，说明大的学习框架已经搭起来了。但目前的问题是：**文档更像“学习提纲”，还不是“从零学习教材”**。

主要问题如下：

1. **核心流程写得太概括**
   - 例如 RAG 问答流程只列了调用链，但没有逐方法解释上下文对象如何变化、每个阶段输入输出是什么、失败时如何短路。
   - 文档入库流程只列了 Fetcher、Parser、Chunker、Indexer 等节点，但没有展开每个节点的真实类、配置来源、数据库落点、Debug 路径。

2. **缺少源码级证据**
   - 很多地方只写了类名，没有写完整路径、关键字段、关键方法、调用方、被调用方。
   - 初学者最需要的是“我该点进哪个类，看哪几行逻辑”，而不是只知道“有一个 Service”。

3. **缺少可操作的实验步骤**
   - 启动文档有命令，但缺少“执行前检查”“执行后验证”“报错定位顺序”。
   - RAG、MCP、入库这些核心模块缺少具体测试用例，比如：上传一个最小 txt 文档、提问一次、查哪些表、看哪些日志。

4. **数据库文档深度不够**
   - 现在只是表格列核心表，适合速查，但不适合学习。
   - 应补充每张关键表的建表 SQL、字段含义、索引、关联关系、写入时机、读取时机、对应 Entity/Mapper/Service。

5. **前端文档只到接口映射，还没有到页面状态流**
   - 应进一步解释：用户点击按钮后，组件状态如何变化，service 如何封装请求，store 如何保存状态，SSE token 如何进入页面渲染。

6. **缺少“改造实践导向”**
   - 学习大型项目，不应该只看文档。每个模块都应该有小实验、小改造、断点建议、验收标准。

7. **缺少分层复盘**
   - 现在有面试稿，但不够细。应该分别形成：1 分钟讲法、3 分钟讲法、10 分钟讲法，以及针对入库、检索、模型路由、MCP 的专项讲法。

---

## 1. 总体扩写目标

请 Codex 把 `docs/learning-notes/` 从“提纲型笔记”升级为“源码学习教材”。扩写后每篇核心文档应达到以下标准：

| 文档类型 | 建议深度 | 需要补充的内容 |
|---|---:|---|
| 总览类 | 200～400 行 | 架构图、模块图、术语、学习路线、源码地图 |
| 启动类 | 250～500 行 | 环境检查、启动命令、配置解释、报错排查、验证步骤 |
| 数据库类 | 400～800 行 | 建表 SQL 摘要、字段解释、写入读取时机、ER 图、查询练习 |
| 核心流程类 | 500～1000 行 | 调用链、方法级解析、上下文变化、异常路径、Debug 断点、实验步骤 |
| 前端类 | 300～700 行 | 页面结构、接口映射、状态流、SSE 解析、组件间关系 |
| 面试/复盘类 | 300～600 行 | 多版本讲稿、追问问题、答题要点、结合源码证据 |

> 注意：行数不是硬性指标，但如果核心流程文档仍然只有几十行，基本可以判断没有达到“从零学习”的深度。

---

## 2. Codex 执行前准备提示词

下面这段先给 Codex 执行，用于建立全局规则。

```text
你现在要在 Ragent 项目中扩写学习文档。请严格遵守以下规则：

目标目录：docs/learning-notes/

总目标：
把现有学习文档从“提纲型笔记”扩写为“初学者能跟着源码一步步学习的教材型 Markdown”。

硬性限制：
1. 本轮任务只允许修改 docs/learning-notes/ 下的 Markdown 文件。
2. 不允许修改 Java、TypeScript、YAML、SQL、POM、package.json、Docker、配置文件等业务源码。
3. 不允许执行 git commit、git push。
4. 不允许编造源码中不存在的类、方法、表、字段、配置。
5. 不确定的内容必须写“需要进一步确认”，并说明如何确认。
6. 每个关键结论必须尽量给出：文件路径 + 类名 + 方法名 + 作用。
7. 所有文档必须适合 Java/Spring Boot/RAG 基础较弱的人阅读。
8. 不要只堆概念，要结合真实调用链、真实配置、真实表结构、真实前端页面。
9. 每篇文档末尾必须包含：本章复习问题、Debug 建议、实践任务、下一步建议。
10. Mermaid 图必须语法正确。

请先做一次文档现状审计，不要马上重写。输出：
1. docs/learning-notes 当前有哪些文件；
2. 每篇文档当前大概解决什么问题；
3. 哪些文档明显过短；
4. 哪些文档缺少源码级证据；
5. 你建议按什么顺序扩写；
6. 本轮你准备先改哪几篇。

完成审计后停止，等待我给你下一条模块提示词。
```

---

## 3. 模块一：项目总览、阅读顺序、术语体系

目标文件：

- `docs/learning-notes/00-阅读顺序与使用说明.md`
- `docs/learning-notes/01-项目总览.md`
- `docs/learning-notes/14-术语表.md`

给 Codex 的提示词：

```text
请只扩写以下 3 篇文档：

- docs/learning-notes/00-阅读顺序与使用说明.md
- docs/learning-notes/01-项目总览.md
- docs/learning-notes/14-术语表.md

本轮禁止修改其他文件。

扩写目标：
把这 3 篇文档写成“初学者进入 Ragent 项目的导航地图”。不要只讲概念，要告诉我：项目为什么这样设计、我应该先看哪里、看不懂怎么办、每个术语在源码哪里出现。

具体要求：

一、扩写 00-阅读顺序与使用说明.md
1. 增加“如何使用这套笔记”的详细说明。
2. 增加“源码阅读五步法”：看入口、看依赖、看方法、看数据、看异常。
3. 增加“每天学习闭环”：读文档 -> 找源码 -> 打断点 -> 查数据库 -> 画图 -> 复述。
4. 增加“不同基础的人怎么学”：
   - Java 基础弱；
   - Spring Boot 弱；
   - 前端弱；
   - AI/RAG 概念弱。
5. 增加“不要怎么学”：不要上来改代码、不要只看 README、不要让 Codex 一次性总结全项目。
6. 增加 14 天学习节奏总览图，使用 Mermaid。

二、扩写 01-项目总览.md
1. 用初学者语言解释 Ragent 是什么，不要只用官方话术。
2. 增加“一个用户问题从前端到模型再回到页面”的总流程图。
3. 增加“一个文档从上传到可检索”的总流程图。
4. 增加“模块边界”：frontend、bootstrap、framework、infra-ai、mcp-server、resources、docs。
5. 增加“核心能力清单”：文档入库、检索、重排、Prompt、模型路由、MCP、会话记忆、Trace、权限、后台管理。
6. 增加“项目学习难点清单”：异步、SSE、上下文对象、模型调用、向量检索、Pipeline、前后端联调。
7. 每个能力都要列出推荐入口文件路径。

三、扩写 14-术语表.md
1. 术语表不要只写一句话，要改成更适合学习的格式。
2. 每个术语至少包含：
   - 通俗解释；
   - 在 Ragent 中的具体位置；
   - 相关类/方法/配置/表；
   - 容易误解的点；
   - 一个学习问题。
3. 至少补充这些术语：
   - RAG、Agentic RAG、MCP、Embedding、Rerank、Vector Store、pgvector、Milvus、Chunk、Top-K、Prompt、Token、SSE、JSON-RPC、模型路由、熔断、降级、首包探测、会话记忆、意图识别、问题重写、多路召回、Trace、幂等、Sa-Token、RocketMQ、RustFS、Pipeline、Node、Context、DTO、DO、Mapper。
4. 术语解释必须结合当前项目源码，不要写成百科。

完成后输出：
1. 修改了哪些文件；
2. 每个文件新增了哪些章节；
3. 仍然需要进一步确认的问题；
4. 建议下一轮扩写哪个模块。
```

---

## 4. 模块二：本地启动、配置、中间件、排错

目标文件：

- `docs/learning-notes/02-本地启动指南.md`

给 Codex 的提示词：

```text
请只扩写：docs/learning-notes/02-本地启动指南.md

本轮禁止修改其他文件。

扩写目标：
把启动指南写成“我从零 clone 项目也能一步步跑起来”的操作手册，尤其照顾基础弱的人。

请重点阅读这些文件：
- README.md
- pom.xml
- bootstrap/pom.xml
- mcp-server/pom.xml
- bootstrap/src/main/resources/application.yaml
- frontend/package.json
- frontend/vite.config.ts
- resources/database/schema_pg.sql
- resources/database/init_data_pg.sql
- resources/docker/**
- scripts/**

文档必须包含以下章节：

1. 启动前总览
   - 前端、后端、MCP、中间件分别是什么；
   - 每个服务的默认端口；
   - 哪些必须启动，哪些可以后启动。

2. 环境准备
   - JDK 17 检查命令；
   - Maven Wrapper 和系统 Maven 区别；
   - Node/npm 版本检查；
   - Docker 是否必须；
   - PostgreSQL、Redis、RocketMQ、RustFS、Milvus 的角色。

3. 克隆仓库与分支建议
   - Fork 后 clone；
   - origin/upstream 的区别；
   - 不要直接在 main 上实验。

4. 后端配置详解
   - 逐段解释 application.yaml；
   - server、datasource、redis、rocketmq、rag.vector、rag.memory、rag.mcp、ai.providers、ai.chat、ai.embedding、ai.rerank、rustfs、sa-token；
   - 哪些配置初学阶段必须改；
   - 哪些配置暂时不用动。

5. 数据库初始化
   - PostgreSQL 创建库；
   - pgvector 扩展；
   - schema_pg.sql 与 init_data_pg.sql 执行顺序；
   - 升级脚本什么时候不能执行；
   - 初始化后应该能看到哪些表。

6. 中间件启动方案
   - 如果仓库有 docker compose，说明怎么用；
   - 如果没有完整一键 compose，明确说明哪些要手工准备；
   - 列出每个中间件启动成功的验证方式。

7. 启动 MCP 服务
   - 启动类路径；
   - Maven 命令；
   - 端口；
   - 如何判断工具服务启动成功。

8. 启动后端 bootstrap
   - 启动类路径；
   - Maven 命令；
   - IDEA 启动方式；
   - 常见失败原因。

9. 启动前端 frontend
   - npm install；
   - npm run dev；
   - 代理配置；
   - 登录页和 Network 检查。

10. 最小可用验证路径
   - 登录；
   - 查看用户信息；
   - 查看知识库；
   - 发起一次普通聊天；
   - 查看 SSE EventStream；
   - 查看数据库写入。

11. 常见报错排查表
   至少包含：
   - 数据库连不上；
   - Redis 连不上；
   - RocketMQ 连不上；
   - RustFS 连不上；
   - 模型 API Key 缺失；
   - Embedding 维度不匹配；
   - 401/403；
   - 前端代理失败；
   - SSE 无响应；
   - MCP 工具列表为空。

12. 初学者推荐启动顺序
   - 不要一口气启动全部；
   - 先数据库 + Redis + 后端；
   - 再前端；
   - 再模型；
   - 再上传文档；
   - 最后 MCP。

每个命令都必须解释“为什么执行它”。不确定的中间件启动命令必须标注需要实际验证，不要硬编。

完成后输出：
1. 修改文件；
2. 新增章节；
3. 还需要用户本机确认的环境项；
4. 下一步建议。
```

---

## 5. 模块三：Maven 模块、目录结构、后端启动流程

目标文件：

- `docs/learning-notes/03-目录结构与模块职责.md`
- `docs/learning-notes/04-后端启动流程源码解析.md`

给 Codex 的提示词：

```text
请只扩写以下 2 篇文档：

- docs/learning-notes/03-目录结构与模块职责.md
- docs/learning-notes/04-后端启动流程源码解析.md

本轮禁止修改其他文件。

扩写目标：
让初学者能理解 Ragent 的后端为什么分成这些模块，以及 Spring Boot 启动后到底装配了哪些东西。

请重点阅读：
- pom.xml
- bootstrap/pom.xml
- framework/pom.xml
- infra-ai/pom.xml
- mcp-server/pom.xml
- bootstrap/src/main/java/com/nageoffer/ai/ragent/RagentApplication.java
- bootstrap/src/main/resources/application.yaml
- 各模块 src/main/java 目录结构
- 所有 Configuration、AutoConfiguration、Controller、Service、Mapper、Aspect、Interceptor 相关类

对 03-目录结构与模块职责.md 的要求：

1. 给出根目录树，并逐项解释：
   - bootstrap
   - framework
   - infra-ai
   - mcp-server
   - frontend
   - resources
   - docs
   - scripts
   - assets

2. 解释 Maven 聚合关系：
   - 根 pom 是父工程；
   - bootstrap 依赖谁；
   - infra-ai 依赖谁；
   - mcp-server 是否独立运行；
   - frontend 为什么不是 Maven 模块。

3. 对每个 Java 模块写：
   - 模块定位；
   - 主要包结构；
   - 关键类；
   - 推荐阅读顺序；
   - 新手容易误解的点。

4. 画 Mermaid 模块依赖图。

5. 增加“按功能找代码”的速查表：
   - 登录在哪里；
   - 聊天在哪里；
   - 文档上传在哪里；
   - 入库 Pipeline 在哪里；
   - 检索在哪里；
   - 模型路由在哪里；
   - MCP 在哪里；
   - 前端页面在哪里；
   - 数据库实体在哪里。

对 04-后端启动流程源码解析.md 的要求：

1. 从 RagentApplication.main() 开始逐步解释。
2. 解释 @SpringBootApplication、@EnableScheduling、@MapperScan 的作用。
3. 解释 Spring Boot 如何扫描 Controller、Service、Component、Configuration。
4. 解释 application.yaml 如何绑定到 Properties 类。
5. 梳理启动过程中关键 Bean：
   - Controller；
   - Service；
   - Pipeline；
   - IngestionEngine；
   - RoutingLLMService；
   - RoutingEmbeddingService；
   - RetrievalEngine；
   - MCP Client；
   - MyBatis Mapper；
   - Redis/Redisson；
   - RocketMQ；
   - S3/RustFS。
6. 补充启动失败排查顺序：
   - 看最底层 Caused by；
   - 看端口；
   - 看数据库；
   - 看 Redis；
   - 看配置绑定；
   - 看 API Key。
7. 增加 Debug 断点路线：
   - 启动入口；
   - 登录接口；
   - RAG 聊天入口；
   - 入库入口；
   - 模型路由；
   - MCP 注册。
8. 画启动流程 Mermaid 图和 Bean 关系图。

完成后输出：
1. 修改文件；
2. 每个文件新增章节；
3. 关键源码路径清单；
4. 下一轮建议。
```

---

## 6. 模块四：数据库、表结构、SQL、数据流

目标文件：

- `docs/learning-notes/05-数据库与核心表结构.md`

给 Codex 的提示词：

```text
请只扩写：docs/learning-notes/05-数据库与核心表结构.md

本轮禁止修改其他文件。

扩写目标：
把数据库文档写成“从表结构理解业务流程”的学习手册，不要只列核心表。

请重点阅读：
- resources/database/schema_pg.sql
- resources/database/init_data_pg.sql
- resources/database/upgrade_*.sql
- bootstrap/src/main/java/**/dao/entity/*.java
- bootstrap/src/main/java/**/dao/mapper/*.java
- bootstrap/src/main/java/**/service/**/*.java
- bootstrap/src/main/resources/application.yaml

文档必须包含：

1. 数据库整体定位
   - PostgreSQL 在项目中存什么；
   - pgvector 存什么；
   - RustFS/S3 和数据库的边界；
   - Milvus 与 pgvector 的关系。

2. 初始化脚本说明
   - schema_pg.sql 创建什么；
   - init_data_pg.sql 初始化什么；
   - upgrade 脚本什么时候用；
   - 新库不能乱执行哪些脚本。

3. 表分组讲解
   按业务域分组，而不是单纯 alphabetical：
   - 用户与认证；
   - 会话与消息；
   - 知识库与文档；
   - Chunk 与向量；
   - 入库 Pipeline；
   - RAG Trace；
   - 意图树与 MCP；
   - 系统设置/反馈/示例问题/术语等。

4. 每张关键表必须说明：
   - 建表 SQL 关键字段；
   - 字段含义；
   - 主键/唯一键/索引；
   - 写入时机；
   - 读取时机；
   - 对应 DO/Mapper/Service；
   - 典型查询 SQL；
   - 初学者容易误解的点。

5. 补充至少 10 条学习 SQL：
   - 查看用户；
   - 查看会话；
   - 查看消息；
   - 查看知识库；
   - 查看文档状态；
   - 查看 chunk；
   - 查看向量数量；
   - 查看入库任务；
   - 查看入库节点日志；
   - 查看 RAG Trace。

6. 画 Mermaid ER 图：
   - 用户会话关系；
   - 知识库文档关系；
   - 入库 Pipeline 关系；
   - RAG Trace 关系。

7. 写“上传一篇文档后哪些表会变化”。
8. 写“问答一次后哪些表会变化”。
9. 写“如何通过数据库定位入库失败”。
10. 写“如何通过数据库定位 RAG 回答慢”。

完成后输出：
1. 修改文件；
2. 关键表清单；
3. 还没完全确认的表关系；
4. 建议下一轮扩写模块。
```

---

## 7. 模块五：文档入库 Pipeline 深度解析

目标文件：

- `docs/learning-notes/06-文档入库流程解析.md`

给 Codex 的提示词：

```text
请只扩写：docs/learning-notes/06-文档入库流程解析.md

本轮禁止修改其他文件。

扩写目标：
把文档入库流程写成项目核心教程。要求从“用户上传文档”追到“数据库和向量存储写入完成”，每一步都要有源码路径、类名、方法名、输入输出、Debug 建议。

请重点阅读：
- bootstrap/src/main/java/com/nageoffer/ai/ragent/knowledge/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/ingestion/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/ingestion/engine/IngestionEngine.java
- bootstrap/src/main/java/com/nageoffer/ai/ragent/ingestion/node/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/ingestion/domain/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/infra/** 或向量存储相关类
- infra-ai/src/main/java/**/embedding/**
- resources/database/schema_pg.sql
- docs/examples/**
- frontend/src/pages/admin/knowledge/**
- frontend/src/pages/admin/ingestion/**
- frontend/src/services/knowledgeService.ts
- frontend/src/services/ingestionService.ts

文档必须包含：

1. 入库流程总览
   - 为什么要入库；
   - 文档、Chunk、Embedding、向量存储的关系；
   - 知识库上传链路与通用 Ingestion Pipeline 链路的区别。

2. 前端入口
   - 知识库文档页面；
   - 入库 Pipeline 页面；
   - 上传按钮到 service 方法；
   - 请求 URL、method、参数、响应。

3. 后端入口
   - KnowledgeDocumentController 上传链路；
   - IngestionTaskController 上传链路；
   - 两者的关系和差异。

4. Pipeline 定义
   - PipelineDefinition；
   - NodeConfig；
   - t_ingestion_pipeline；
   - t_ingestion_pipeline_node；
   - node_type、next_node_id、settings_json、condition_json。

5. IngestionEngine 源码逐段解析
   - execute()；
   - buildNodeConfigMap()；
   - validatePipeline()；
   - findStartNode()；
   - executeChain()；
   - executeNode()；
   - 条件判断；
   - NodeLog；
   - 失败处理。

6. 节点逐个解析
   对每类节点都要写：
   - 类路径；
   - 职责；
   - 输入；
   - 输出；
   - 依赖的配置；
   - 失败场景；
   - Debug 断点。

   至少包括：
   - FetcherNode；
   - ParserNode；
   - EnhancerNode；
   - ChunkerNode；
   - EnricherNode；
   - IndexerNode。

7. 向量写入
   - EmbeddingService 如何被调用；
   - RoutingEmbeddingService 如何选模型；
   - pgvector/Milvus 如何写入；
   - 维度为什么重要；
   - t_knowledge_vector 或对应向量表如何关联 chunk。

8. 数据库变化
   - 上传前；
   - 创建任务；
   - 每个节点执行；
   - 切块完成；
   - 向量写入；
   - 任务完成/失败。

9. 失败路径
   - 文件拿不到；
   - 解析失败；
   - 切块为空；
   - Embedding 失败；
   - 向量维度不匹配；
   - RustFS/S3 失败；
   - 数据库写入失败。

10. Debug 实验
   - 准备一个最小 txt 文件；
   - 上传；
   - 断点位置；
   - 查哪些表；
   - 看哪些日志；
   - 期望结果。

11. Mermaid 图
   - 入库总流程图；
   - 前后端时序图；
   - IngestionEngine 节点执行图；
   - 数据落库关系图。

12. 本章复习问题至少 15 个，附参考答案。

要求：
不要停留在“Fetcher -> Parser -> Chunker”这种概括，要展开到方法级别。初学者读完后应该能在 IDE 中独立打断点追完整入库流程。

完成后输出：
1. 修改文件；
2. 本次确认的核心类和方法；
3. 未确认内容；
4. 建议下一轮扩写模块。
```

---

## 8. 模块六：RAG 问答主流程深度解析

目标文件：

- `docs/learning-notes/07-RAG问答主流程解析.md`

给 Codex 的提示词：

```text
请只扩写：docs/learning-notes/07-RAG问答主流程解析.md

本轮禁止修改其他文件。

扩写目标：
把 RAG 问答主流程写成整个项目最重要的一篇源码学习文档。要求从“用户在前端输入问题”追踪到“SSE 流式返回最终答案”，每一步都要解释上下文对象、核心类、方法、输入输出、异常短路、数据库写入和前端渲染。

请重点阅读：
- frontend/src/pages/**/Chat*.tsx
- frontend/src/hooks/useStreamResponse.ts
- frontend/src/stores/**
- frontend/src/services/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/controller/RAGChatController.java
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/service/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/service/pipeline/StreamChatPipeline.java
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/service/pipeline/StreamChatContext.java
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/core/memory/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/core/rewrite/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/core/intent/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/core/guidance/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/core/retrieve/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/core/prompt/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/service/handler/**
- infra-ai/src/main/java/**
- resources/prompt/**
- resources/database/schema_pg.sql

文档必须包含：

1. 一次问答的全局图
   - 前端输入；
   - 后端 Controller；
   - Service；
   - Pipeline；
   - 检索；
   - 模型；
   - SSE；
   - 数据库存储。

2. 前端发起请求
   - 用户输入在哪个组件；
   - store 如何保存用户消息；
   - useStreamResponse 如何发请求；
   - 为什么用 fetch 读取流而不是普通 axios；
   - AbortSignal 如何停止回答。

3. Controller 层
   - RAGChatController.chat()；
   - 请求参数；
   - SseEmitter；
   - /rag/v3/stop。

4. Service 层
   - RAGChatServiceImpl.streamChat()；
   - taskId；
   - conversationId；
   - 用户上下文；
   - 限流/排队；
   - Trace 创建；
   - StreamChatEventHandler。

5. StreamChatPipeline.execute() 逐行/逐方法解析
   必须按方法展开：
   - loadMemory()；
   - rewriteQuery()；
   - resolveIntents()；
   - handleGuidance()；
   - handleSystemOnly()；
   - retrieve()；
   - handleEmptyRetrieval()；
   - streamRagResponse()；
   - streamSystemResponse()；
   - streamLLMResponse()。

6. StreamChatContext 解析
   - 它保存了哪些字段；
   - 每个阶段往里面写什么；
   - 为什么 RAG 项目喜欢用 Context 对象。

7. 会话记忆
   - 历史消息；
   - 摘要；
   - 配置项；
   - 写入和读取表；
   - Token 成本控制。

8. 问题改写
   - 为什么要重写；
   - 多轮上下文如何影响改写；
   - 拆问如何影响后续检索；
   - 失败时如何处理。

9. 意图识别与澄清
   - 意图树；
   - 置信度；
   - knowledge intent；
   - MCP intent；
   - system-only intent；
   - 何时短路。

10. 检索阶段
   - RetrievalEngine；
   - MultiChannelRetrievalEngine；
   - vector-global；
   - intent-directed；
   - 去重；
   - Rerank；
   - Top-K。

11. Prompt 组装
   - RAGPromptService；
   - PromptContext；
   - kbContext；
   - mcpContext；
   - history；
   - system prompt；
   - user prompt。

12. 模型流式调用
   - LLMService；
   - RoutingLLMService；
   - ChatRequest；
   - StreamCallback；
   - thinking 与 content；
   - 取消句柄。

13. SSE 返回
   - meta；
   - message；
   - thinking；
   - finish；
   - done；
   - error；
   - 前端如何解析。

14. 数据库变化
   - t_conversation；
   - t_message；
   - t_conversation_summary；
   - t_rag_trace_run；
   - t_rag_trace_node。

15. 短路和异常路径
   - 需要澄清；
   - system-only；
   - 检索为空；
   - 模型失败；
   - 用户停止；
   - SSE 超时；
   - 并发限流。

16. 两个完整案例
   - 案例 A：知识库问题；
   - 案例 B：天气/MCP 工具问题；
   每个案例都要写“每个阶段会发生什么”。

17. Debug 路线
   至少列 12 个断点，按顺序说明看什么变量。

18. Mermaid 图
   - 总流程图；
   - 时序图；
   - Context 状态变化图；
   - 异常短路图。

19. 本章复习问题至少 20 个，附参考答案。

要求：
这是最核心的一篇文档，不能只有调用链表格。必须写成可跟着 IDE 逐步 Debug 的教程。

完成后输出：
1. 修改文件；
2. 核心调用链；
3. 关键断点；
4. 不确定内容；
5. 下一轮建议。
```

---

## 9. 模块七：检索、重排、向量存储专项

目标文件：

- 建议新增或扩写：`docs/learning-notes/16-检索与重排专项解析.md`
- 同时可少量补充：`docs/learning-notes/07-RAG问答主流程解析.md`
- 同时可少量补充：`docs/learning-notes/14-术语表.md`

给 Codex 的提示词：

```text
请新增一篇专项文档：docs/learning-notes/16-检索与重排专项解析.md

允许少量补充：
- docs/learning-notes/07-RAG问答主流程解析.md
- docs/learning-notes/14-术语表.md

本轮禁止修改其他文件。

扩写目标：
把“检索”从 RAG 主流程中单独拆出来讲清楚。初学者经常以为 RAG 就是一次向量查询，这篇文档要说明 Ragent 如何做多路召回、意图定向、去重、Rerank、Top-K 控制和向量存储。

请重点阅读：
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/core/retrieve/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/config/SearchChannelProperties.java
- bootstrap/src/main/resources/application.yaml 中 rag.search、rag.rerank、rag.vector
- bootstrap/src/main/java/**/vector/** 或向量存储相关实现
- infra-ai/src/main/java/**/rerank/**
- infra-ai/src/main/java/**/embedding/**
- resources/database/schema_pg.sql

文档必须包含：

1. 为什么不能只做一次向量检索。
2. 检索阶段在 RAG 主流程中的位置。
3. RetrievalEngine 的职责。
4. MultiChannelRetrievalEngine 的职责。
5. 检索通道解释：
   - vector-global；
   - intent-directed；
   - 其他源码中存在的通道。
6. SearchChannelProperties 配置解释。
7. Top-K、top-k-multiplier、confidence-threshold、min-intent-score 的含义。
8. pgvector 检索如何工作。
9. Milvus 检索如何工作，若当前默认不用 Milvus，要说明它是可选路径。
10. Embedding 在入库和查询中的两次角色。
11. 去重逻辑。
12. Rerank 调用链。
13. rerank-noop 的意义和代价。
14. 检索结果如何进入 Prompt。
15. 检索为空时如何短路。
16. 检索慢如何排查：数据库、向量库、Embedding、Rerank、网络。
17. 实验：上传 3 个相似文档，提 3 类问题，看召回结果。
18. Debug 断点清单。
19. Mermaid：检索内部流程图。
20. 面试追问：为什么需要多路召回？为什么需要 Rerank？为什么不能 Top-K 越大越好？

完成后输出：
1. 新增/修改文件；
2. 检索核心类清单；
3. 配置项清单；
4. 下一轮建议。
```

---

## 10. 模块八：模型调用、路由、熔断、降级

目标文件：

- `docs/learning-notes/08-模型调用与路由容错.md`

给 Codex 的提示词：

```text
请只扩写：docs/learning-notes/08-模型调用与路由容错.md

本轮禁止修改其他文件。

扩写目标：
让初学者理解 infra-ai 模块不是“调个 API”，而是负责多供应商适配、模型能力抽象、候选选择、健康状态、熔断、降级、流式首包探测。

请重点阅读：
- infra-ai/src/main/java/**
- infra-ai/pom.xml
- framework/src/main/java/**/ChatRequest.java 或相关通用模型对象
- bootstrap/src/main/resources/application.yaml 中 ai.* 配置
- bootstrap/src/main/java/** 中调用 LLMService/EmbeddingService/RerankService 的地方

文档必须包含：

1. infra-ai 模块定位
   - 为什么不在业务代码里直接写 HTTP 调模型；
   - LLMService、EmbeddingService、RerankService 的边界。

2. 模型供应商适配
   - bailian；
   - siliconflow；
   - aihubmix；
   - ollama；
   - noop。
   每个供应商说明：支持能力、配置位置、Client 类、请求差异。

3. Chat 调用链
   - ChatRequest；
   - LLMService；
   - RoutingLLMService；
   - ChatClient；
   - StreamCallback；
   - StreamCancellationHandle。

4. Embedding 调用链
   - 入库阶段如何调用；
   - 查询阶段如何调用；
   - 维度如何保证一致；
   - 批量 Embedding 如何处理。

5. Rerank 调用链
   - 检索结果如何进入 Rerank；
   - Rerank 输出如何影响 Top-K；
   - rerank-noop 的作用。

6. ModelSelector
   - default-model；
   - candidates；
   - priority；
   - supports-thinking；
   - 候选过滤规则。

7. ModelHealthStore
   - CLOSED；
   - OPEN；
   - HALF_OPEN；
   - failure-threshold；
   - open-duration-ms；
   - markSuccess/markFailure。

8. ModelRoutingExecutor
   - executeWithFallback()；
   - 失败后如何尝试下一个；
   - 最终失败如何抛出；
   - 哪些能力共用这个逻辑。

9. 流式 Chat 特殊性
   - 为什么流式不能简单重试；
   - 首包探测；
   - 已输出内容后失败的处理限制；
   - 取消句柄。

10. 配置项逐段解释
   - ai.providers；
   - ai.selection；
   - ai.stream；
   - ai.chat；
   - ai.embedding；
   - ai.rerank。

11. 故障实验设计
   - 错误 API Key；
   - 关闭 Ollama；
   - 设置不可用模型；
   - 观察候选切换；
   - 观察熔断状态。
   注意：只写实验方案，不要真的改用户配置。

12. Mermaid 图
   - 非流式调用降级图；
   - 流式调用图；
   - 熔断状态机图。

13. 面试讲法
   - 1 分钟；
   - 3 分钟；
   - 面试追问与回答。

14. 本章复习问题至少 15 个，附参考答案。

完成后输出：
1. 修改文件；
2. 关键源码路径；
3. 关键配置项；
4. 下一轮建议。
```

---

## 11. 模块九：MCP 工具调用深度解析

目标文件：

- `docs/learning-notes/09-MCP工具调用解析.md`

给 Codex 的提示词：

```text
请只扩写：docs/learning-notes/09-MCP工具调用解析.md

本轮禁止修改其他文件。

扩写目标：
让初学者理解 MCP 在 Ragent 中解决什么问题、服务端如何暴露工具、客户端如何发现工具、RAG 主流程如何选择并调用工具、工具结果如何进入最终 Prompt。

请重点阅读：
- mcp-server/src/main/java/**
- mcp-server/pom.xml
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/core/mcp/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/core/intent/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/core/retrieve/**
- bootstrap/src/main/resources/application.yaml 中 rag.mcp
- resources/database/schema_pg.sql 中意图/MCP 相关表

文档必须包含：

1. MCP 是什么，用初学者语言解释。
2. MCP 和 RAG 检索的区别。
3. 为什么企业 RAG 项目需要工具调用。
4. mcp-server 模块结构。
5. MCP Server 启动入口。
6. 工具实现类逐个解释：
   - 天气；
   - 工单；
   - 销售；
   - 其他源码中存在的工具。
7. 工具注册配置。
8. MCP Java SDK 在项目中的作用。
9. bootstrap 侧 MCP Client 配置。
10. 启动时 tools/list 如何发现工具。
11. DefaultMcpToolRegistry 如何保存工具。
12. IntentResolver 如何把用户问题关联到 mcp_tool_id。
13. LLMMcpParameterExtractor 如何从自然语言提参数。
14. McpClientToolExecutor 如何执行 callTool。
15. 工具结果如何进入 RetrievalContext.mcpContext。
16. RAGPromptService 如何把工具结果塞进 Prompt。
17. 工具失败如何处理。
18. MCP 服务没启动时会怎样。
19. toolId 不匹配时如何排查。
20. 参数提取失败如何排查。
21. 完整案例：用户问“北京今天天气怎么样”，从前端到 MCP Server 到最终回答。
22. Mermaid：工具发现图、工具调用时序图、MCP 与知识检索融合图。
23. Debug 断点：服务端工具、客户端注册、参数提取、工具执行、Prompt 组装。
24. 本章复习问题至少 15 个，附参考答案。

要求：
不要把 MCP 写成抽象概念。必须结合 Ragent 当前代码和配置讲。

完成后输出：
1. 修改文件；
2. 服务端核心类；
3. 客户端核心类；
4. 如何本地验证 MCP；
5. 下一轮建议。
```

---

## 12. 模块十：前端页面、接口、状态流、SSE

目标文件：

- `docs/learning-notes/10-前端页面与接口关系.md`

给 Codex 的提示词：

```text
请只扩写：docs/learning-notes/10-前端页面与接口关系.md

本轮禁止修改其他文件。

扩写目标：
让后端基础学习者也能看懂 Ragent 前端：页面在哪里、接口在哪里、状态在哪里、SSE 怎么解析、如何从按钮反查后端 Controller。

请重点阅读：
- frontend/package.json
- frontend/vite.config.ts
- frontend/src/main.tsx
- frontend/src/router.tsx
- frontend/src/pages/**
- frontend/src/components/**
- frontend/src/services/**
- frontend/src/stores/**
- frontend/src/hooks/useStreamResponse.ts
- bootstrap/src/main/java/**/controller/**

文档必须包含：

1. 前端技术栈解释
   - React；
   - TypeScript；
   - Vite；
   - React Router；
   - Zustand；
   - Axios/fetch；
   - Tailwind；
   - Radix UI。

2. 前端目录结构
   - pages；
   - components；
   - services；
   - stores；
   - hooks；
   - utils；
   - types。

3. 路由总览
   - 登录页；
   - 聊天页；
   - 管理后台；
   - 知识库；
   - 文档；
   - 入库 Pipeline；
   - 意图树；
   - Trace；
   - 用户和系统设置。

4. 接口封装方式
   - api.ts；
   - token 处理；
   - 错误处理；
   - baseURL/代理。

5. 页面到后端 Controller 映射表
   每个页面至少写：
   - 前端文件；
   - service 方法；
   - URL；
   - HTTP 方法；
   - 后端 Controller；
   - 后端方法；
   - 用途。

6. 登录流程
   - 表单；
   - authService；
   - token 保存；
   - user/me；
   - 路由守卫。

7. 聊天流程
   - 输入框；
   - chatStore；
   - useStreamResponse；
   - fetch 请求；
   - SSE 解析；
   - thinking/content；
   - 停止回答。

8. 文档上传流程
   - 页面；
   - FormData；
   - 上传接口；
   - 上传后刷新列表；
   - 状态展示。

9. 管理后台流程
   - 知识库；
   - Pipeline；
   - Trace；
   - 设置。

10. SSE 专项
   - 为什么不用普通 JSON；
   - EventSource 和 fetch stream 的区别；
   - 如何带 Authorization；
   - 如何处理 AbortSignal；
   - meta/message/finish/done；
   - 浏览器 Network 如何观察。

11. 初学者反查后端方法
   - 从按钮找 onClick；
   - 找 service；
   - 搜 URL；
   - 找 Controller；
   - Step Into Service。

12. Mermaid 图
   - 前端目录关系图；
   - 登录时序图；
   - 聊天 SSE 时序图；
   - 文档上传时序图。

13. 实践任务
   - 改一个页面文案；
   - 新增一个接口 loading 状态；
   - 给 SSE 事件打印调试日志。
   注意只写建议，不要实际改业务代码。

14. 本章复习问题至少 15 个，附参考答案。

完成后输出：
1. 修改文件；
2. 页面接口映射是否完整；
3. 前端仍需确认的问题；
4. 下一轮建议。
```

---

## 13. 模块十一：认证、上下文、幂等、异常处理、通用框架能力

目标文件：

- 建议新增：`docs/learning-notes/17-认证上下文幂等与异常处理.md`
- 可少量补充：`docs/learning-notes/03-目录结构与模块职责.md`
- 可少量补充：`docs/learning-notes/14-术语表.md`

给 Codex 的提示词：

```text
请新增：docs/learning-notes/17-认证上下文幂等与异常处理.md

允许少量补充：
- docs/learning-notes/03-目录结构与模块职责.md
- docs/learning-notes/14-术语表.md

本轮禁止修改其他文件。

扩写目标：
补齐 framework 和用户认证相关内容。很多初学者只看 RAG 主流程，忽略了企业级项目必须有的认证、上下文、异常、响应封装、幂等、拦截器、AOP。本篇专门讲这些“看起来不起眼但很重要”的通用能力。

请重点阅读：
- framework/src/main/java/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/user/**
- bootstrap/src/main/java/**/config/**
- bootstrap/src/main/java/**/interceptor/**
- bootstrap/src/main/java/**/aspect/**
- bootstrap/src/main/resources/application.yaml 中 sa-token

文档必须包含：

1. framework 模块定位。
2. 统一返回 Result/响应对象。
3. 业务异常体系。
4. 全局异常处理。
5. 用户登录流程。
6. Sa-Token 配置。
7. Authorization token 如何传递。
8. UserContext/上下文对象如何保存用户信息。
9. 拦截器如何工作。
10. 幂等注解/幂等 AOP 如何避免重复提交。
11. SSE 发送工具或相关通用能力。
12. RocketMQ/Redis/Redisson 通用封装，如源码中存在则详细讲。
13. 初学者如何从 Controller 看到用户身份。
14. 常见安全问题：不要提交真实密码/API Key；不要把 token 打进日志；不要把用户数据写入学习文档。
15. Debug 实验：登录后调用一个需要认证的接口，看用户上下文如何进入 Controller。
16. Mermaid：登录认证时序图、请求上下文流转图、异常处理图。
17. 本章复习问题至少 12 个，附参考答案。

完成后输出：
1. 新增/修改文件；
2. framework 核心类清单；
3. 用户认证核心类清单；
4. 下一轮建议。
```

---

## 14. 模块十二：Trace、日志、可观测性、问题排查

目标文件：

- 建议新增：`docs/learning-notes/18-Trace日志与问题排查.md`
- 可少量补充：`docs/learning-notes/07-RAG问答主流程解析.md`
- 可少量补充：`docs/learning-notes/06-文档入库流程解析.md`

给 Codex 的提示词：

```text
请新增：docs/learning-notes/18-Trace日志与问题排查.md

允许少量补充：
- docs/learning-notes/07-RAG问答主流程解析.md
- docs/learning-notes/06-文档入库流程解析.md

本轮禁止修改其他文件。

扩写目标：
补齐项目可观测性学习内容：RAG Trace、入库节点日志、普通日志、前端 Network、数据库排查。初学者以后调 bug 时能按这篇文档定位问题。

请重点阅读：
- bootstrap/src/main/java/**/trace/**
- bootstrap/src/main/java/**/RagTrace*.java
- bootstrap/src/main/java/com/nageoffer/ai/ragent/rag/service/handler/**
- bootstrap/src/main/java/com/nageoffer/ai/ragent/ingestion/engine/IngestionEngine.java
- bootstrap/src/main/java/com/nageoffer/ai/ragent/ingestion/domain/context/NodeLog.java
- resources/database/schema_pg.sql 中 trace、task、task_node 表
- frontend/src/pages/**/trace/** 或 Trace 页面相关代码
- bootstrap/src/main/resources/application.yaml 中 rag.trace

文档必须包含：

1. 为什么 RAG 项目必须有 Trace。
2. 普通日志、Trace、数据库记录、前端 Network 的区别。
3. RAG Trace 表结构。
4. RAG Trace 创建时机。
5. 每个 RAG 阶段如何记录。
6. Trace 页面如何读取数据。
7. 入库任务日志和节点日志。
8. IngestionEngine 如何生成 NodeLog。
9. 如何定位：
   - 文档上传失败；
   - Parser 失败；
   - Embedding 失败；
   - 检索为空；
   - Rerank 失败；
   - 模型超时；
   - SSE 中断；
   - MCP 工具失败。
10. 给出排查路径表：现象 -> 看前端 -> 看后端日志 -> 查表 -> 打断点。
11. 给出常用 SQL。
12. 给出 Debug 断点。
13. 给出日志安全注意事项：密钥、token、用户隐私、原文档内容。
14. Mermaid：一次 RAG Trace 记录图、一次入库日志记录图。
15. 本章复习问题至少 12 个，附参考答案。

完成后输出：
1. 新增/修改文件；
2. 排查表是否完整；
3. 仍需实际运行验证的问题；
4. 下一轮建议。
```

---

## 15. 模块十三：两周学习计划、小改造、面试复盘

目标文件：

- `docs/learning-notes/11-两周学习计划.md`
- `docs/learning-notes/12-小改造任务建议.md`
- `docs/learning-notes/13-面试复盘与项目讲解稿.md`

给 Codex 的提示词：

```text
请只扩写以下 3 篇文档：

- docs/learning-notes/11-两周学习计划.md
- docs/learning-notes/12-小改造任务建议.md
- docs/learning-notes/13-面试复盘与项目讲解稿.md

本轮禁止修改其他文件。

扩写目标：
让学习计划变得更可执行，让小改造任务变得更安全，让面试稿真正能结合源码讲。

对 11-两周学习计划.md 的要求：

1. 每天写成详细模板：
   - 今日目标；
   - 今日必读文档；
   - 今日必读源码；
   - 今日实操；
   - 今日 Debug 断点；
   - 今日数据库观察；
   - 今日 Mermaid 图；
   - 今日输出物；
   - 验收标准；
   - 如果卡住怎么办。
2. 每天控制 2～4 小时。
3. Day 1～7 重在跑通、架构、入库。
4. Day 8～14 重在 RAG、检索、模型、MCP、前端、改造、面试。
5. 加一个“每天 30 分钟复盘模板”。
6. 加一个“周末总复盘模板”。

对 12-小改造任务建议.md 的要求：

1. 至少给 10 个小改造任务，从简单到困难。
2. 每个任务必须包含：
   - 改造目标；
   - 适合学习什么；
   - 涉及文件；
   - 涉及方法；
   - 是否改数据库；
   - 是否改前端；
   - 风险点；
   - 测试方法；
   - 回滚方式；
   - 完成标准。
3. 任务分类：
   - 文档/日志类；
   - 前端展示类；
   - 配置校验类；
   - 入库流程类；
   - RAG 提示优化类；
   - Trace 可观测性类；
   - MCP 工具类。
4. 明确哪些任务适合第一次做，哪些不建议初学者先做。
5. 给出每个任务的 Codex 改造方案提示词模板。

对 13-面试复盘与项目讲解稿.md 的要求：

1. 增加 1 分钟、3 分钟、10 分钟讲法。
2. 增加专项讲法：
   - 文档入库；
   - RAG 问答；
   - 检索与 Rerank；
   - 模型路由与容错；
   - MCP；
   - 前端 SSE；
   - 数据库设计；
   - 可观测性。
3. 每个讲法必须带源码证据：类名、方法名、配置项、表名。
4. 增加至少 30 个面试题，分为：
   - 基础概念；
   - 项目架构；
   - 源码流程；
   - 设计取舍；
   - 排错场景；
   - 扩展改造。
5. 每个面试题都给参考回答，回答要结合 Ragent，不要泛泛而谈。
6. 增加“面试官追问时怎么展开”。
7. 增加“不能吹过头的地方”，提醒哪些能力你只是学习过、哪些要实际验证后再讲。

完成后输出：
1. 修改文件；
2. 学习计划是否可执行；
3. 小改造任务数量；
4. 面试题数量；
5. 下一轮建议。
```

---

## 16. 模块十四：GitHub Fork、同步、分支、PR 工作流

目标文件：

- `docs/learning-notes/15-GitHub-Fork与同步流程.md`

给 Codex 的提示词：

```text
请只扩写：docs/learning-notes/15-GitHub-Fork与同步流程.md

本轮禁止修改其他文件。

扩写目标：
把 GitHub 工作流写成初学者能照着操作的手册，解决这些问题：我改的是自己的仓库吗？原项目更新后怎么同步？我自己的学习文档怎么提交？冲突怎么处理？

文档必须包含：

1. Fork、clone、origin、upstream 的区别。
2. 推荐仓库结构：
   - origin 指向 Purpose-C/ragent；
   - upstream 指向 nageoffer/ragent。
3. 如何检查当前远程仓库：
   - git remote -v；
   - git branch -vv；
   - git status。
4. 如果已经 clone 原仓库，如何改 remote。
5. 如何新建学习分支。
6. 如何提交学习文档。
7. 如何同步原项目更新：
   - fetch upstream；
   - merge upstream/main；
   - push origin main。
8. merge 与 rebase 的区别，初学阶段推荐 merge。
9. 如何让自己的学习分支跟上 main。
10. 冲突是什么。
11. 如何解决 Markdown 冲突。
12. 如何解决代码冲突。
13. 哪些文件不应该提交：密钥、日志、构建产物、真实业务数据。
14. 如何写 commit message。
15. 如何开 PR 到自己的 main。
16. 是否应该给原作者提 PR，以及什么时候适合。
17. 常见错误：
   - push 到 upstream 失败；
   - main 分支混入实验代码；
   - reset hard 丢东西；
   - merge 后冲突没解决；
   - API Key 被提交。
18. 给出安全操作流程图。
19. 给出日常学习前/学习后 checklist。
20. 本章复习问题至少 10 个，附参考答案。

完成后输出：
1. 修改文件；
2. 新增命令清单；
3. 风险提示是否完整；
4. 下一轮建议。
```

---

## 17. 最终整合与质量检查提示词

当所有模块都执行完后，把下面这段给 Codex，用于统一检查。

```text
请对 docs/learning-notes/ 做最终质量检查和轻量整理。

本轮只允许修改 docs/learning-notes/ 下的 Markdown 文件，不允许修改业务源码，不允许 commit/push。

检查目标：
1. 所有文档是否有清晰标题和目录；
2. 核心文档是否足够详细，而不是几十行提纲；
3. 是否有过多重复内容；
4. 是否存在编造的类名、方法名、表名、配置；
5. 是否每个关键结论都有源码路径；
6. Mermaid 图是否语法合理；
7. 代码块语言标识是否正确；
8. 是否有真实密钥、token、密码、私有地址；
9. 是否把不确定内容标注为“需要进一步确认”；
10. 是否每篇文档都有：复习问题、Debug 建议、实践任务、下一步建议；
11. 是否有统一的阅读顺序；
12. 是否有总索引。

请新增或更新：docs/learning-notes/README.md

README.md 需要包含：
1. 这套学习笔记怎么用；
2. 推荐阅读顺序；
3. 按主题分类的文档索引；
4. 两周学习路线入口；
5. 核心流程入口；
6. 实战改造入口；
7. 面试复盘入口。

完成后输出：
1. 修改了哪些文件；
2. 每篇文档当前大概覆盖内容；
3. 仍然薄弱的地方；
4. 建议人工重点复查的内容；
5. 是否发现潜在敏感信息。
```

---

## 18. 每轮执行后的人工检查清单

每次 Codex 执行完，都建议你自己运行：

```bash
git status
git diff -- docs/learning-notes/
```

检查：

```text
1. 是否只改了 docs/learning-notes/？
2. 有没有误改源码？
3. 有没有生成真实密钥、密码、token？
4. 有没有编造不存在的类或方法？
5. Mermaid 图是否能渲染？
6. 文档是否真的变详细了？
7. 是否适合你当前基础阅读？
8. 有没有一眼看起来很“AI 套话”的段落？
9. 有没有需要你本机运行确认的地方？
10. 是否应该拆成下一轮继续扩写？
```

如果发现 Codex 改了业务源码，可以先执行：

```bash
git diff
```

确认无关改动后再撤销。例如：

```bash
git checkout -- bootstrap frontend framework infra-ai mcp-server resources pom.xml package.json
```

> 注意：撤销前一定先看 `git diff`，别把自己真正想保留的改动也删了。

---

## 19. 推荐执行顺序

建议你按这个顺序执行提示词：

```text
第 0 轮：执行“执行前准备提示词”，让 Codex 先审计。
第 1 轮：模块一，总览/阅读顺序/术语。
第 2 轮：模块二，启动与配置。
第 3 轮：模块三，目录结构与启动流程。
第 4 轮：模块四，数据库。
第 5 轮：模块五，文档入库。
第 6 轮：模块六，RAG 问答主流程。
第 7 轮：模块七，检索与重排。
第 8 轮：模块八，模型调用与路由容错。
第 9 轮：模块九，MCP。
第 10 轮：模块十，前端与 SSE。
第 11 轮：模块十一，认证/上下文/异常/幂等。
第 12 轮：模块十二，Trace 与排查。
第 13 轮：模块十三，学习计划/改造/面试。
第 14 轮：模块十四，Git 工作流。
第 15 轮：最终整合与质量检查。
```

不要心急一次跑完。大模型一次处理太多文件时，很容易“每篇都加一点，但每篇都不深”。你的目标是把每个模块打穿。

---

## 20. 给 Codex 的总入口短提示词

如果 Codex 只能收一条入口提示，可以先发这一段：

```text
请阅读当前项目根目录下的 RAGENT_LEARNING_NOTES_DEEPEN_PROMPTS.md。
先执行其中“第 2 节：Codex 执行前准备提示词”，只做学习文档现状审计，不要修改任何文件。
审计完成后停止，等待我继续指定要执行的模块。
```

