# OpenSpec 迭代演进实战指南

> 基于 PackageIMSDK 项目的真实案例，讲解如何使用 OpenSpec 进行规范驱动开发

---

# 第一部分：理念篇

## 什么是 OpenSpec？

**OpenSpec** 是一套规范驱动开发（Spec-Driven Development）的方法论和工具。

### 一句话概括

```
先写规范，再写代码。规范即文档，规范即测试用例。
```

### 核心理念

```
┌─────────────────────────────────────────────────────────────┐
│                    OpenSpec 核心思想                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   specs/  ═══════════════════  当前真实状态（已构建的）        │
│                                                             │
│   changes/ ══════════════════  变更提案（计划要做的）          │
│                                                             │
│   归档后 specs/ 自动更新 ════  保持文档与代码同步              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 传统开发 vs OpenSpec 开发

| 维度 | 传统开发 | OpenSpec 开发 |
|------|---------|--------------|
| **需求管理** | 需求在脑子里/聊天记录里 | 需求在 `specs/` 目录，结构化存储 |
| **变更流程** | 想到就改，随意提交 | 先写 `proposal.md`，审批后再改 |
| **文档维护** | 文档和代码经常脱节 | 规范即文档，归档时自动同步 |
| **AI 协作** | AI 不知道项目背景 | AI 读取 `project.md` 理解上下文 |
| **历史追溯** | 靠 git log 和记忆 | 变更历史在 `changes/archive/` |
| **团队协作** | 口头沟通，容易遗漏 | 提案文档化，可追溯可复查 |

### 为什么需要 OpenSpec？

```
问题场景：

❌ "这个功能当初为什么这么设计的？" → 没人记得
❌ "文档和代码对不上" → 文档过时了
❌ "AI 帮我改这个功能" → AI 不知道项目规范
❌ "这次改动会影响什么？" → 只能靠经验猜

OpenSpec 解决方案：

