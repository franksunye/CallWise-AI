---
layout: default
title: 系统架构设计
---

# CallWise-AI（销教通）系统架构设计 v2.0

## 📋 架构概述

CallWise-AI采用**微服务架构**和**移动优先**的设计理念，为个人销售专业人士提供AI驱动的销售辅导服务。系统遵循**敏捷开发**原则，支持快速迭代和横向扩展。

### 架构原则
- **移动优先**：核心功能优先考虑移动端体验
- **离线优先**：支持离线录音和本地存储
- **API优先**：所有功能通过RESTful API提供
- **数据安全**：端到端加密，符合隐私法规
- **可扩展性**：模块化设计，支持功能扩展

## 🏗️ C4架构模型

### Level 1: 系统上下文图

```mermaid
C4Context
    title CallWise-AI 系统上下文图

    Person(user, "防水维修技师", "个人销售专业人士")
    Person(customer, "客户", "需要防水维修服务的用户")

    System(callwise, "CallWise-AI", "个人销售AI教练系统")

    System_Ext(speech_api, "语音识别服务", "Whisper API / 云端语音服务")
    System_Ext(ai_service, "AI分析服务", "对话分析与建议生成")
    System_Ext(notification, "通知服务", "跟进提醒推送")
    System_Ext(storage, "云存储服务", "AWS S3 / 阿里云OSS")

    Rel(user, callwise, "使用", "录音、查看分析、获取建议")
    Rel(user, customer, "销售沟通", "电话/现场交流")
    Rel(callwise, speech_api, "调用", "语音转文字")
    Rel(callwise, ai_service, "调用", "对话分析")
    Rel(callwise, notification, "调用", "发送提醒")
    Rel(callwise, storage, "存储", "音频文件")
```

### Level 2: 容器架构图

```mermaid
C4Container
    title CallWise-AI 容器架构图

    Person(user, "防水维修技师")

    Container_Boundary(client, "客户端") {
        Container(mobile_app, "移动应用", "React Native/Flutter", "用户界面与交互")
    }

    Container_Boundary(api_layer, "API层") {
        Container(api_gateway, "API网关", "Node.js/Express", "请求路由、认证、限流")
        Container(auth_service, "认证服务", "Node.js/JWT", "用户认证与授权")
    }

    Container_Boundary(business_layer, "业务层") {
        Container(speech_service, "语音处理服务", "Python/FastAPI", "录音转写与分析")
        Container(ai_engine, "AI分析引擎", "Python/ML", "对话分析与建议生成")
        Container(notification_service, "通知服务", "Node.js", "跟进提醒管理")
        Container(user_service, "用户服务", "Node.js", "用户数据管理")
    }

    Container_Boundary(data_layer, "数据层") {
        ContainerDb(local_db, "本地数据库", "SQLite", "离线数据存储")
        ContainerDb(cloud_db, "云端数据库", "PostgreSQL", "用户数据同步")
        ContainerDb(cache, "缓存", "Redis", "会话和临时数据")
        ContainerDb(file_storage, "文件存储", "AWS S3/阿里云OSS", "音频文件存储")
    }

    Rel(user, mobile_app, "使用")
    Rel(mobile_app, api_gateway, "API调用", "HTTPS/REST")
    Rel(api_gateway, auth_service, "认证")
    Rel(api_gateway, speech_service, "转写请求")
    Rel(api_gateway, ai_engine, "分析请求")
    Rel(api_gateway, notification_service, "提醒设置")
    Rel(api_gateway, user_service, "用户管理")

    Rel(mobile_app, local_db, "读写", "本地存储")
    Rel(user_service, cloud_db, "读写", "数据同步")
    Rel(speech_service, file_storage, "存取", "音频文件")
    Rel(ai_engine, cache, "缓存", "分析结果")
```

### Level 3: 核心组件架构

