# OpenSpec 迭代演进实战指南

> 基于 PackageIMSDK 项目的真实案例，讲解如何使用 OpenSpec 进行规范驱动开发

## 什么是 OpenSpec？

**OpenSpec** 是一套规范驱动开发（Spec-Driven Development）的方法论和工具，核心理念是：

```
先写规范，再写代码
规范即文档，规范即测试用例
```

### 核心价值

| 传统开发 | OpenSpec 开发 |
|---------|--------------|
| 需求在脑子里 | 需求在 `specs/` 目录 |
| 改动随意提交 | 改动先写 `proposal.md` |
| 文档和代码脱节 | 规范即文档，始终同步 |
| AI 不知道项目背景 | AI 读取 `project.md` 理解上下文 |
| 回顾历史靠 git log | 变更历史在 `changes/archive/` |

---

## 目录结构解析

以 PackageIMSDK 项目为例：

```
openspec/
├── AGENTS.md              # AI 助手指令（告诉 AI 如何使用 OpenSpec）
├── project.md             # 项目上下文（技术栈、约定、约束）
├── specs/                 # 📦 当前真实状态 - 已构建的功能
│   └── [capability]/      # 每个能力一个目录
│       ├── spec.md        # 需求和场景
│       └── design.md      # 技术设计（可选）
└── changes/               # 📝 变更提案 - 计划要做的改动
    ├── [change-name]/     # 每个变更一个目录
    │   ├── proposal.md    # 为什么改、改什么、影响范围
    │   ├── tasks.md       # 实现任务清单
    │   ├── design.md      # 技术决策（可选）
    │   └── specs/         # 增量规范（delta specs）
    │       └── [capability]/
    │           └── spec.md
    └── archive/           # 已完成的变更归档
```

### 关键概念

| 概念 | 位置 | 含义 |
|------|------|------|
| **Capability** | `specs/[name]/` | 一个独立的功能模块 |
| **Spec** | `spec.md` | 需求规范（包含场景） |
| **Change** | `changes/[name]/` | 一次变更提案 |
| **Delta** | `changes/[name]/specs/` | 增量规范（ADDED/MODIFIED/REMOVED） |

---

## 三阶段工作流

### 🔵 Stage 1: 创建变更提案

**什么时候需要创建提案？**

```
✅ 需要创建提案：
- 新增功能或能力
- 破坏性变更（API、数据结构）
- 架构或模式变更
- 性能优化（改变行为）
- 安全模式更新

❌ 不需要创建提案：
- Bug 修复（恢复预期行为）
- 拼写错误、格式、注释
- 依赖更新（非破坏性）
- 配置变更
- 为现有行为补充测试
```

**创建流程：**

```bash
# 1. 了解当前状态
openspec list              # 查看进行中的变更
openspec list --specs      # 查看现有能力

# 2. 选择变更 ID（kebab-case，动词开头）
# 好的命名：add-two-factor-auth, update-login-flow, remove-legacy-api
# 坏的命名：new-feature, changes, update

# 3. 创建目录结构
CHANGE=add-user-management
mkdir -p openspec/changes/$CHANGE/specs/user-auth

# 4. 编写 proposal.md
# 5. 编写 tasks.md
# 6. 编写增量规范 specs/[capability]/spec.md

# 7. 验证
openspec validate $CHANGE --strict
```

---

### 🟢 Stage 2: 实现变更

**实现流程：**

```
1. 阅读 proposal.md     → 理解要构建什么
2. 阅读 design.md       → 了解技术决策（如果有）
3. 阅读 tasks.md        → 获取实现清单
4. 按顺序完成任务       → 逐个实现
5. 确认完成             → 确保所有任务都完成
6. 更新清单             → 将 [ ] 改为 [x]
```

**重要原则：**
- ⚠️ **未经批准不要开始实现** - 提案需要先审核通过
- 按 `tasks.md` 中的顺序执行
- 完成一个任务立即标记为 `[x]`

---

### 🟣 Stage 3: 归档变更

**部署后归档：**

```bash
# 归档变更（会更新 specs/ 目录）
openspec archive add-user-management --yes

# 如果是纯工具类变更（不影响 specs）
openspec archive add-tooling --skip-specs --yes

# 验证归档后的状态
openspec validate --strict
```

**归档后的目录变化：**

```
# 归档前
changes/add-user-management/
specs/                          # 不变

# 归档后
changes/archive/2025-02-04-add-user-management/
specs/user-auth/spec.md         # 已合并增量规范
```

---

## 实战案例：PackageIMSDK

### 案例背景

PackageIMSDK 是一个 IM SDK 自动化打包系统，需要：
- Web 界面接受打包请求
- 自动执行 iOS SDK 构建
- 管理打包产物并支持下载