✅ 每个变更都有 proposal.md 记录原因
✅ 归档时自动合并规范到 specs/
✅ AI 读取 AGENTS.md + project.md 理解项目
✅ proposal.md 明确列出影响范围
```

### OpenSpec 的三个核心文件

| 文件 | 作用 | 谁读 |
|------|------|------|
| `project.md` | 项目背景、技术栈、约束 | AI + 开发者 |
| `AGENTS.md` | AI 助手的工作指令 | AI |
| `specs/*/spec.md` | 具体功能的需求规范 | AI + 开发者 |

---

# 第二部分：流程篇

## 目录结构

```
openspec/
├── AGENTS.md              # AI 助手指令
├── project.md             # 项目上下文
├── specs/                 # 📦 当前真实状态（已构建）
│   └── [capability]/
│       ├── spec.md        # 需求和场景
│       └── design.md      # 技术设计（可选）
└── changes/               # 📝 变更提案（计划构建）
    ├── [change-name]/
    │   ├── proposal.md    # 为什么改、改什么
    │   ├── tasks.md       # 实现任务清单
    │   ├── design.md      # 技术决策（可选）
    │   └── specs/         # 增量规范
    │       └── [capability]/
    │           └── spec.md
    └── archive/           # 已完成的变更
```

## 三阶段工作流

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  Stage 1         │     │  Stage 2         │     │  Stage 3         │
│  创建变更提案     │ ──► │  实现变更         │ ──► │  归档变更         │
│                  │     │                  │     │                  │
│  proposal.md     │     │  按 tasks.md     │     │  openspec        │
│  tasks.md        │     │  顺序实现        │     │  archive         │
│  delta specs     │     │                  │     │                  │
└──────────────────┘     └──────────────────┘     └──────────────────┘
```

---

## Stage 1: 创建变更提案

### 什么时候需要创建提案？

```
✅ 需要创建提案：
├── 新增功能或能力
├── 破坏性变更（API、数据结构）
├── 架构或模式变更
├── 性能优化（改变行为）
└── 安全模式更新

❌ 不需要创建提案：
├── Bug 修复（恢复预期行为）
├── 拼写错误、格式、注释
├── 依赖更新（非破坏性）
├── 配置变更
└── 为现有行为补充测试
```

### 创建流程演示

```bash
# Step 1: 了解当前状态
openspec list              # 查看进行中的变更
openspec list --specs      # 查看现有能力

# Step 2: 创建变更目录
CHANGE=add-user-management
mkdir -p openspec/changes/$CHANGE/specs/user-auth

# Step 3: 编写核心文件
# - proposal.md（必需）
# - tasks.md（必需）
# - specs/[capability]/spec.md（必需）
# - design.md（可选，复杂变更时需要）

# Step 4: 验证
openspec validate $CHANGE --strict
```

### proposal.md 模板

```markdown
## Why
当前 IM SDK 的打包流程需要手动执行多个步骤，效率低下且容易出错。

## What Changes
- **新增 Web 前端界面**：提供直观的打包请求界面
- **新增 Python 后端服务**：处理打包请求、管理构建任务
- **新增构建自动化流程**：在 macOS 上自动执行构建脚本
- **BREAKING**：废弃旧的手动打包脚本

## Impact
### Affected Specs
- `web-frontend` - 新增 capability
- `python-backend` - 新增 capability
- `build-automation` - 新增 capability

### Affected Code
- 前端代码位于 `/frontend` 目录
- 后端代码位于 `/backend` 目录
```

### tasks.md 模板

```markdown
## 1. 项目初始化
- [ ] 1.1 创建前端项目结构
- [ ] 1.2 创建后端项目结构
- [ ] 1.3 配置代码格式化工具

## 2. 后端服务实现
- [ ] 2.1 实现打包请求接收 API
- [ ] 2.2 实现终端窗口打开功能
- [ ] 2.3 实现构建进程监控

## 3. 前端界面实现
- [ ] 3.1 创建打包请求提交页面
- [ ] 3.2 创建构建状态监控界面
```

### spec.md（增量规范）模板

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
```

---

## Stage 2: 实现变更

### 实现流程

```
┌─────────────────────────────────────────────────────────────┐
│                     实现阶段工作流                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. 阅读 proposal.md  ────────►  理解要构建什么               │
│                                                             │
│  2. 阅读 design.md    ────────►  了解技术决策（如果有）        │
│                                                             │
│  3. 阅读 tasks.md     ────────►  获取实现清单                 │
│                                                             │
│  4. 按顺序完成任务    ────────►  逐个实现                     │
│                                                             │
│  5. 更新清单          ────────►  将 [ ] 改为 [x]             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 重要原则

```
⚠️ 未经批准不要开始实现
   提案需要先审核通过

⚠️ 按 tasks.md 中的顺序执行
   任务之间可能有依赖关系

⚠️ 完成一个任务立即标记
   保持进度可见
```

---

## Stage 3: 归档变更

### 归档流程

```bash
# 标准归档（会更新 specs/ 目录）
openspec archive add-user-management --yes

# 纯工具类变更（不影响 specs）
openspec archive add-tooling --skip-specs --yes

# 验证归档后的状态
openspec validate --strict
```

### 归档前后对比

```
归档前：
├── changes/add-user-management/    # 变更提案
│   ├── proposal.md
│   ├── tasks.md
│   └── specs/user-auth/spec.md     # 增量规范
└── specs/                          # 不变

归档后：
├── changes/archive/2025-02-04-add-user-management/  # 已归档
└── specs/user-auth/spec.md         # ✅ 已合并增量规范
```

---

## CLI 命令速查

```bash
# 📋 查看状态
openspec list                    # 列出进行中的变更
openspec list --specs            # 列出所有能力
openspec show [item]             # 查看详情

# ✅ 验证
openspec validate [change]       # 基础验证
openspec validate [change] --strict  # 严格验证（推荐）

# 📦 归档
openspec archive <change-id> --yes        # 归档变更
openspec archive <change-id> --skip-specs --yes  # 跳过 specs 更新

# 🔍 调试
openspec show [change] --json --deltas-only  # 查看增量解析结果
```

---

## 与 AI 助手协作

### AGENTS.md 的作用

```
AGENTS.md 告诉 AI：
1. 如何理解 OpenSpec 结构
2. 何时创建提案
3. 如何验证规范
4. 工作流程是什么
```

### AI 触发词

```
当你对 AI 说以下内容时，AI 会进入 OpenSpec 工作流：

"帮我创建一个变更提案"
"帮我规划一个变更"
"我想创建一个 spec"
"Help me create a proposal"
```

### AI 工作流程

```
1. AI 读取 project.md     →  了解项目背景
2. AI 运行 openspec list  →  查看现有变更
3. AI 创建变更目录和文件   →  scaffold
4. AI 运行 validate       →  验证格式
5. AI 等待你的批准        →  不自动实现
```

---

# 第三部分：避坑指南

## 🚨 格式类错误

### 坑 1：Scenario 格式错误

```markdown
❌ 错误写法（会导致解析失败）：
- **Scenario: 用户登录**
**Scenario**: 用户登录
### Scenario: 用户登录

✅ 正确写法（必须用 4 个 #）：
#### Scenario: 用户登录
- **WHEN** 用户输入凭证
- **THEN** 验证成功
```

### 坑 2：Requirement 没有 Scenario

```markdown
❌ 错误（缺少 Scenario）：
### Requirement: User Login
系统 SHALL 支持用户登录。

✅ 正确（至少一个 Scenario）：
### Requirement: User Login
系统 SHALL 支持用户登录。

#### Scenario: 成功登录
- **WHEN** 用户提供有效凭证
- **THEN** 返回 JWT token
```

### 坑 3：Delta 文件位置错误

```
❌ 错误位置：
changes/add-feature/spec.md          # 直接放在变更目录下

✅ 正确位置：
changes/add-feature/specs/auth/spec.md   # 必须在 specs/ 子目录
```

---

## 🚨 内容类错误

### 坑 4：MODIFIED 只写增量

```markdown
❌ 错误（只写修改部分，归档后丢失原有内容）：
## MODIFIED Requirements
### Requirement: User Login
新增 OTP 验证。

✅ 正确（包含完整的修改后内容）：
## MODIFIED Requirements
### Requirement: User Login
系统 SHALL 支持用户登录，包含密码验证和 OTP 验证。

#### Scenario: 成功登录
- **WHEN** 用户提供有效凭证
- **THEN** 发送 OTP 到用户手机
- **AND** 用户输入正确 OTP 后返回 JWT token
```

### 坑 5：ADDED vs MODIFIED 选错

```
❓ 什么时候用 ADDED？
   引入全新的、可独立存在的能力
   例：添加"两步认证"功能

❓ 什么时候用 MODIFIED？
   改变现有需求的行为、范围或验收标准
   例：修改"登录"功能增加 OTP 验证
```

### 坑 6：变更 ID 命名不规范

```
❌ 坏的命名：
- new-feature      # 太模糊
- changes          # 无意义
- update           # 不具体
- fix              # 应该直接修 bug

✅ 好的命名（动词开头 + 具体描述）：
- add-two-factor-auth
- update-login-flow
- remove-legacy-api
- refactor-user-service
```

---

## 🚨 流程类错误

### 坑 7：未审批就开始实现

```
❌ 错误流程：
写 proposal → 直接开始写代码

✅ 正确流程：
写 proposal → 验证 → 请求审批 → 审批通过 → 开始实现
```

### 坑 8：不按 tasks.md 顺序执行

```
❌ 错误：跳着做，漏掉依赖任务

✅ 正确：严格按顺序执行，完成一个标记一个
```

### 坑 9：归档前不验证

```bash
# ❌ 直接归档
openspec archive my-change --yes

# ✅ 先验证再归档
openspec validate my-change --strict
openspec archive my-change --yes
```

---

## 🔧 常见问题排查

### 验证失败怎么办？

```bash
# Step 1: 使用严格模式查看详细错误
openspec validate [change] --strict

# Step 2: 检查 JSON 输出定位问题
openspec show [change] --json --deltas-only

# Step 3: 常见问题检查清单
□ Scenario 是否用 #### 格式？
□ 每个 Requirement 是否有 Scenario？
□ delta 文件是否在 specs/ 子目录？
□ 文件是否有 ## ADDED/MODIFIED/REMOVED 标题？
```

### 多个能力受影响怎么办？

```
创建多个 delta 文件，每个能力一个：

changes/add-2fa-notify/
├── proposal.md
├── tasks.md
└── specs/
    ├── auth/
    │   └── spec.md      # 认证相关变更
    └── notifications/
        └── spec.md      # 通知相关变更
```

---

## 📋 检查清单

### 创建提案前

```
□ 运行 openspec list 确认无冲突变更
□ 运行 openspec list --specs 了解现有能力
□ 确定是新增能力还是修改现有能力
□ 选择规范的变更 ID（动词开头，kebab-case）
```

### 提交审批前

```
□ proposal.md 包含 Why / What Changes / Impact
□ tasks.md 任务清单完整、可执行
□ 每个 Requirement 至少有一个 Scenario
□ Scenario 使用 #### 格式
□ openspec validate --strict 通过
```

### 归档前

```
□ tasks.md 所有任务已完成 [x]
□ 代码已部署/合并
□ openspec validate --strict 通过
□ 确认 specs/ 更新正确
```

---

## 总结

```
┌─────────────────────────────────────────────────────────────┐
│                    OpenSpec 核心要点                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  理念：先写规范，再写代码                                     │
│                                                             │
│  结构：specs/ = 已构建  |  changes/ = 计划构建               │
│                                                             │
│  流程：创建提案 → 审批 → 实现 → 归档                          │
│                                                             │
│  协作：AI 通过 AGENTS.md + project.md 理解项目               │
│                                                             │
│  避坑：Scenario 用 ####  |  MODIFIED 写完整内容              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

*基于 PackageIMSDK 项目实战经验整理*
*最后更新：2025-02*