```mermaid
C4Component
    title AI分析引擎组件架构

    Container_Boundary(ai_engine, "AI分析引擎") {
        Component(speech_processor, "语音处理器", "Python/Whisper", "语音转文字，准确率≥95%")
        Component(dialogue_analyzer, "对话分析器", "Python/NLP", "异议识别、话术分析")
        Component(scoring_engine, "评分引擎", "Python/ML", "多维度评分算法")
        Component(suggestion_generator, "建议生成器", "Python/LLM", "个性化建议生成")
        Component(feedback_processor, "反馈处理器", "Python", "用户反馈学习")
        Component(knowledge_retriever, "知识检索器", "Python/RAG", "行业知识智能检索")

        ComponentDb(model_store, "模型存储", "文件系统", "AI模型和权重")
        ComponentDb(knowledge_base, "行业知识库", "向量数据库", "分层行业专业知识")
        ComponentDb(template_store, "话术模板库", "PostgreSQL", "标准化沟通模板")
    }

    Container_Ext(file_storage, "文件存储")
    Container_Ext(cache, "缓存")

    Rel(speech_processor, file_storage, "读取", "音频文件")
    Rel(speech_processor, dialogue_analyzer, "传递", "转写文本")
    Rel(dialogue_analyzer, scoring_engine, "传递", "分析结果")
    Rel(scoring_engine, suggestion_generator, "传递", "评分数据")
    Rel(suggestion_generator, knowledge_base, "查询", "话术模板")
    Rel(feedback_processor, model_store, "更新", "模型优化")
    Rel(suggestion_generator, cache, "缓存", "建议结果")
```

## 🔄 数据流与业务流程

### 核心业务流程

```mermaid
sequenceDiagram
    participant U as 用户
    participant M as 移动应用
    participant A as API网关
    participant S as 语音服务
    participant AI as AI引擎
    participant N as 通知服务

    U->>M: 开始录音
    M->>M: 本地录音存储
    U->>M: 结束录音
    M->>A: 上传音频文件
    A->>S: 语音转写请求
    S->>S: Whisper转写处理
    S->>AI: 发送转写文本
    AI->>AI: 对话分析与评分
    AI->>AI: 生成个性化建议
    AI-->>A: 返回分析结果
    A-->>M: 返回建议和评分
    M->>U: 展示分析结果
    U->>M: 反馈建议有用性
    M->>A: 提交用户反馈
    A->>AI: 更新模型训练数据
    A->>N: 设置跟进提醒
    N-->>M: 推送跟进通知
```

### 数据流架构

```mermaid
flowchart TD
    A[用户录音] --> B[本地存储]
    B --> C[云端上传]
    C --> D[语音转写]
    D --> E[对话分析]
    E --> F[评分计算]
    F --> G[建议生成]
    G --> H[结果缓存]
    H --> I[用户界面]
    I --> J[用户反馈]
    J --> K[模型优化]
    K --> E

    style A fill:#e3f2fd
    style I fill:#e8f5e8
    style K fill:#fff3e0
```

## 🛠️ 技术栈与实现

### 前端技术栈

| 组件 | 技术选型 | 理由 |
|------|----------|------|
| 移动框架 | React Native | 跨平台开发，快速迭代 |
| 状态管理 | Redux Toolkit | 可预测的状态管理 |
| 本地存储 | SQLite + AsyncStorage | 离线支持，轻量级 |
| 音频处理 | react-native-audio-recorder | 原生音频录制能力 |
| 图表组件 | Victory Native | 数据可视化 |

### 后端技术栈

| 组件 | 技术选型 | 理由 |
|------|----------|------|
| API网关 | Node.js + Express | 轻量级，快速开发 |
| 语音服务 | Python + FastAPI | AI/ML生态丰富 |
| AI引擎 | Python + PyTorch | 深度学习框架 |
| 数据库 | PostgreSQL | 关系型数据，ACID特性 |
| 缓存 | Redis | 高性能缓存 |
| 消息队列 | Redis Pub/Sub | 异步处理 |

### AI/ML技术栈

| 组件 | 技术选型 | 版本要求 |
|------|----------|----------|
| 语音识别 | OpenAI Whisper | v3+ |
| 自然语言处理 | spaCy + transformers | 最新稳定版 |
| 对话分析 | 自训练BERT模型 | 中文优化 |
| 建议生成 | GPT-3.5/4 API | 备选本地LLM |
| 知识检索 | ChromaDB + Sentence-BERT | RAG架构 |
| 向量化 | text-embedding-ada-002 | OpenAI嵌入模型 |

## 🧠 行业知识库架构

### 知识库分层设计

