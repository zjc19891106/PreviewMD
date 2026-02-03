# Claude Code Skills 创建与使用指南

本文档以 `em-restful-api-checker` 为例，详细介绍如何创建和使用 Claude Code 的自定义 Skills。

## 目录

- [什么是 Skills](#什么是-skills)
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