### Step 1: 创建 project.md

首先定义项目上下文：

```markdown
# Project Context

## Purpose
PackageIMSDK 是一个 IM SDK 自动化打包系统...

## Tech Stack
### 前端
- 语言：TypeScript
- 框架：Vue 3 或 React

### 后端
- 语言：Python 3
- 框架：FastAPI 或 Flask

## Project Conventions
### Code Style
- Python：遵循 PEP 8 规范
- TypeScript：遵循 ESLint + Prettier

## Important Constraints
- 必须在 macOS 上运行
- 需要用户身份执行
- 单进程限制
```

### Step 2: 创建变更提案

创建 `changes/add-sdk-automation-platform/proposal.md`：

```markdown
## Why
当前 IM SDK 的打包流程需要手动执行多个步骤，效率低下且容易出错。

## What Changes
- **新增 Web 前端界面**：提供直观的打包请求界面
- **新增 Python 后端服务**：处理打包请求、管理构建任务
- **新增构建自动化流程**：在 macOS 上自动执行构建脚本
- **新增文件管理系统**：管理构建产物
- **新增一键部署方案**：自动检测重复进程

## Impact
### Affected Specs
- `web-frontend` - 新增 capability
- `python-backend` - 新增 capability
- `build-automation` - 新增 capability
- `file-management` - 新增 capability
- `deployment` - 新增 capability
```

### Step 3: 编写增量规范

创建 `changes/add-sdk-automation-platform/specs/build-automation/spec.md`：

```markdown
## ADDED Requirements

### Requirement: Terminal Window Launch
系统 SHALL 在 macOS 上以用户身份打开新终端窗口执行构建脚本。

#### Scenario: 启动构建脚本
- **WHEN** 系统接收到构建请求
- **THEN** 使用 AppleScript 打开新的 Terminal.app 窗口
- **AND** 在新窗口中执行命令 "cd ~/Desktop && sh buildChat.sh"
- **AND** 记录终端进程 PID

#### Scenario: 终端启动失败
- **WHEN** 尝试启动终端时发生错误
- **THEN** 记录错误日志
- **AND** 标记构建任务为失败状态

### Requirement: Build Process Monitoring
系统 SHALL 监控构建进程的运行状态。

#### Scenario: 检测进程运行中
- **WHEN** 构建脚本启动后
- **THEN** 每 5 秒检查一次进程是否存在
- **AND** 更新构建状态为"构建中"
```

### Step 4: 编写任务清单

创建 `changes/add-sdk-automation-platform/tasks.md`：

```markdown
## 1. 项目初始化
- [ ] 1.1 创建前端项目结构
- [ ] 1.2 创建后端项目结构
- [ ] 1.3 配置代码格式化工具
- [ ] 1.4 设置 Git 仓库

## 2. 后端服务实现
- [ ] 2.1 实现打包请求接收 API
- [ ] 2.2 实现终端窗口打开功能
- [ ] 2.3 实现构建进程监控
- [ ] 2.4 实现构建日志收集
- [ ] 2.5 实现文件压缩功能
- [ ] 2.6 实现文件下载 API

## 3. 前端界面实现
- [ ] 3.1 创建打包请求提交页面
- [ ] 3.2 创建构建状态监控界面
- [ ] 3.3 实现实时状态更新
- [ ] 3.4 实现产物下载功能
```

### Step 5: 验证并实现

```bash
# 验证提案格式正确
openspec validate add-sdk-automation-platform --strict

# 开始实现（按 tasks.md 顺序）
# ... 实现代码 ...

# 完成后归档
openspec archive add-sdk-automation-platform --yes
```

---

## 规范文件格式详解

### Requirement 格式

```markdown
### Requirement: 需求名称
系统 SHALL/MUST 做什么...

#### Scenario: 场景名称
- **WHEN** 触发条件
- **THEN** 预期结果
- **AND** 附加结果
```

**关键规则：**
- 使用 `SHALL` 或 `MUST` 表示强制要求
- 每个 Requirement 必须至少有一个 Scenario
- Scenario 必须使用 `#### Scenario:` 格式（4 个 #）

### Delta 操作类型

| 操作 | 用途 | 示例 |
|------|------|------|
| `## ADDED Requirements` | 新增能力 | 添加新功能 |
| `## MODIFIED Requirements` | 修改行为 | 更改现有功能 |
| `## REMOVED Requirements` | 删除功能 | 废弃旧功能 |
| `## RENAMED Requirements` | 重命名 | 仅改名称 |

### MODIFIED 的正确写法