```mermaid
graph TD
    A[行业知识库] --> B[通用销售知识]
    A --> C[行业专业知识]
    A --> D[场景化话术]

    B --> E[异议处理标准]
    B --> F[需求挖掘技巧]
    B --> G[成交信号识别]

    C --> H[家装材料知识]
    C --> I[施工流程标准]
    C --> J[产品技术规格]

    D --> K[报价说明模板]
    D --> L[方案解释话术]
    D --> M[跟进沟通模板]
```

### RAG增强生成流程

```mermaid
graph LR
    A[用户对话] --> B[语义理解]
    B --> C[知识检索]
    C --> D[上下文增强]
    D --> E[专业建议生成]

    F[向量知识库] --> C
    G[用户反馈] --> H[知识更新]
    H --> F
```

### 知识库实现策略

| 组件 | 技术选型 | 用途 |
|------|----------|------|
| 向量数据库 | ChromaDB | 语义检索 |
| 检索算法 | 混合检索(语义+关键词) | 提高召回率 |
| 知识更新 | 增量学习 | 持续优化 |
| 质量控制 | 专家审核+用户反馈 | 确保准确性 |

## 🔒 安全与隐私设计

### 数据安全架构

```mermaid
graph TD
    A[用户录音] --> B[客户端加密]
    B --> C[HTTPS传输]
    C --> D[服务端解密]
    D --> E[处理后删除]
    E --> F[结果加密存储]

    G[用户认证] --> H[JWT Token]
    H --> I[API访问控制]
    I --> J[数据权限验证]

    style B fill:#ffebee
    style F fill:#ffebee
    style H fill:#e8f5e8
```

### 隐私保护措施
- **端到端加密**：音频文件在客户端加密，服务端处理后立即删除
- **数据最小化**：只收集必要的业务数据
- **匿名化处理**：用户反馈数据去标识化
- **合规认证**：符合GDPR、CCPA、个保法要求
- **用户控制**：支持数据导出和删除

## 📊 性能与可扩展性

### 性能指标

| 指标 | 目标值 | 监控方式 |
|------|--------|----------|
| 语音转写准确率 | ≥95% | 自动化测试 |
| API响应时间 | ≤30秒 | APM监控 |
| 系统可用性 | ≥99.5% | 健康检查 |
| 并发用户数 | 1000+ | 负载测试 |

### 扩展策略
- **水平扩展**：微服务架构，独立扩展
- **缓存策略**：多层缓存，减少计算负载
- **CDN加速**：静态资源和音频文件分发
- **数据库优化**：读写分离，分库分表

## 🚀 部署与运维

### 部署架构

```mermaid
graph TD
    A[开发环境] --> B[CI/CD Pipeline]
    B --> C[测试环境]
    C --> D[预生产环境]
    D --> E[生产环境]

    F[Docker镜像] --> G[Kubernetes集群]
    G --> H[负载均衡器]
    H --> I[微服务实例]

    J[监控系统] --> K[日志聚合]
    K --> L[告警通知]

    style E fill:#e8f5e8
    style G fill:#e3f2fd
```

### 运维策略
- **容器化部署**：Docker + Kubernetes
- **自动化运维**：GitOps + ArgoCD
- **监控告警**：Prometheus + Grafana
- **日志管理**：ELK Stack
- **备份策略**：自动化数据备份和恢复

## 📈 监控与分析

### 业务指标监控
```mermaid
dashboard
    title CallWise-AI 业务监控仪表盘

    metric "日活用户" {
        value 1250
        trend up
    }

    metric "录音转写成功率" {
        value 96.8%
        trend stable
    }

    metric "AI建议接受率" {
        value 52.3%
        trend up
    }

    metric "平均响应时间" {
        value 18s
        trend down
    }
```

### 技术指标追踪
- **用户行为**：录音频次、功能使用率、留存率
- **系统性能**：响应时间、错误率、资源使用率
- **AI模型**：准确率、置信度、用户满意度
- **业务转化**：免费转付费、功能采用率

---

**文档版本**: v2.0
**创建日期**: 2025-08-06
**最后更新**: 2025-08-06
**下次评审**: 2025-09-06

### 更新记录
- v2.0 (2025-08-06): **完整重构架构设计**
  - 引入C4模型进行系统架构设计
  - 完善技术栈选型和实现细节
  - 增强安全与隐私保护设计
  - 添加性能指标和扩展策略
  - 完善部署运维和监控方案
- v1.0 (2025-08-06): 初始版本