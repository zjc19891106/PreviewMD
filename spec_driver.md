# Spec-Driven AI 开发：Spec Kit vs OpenSpec 方法论与实践指南

本文总结两种主流 规范驱动开发（Spec-Driven Development, SDD） 工具体系在 大模型 CLI 工具链 场景下的应用方法论、Prompt 结构方式以及复杂项目中的防误生成与验证机制。

涉及项目：
•    [Spec Kit](https://github.com/github/spec-kit)
•    [OpenSpec](https://github.com/Fission-AI/OpenSpec)

⸻

## 一、核心思想：为什么要 Spec-Driven

传统 AI 编码问题：


| 问题         | 原因                            |
| ------------ | ------------------------------- |
| 生成漂移     | Prompt 是叙事式的，不是结构化的 |
| 需求走样     | 没有“可检查的需求合同”        |
| 难以复审     | 输出是自然语言，不是工程资产    |
| 多轮协作失控 | 模型每轮都可能改变假设          |

Spec-Driven Development 的核心理念：

在 AI 写代码之前，先写“结构化规范”，并让 AI 只围绕规范行动。

⸻

## 二、两种方法论差异


| 维度       | Spec Kit                                         | OpenSpec                                                    |
| ---------- | ------------------------------------------------ | ----------------------------------------------------------- |
| 方法论核心 | 流程驱动（Workflow First）                       | 规范驱动（Spec as Source of Truth）                         |
| 组织方式   | 分阶段产出（spec → plan → tasks → implement） | 每次改动一个 change（proposal → design → tasks → specs） |
| 防漂移手段 | 阶段准入口（Phase Gates）                        | 规范验证（Validate）+ 漂移检测（Verify）                    |
| 适用场景   | 从 0 到 1 新功能构建                             | 大型项目持续演进（增量式变更）                              |

⸻

## 三、Prompt 与规范片段的结构化方法

### 两种方法论的文件结构对比


| 维度         | Spec Kit                                                                                 | OpenSpec                                                                                                         |
| ------------ | ---------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| 核心文件     | Constitution（治理约束）<br/>Spec（需求规格）<br/>Plan（技术方案）<br/>Tasks（任务拆解） | proposal.md（变更提案）<br/>design.md（设计文档，可选）<br/>tasks.md（任务列表）<br/>specs/*/spec.md（规格增量） |
| Constitution | Non-Negotiables（不可协商约束）<br/>Quality Gates（质量门槛）                            | 无直接对应概念                                                                                                   |
| Spec/规格    | Requirement + Scenario                                                                   | ADDED/MODIFIED/REMOVED Requirements<br/>每个 Requirement 至少一个 Scenario                                       |
| Plan/设计    | Architecture Choice<br/>Risks<br/>Constitution Check                                     | design.md（Context / Goals / Decisions / Risks / Migration Plan / Open Questions）                               |
| Tasks        | DoD（Definition of Done）                                                                | 标准 Markdown 任务列表                                                                                           |
| 规范演进方式 | 分阶段产出，阶段门控制                                                                   | 增量变更 + archive 归档到 specs/                                                                                 |
| 验证机制     | Constitution Check                                                                       | `openspec validate --strict`<br/>`openspec verify`                                                               |

方法论本质：

- 模型只能在模板规则内填写内容，不能自由发挥。

## 四、AI Agent 如何与规范互动

Spec Kit 流程

1. 读取 constitution
2. 生成 spec
3. 基于 spec 生成 plan
4. 基于 plan 拆 tasks
5. 只允许根据 tasks 实现

关键规则：
•    不允许跳过阶段
•    plan 必须包含 Constitution Check
•    tasks 必须带 DoD（完成定义）

⸻

OpenSpec 流程

1. 创建 change（变更提案）
2. 写 proposal → design → specs delta
3. validate 规范完整性
4. 按 tasks 执行
5. verify 检测实现与规范是否漂移
6. 归档 change，更新系统规格

关键规则：
•    实现必须对应 tasks 条目
•    代码变更必须能映射回 specs
•    不允许“隐形需求”

⸻

### OpenSpec 高频坑点与防范

#### 高频坑点

- Scenario 格式必须是 `#### Scenario: ...`（4 个 `#`），否则校验通过但解析为空或失败。
- MODIFIED 必须粘贴完整 Requirement 块再改，否则归档时会丢历史细节。
- `specs/` 目录里是"当前真相"，不允许出现 ADDED/MODIFIED/REMOVED 章节，只能是 Requirements。
- 同一能力不要拆成多个 spec 文件，保持 `specs/<capability>/spec.md` 单一入口。
- Capability 命名必须稳定（kebab-case），变更和真相用同一命名，否则归档映射失败。
- 变更里每条 Requirement 至少一个 Scenario，否则 `openspec validate --strict` 会直接报错。
- 场景标题不要用粗体或列表假装"Scenario"（必须用 `####` 标题）。
- 没明确需求就不要设计过度（KISS / YAGNI）。
- 不要实现未在 `tasks.md` 或规格里明确的功能（DRY / SOLID）。

#### 流程坑点

- 未经 proposal 批准就直接实现：流程上违规，演示时要强调"先 proposal 再实现"。
- 演示"变更 → 归档 → 规格更新"时，容易忘记 `openspec archive` 会改动 `specs/`。
- 只展示 `changes/` 不展示 `specs/`，听众容易误以为 changes 就是最终真相。

#### 项目内适配坑点

- 当 `openspec/specs/` 为空时，`openspec list --specs` 不会显示内容，演示前要准备好真实规格（例如先补齐示例）。
- 使用已有的 `changes/add-sdk-automation-platform` 做演示时，要强调：这是变更草案而非真相。
- 你已有的 `specs/*` 是从增量转正的教学素材；真实项目应通过 `archive` 产生，而不是手工复制。

#### 推荐演示步骤（简版）

1. 执行 `openspec list --specs`，展示当前"真相"。
2. 执行 `openspec list`，展示当前变更列表。
3. 打开 `changes/.../specs/<capability>/spec.md` 对比 `specs/<capability>/spec.md`。
4. 运行 `openspec validate <change> --strict`，强调格式与结构的刚性约束。
5. 说明 `openspec archive` 才是"真相落地"的路径。

⸻

五、复杂项目中如何防止误生成

Spec Kit 防漂移策略


| 问题             | 对策                             |
| ---------------- | -------------------------------- |
| 模型边写边改需求 | 严格阶段门，不允许直接 implement |
| 模型补设计       | 未确定项必须生成 Spike Task      |
| 模型扩大范围     | spec 必须写 Out of Scope         |

⸻

OpenSpec 防漂移策略


| 问题             | 对策                             |
| ---------------- | -------------------------------- |
| 实现偏离规范     | validate --strict 检查规范完整性 |
| 规范与代码不同步 | verify 检测 drift                |
| 需求被隐式修改   | 所有变更必须是 specs delta       |

⸻

六、结合两者构建 AI CLI 工具的最佳实践

如果你在做 “大模型 CLI + 两段提示词辅助” 的工具，推荐组合策略：

1️⃣ 用 Spec Kit 思路控制生成流程

规范阶段 → 计划阶段 → 任务阶段 → 执行阶段

执行前必须具备完整 spec/plan/tasks。

2️⃣ 用 OpenSpec 思路做结构化校验

为规范设计 schema 校验规则，例如：
•    每个 Requirement 必须有 Scenario
•    每个 Task 必须有 DoD
•    Plan 必须包含风险分析
•    Spec 必须定义 Out of Scope

3️⃣ 增加 CLI 执行护栏


| 阶段   | 护栏机制                                 |
| ------ | ---------------------------------------- |
| 执行前 | 计算 spec/plan/tasks hash 防止被改写     |
| 执行中 | 每完成 task 强制回写状态                 |
| 执行后 | 自动运行 verify（测试 + 规范一致性检查） |

⸻

七、总结


| 目标                | 推荐方法                           |
| ------------------- | ---------------------------------- |
| 降低 AI 乱写代码    | 使用 Spec Kit 的阶段门             |
| 保证长期一致性      | 使用 OpenSpec 的规格增量与验证     |
| 做 CLI 自动动化工具 | 模板化规范 + 结构化校验 + 执行护栏 |

核心认知升级：

Prompt 不是“让 AI 写代码的指令”，
Prompt 是“让 AI 在合同框架内执行工程任务的协议”。
