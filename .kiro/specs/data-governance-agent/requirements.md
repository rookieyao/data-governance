# Requirements Document

## Introduction

数据治理智能体（Data Governance Agent）是一个面向数据中心的智能助手系统，旨在解决当前数据中心服务流程中的核心痛点。该智能体覆盖从任务下发（UMPS）、爬虫采集（SPIDER）、数据生产（data-producer）到数据清洗（data-flow）的完整链路，提供异常诊断、数据质检、流程监控和知识查询等能力。

需要说明的是，数据治理团队只负责任务的接收和下发、采集数据的清洗，不负责数据的具体采集。

## Glossary

- **UMPS**: 统一任务管理平台服务，负责接收和下发采集任务
- **SPIDER**: 爬虫服务，负责从目标平台采集数据
- **data-producer**: 数据生产服务，将采集的原始数据生产到Kafka
- **data-flow**: 数据清洗服务，负责ETL处理并将数据沉淀到清洗库
- **Origin Mongo**: 原始数据存储库，存放爬虫采集的原始数据
- **Clean Mongo**: 清洗数据存储库，存放经过ETL处理后的沉淀数据
- **Kafka**: 消息队列，用于服务间数据传输
- **Redis**: 缓存服务，用于任务状态存储
- **链路追踪**: 跨服务的请求追踪和问题定位能力
- **数据质检**: 对数据结构和内容进行实时校验的能力
- **智能体**: 基于AI的智能助手，能够理解用户意图并执行相应操作

## Requirements

### Requirement 1: 链路异常诊断

**User Story:** 作为运维人员，我希望能够快速定位整个数据链路中的异常点，以便缩短服务恢复时间。

#### Acceptance Criteria

1. WHEN 用户查询某个任务的执行状态 THEN Data_Governance_Agent SHALL 展示该任务在UMPS、SPIDER、data-producer、data-flow各节点的状态信息
2. WHEN 链路中某个服务发生异常 THEN Data_Governance_Agent SHALL 自动识别异常服务并提供异常详情
3. WHEN 用户请求诊断某个失败任务 THEN Data_Governance_Agent SHALL 提供从任务下发到数据清洗的完整链路追踪信息
4. WHEN 用户描述异常现象 THEN Data_Governance_Agent SHALL 基于历史案例和当前状态给出可能的原因分析
5. IF 异常涉及多个服务 THEN Data_Governance_Agent SHALL 按时间顺序展示异常传播路径

### Requirement 2: 数据结构变化感知与质检

**User Story:** 作为数据治理人员，我希望能够实时感知采集数据结构的变化，以便在问题暴露给下游客户之前及时处理。

#### Acceptance Criteria

1. WHEN 采集数据的字段结构发生变化 THEN Data_Governance_Agent SHALL 在数据入库前检测到该变化并发出告警
2. WHEN 检测到新增字段 THEN Data_Governance_Agent SHALL 记录新字段信息并通知相关人员
3. WHEN 检测到字段缺失 THEN Data_Governance_Agent SHALL 标记该数据为异常并阻止其流入下游
4. WHEN 检测到字段类型变化 THEN Data_Governance_Agent SHALL 评估影响范围并生成变更报告
5. WHEN 用户查询某个数据源的结构变化历史 THEN Data_Governance_Agent SHALL 展示该数据源的字段变更时间线

### Requirement 3: 数据流进度监控

**User Story:** 作为数据治理人员，我希望能够实时了解数据流的具体进度和统计信息，以便掌握整体运行状况。

#### Acceptance Criteria

1. WHEN 用户查询任务执行进度 THEN Data_Governance_Agent SHALL 展示任务在各阶段的处理数量和耗时
2. WHEN 用户查询某时间段的数据统计 THEN Data_Governance_Agent SHALL 提供任务数、采集量、清洗量等汇总信息
3. WHEN 数据处理出现积压 THEN Data_Governance_Agent SHALL 识别积压节点并估算恢复时间
4. WHILE 任务正在执行 THEN Data_Governance_Agent SHALL 提供实时的处理进度百分比
5. WHEN 用户订阅某类任务的进度 THEN Data_Governance_Agent SHALL 在关键节点主动推送状态更新

### Requirement 4: 知识库与能力查询

**User Story:** 作为开发人员，我希望能够快速查询数据中心的API信息、字段定义和能力边界，以便高效完成对接工作。

#### Acceptance Criteria

1. WHEN 用户查询某个服务的API信息 THEN Data_Governance_Agent SHALL 返回该服务的接口列表、参数说明和调用示例
2. WHEN 用户查询某个集合的字段定义 THEN Data_Governance_Agent SHALL 展示原始集合和沉淀集合的完整字段结构
3. WHEN 用户询问数据中心的能力边界 THEN Data_Governance_Agent SHALL 列出当前支持的数据源、清洗规则和输出格式
4. WHEN 用户搜索对接文档 THEN Data_Governance_Agent SHALL 返回相关文档链接和关键内容摘要
5. WHEN 用户查询清洗规则配置 THEN Data_Governance_Agent SHALL 展示当前生效的清洗流程和字段映射关系

### Requirement 5: 智能交互与扩展能力

**User Story:** 作为用户，我希望能够通过自然语言与智能体交互，并在智能体能力不足时获得替代方案。

#### Acceptance Criteria

1. WHEN 用户使用自然语言提问 THEN Data_Governance_Agent SHALL 理解用户意图并给出准确回答
2. WHEN 智能体无法直接回答问题 THEN Data_Governance_Agent SHALL 提供相关的查询入口或操作指引
3. WHEN 用户请求执行某个操作 THEN Data_Governance_Agent SHALL 在执行前确认操作内容和影响范围
4. IF 请求超出智能体能力范围 THEN Data_Governance_Agent SHALL 说明限制原因并推荐替代的技术方案
5. WHEN 用户反馈回答不准确 THEN Data_Governance_Agent SHALL 记录反馈并优化后续回答质量