```markdown
## MODIFIED Requirements

### Requirement: User Login
（必须包含完整的修改后内容，不是增量）

#### Scenario: 成功登录
- **WHEN** 用户提供有效凭证
- **THEN** 返回 JWT token
- **AND** 记录登录时间（新增）
```

⚠️ **常见错误**：只写修改的部分，导致归档时丢失原有内容。

---

## CLI 命令速查

```bash
# 查看状态
openspec list                    # 列出进行中的变更
openspec list --specs            # 列出所有能力
openspec show [item]             # 查看详情

# 验证
openspec validate [change]       # 基础验证
openspec validate [change] --strict  # 严格验证

# 归档
openspec archive <change-id> --yes   # 归档变更
openspec archive <change-id> --skip-specs --yes  # 跳过 specs 更新

# 调试
openspec show [change] --json --deltas-only  # 查看增量解析结果
```

---

## 与 AI 助手协作

### AGENTS.md 的作用

`AGENTS.md` 文件告诉 AI 助手如何使用 OpenSpec：

```markdown
# OpenSpec Instructions

## TL;DR Quick Checklist
- Search existing work: `openspec spec list --long`
- Decide scope: new capability vs modify existing
- Pick a unique `change-id`: kebab-case, verb-led
- Scaffold: proposal.md, tasks.md, design.md, delta specs
- Validate: `openspec validate [change-id] --strict`
- Request approval: Do not start implementation until approved
```

### AI 触发词

当你对 AI 说以下内容时，AI 会进入 OpenSpec 工作流：

```
"帮我创建一个变更提案"
"帮我规划一个变更"
"我想创建一个 spec"
"Help me create a proposal"
```

### AI 工作流程

```
1. AI 读取 project.md 了解项目背景
2. AI 运行 openspec list 查看现有变更
3. AI 运行 openspec list --specs 查看现有能力
4. AI 创建变更目录和文件
5. AI 运行 openspec validate --strict 验证
6. AI 等待你的批准后才开始实现
```

---

## 最佳实践

### 1. 命名规范

```
✅ 好的变更 ID：
- add-two-factor-auth
- update-login-flow
- remove-legacy-api
- refactor-user-service

❌ 坏的变更 ID：
- new-feature
- changes
- update
- fix
```

### 2. 能力划分原则

```
✅ 好的能力划分：
- user-auth（用户认证）
- payment-capture（支付捕获）
- file-management（文件管理）

❌ 坏的能力划分：
- user-auth-and-profile（包含 AND，应该拆分）
- misc-features（太模糊）
```

### 3. 简单优先

```
默认原则：
- 新代码 < 100 行
- 单文件实现，直到证明不够用
- 避免无明确理由的框架
- 选择无聊但可靠的模式

只有在以下情况才增加复杂度：
- 性能数据证明当前方案太慢
- 具体的规模需求（>1000 用户，>100MB 数据）
- 多个已证明的用例需要抽象
```

### 4. 清晰的引用

```markdown
# 代码位置
file.ts:42

# 规范引用
specs/auth/spec.md

# 变更引用
changes/add-user-auth/proposal.md
```

---

## 常见问题

### Q1: 什么时候用 ADDED vs MODIFIED？

```
ADDED：引入全新的、可独立存在的能力
MODIFIED：改变现有需求的行为、范围或验收标准

例如：
- 添加"两步认证"功能 → ADDED
- 修改"登录"功能增加 OTP 验证 → MODIFIED
```

### Q2: 验证失败怎么办？

```bash
# 1. 使用严格模式查看详细错误
openspec validate [change] --strict

# 2. 检查 JSON 输出
openspec show [change] --json --deltas-only

# 3. 常见问题：
# - Scenario 格式错误（必须用 #### Scenario:）
# - 缺少 delta 文件
# - Requirement 没有 Scenario
```

### Q3: 多个能力受影响怎么办？

```
创建多个 delta 文件：

changes/add-2fa-notify/
├── proposal.md
├── tasks.md
└── specs/
    ├── auth/
    │   └── spec.md      # ADDED: Two-Factor Authentication
    └── notifications/
        └── spec.md      # ADDED: OTP Email Notification
```

---

## 总结

```
OpenSpec 核心理念：

1. specs/ = 当前真实状态（已构建）
2. changes/ = 变更提案（计划构建）
3. 先写规范，再写代码
4. 规范即文档，规范即测试用例
5. AI 助手通过 AGENTS.md 理解工作流

三阶段工作流：
Stage 1: 创建变更提案 → proposal.md + tasks.md + delta specs
Stage 2: 实现变更 → 按 tasks.md 顺序执行
Stage 3: 归档变更 → openspec archive
```

---

*基于 PackageIMSDK 项目实战经验整理*
*最后更新：2025-02*
