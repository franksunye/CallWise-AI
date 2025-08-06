# CallWise-AI 文档指南

## 📋 文档编码规则

本项目的文档采用两位数字编码系统，便于阅读和管理：

### 编码规则
- **第一位数字**: 表示文档阶段/类别
- **第二位数字**: 表示该阶段内的顺序

### 文档分类

#### 项目概述和基础信息
- `index.html` - 项目首页
- `documentation-guide.md` - 本文档指南（规范文档，不使用编号前缀）

#### 0x - 市场分析和机会评估
- `00-opportunity-assessment.md` - 机会评估文档

#### 1x - 产品设计和MVP
- `10-mvp-design.md` - MVP设计文档
- `11-backlog.md` - 产品Backlog

#### 2x - 技术架构和实现
- `20-architecture.md` - 系统架构文档

#### 3x - 商业化和策略
- `30-business-strategy.md` - 商业化策略文档

## 📚 文档阅读顺序建议

### 新读者推荐顺序
1. `index.html` - 了解项目概览
2. `00-opportunity-assessment.md` - 理解市场机会
3. `10-mvp-design.md` - 了解产品设计
4. `11-backlog.md` - 查看开发计划
5. `20-architecture.md` - 了解技术架构
6. `30-business-strategy.md` - 了解商业化策略

### 开发团队推荐顺序
1. `10-mvp-design.md` - 产品设计
2. `11-backlog.md` - 开发任务
3. `20-architecture.md` - 技术实现
4. `00-opportunity-assessment.md` - 市场背景
5. `30-business-strategy.md` - 商业目标

## 🔄 文档更新规则

### 版本控制
- 所有文档使用Git进行版本控制
- 重要更新需要添加版本号和更新日期
- 文档变更需要在提交信息中说明

### 命名规范
- 新文档按照编码规则命名
- 文件名使用kebab-case格式
- 中文文档使用.md扩展名

## 📝 文档模板

### 标准文档结构
```markdown
# 文档标题

## 概述
文档简介和目的

## 主要内容
具体内容...

## 总结
关键要点...

---
**文档版本**: v1.0  
**创建日期**: YYYY-MM-DD  
**下次更新**: YYYY-MM-DD
```

---

**文档版本**: v1.0  
**创建日期**: 2025-08-06  
**下次更新**: 2025-09-06 