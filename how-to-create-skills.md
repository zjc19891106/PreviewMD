# Claude Code Skills 创建与使用指南

本文档以 `em-restful-api-checker` 为例，详细介绍如何创建和使用 Claude Code 的自定义 Skills。

## 目录

- [什么是 Skills](#什么是-skills)
- [Skills 适用场景](#skills-适用场景)
- [Skills 目录结构](#skills-目录结构)
- [创建 Skill 步骤](#创建-skill-步骤)
- [SKILL.md 文件规范](#skillmd-文件规范)
- [实战示例：em-restful-api-checker](#实战示例em-restful-api-checker)
- [使用 Skills](#使用-skills)
- [最佳实践](#最佳实践)

---

## 什么是 Skills

Skills 是 Claude Code 的扩展能力模块，允许用户定义特定领域的专业知识和工作流程。通过 Skills，你可以：

- 封装特定领域的专业知识（如 API 规范、代码审查标准）
- 定义标准化的工作流程
- 复用检测规则和最佳实践
- 生成结构化的报告

---

## Skills 适用场景

Skills 最适合用于**需要领域知识 + 标准化流程 + 结构化输出**的场景。以下是常见的使用场景分类：

### 1. 代码质量检测类

| Skill 名称 | 用途 | 输出 |
|-----------|------|------|
| `api-checker` | RESTful API 规范检测 | 规范符合度报告 |
| `code-reviewer` | 代码审查 | 审查意见报告 |
| `security-scanner` | 安全漏洞扫描 | 安全风险报告 |
| `sql-analyzer` | SQL 语句分析优化 | 性能优化建议 |
| `dependency-checker` | 依赖版本/漏洞检测 | 依赖健康报告 |

**示例：SQL 分析器**
```yaml
name: sql-analyzer
description: |
  分析 SQL 语句的性能问题，包括：
  - 全表扫描检测
  - 索引使用分析
  - 慢查询识别
  - JOIN 优化建议
```

### 2. 构建与部署类

| Skill 名称 | 用途 | 输出 |
|-----------|------|------|
| `build-doctor` | 构建问题诊断 | 构建错误分析报告 |
| `docker-optimizer` | Dockerfile 优化 | 镜像优化建议 |
| `k8s-validator` | K8s 配置验证 | 配置问题报告 |
| `ci-analyzer` | CI/CD 流水线分析 | 流水线优化建议 |
| `release-notes` | 自动生成发布说明 | Release Notes 文档 |

**示例：构建诊断器**
```yaml
name: build-doctor
description: |
  诊断项目构建失败问题：
  - 依赖冲突分析
  - 编译错误定位
  - 环境配置检查
  - 常见构建问题解决方案
```

### 3. 日志与监控分析类

| Skill 名称 | 用途 | 输出 |
|-----------|------|------|
| `log-analyzer` | 日志分析与问题定位 | 问题根因报告 |
| `error-tracker` | 错误追踪与聚合 | 错误分类报告 |
| `performance-profiler` | 性能日志分析 | 性能瓶颈报告 |
| `access-log-analyzer` | 访问日志分析 | 流量/异常报告 |
| `audit-log-reviewer` | 审计日志审查 | 安全审计报告 |
| `crash-analyzer` | Crash 日志分析 | 崩溃原因报告 |
| `apm-analyzer` | APM 数据分析 | 性能诊断报告 |

**示例：日志分析器**
```yaml
name: log-analyzer
description: |
  分析应用日志，识别问题：
  - 异常堆栈聚合
  - 错误频率统计
  - 时间线关联分析
  - 根因推断
  - 解决方案建议
```

### 3.1 Crash 分析类（重点场景）

针对应用崩溃问题的专项分析能力：

| Skill 名称 | 用途 | 输出 |
|-----------|------|------|
| `crash-analyzer` | 通用 Crash 日志分析 | 崩溃原因报告 |
| `ios-crash-analyzer` | iOS Crash 日志分析 | 符号化分析报告 |
| `android-crash-analyzer` | Android Crash/ANR 分析 | 崩溃分析报告 |
| `jvm-crash-analyzer` | JVM Crash (hs_err) 分析 | JVM 崩溃报告 |
| `native-crash-analyzer` | Native Crash 分析 | 内存/指针问题报告 |
| `oom-analyzer` | OOM 问题分析 | 内存泄漏报告 |

**示例：通用 Crash 分析器**
```yaml
name: crash-analyzer
description: |
  分析应用崩溃日志，定位根因：
  - 堆栈解析与符号化
  - 崩溃类型分类（NPE、OOM、ANR、Native Crash）
  - 崩溃上下文还原
  - 相似崩溃聚合
  - 影响范围评估
  - 修复建议生成
```

**示例：iOS Crash 分析器**
```yaml
name: ios-crash-analyzer
description: |
  分析 iOS 崩溃日志：
  - .crash / .ips 文件解析
  - 符号化（dSYM 匹配）
  - Exception Type 分析（EXC_BAD_ACCESS、SIGABRT 等）
  - 线程状态分析
  - 内存问题检测（野指针、内存越界）
  - 主线程卡死检测
  - 崩溃复现路径推断
```

**示例：Android Crash 分析器**
```yaml
name: android-crash-analyzer
description: |
  分析 Android 崩溃和 ANR：
  - Java/Kotlin 异常堆栈分析
  - Native Crash (tombstone) 分析
  - ANR traces.txt 分析
  - ProGuard/R8 反混淆
  - 设备/系统版本关联
  - 崩溃率趋势分析
  - 热修复建议
```

**示例：JVM Crash 分析器**
```yaml
name: jvm-crash-analyzer
description: |
  分析 JVM 崩溃日志（hs_err_pid.log）：
  - 崩溃原因分类（SIGSEGV、SIGBUS、OOM）
  - 问题代码定位
  - JVM 参数分析
  - 内存区域分析（Heap、Metaspace、Stack）
  - GC 状态分析
  - Native 库问题检测
  - JVM 版本兼容性检查
```

**示例：OOM 分析器**
```yaml
name: oom-analyzer
description: |
  分析内存溢出问题：
  - Heap Dump 分析
  - 内存泄漏对象定位
  - GC Root 链路分析
  - 大对象检测
  - 内存增长趋势分析
  - 泄漏代码定位
  - 修复方案建议
```

### 4. 文档生成类

| Skill 名称 | 用途 | 输出 |
|-----------|------|------|
| `api-doc-generator` | API 文档生成 | OpenAPI/Swagger 文档 |
| `changelog-generator` | 变更日志生成 | CHANGELOG.md |
| `readme-generator` | README 文档生成 | README.md |
| `architecture-doc` | 架构文档生成 | 架构设计文档 |
| `db-doc-generator` | 数据库文档生成 | 数据字典文档 |

**示例：API 文档生成器**
```yaml
name: api-doc-generator
description: |
  从代码自动生成 API 文档：
  - 扫描 Controller 注解
  - 提取请求/响应模型
  - 生成 OpenAPI 3.0 规范
  - 支持 Markdown/HTML 输出
```

### 5. 数据库相关类

| Skill 名称 | 用途 | 输出 |
|-----------|------|------|
| `migration-generator` | 数据库迁移脚本生成 | Migration SQL |
| `schema-diff` | 数据库结构对比 | 差异报告 |
| `index-advisor` | 索引优化建议 | 索引建议报告 |
| `data-masker` | 数据脱敏方案 | 脱敏规则配置 |
| `query-optimizer` | 查询优化分析 | 优化建议报告 |
| `data-anomaly-detector` | 数据异常检测 | 异常数据报告 |
| `data-consistency-checker` | 数据一致性检查 | 一致性报告 |

**示例：索引顾问**
```yaml
name: index-advisor
description: |
  分析数据库查询并提供索引建议：
  - 慢查询日志分析
  - 执行计划解读
  - 索引覆盖度检查
  - 冗余索引识别
  - 索引创建 DDL 生成
```

### 5.1 数据库数据异常分析类（重点场景）

针对数据库数据质量和异常问题的专项分析能力：

| Skill 名称 | 用途 | 输出 |
|-----------|------|------|
| `data-anomaly-detector` | 数据异常检测 | 异常数据报告 |
| `data-consistency-checker` | 数据一致性检查 | 一致性报告 |
| `data-quality-analyzer` | 数据质量分析 | 质量评估报告 |
| `orphan-data-finder` | 孤儿数据检测 | 孤儿记录报告 |
| `duplicate-detector` | 重复数据检测 | 重复记录报告 |
| `data-drift-monitor` | 数据漂移监控 | 漂移分析报告 |
| `foreign-key-validator` | 外键关系验证 | 关系完整性报告 |
| `data-recovery-advisor` | 数据恢复建议 | 恢复方案报告 |

**示例：数据异常检测器**
```yaml
name: data-anomaly-detector
description: |
  检测数据库中的异常数据：
  - 空值/NULL 异常检测
  - 数值范围异常（超出业务边界）
  - 时间字段异常（未来时间、过早时间）
  - 状态字段异常（非法状态值）
  - 金额/数量负值检测
  - 字符串格式异常（邮箱、手机号等）
  - 统计离群值检测
  - 生成异常数据修复 SQL
```

**示例：数据一致性检查器**
```yaml
name: data-consistency-checker
description: |
  检查数据库数据一致性问题：
  - 主从数据一致性检查
  - 跨表数据一致性（如订单金额 vs 明细汇总）
  - 冗余字段一致性（如 count 字段 vs 实际数量）
  - 状态流转一致性（如订单状态 vs 支付状态）
  - 时间逻辑一致性（如创建时间 < 更新时间）
  - 业务规则一致性检查
  - 生成数据修复方案
```

**示例：数据质量分析器**
```yaml
name: data-quality-analyzer
description: |
  全面评估数据质量：
  - 完整性分析（必填字段填充率）
  - 准确性分析（格式、范围校验）
  - 一致性分析（跨表、跨系统）
  - 唯一性分析（重复记录检测）
  - 时效性分析（数据新鲜度）
  - 生成数据质量评分报告
  - 提供数据治理建议
```

**示例：孤儿数据检测器**
```yaml
name: orphan-data-finder
description: |
  检测数据库中的孤儿数据：
  - 无父记录的子表数据
  - 已删除关联的残留数据
  - 软删除不一致的关联数据
  - 业务流程中断产生的孤儿记录
  - 生成清理 SQL（带安全确认）
  - 评估清理影响范围
```

**示例：重复数据检测器**
```yaml
name: duplicate-detector
description: |
  检测和处理重复数据：
  - 精确重复检测（完全相同）
  - 模糊重复检测（相似度匹配）
  - 业务键重复检测
  - 跨表重复检测
  - 重复数据合并建议
  - 生成去重 SQL
  - 保留策略建议（保留最新/最早/最完整）
```

**示例：数据漂移监控器**
```yaml
name: data-drift-monitor
description: |
  监控数据分布变化：
  - 字段值分布变化检测
  - 数据量异常波动检测
  - 新增异常值检测
  - 业务指标异常检测
  - 环比/同比分析
  - 趋势预警
  - 根因分析建议
```

**示例：数据恢复顾问**
```yaml
name: data-recovery-advisor
description: |
  数据误操作恢复建议：
  - 分析误删除/误更新影响范围
  - 从 binlog 提取恢复 SQL
  - 从备份恢复方案
  - 关联数据恢复顺序
  - 数据校验方案
  - 回滚风险评估
```

### 6. 测试相关类

| Skill 名称 | 用途 | 输出 |
|-----------|------|------|
| `test-generator` | 单元测试生成 | 测试代码 |
| `coverage-analyzer` | 测试覆盖率分析 | 覆盖率报告 |
| `mock-generator` | Mock 数据生成 | Mock 配置/数据 |
| `api-test-generator` | API 测试用例生成 | Postman/JMeter 脚本 |
| `e2e-scenario-writer` | E2E 测试场景编写 | 测试场景文档 |

**示例：测试生成器**
```yaml
name: test-generator
description: |
  为指定代码生成单元测试：
  - 分析方法签名和逻辑
  - 生成边界条件测试
  - 生成异常场景测试
  - 支持 JUnit/TestNG/Jest 等框架
```

### 7. 代码迁移与重构类

| Skill 名称 | 用途 | 输出 |
|-----------|------|------|
| `java-version-migrator` | Java 版本升级辅助 | 迁移指南/代码修改 |
| `framework-migrator` | 框架迁移辅助 | 迁移方案报告 |
| `deprecated-finder` | 废弃 API 检测 | 废弃代码报告 |
| `code-modernizer` | 代码现代化重构 | 重构建议 |
| `i18n-extractor` | 国际化文本提取 | i18n 资源文件 |

**示例：Java 版本迁移器**
```yaml
name: java-version-migrator
description: |
  辅助 Java 版本升级（如 8 -> 17）：
  - 检测不兼容 API
  - 识别废弃特性
  - 建议新特性替代方案
  - 生成迁移清单
```

### 8. 配置与环境类

| Skill 名称 | 用途 | 输出 |
|-----------|------|------|
| `config-validator` | 配置文件验证 | 配置问题报告 |
| `env-diff` | 环境配置对比 | 差异报告 |
| `secret-scanner` | 敏感信息扫描 | 泄露风险报告 |
| `config-generator` | 配置文件生成 | 配置文件模板 |
| `feature-flag-analyzer` | 特性开关分析 | 开关状态报告 |

**示例：敏感信息扫描器**
```yaml
name: secret-scanner
description: |
  扫描代码中的敏感信息泄露：
  - API Key / Secret 检测
  - 密码硬编码检测
  - 私钥文件检测
  - 数据库连接串检测
  - 提供修复建议
```

### 9. Git 与版本控制类

| Skill 名称 | 用途 | 输出 |
|-----------|------|------|
| `commit-message` | 规范化提交信息生成 | Commit Message |
| `pr-reviewer` | PR 自动审查 | 审查意见 |
| `branch-analyzer` | 分支状态分析 | 分支健康报告 |
| `merge-conflict-resolver` | 合并冲突辅助解决 | 解决方案建议 |
| `git-history-analyzer` | Git 历史分析 | 代码演进报告 |

**示例：PR 审查器**
```yaml
name: pr-reviewer
description: |
  自动审查 Pull Request：
  - 代码变更分析
  - 规范符合度检查
  - 潜在问题识别
  - 测试覆盖检查
  - 生成审查意见
```

### 10. 项目管理与协作类

| Skill 名称 | 用途 | 输出 |
|-----------|------|------|
| `issue-analyzer` | Issue 分析与分类 | 分类报告 |
| `sprint-reporter` | Sprint 报告生成 | Sprint 总结 |
| `tech-debt-tracker` | 技术债务追踪 | 技术债务报告 |
| `onboarding-guide` | 新人入职指南生成 | 入职文档 |
| `codebase-overview` | 代码库概览生成 | 项目概览文档 |

**示例：技术债务追踪器**
```yaml
name: tech-debt-tracker
description: |
  识别和追踪技术债务：
  - TODO/FIXME 扫描
  - 代码复杂度分析
  - 过时依赖检测
  - 重复代码识别
  - 生成技术债务清单
```

---

### 场景选择指南

| 如果你需要... | 推荐 Skill 类型 |
|--------------|----------------|
| 确保代码符合团队规范 | 代码质量检测类 |
| 快速定位线上问题 | 日志与监控分析类 |
| **分析应用崩溃问题** | **Crash 分析类（3.1）** |
| 自动化文档维护 | 文档生成类 |
| 优化数据库性能 | 数据库相关类 |
| **排查数据异常/不一致** | **数据库数据异常分析类（5.1）** |
| 提高测试覆盖率 | 测试相关类 |
| 技术栈升级 | 代码迁移与重构类 |
| 保障配置安全 | 配置与环境类 |
| 规范化 Git 工作流 | Git 与版本控制类 |
| 项目管理可视化 | 项目管理与协作类 |

---

### 什么场景不适合用 Skills？

Skills 不适合以下场景：

| 场景 | 原因 | 替代方案 |
|------|------|---------|
| 简单的一次性任务 | 创建 Skill 成本高于直接执行 | 直接对话完成 |
| 高度动态的需求 | 规则频繁变化，维护成本高 | 使用 Prompt 模板 |
| 需要实时交互的任务 | Skills 更适合批处理式工作流 | 直接对话 |
| 纯粹的信息查询 | 不需要标准化流程 | 直接提问 |

---

## Skills 目录结构

Claude Code 使用两个目录来管理 Skills：

```
~/.agents/skills/          # Skills 源文件存放目录
├── em-restful-api-checker/
│   └── SKILL.md
├── code-reviewer/
│   └── SKILL.md
└── find-skills/
    └── SKILL.md

~/.claude/skills/          # Skills 符号链接目录（Claude Code 读取）
├── em-restful-api-checker -> ~/.agents/skills/em-restful-api-checker
├── code-reviewer -> ~/.agents/skills/code-reviewer
└── find-skills -> ~/.agents/skills/find-skills
```

### 目录说明

| 目录 | 用途 |
|------|------|
| `~/.agents/skills/` | 存放 Skill 的实际文件，便于版本管理 |
| `~/.claude/skills/` | Claude Code 读取 Skills 的目录，通过符号链接指向实际文件 |

---

## 创建 Skill 步骤

### 步骤 1：创建 Skill 目录

```bash
# 创建 skill 目录
mkdir -p ~/.agents/skills/em-restful-api-checker
```

### 步骤 2：编写 SKILL.md 文件

```bash
# 创建并编辑 SKILL.md
vim ~/.agents/skills/em-restful-api-checker/SKILL.md
```

### 步骤 3：创建符号链接

```bash
# 创建符号链接到 Claude Code 的 skills 目录
ln -sf ~/.agents/skills/em-restful-api-checker ~/.claude/skills/em-restful-api-checker
```

### 步骤 4：验证创建成功

```bash
# 查看 skills 目录
ls -la ~/.claude/skills/

# 输出示例：
# em-restful-api-checker -> /Users/xxx/.agents/skills/em-restful-api-checker
```

---

## SKILL.md 文件规范

### 基本结构

```markdown
---
name: skill-name
description: |
  Skill 的简短描述，说明这个 Skill 的用途和功能。
  支持多行描述。
---

# Skill 标题

详细的 Skill 说明和使用指南。

## 检测范围 / 功能说明

描述 Skill 能做什么。

## 工作流程

描述 Skill 的执行步骤。

## 使用示例

提供使用示例。
```

### Front Matter 说明

SKILL.md 文件开头的 YAML Front Matter 是必需的：

```yaml
---
name: em-restful-api-checker          # Skill 名称（必需）
description: |                         # Skill 描述（必需）
  环信 RESTful API 规范检测工具。
  用于检查 API 设计是否符合规范。
---
```

| 字段 | 必需 | 说明 |
|------|------|------|
| `name` | ✅ | Skill 的唯一标识符，用于调用 |
| `description` | ✅ | Skill 的功能描述，帮助 Claude 理解何时使用此 Skill |

---

## 实战示例：em-restful-api-checker

### 完整的 SKILL.md 结构

```markdown
---
name: em-restful-api-checker
description: |
  环信 RESTful API 规范检测工具。用于检查 API 设计是否符合环信 RESTful API 开发设计规范，
  涵盖 HTTP 状态码、错误码、查询分页、批处理、安全策略、速率限制等方面，
  同时检测常见的服务端开发坑点和性能问题。
---

# 环信 RESTful API 规范检测器

本 Skill 用于对 RESTful API 代码进行全面的规范性检测...

## 检测范围

### 1. HTTP 状态码规范检测
- 成功状态码 (2xx)
- 重定向状态码 (3xx)
- 客户端错误状态码 (4xx)
- 服务端错误状态码 (5xx)

### 2. 错误码规范检测
- 参数验证类 (0001-0099)
- 认证授权类 (0101-0199)
- ...

### 3. 查询规范检测
- 排序规范
- 分页规范
- 搜索规范

### 4. 批处理规范检测
...

### 5. 安全策略检测
...

### 6. 速率限制检测
...

### 7. 服务端常见坑点检测
- 注解缺失问题
- 性能问题（N+1 查询、缺少分页）
- 并发问题
- 资源泄漏
- 序列化问题
- 缓存问题
- 异常处理问题
- API 设计问题

## 检测工作流程

### 1. 确定检测目标
### 2. 代码扫描
### 3. 规范检测
### 4. 坑点检测
### 5. 生成报告

## 使用示例

/em-restful-api-checker
/em-restful-api-checker src/main/java/com/example/controller
```

### Skill 内容设计要点

#### 1. 明确的检测规则

将规范转化为可检测的规则：

```markdown
#### HTTP 状态码检测规则

| 场景 | 正确状态码 | 常见错误 |
|------|-----------|---------|
| 创建资源成功 | 201 Created | 使用 200 OK |
| 删除成功无返回 | 204 No Content | 使用 200 OK |
| 限流触发 | 429 Too Many Requests | 使用 403 或 500 |
```

#### 2. 代码示例（正确 vs 错误）

```markdown
**❌ 错误示例**
```java
@PostMapping("/users")
public User createUser(@RequestBody UserDTO dto) {
    return userService.create(dto); // 缺少 @Validated
}
```

**✅ 正确示例**
```java
@PostMapping("/users")
public User createUser(@Validated @RequestBody UserDTO dto) {
    return userService.create(dto);
}
```
```

#### 3. 结构化的报告模板

```markdown
## 报告结构

# RESTful API 规范检测报告

## 概览
- 检测时间：YYYY-MM-DD HH:mm:ss
- 总体评分：xx/100

## 检测结果汇总
| 类别 | 通过 | 警告 | 错误 |
|------|------|------|------|

## 详细问题列表
### 🔴 严重问题
### 🟡 警告
### 🔵 建议
```

---

## 使用 Skills

### 方式 1：直接调用

在 Claude Code 中使用斜杠命令调用：

```bash
# 调用 skill
/em-restful-api-checker

# 带参数调用
/em-restful-api-checker src/main/java/com/example/controller
```

### 方式 2：自然语言触发

Claude Code 会根据上下文自动识别并使用合适的 Skill：

```
用户：帮我检查一下这个项目的 API 是否符合环信规范
Claude：[自动调用 em-restful-api-checker skill]
```

### 方式 3：明确指定

```
用户：使用 em-restful-api-checker 检查 UserController.java
```

---

## 最佳实践

### 1. 命名规范

```
推荐格式：{团队/项目前缀}-{功能描述}

示例：
- em-restful-api-checker    # 环信 RESTful API 检测
- em-code-reviewer          # 环信代码审查
- my-sql-optimizer          # SQL 优化检测
```

### 2. 描述要清晰

```yaml
# ❌ 不好的描述
description: 检查 API

# ✅ 好的描述
description: |
  环信 RESTful API 规范检测工具。用于检查 API 设计是否符合环信 RESTful API 开发设计规范，
  涵盖 HTTP 状态码、错误码、查询分页、批处理、安全策略、速率限制等方面。
```

### 3. 工作流程要具体

```markdown
## 检测工作流程

### 1. 确定检测目标
- 询问用户要检测的 API 代码路径或模块
- 确认检测范围（全量检测 / 增量检测）

### 2. 代码扫描
1. 扫描 Controller 层代码
2. 扫描 Service 层代码
3. 扫描 DTO/VO 定义

### 3. 生成报告
按照报告模板输出结果
```

### 4. 提供使用示例

```markdown
## 使用示例

# 检测整个项目
/em-restful-api-checker

# 检测指定目录
/em-restful-api-checker src/main/java/com/example/controller

# 检测指定文件
/em-restful-api-checker UserController.java
```

### 5. 版本管理

建议将 `~/.agents/skills/` 目录纳入 Git 版本管理：

```bash
cd ~/.agents
git init
git add skills/
git commit -m "Add em-restful-api-checker skill"
```

---

## 常见问题

### Q: Skill 创建后没有生效？

检查以下几点：
1. 符号链接是否正确创建
2. SKILL.md 文件的 Front Matter 格式是否正确
3. 重启 Claude Code 会话

```bash
# 验证符号链接
ls -la ~/.claude/skills/

# 验证文件内容
cat ~/.claude/skills/em-restful-api-checker/SKILL.md
```

### Q: 如何更新 Skill？

直接编辑源文件即可，符号链接会自动指向最新内容：

```bash
vim ~/.agents/skills/em-restful-api-checker/SKILL.md
```

### Q: 如何删除 Skill？

```bash
# 删除符号链接
rm ~/.claude/skills/em-restful-api-checker

# 删除源文件（可选）
rm -rf ~/.agents/skills/em-restful-api-checker
```

### Q: 如何查看所有可用的 Skills？

```bash
ls ~/.claude/skills/
```

---

## 总结

创建 Claude Code Skill 的核心步骤：

1. **创建目录**：`mkdir -p ~/.agents/skills/{skill-name}`
2. **编写 SKILL.md**：包含 Front Matter 和详细的检测规则/工作流程
3. **创建符号链接**：`ln -sf ~/.agents/skills/{skill-name} ~/.claude/skills/{skill-name}`
4. **使用 Skill**：`/{skill-name}` 或自然语言触发

通过 Skills，你可以将团队的最佳实践、规范要求封装成可复用的检测工具，提升代码质量和开发效率。
