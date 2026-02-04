# AI CLI 工具新手完全指南

> Claude Code / OpenAI Codex CLI / Gemini CLI 从入门到精通

本指南面向刚接触 AI 编程助手的开发者，帮助你从零开始掌握主流 AI CLI 工具的安装、配置和使用。

## 目录

- [为什么选择 CLI 而不是 IDE？](#为什么选择-cli-而不是-ide)
- [主流 AI CLI 工具对比](#主流-ai-cli-工具对比)
- [Claude Code 完全指南](#claude-code-完全指南)
- [OpenAI Codex CLI 完全指南](#openai-codex-cli-完全指南)
- [Gemini CLI 完全指南](#gemini-cli-完全指南)
- [Kiro CLI 完全指南](#kiro-cli-完全指南)
- [GitHub Copilot CLI 完全指南](#github-copilot-cli-完全指南)
- [OpenCode 完全指南](#opencode-完全指南)
- [Aider 完全指南](#aider-完全指南)
- [国产 CLI 工具](#国产-cli-工具)
- [代理配置（翻墙）](#代理配置翻墙)
- [多任务并行工作流](#多任务并行工作流)
- [CLI 进阶技巧](#cli-进阶技巧)
- [常见问题解答](#常见问题解答)

---

## 为什么选择 CLI 而不是 IDE？

### CLI vs AI IDE 对比

| 特性 | AI CLI 工具 | AI IDE（Cursor/Windsurf） |
|------|------------|--------------------------|
| **资源占用** | 极低（终端即可） | 高（完整 IDE + AI 服务） |
| **启动速度** | 秒级启动 | 需要加载整个 IDE |
| **上下文控制** | 精确控制，按需加载 | 自动加载，可能冗余 |
| **多任务并行** | 轻松开多个终端窗口 | 需要开多个 IDE 实例 |
| **自动化能力** | 强（可脚本化、管道化） | 弱（GUI 操作为主） |
| **远程开发** | 原生支持（SSH 即可） | 需要额外配置 |
| **定制化** | 高度可定制（hooks/skills） | 受限于 IDE 插件系统 |
| **学习曲线** | 稍陡（需要终端基础） | 平缓（GUI 友好） |

### CLI 的核心优势

#### 1. 🚀 轻量高效
```bash
# CLI 启动只需一条命令
claude

# 而 IDE 需要：启动应用 → 加载项目 → 等待索引 → 启动 AI 服务
```

#### 2. 🎯 精确的上下文控制
```bash
# CLI 可以精确指定上下文
claude --context "只看 src/api 目录"

# IDE 通常会自动加载整个项目，消耗更多 token
```

#### 3. 🔄 多任务并行
```bash
# 终端 1：重构代码
claude "重构 UserService"

# 终端 2：写测试
claude "为 UserService 写单元测试"

# 终端 3：修复 bug
claude "修复登录超时问题"

# 三个任务同时进行，互不干扰
```

#### 4. 🤖 自动化集成
```bash
# 可以集成到 CI/CD
claude --print "检查这个 PR 的代码质量" | tee review.md

# 可以管道化
git diff | claude "解释这些改动"

# 可以脚本化
for file in src/*.ts; do
  claude "优化 $file 的性能"
done
```

### 什么时候用 IDE 更好？

- 刚接触编程的新手（GUI 更友好）
- 需要实时代码补全（IDE 体验更流畅）
- 团队统一使用某个 IDE
- 不想折腾配置

---

## 主流 AI CLI 工具对比

| 工具 | 开发商 | 默认模型 | 特点 | 定价 |
|------|--------|---------|------|------|
| **Claude Code** | Anthropic | Claude Sonnet/Opus | 最强代码理解，支持 200K 上下文 | API 按量付费，或者月度套餐 |
| **Codex CLI** | OpenAI | GPT-4o/o1 | 多模型支持，生态丰富 | API 按量付费，或者月度套餐 |
| **Gemini CLI** | Google | Gemini 2.5 Pro | 免费额度大，支持多模态 | 免费 + API |
| **Kiro CLI** | AWS/Amazon | Claude 系列 | Spec 驱动开发，Agent Hooks，MCP 支持 | AWS 订阅 |
| **Copilot CLI** | GitHub | 多模型 | GitHub 深度集成，PR/Issues 管理 | GitHub Copilot 订阅 |
| **OpenCode** | 开源社区 | 多模型 | Go 编写，轻量快速，支持 LSP | 仅 API 费用 |
| **Aider** | 开源社区 | 多模型 | Git 集成强，支持多文件编辑 | 仅 API 费用 |

### 选择建议

```
新手入门 → Gemini CLI（免费额度大）
专业开发 → Claude Code（代码理解最强）
AWS 生态 → Kiro CLI（深度 AWS 集成）
GitHub 用户 → Copilot CLI（GitHub 深度集成）
多模型需求 → Codex CLI / OpenCode
预算有限 → Gemini CLI + Aider（开源 + 免费额度）
极致轻量 → OpenCode（Go 编写，启动快）
Git 重度用户 → Aider（Git 集成最强）
```
### LSP?

  LSP 是一种标准化协议，让代码编辑器能够获得智能代码功能（如自动补全、跳转定义、查找引用等），而无需为每种语言单独开发插件。

  工作原理

  ┌─────────────────┐                    ┌─────────────────┐
  │   编辑器/IDE     │  ◄── LSP 协议 ──►  │   语言服务器     │
  │  (VS Code等)    │                    │  (分析代码)      │
  └─────────────────┘                    └─────────────────
 对比：
  - 没有 LSP：AI 只能通过文本分析猜测代码结构
  - 有 LSP：AI 能获得编译器级别的代码理解
  
### 📊 七大 CLI 工具全面横向对比

#### 基础信息对比

| 维度 | Claude Code | Codex CLI | Gemini CLI | Kiro CLI | Copilot CLI | OpenCode | Aider |
|------|-------------|-----------|------------|----------|-------------|----------|-------|
| **开发商** | Anthropic | OpenAI | Google | AWS/Amazon | GitHub | 开源社区 | 开源社区 |
| **编写语言** | TypeScript | TypeScript | TypeScript | TypeScript | TypeScript | Go | Python |
| **默认模型** | Claude Sonnet | GPT-4o | Gemini 2.5 Pro | Claude 系列 | 多模型 | 多模型 | 多模型 |
| **上下文窗口** | 200K tokens | 128K tokens | 1M tokens | 200K tokens | 取决于模型 | 取决于模型 | 取决于模型 |
| **开源** | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ |

#### 安装与配置

| 维度 | Claude Code | Codex CLI | Gemini CLI | Kiro CLI | Copilot CLI | OpenCode | Aider |
|------|-------------|-----------|------------|----------|-------------|----------|-------|
| **安装方式** | npm/brew | npm/brew | npm/brew/curl | curl/brew | npm | brew/go | pip/brew |
| **认证方式** | API Key+订阅 | API Key+订阅 | Google 账号/API Key | AWS 账号/浏览器登录 | GitHub 登录 | API Key+订阅 | API Key+订阅 |
| **配置文件** | ~/.claude/ | ~/.codex/ | ~/.gemini/ | ~/.kiro/ | ~/.copilot/ | ~/.config/opencode/ | ~/.aider.conf.yml |
| **项目配置** | CLAUDE.md | instructions.md | GEMINI.md | KIRO.md | copilot-instructions.md | prompts/ | .aider.conf.yml |

#### 功能特性对比

| 功能 | Claude Code | Codex CLI | Gemini CLI | Kiro CLI | Copilot CLI | OpenCode | Aider |
|------|:-----------:|:---------:|:----------:|:--------:|:-----------:|:--------:|:-----:|
| **多模型支持** | ⭐⭐ | ⭐⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Git 集成** | ⭐⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **多文件编辑** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **代码理解** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| **多模态** | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ | ⭐⭐ |
| **插件/扩展** | Skills/MCP | ❌ | ❌ | Agent Hooks/MCP | MCP | oh-my-opencode | ❌ |
| **LSP 支持** | ✅ | ❌ | ❌ | ❌ | ❌ | ✅ | ❌ |
| **沙盒模式** | ❌ | ❌ | ✅ | ❌ | ❌ | ❌ | ❌ |
| **语音输入** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| **Web UI** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| **本地模型** | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ Ollama | ✅ Ollama |
| **Spec 驱动开发** | ❌ | ❌ | ❌ | ✅ | ❌ | ❌ | ❌ |
| **持久化聊天历史** | ✅ | ❌ | ❌ | ✅ | ✅ | ❌ | ❌ |
| **PR/Issues 管理** | ❌ | ❌ | ❌ | ❌ | ✅ | ❌ | ❌ |

#### 斜杠命令数量

| 工具 | 命令数量 | 特色命令 |
|------|:--------:|----------|
| **Claude Code** | 30+ | `/compact`, `/memory`, `/mcp`, `/skills` |
| **Codex CLI** | 21 | `/approval`, `/undo`, `/run` |
| **Gemini CLI** | 20+ | `/sandbox`, `/image`, `/export` |
| **Kiro CLI** | 15+ | `/context`, `/model`, `/help`, `/clear` |
| **Copilot CLI** | 15+ | `/login`, `/mcp`, `/add-dir`, `/list-dirs` |
| **OpenCode** | 21 | `/provider`, `/tokens`, `/cost` |
| **Aider** | 27 | `/architect`, `/voice`, `/web`, `/map` |

#### 定价对比

| 工具 | 定价模式 | 免费额度 | 典型费用（每百万 token） |
|------|----------|----------|------------------------|
| **Claude Code** | API 按量 | 无 | Sonnet: $3/$15, Opus: $15/$75 |
| **Codex CLI** | API 按量 | 无 | GPT-4o: $2.5/$10 |
| **Gemini CLI** | 免费+API | 1500次/天 | Pro: $1.25/$5 |
| **Kiro CLI** | AWS 订阅 | 有限免费试用 | 包含在 AWS 订阅中 |
| **Copilot CLI** | 订阅制 | 无 | $10-19/月（个人/商业） |
| **OpenCode** | 仅 API | 取决于提供商 | 取决于选择的模型 |
| **Aider** | 仅 API | 取决于提供商 | 取决于选择的模型 |

#### 优缺点速览

| 工具 | 优点 | 缺点 |
|------|------|------|
| **Claude Code** | 代码理解最强、200K 上下文、Skills 扩展 | 无免费额度、不支持本地模型 |
| **Codex CLI** | OpenAI 生态、多模型、稳定 | 功能相对基础、无插件系统 |
| **Gemini CLI** | 免费额度大、1M 上下文、多模态强 | 国内访问困难、功能较新 |
| **Kiro CLI** | Spec 驱动开发、AWS 深度集成、Agent Hooks | 需要 AWS 账号、生态较新 |
| **Copilot CLI** | GitHub 深度集成、多模型支持、PR/Issues 管理 | 需订阅、旧版即将停用 |
| **OpenCode** | 极速启动、多模型、LSP、插件系统 | 社区较小、文档较少 |
| **Aider** | Git 集成最强、多文件编辑、架构师模式 | Python 依赖、启动稍慢 |

#### 最佳使用场景

| 场景 | 推荐工具 | 原因 |
|------|----------|------|
| **企业级开发** | Claude Code | 代码理解强、安全性高 |
| **新手学习** | Gemini CLI | 免费、上手简单 |
| **AWS 项目** | Kiro CLI | 深度 AWS 集成、Spec 驱动开发 |
| **GitHub 项目** | Copilot CLI | PR/Issues 管理、GitHub 深度集成 |
| **开源项目** | Aider | Git 集成、自动提交 |
| **多模型实验** | OpenCode | 支持所有主流模型 |
| **快速原型** | Codex CLI | 稳定、生态丰富 |
| **大型重构** | Aider | 多文件编辑、架构师模式 |
| **预算有限** | Gemini CLI + Aider | 免费额度 + 开源 |
| **本地部署** | OpenCode / Aider | 支持 Ollama |

---

## Claude Code 完全指南

### 1. 环境要求

```bash
# 检查 Node.js 版本（需要 >= 18）
node --version

# 如果没有安装 Node.js
# macOS
brew install node

# Windows（使用 winget）
winget install OpenJS.NodeJS.LTS

# Linux（Ubuntu/Debian）
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### 2. 安装 Claude Code

```bash
# 全局安装
npm install -g @anthropic-ai/claude-code

# 验证安装
claude --version
```

### 3. 配置 API Key

#### 方式一：交互式配置（推荐新手）
```bash
# 首次运行会引导配置
claude
```

#### 方式二：环境变量
```bash
# macOS/Linux - 添加到 ~/.bashrc 或 ~/.zshrc
export ANTHROPIC_API_KEY="sk-ant-xxxxx"

# Windows PowerShell - 添加到 $PROFILE
$env:ANTHROPIC_API_KEY="sk-ant-xxxxx"

# 使配置生效
source ~/.zshrc  # macOS/Linux
```

#### 方式三：配置文件
```bash
# 创建配置文件
mkdir -p ~/.claude
cat > ~/.claude/.env << EOF
ANTHROPIC_API_KEY=sk-ant-xxxxx
EOF
```

### 4. 获取 API Key

1. 访问 [Anthropic Console](https://console.anthropic.com/)
2. 注册/登录账号
3. 进入 API Keys 页面
4. 点击 "Create Key"
5. 复制保存（只显示一次）

### 5. API Key 管理工具

当你有多个 API Key（个人/公司/测试）需要切换时，手动修改环境变量很麻烦。以下工具可以帮助你高效管理：

#### 方式一：ccuswitcher（终端工具，推荐）

[ccuswitcher](https://github.com/yingca1/ccuswitcher) 是专为 Claude Code 设计的 API Key 切换工具。

```bash
# 安装
npm install -g ccuswitcher

# 添加 API Key
ccswitch add personal sk-ant-xxxxx-personal
ccswitch add work sk-ant-xxxxx-work
ccswitch add test sk-ant-xxxxx-test

# 查看所有 Key
ccswitch list

# 切换 Key
ccswitch use personal
ccswitch use work

# 查看当前使用的 Key
ccswitch current
```

#### 方式二：AI Toolbox（GUI 工具）

如果你不习惯终端操作，可以使用 [AI Toolbox](https://github.com/coulsontl/ai-toolbox) 图形界面工具。

**特点**：
- 🖥️ 图形界面，操作直观
- 🔑 支持多个 AI 服务的 Key 管理（Claude、OpenAI、Gemini 等）
- 🔄 一键切换不同的 Key
- 📊 API 用量统计
- 🔒 本地加密存储

```bash
# macOS 安装
brew install --cask ai-toolbox

# 或下载 Release
# https://github.com/coulsontl/ai-toolbox/releases
```

#### 方式三：自定义 Shell 函数

你也可以在 `~/.zshrc` 或 `~/.bashrc` 中添加自定义切换函数：

```bash
# 添加到 ~/.zshrc 或 ~/.bashrc

# 定义多个 API Key
export ANTHROPIC_KEY_PERSONAL="sk-ant-xxxxx-personal"
export ANTHROPIC_KEY_WORK="sk-ant-xxxxx-work"
export ANTHROPIC_KEY_TEST="sk-ant-xxxxx-test"

# 切换函数
claude-key() {
  case $1 in
    personal)
      export ANTHROPIC_API_KEY=$ANTHROPIC_KEY_PERSONAL
      echo "✅ 已切换到 Personal Key"
      ;;
    work)
      export ANTHROPIC_API_KEY=$ANTHROPIC_KEY_WORK
      echo "✅ 已切换到 Work Key"
      ;;
    test)
      export ANTHROPIC_API_KEY=$ANTHROPIC_KEY_TEST
      echo "✅ 已切换到 Test Key"
      ;;
    *)
      echo "当前 Key: ${ANTHROPIC_API_KEY:0:20}..."
      echo "用法: claude-key [personal|work|test]"
      ;;
  esac
}

# 快捷别名
alias ck="claude-key"
alias ck-p="claude-key personal"
alias ck-w="claude-key work"
alias ck-t="claude-key test"
```

使用方式：

```bash
# 切换到个人 Key
claude-key personal
# 或
ck-p

# 切换到工作 Key
claude-key work
# 或
ck-w

# 查看当前 Key
claude-key
```

#### API Key 管理最佳实践

```
1. 分离用途：个人学习、工作项目、测试环境使用不同的 Key
2. 设置预算：在 Anthropic Console 为每个 Key 设置用量上限
3. 定期轮换：定期更换 Key，提高安全性
4. 不要硬编码：永远不要把 Key 写在代码里
5. 使用 .env：项目级 Key 放在 .env 文件，并加入 .gitignore
```

### 6. 基本使用

```bash
# 进入项目目录
cd your-project

# 启动 Claude Code
claude

# 带初始提示启动
claude "帮我分析这个项目的结构"

# 非交互模式（执行后退出）
claude --print "解释 main.py 的作用"
```

### 6. 内置斜杠命令完整列表

Claude Code 提供了丰富的内置斜杠命令，在会话中输入 `/` 即可查看。

#### 会话管理类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/help` | 查看帮助信息 | `/help` |
| `/status` | 查看当前会话状态 | `/status` |
| `/clear` | 清除当前会话上下文 | `/clear` |
| `/compact` | 压缩上下文（节省 token） | `/compact` |
| `/cost` | 查看当前会话的 API 费用 | `/cost` |
| `/quit` 或 `/exit` | 退出 Claude Code | `/quit` |

#### 模型与配置类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/model` | 查看或切换模型 | `/model opus` |
| `/config` | 查看或修改配置 | `/config` |
| `/permissions` | 管理工具权限 | `/permissions` |
| `/allowed-tools` | 查看允许使用的工具 | `/allowed-tools` |

#### 文件与上下文类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/add` | 添加文件到上下文 | `/add src/utils.ts` |
| `/add-dir` | 添加整个目录到上下文 | `/add-dir src/components` |
| `/drop` | 从上下文移除文件 | `/drop src/utils.ts` |

#### 会话历史类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/resume` | 恢复之前的会话 | `/resume` |
| `/continue` | 继续最近的会话 | `/continue` |

#### 账户与认证类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/login` | 登录 Anthropic 账户 | `/login` |
| `/logout` | 退出登录 | `/logout` |

#### 系统工具类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/doctor` | 检查 Claude Code 健康状态 | `/doctor` |
| `/bug` | 报告 bug | `/bug` |
| `/init` | 初始化项目配置 | `/init` |
| `/terminal-setup` | 设置终端集成 | `/terminal-setup` |
| `/vim` | 启用 vim 模式 | `/vim` |

#### 记忆与学习类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/memory` | 管理 Claude 的记忆 | `/memory` |
| `/forget` | 让 Claude 忘记某些内容 | `/forget` |

#### MCP 与插件类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/mcp` | 管理 MCP 服务器 | `/mcp` |
| `/plugin` | 管理插件 | `/plugin list` |
| `/install` | 安装插件或扩展 | `/install` |

#### 任务管理类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/tasks` | 查看后台任务 | `/tasks` |
| `/task-output` | 获取任务输出 | `/task-output <task-id>` |

#### 代码审查类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/review` | 审查代码（需要配置） | `/review` |
| `/pr-comments` | 查看 PR 评论 | `/pr-comments` |

#### 快捷操作

```bash
# 快速查看所有可用命令
/help

# 查看特定命令的帮助
/help model

# 使用 Tab 键自动补全命令
/mo<Tab>  # 自动补全为 /model
```

### 7. 配置文件详解

```bash
# 全局配置位置
~/.claude/
├── settings.json      # 主配置文件
├── CLAUDE.md          # 全局指令（所有项目生效）
├── rules/             # 规则目录
├── skills/            # 技能目录
├── commands/          # 自定义命令
└── agents/            # 自定义代理

# 项目级配置
your-project/
├── .claude/
│   └── settings.json  # 项目配置（覆盖全局）
└── CLAUDE.md          # 项目指令
```

#### settings.json 示例
```json
{
  "model": "claude-sonnet-4-20250514",
  "permissions": {
    "allow": ["Read", "Write", "Edit", "Bash"],
    "deny": []
  },
  "contextLimit": 200000
}
```

#### CLAUDE.md 示例
```markdown
# 项目说明

这是一个 React + TypeScript 项目。

## 代码规范
- 使用函数式组件
- 使用 TypeScript 严格模式
- 组件文件使用 PascalCase

## 禁止事项
- 不要使用 any 类型
- 不要使用 class 组件
```

### 8. 自定义斜杠命令

你可以创建自己的斜杠命令来简化常用操作。

#### 创建自定义命令

```bash
# 在 ~/.claude/commands/ 目录下创建 .md 文件
mkdir -p ~/.claude/commands
```

#### 示例：创建 /review 命令

```bash
cat > ~/.claude/commands/review.md << 'EOF'
---
name: review
description: 审查当前改动的代码质量
---

请审查当前 git 暂存区的代码改动：

1. 检查代码质量和可读性
2. 检查潜在的 bug 和边界情况
3. 检查是否符合项目代码规范
4. 给出改进建议

使用 `git diff --staged` 获取改动内容。
EOF
```

#### 示例：创建 /test 命令

```bash
cat > ~/.claude/commands/test.md << 'EOF'
---
name: test
description: 为指定文件生成单元测试
---

为 $ARGUMENTS 生成完整的单元测试：

1. 分析文件中的所有函数/方法
2. 为每个函数生成测试用例
3. 包含正常情况和边界情况
4. 使用项目现有的测试框架
EOF
```

#### 示例：创建 /doc 命令

```bash
cat > ~/.claude/commands/doc.md << 'EOF'
---
name: doc
description: 为代码生成文档注释
---

为 $ARGUMENTS 生成详细的文档注释：

1. 函数/方法添加 JSDoc/docstring
2. 说明参数和返回值
3. 添加使用示例
4. 说明可能抛出的异常
EOF
```

#### 使用自定义命令

```bash
# 在 Claude Code 中使用
/review                    # 审查代码
/test src/utils.ts         # 为 utils.ts 生成测试
/doc src/api/user.ts       # 为 user.ts 生成文档
```

#### 命令文件格式说明

```markdown
---
name: 命令名称           # 必需，调用时使用 /命令名称
description: 命令描述    # 可选，显示在帮助中
---

命令的具体指令内容...

$ARGUMENTS 会被替换为用户输入的参数
```

#### 更多自定义命令示例

参考社区分享的命令配置：
- [bcherny 的自定义命令集合](https://x.com/bcherny/status/2016222113523483050)

---

## OpenAI Codex CLI 完全指南

### 1. 安装

```bash
# 全局安装
npm install -g @openai/codex

# 验证安装
codex --version
```

### 2. 配置 API Key

```bash
# 环境变量方式
export OPENAI_API_KEY="sk-xxxxx"

# 或使用配置命令
codex config set apiKey sk-xxxxx
```

### 3. 获取 API Key

1. 访问 [OpenAI Platform](https://platform.openai.com/)
2. 登录账号
3. 进入 API Keys 页面
4. 创建新的 API Key

### 4. 基本使用

```bash
# 启动交互模式
codex

# 直接提问
codex "如何用 Python 读取 CSV 文件"

# 指定模型
codex --model gpt-4o "优化这段代码"

# 执行模式（自动执行建议的命令）
codex --auto "创建一个 React 项目"
```

### 5. 模型选择

```bash
# 查看可用模型
codex models

# 常用模型
codex --model gpt-4o          # 最新 GPT-4o
codex --model gpt-4-turbo     # GPT-4 Turbo
codex --model o1-preview      # o1 推理模型
codex --model o1-mini         # o1 mini（更快更便宜）
```

### 6. 配置文件

```bash
# 配置文件位置
~/.codex/
├── config.json        # 主配置
└── instructions.md    # 全局指令
```

```json
// config.json
{
  "model": "gpt-4o",
  "approvalMode": "suggest",
  "fullAutoErrorMode": "ask-user"
}
```

### 7. 内置斜杠命令

Codex CLI 在交互模式下支持以下斜杠命令：

#### 会话管理类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/help` | 查看帮助信息 | `/help` |
| `/clear` | 清除当前会话上下文 | `/clear` |
| `/history` | 查看会话历史 | `/history` |
| `/exit` 或 `/quit` | 退出 Codex CLI | `/exit` |

#### 模型与配置类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/model` | 切换模型 | `/model gpt-4o` |
| `/config` | 查看或修改配置 | `/config` |
| `/approval` | 设置审批模式 | `/approval suggest` |

#### 文件与上下文类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/add` | 添加文件到上下文 | `/add src/main.py` |
| `/ls` | 列出当前目录文件 | `/ls` |
| `/cd` | 切换工作目录 | `/cd src` |
| `/pwd` | 显示当前目录 | `/pwd` |

#### 执行控制类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/run` | 执行上一个建议的命令 | `/run` |
| `/undo` | 撤销上一个操作 | `/undo` |
| `/diff` | 显示文件差异 | `/diff` |

---

## Gemini CLI 完全指南

### 1. 安装

```bash
# 全局安装
npm install -g @anthropic-ai/gemini-cli

# 或使用 Google 官方安装方式
# macOS/Linux
curl -fsSL https://gemini.google.com/cli/install.sh | bash

# 验证安装
gemini --version
```

### 2. 认证配置

#### 方式一：Google 账号登录（推荐）
```bash
# 首次运行会打开浏览器登录
gemini

# 或手动触发登录
gemini auth login
```

#### 方式二：API Key
```bash
# 设置环境变量
export GEMINI_API_KEY="xxxxx"

# 或配置文件
gemini config set apiKey xxxxx
```

### 3. 获取 API Key

1. 访问 [Google AI Studio](https://aistudio.google.com/)
2. 登录 Google 账号
3. 点击 "Get API Key"
4. 创建或选择项目
5. 复制 API Key

### 4. 免费额度说明

```
Gemini CLI 免费额度（使用 Google 账号登录）：
- 每分钟 60 次请求
- 每天 1500 次请求
- 支持 Gemini 2.5 Pro

超出免费额度后：
- 需要绑定付费账号
- 或等待额度重置
```

### 5. 基本使用

```bash
# 启动交互模式
gemini

# 直接提问
gemini "解释量子计算的基本原理"

# 多模态（支持图片）
gemini "描述这张图片" --image photo.jpg

# 指定模型
gemini --model gemini-2.5-pro "写一个排序算法"
```

### 6. 沙盒模式

```bash
# 沙盒模式（安全执行代码）
gemini --sandbox

# 在沙盒中，Gemini 可以：
# - 执行代码
# - 安装包
# - 操作文件
# 但都在隔离环境中，不影响主系统
```

### 7. 配置文件

```bash
# 配置文件位置
~/.gemini/
├── config.json        # 主配置
├── settings.json      # 设置
└── GEMINI.md          # 全局指令
```

### 8. 内置斜杠命令

Gemini CLI 在交互模式下支持以下斜杠命令：

#### 会话管理类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/help` 或 `/?` | 查看帮助信息 | `/help` |
| `/clear` | 清除当前会话上下文 | `/clear` |
| `/reset` | 重置会话（清除所有状态） | `/reset` |
| `/quit` 或 `/exit` | 退出 Gemini CLI | `/quit` |
| `/stats` | 查看会话统计信息 | `/stats` |

#### 模型与配置类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/model` | 切换或查看当前模型 | `/model gemini-2.5-pro` |
| `/config` | 查看或修改配置 | `/config` |
| `/settings` | 打开设置 | `/settings` |
| `/theme` | 切换主题（亮/暗） | `/theme dark` |

#### 文件与上下文类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/read` | 读取文件内容到上下文 | `/read src/main.py` |
| `/add` | 添加文件到上下文 | `/add config.json` |
| `/image` | 添加图片进行多模态分析 | `/image photo.jpg` |
| `/upload` | 上传文件 | `/upload document.pdf` |

#### 执行与沙盒类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/run` | 执行代码或命令 | `/run` |
| `/sandbox` | 进入/退出沙盒模式 | `/sandbox on` |
| `/shell` | 执行 shell 命令 | `/shell ls -la` |

#### 历史与保存类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/history` | 查看对话历史 | `/history` |
| `/save` | 保存当前会话 | `/save session.json` |
| `/load` | 加载之前的会话 | `/load session.json` |
| `/export` | 导出对话为 Markdown | `/export chat.md` |

#### 认证类

| 命令 | 作用 | 示例 |
|------|------|------|
| `/auth` | 查看认证状态 | `/auth` |
| `/login` | 登录 Google 账号 | `/login` |
| `/logout` | 退出登录 | `/logout` |

---

## Kiro CLI 完全指南

[Kiro](https://kiro.dev) 是 AWS/Amazon 推出的 AI 驱动 IDE 和 CLI 工具，与 Amazon Q Developer 深度集成，特点是 Spec 驱动开发和 Agent Hooks 自动化。

### 1. 安装

#### macOS

```bash
# Homebrew（推荐）
brew install kiro-cli

# 或使用 curl
curl -fsSL https://cli.kiro.dev/install | bash

# 验证安装
kiro-cli version
```

#### Linux

```bash
# 通用安装
curl -fsSL https://cli.kiro.dev/install | bash

# Ubuntu/Debian（.deb 包）
sudo dpkg -i kiro-cli.deb
sudo apt-get install -f

# Arm Linux
curl --proto '=https' --tlsv1.2 -sSf 'https://desktop-release.q.us-east-1.amazonaws.com/latest/kirocli-aarch64-linux.zip' -o 'kirocli.zip'
unzip kirocli.zip
bash ./kirocli/install.sh

# 验证安装
kiro-cli version
```

#### Windows

```bash
# 通过 WSL（Windows Subsystem for Linux）安装
wsl
curl -fsSL https://cli.kiro.dev/install | bash
```

#### 系统要求

| 平台 | 要求 |
|------|------|
| Windows | Windows 10 (64-bit) 或 11 |
| macOS | macOS 10.15 (Catalina) 或更高 |
| Linux | Ubuntu 18.04+、CentOS 7+ 等，glibc 2.34+ |
| 通用 | 4GB RAM（推荐 8GB），500MB 磁盘空间 |

### 2. 认证配置

Kiro CLI 使用浏览器登录方式认证：

```bash
# 首次运行会打开浏览器进行 AWS 账号登录
kiro-cli

# 或手动触发认证
kiro-cli auth login
```

登录后，Kiro CLI 会与 Amazon Q Developer 共享订阅，无需额外配置 API Key。

### 3. 基本使用

```bash
# 启动交互模式
kiro-cli

# 在项目目录中启动
cd your-project
kiro-cli

# 直接提问
kiro-cli "帮我分析这个项目的架构"
```

### 4. 核心特性

#### Spec 驱动开发（Spec-Driven Development）

Kiro 的独特功能，先写规格说明再生成代码：

```bash
# 在交互模式中
> 为用户认证模块创建 spec

# Kiro 会生成详细的规格文档，包括：
# - 功能需求
# - API 设计
# - 数据模型
# - 测试用例

# 确认后自动生成符合 spec 的代码
```

#### Agent Hooks

自动化钩子，在特定事件触发时执行：

```yaml
# KIRO.md 或 .kiro/hooks.yaml
hooks:
  on_file_save:
    - run: npm run lint
    - run: npm run test
  on_commit:
    - run: npm run build
```

#### MCP（Model Context Protocol）支持

连接外部工具和服务：

```bash
# 查看已连接的 MCP 服务
/context

# Kiro 可以通过 MCP 连接：
# - 文件系统
# - 数据库
# - API 服务
# - 云资源
```

#### 持久化聊天历史

Kiro CLI 会按项目目录保存聊天历史，下次进入同一目录时可以继续之前的对话。

### 5. 内置斜杠命令

Kiro CLI 在交互模式下支持以下斜杠命令：

| 命令 | 作用 | 示例 |
|------|------|------|
| `/help` | 查看帮助信息 | `/help` |
| `/quit` | 退出 Kiro CLI | `/quit` |
| `/clear` | 清除当前会话上下文 | `/clear` |
| `/context` | 查看/管理上下文和 MCP 连接 | `/context` |
| `/model` | 切换模型 | `/model` |
| `/history` | 查看对话历史 | `/history` |
| `/spec` | 创建或查看规格文档 | `/spec create auth` |
| `/hooks` | 管理 Agent Hooks | `/hooks list` |

### 6. 配置文件

```bash
# 全局配置位置
~/.kiro/
├── config.json        # 主配置
├── auth/              # 认证信息
└── history/           # 聊天历史

# 项目级配置
your-project/
├── .kiro/
│   ├── hooks.yaml     # Agent Hooks 配置
│   └── settings.json  # 项目设置
└── KIRO.md            # 项目指令（类似 CLAUDE.md）
```

#### KIRO.md 示例

```markdown
# 项目说明

这是一个 AWS Lambda + DynamoDB 项目。

## 开发规范
- 使用 TypeScript 严格模式
- 所有 Lambda 函数需要有单元测试
- 遵循 AWS 最佳实践

## 架构说明
- API Gateway -> Lambda -> DynamoDB
- 使用 SAM 进行部署
```

### 7. 与 Amazon Q Developer 集成

Kiro CLI 与 Amazon Q Developer CLI 共享订阅，可以无缝切换：

```bash
# 使用 Kiro CLI
kiro-cli "优化这个 Lambda 函数"

# 使用 Amazon Q Developer CLI
q "分析这个 CloudFormation 模板"
```

### 8. 安全特性

Kiro CLI 在 2025 年 10 月增加了安全增强：

- **Human-in-the-Loop 确认**：危险操作需要用户确认
- **权限控制**：可配置允许的操作范围
- **审计日志**：记录所有 AI 操作

```bash
# 查看安全设置
kiro-cli config security

# 启用严格模式（所有操作需确认）
kiro-cli config set security.strict true
```

---

## GitHub Copilot CLI 完全指南

[GitHub Copilot CLI](https://github.com/features/copilot) 是 GitHub 官方推出的 AI 编码 CLI 工具，与 GitHub 生态深度集成。

### 1. 安装

```bash
# npm 安装（推荐）
npm install -g @github/copilot

# 验证安装
copilot --version
```

> ⚠️ **重要**: 旧版 `gh copilot` 命令将于 **2025年10月25日** 停用，请迁移到新版 npm 包

### 2. 认证配置

```bash
# 首次运行会引导登录
copilot

# 或手动登录
/login

# 登录会打开浏览器进行 GitHub OAuth 认证
```

需要有效的 GitHub Copilot 订阅：
- **个人版**: $10/月 或 $100/年
- **商业版**: $19/月/用户
- **企业版**: $39/月/用户

### 3. 基本使用

```bash
# 启动交互模式
copilot

# 在项目目录中启动
cd your-project
copilot

# 直接提问
copilot "帮我分析这个 PR 的改动"
```

### 4. 核心特性

#### GitHub 深度集成

Copilot CLI 与 GitHub 无缝集成：

```bash
# 管理 Pull Requests
> 查看 PR #123 的所有评论

# 处理 Issues
> 分析 Issue #456 并给出解决方案

# 代码审查
> 审查当前分支与 main 的差异
```

#### 多模型支持

支持切换不同的 AI 模型：

```bash
# 切换模型
/model

# 支持的模型包括：
# - Claude Sonnet 4
# - GPT-4o
# - 其他
```

#### 指令文件支持

Copilot CLI 可以读取多种项目指令文件：

```bash
# 支持的指令文件（按优先级）
.github/copilot-instructions.md
CLAUDE.md
GEMINI.md
```

#### MCP 支持

```bash
# 管理 MCP 服务器
/mcp show           # 显示所有 MCP 服务器
/mcp add <name>     # 添加 MCP 服务器
/mcp delete <name>  # 删除 MCP 服务器
/mcp enable <name>  # 启用 MCP 服务器
/mcp disable <name> # 禁用 MCP 服务器
```

### 5. 内置斜杠命令

| 命令 | 作用 | 示例 |
|------|------|------|
| `/login` | 登录 GitHub 账号 | `/login` |
| `/logout` | 退出登录 | `/logout` |
| `/help` | 查看帮助信息 | `/help` |
| `/clear` | 清除屏幕上的聊天历史 | `/clear` |
| `/exit` | 退出 CLI | `/exit` |
| `/model` | 切换 AI 模型 | `/model` |
| `/mcp` | 管理 MCP 服务器 | `/mcp show` |
| `/add-dir` | 添加目录到允许访问列表 | `/add-dir src/` |
| `/list-dirs` | 列出允许访问的目录 | `/list-dirs` |
| `/cwd` | 查看/切换工作目录 | `/cwd` |
| `/feedback` | 提交反馈 | `/feedback` |
| `/reset-allowed-tools` | 重置允许的工具列表 | `/reset-allowed-tools` |

### 6. 配置文件

```bash
# 全局配置位置
~/.copilot/
├── config.json        # 主配置
├── auth/              # 认证信息
└── mcp/               # MCP 配置

# 项目级配置
your-project/
└── .github/
    └── copilot-instructions.md  # 项目指令
```

#### copilot-instructions.md 示例

```markdown
# 项目说明

这是一个使用 Next.js + TypeScript 的项目。

## 代码规范
- 使用 ESLint + Prettier
- 组件使用函数式写法
- 使用 Tailwind CSS

## GitHub 工作流
- 分支命名：feature/xxx, fix/xxx
- PR 需要至少一个 review
- 合并使用 squash merge
```

### 7. 企业功能

企业版提供额外功能：

- **高风险命令确认**: 危险操作需要人工确认
- **工具权限细粒度控制**: 可配置允许的工具范围
- **审计日志**: 记录所有 AI 操作
- **工具链集成**: 与企业内部工具集成

### 8. 从 gh copilot 迁移

如果你之前使用 `gh copilot`：

```bash
# 卸载旧版（可选）
gh extension remove copilot

# 安装新版
npm install -g @github/copilot

# 重新登录
copilot
/login
```

---

## OpenCode 完全指南

[OpenCode](https://github.com/opencode-ai/opencode) 是一个用 Go 编写的开源 AI 编程助手 CLI，特点是轻量、快速、支持多模型。

### 1. 安装

```bash
# macOS (Homebrew)
brew install opencode-ai/tap/opencode

# Linux (curl)
curl -fsSL https://raw.githubusercontent.com/opencode-ai/opencode/main/install.sh | bash

# Go 安装
go install github.com/opencode-ai/opencode@latest

# 验证安装
opencode --version
```

### 2. 配置

```bash
# 首次运行会引导配置
opencode

# 或手动配置
opencode config
```

#### 配置文件位置
```bash
~/.config/opencode/
├── config.json        # 主配置文件
└── prompts/           # 自定义提示词
```

#### config.json 示例
```json
{
  "provider": "anthropic",
  "model": "claude-sonnet-4-20250514",
  "api_key": "sk-ant-xxxxx",
  "theme": "dark",
  "auto_commit": false
}
```

### 3. 支持的模型提供商

| 提供商 | 环境变量 | 支持模型 |
|--------|---------|---------|
| Anthropic | `ANTHROPIC_API_KEY` | Claude 系列 |
| OpenAI | `OPENAI_API_KEY` | GPT-4o, o1 系列 |
| Google | `GOOGLE_API_KEY` | Gemini 系列 |
| Groq | `GROQ_API_KEY` | Llama, Mixtral |
| Ollama | 本地运行 | 所有 Ollama 模型 |
| OpenRouter | `OPENROUTER_API_KEY` | 多模型聚合 |

```bash
# 切换提供商
opencode --provider openai --model gpt-4o

# 使用本地 Ollama
opencode --provider ollama --model codellama
```

### 4. 基本使用

```bash
# 启动交互模式
opencode

# 直接提问
opencode "解释这段代码的作用"

# 指定文件
opencode -f main.go "优化这个函数"

# 非交互模式
opencode -p "生成 README" > README.md
```

### 5. 内置斜杠命令

OpenCode 的斜杠命令完整列表及详细说明：

| 命令 | 作用 | 详细说明 | 示例 |
|------|------|---------|------|
| `/help` | 查看帮助 | 显示所有可用命令及其用法 | `/help` |
| `/clear` | 清除上下文 | 清除当前会话的所有对话历史，重新开始 | `/clear` |
| `/quit` | 退出程序 | 退出 OpenCode CLI | `/quit` |
| `/compact` | 压缩上下文 | 智能压缩对话历史，保留关键信息，节省 token | `/compact` |
| `/model` | 切换模型 | 在当前会话中切换到不同的 AI 模型 | `/model gpt-4o` |
| `/provider` | 切换提供商 | 切换 API 提供商（anthropic/openai/google 等） | `/provider openai` |
| `/config` | 查看配置 | 显示当前的配置信息，包括模型、API Key 状态等 | `/config` |
| `/add` | 添加文件 | 将指定文件添加到上下文，AI 可以读取和修改 | `/add src/main.go` |
| `/drop` | 移除文件 | 从上下文中移除指定文件，减少 token 消耗 | `/drop src/main.go` |
| `/files` | 列出文件 | 显示当前上下文中已添加的所有文件列表 | `/files` |
| `/ls` | 列出目录 | 列出指定目录下的文件和子目录 | `/ls src/` |
| `/diff` | 显示差异 | 显示 AI 修改前后的代码差异（类似 git diff） | `/diff` |
| `/undo` | 撤销更改 | 撤销 AI 最近一次对文件的修改 | `/undo` |
| `/commit` | 提交更改 | 将当前更改提交到 Git 仓库 | `/commit "feat: add"` |
| `/copy` | 复制内容 | 将 AI 的最后一次回复复制到剪贴板 | `/copy` |
| `/paste` | 粘贴内容 | 将剪贴板内容粘贴到当前输入 | `/paste` |
| `/history` | 查看历史 | 显示当前会话的对话历史 | `/history` |
| `/save` | 保存会话 | 将当前会话保存到文件 | `/save session.json` |
| `/load` | 加载会话 | 从文件加载之前保存的会话 | `/load session.json` |
| `/tokens` | Token 统计 | 显示当前会话的 token 使用情况 | `/tokens` |
| `/cost` | 费用统计 | 显示当前会话的 API 调用费用 | `/cost` |

### 6. LSP 集成

OpenCode 支持 Language Server Protocol，提供智能代码补全：

```bash
# 启用 LSP
opencode --lsp

# 配置文件中启用
{
  "lsp": {
    "enabled": true,
    "languages": ["go", "python", "typescript"]
  }
}
```

### 7. oh-my-opencode 插件系统

[oh-my-opencode](https://github.com/opencode-ai/oh-my-opencode) 是 OpenCode 的插件管理框架，类似于 oh-my-zsh。

#### 安装 oh-my-opencode

```bash
# 安装
curl -fsSL https://raw.githubusercontent.com/opencode-ai/oh-my-opencode/main/install.sh | bash

# 或手动克隆
git clone https://github.com/opencode-ai/oh-my-opencode ~/.oh-my-opencode
```

#### 目录结构

```bash
~/.oh-my-opencode/
├── plugins/           # 插件目录
│   ├── git/           # Git 增强插件
│   ├── docker/        # Docker 插件
│   ├── k8s/           # Kubernetes 插件
│   └── ...
├── themes/            # 主题目录
├── prompts/           # 提示词模板
└── config.yaml        # 配置文件
```

#### 启用插件

```yaml
# ~/.oh-my-opencode/config.yaml
plugins:
  - git              # Git 增强
  - docker           # Docker 命令
  - k8s              # Kubernetes
  - aws              # AWS CLI
  - code-review      # 代码审查
  - test-gen         # 测试生成

theme: "dracula"
```

#### 常用插件

| 插件 | 功能 | 提供的命令 |
|------|------|-----------|
| `git` | Git 增强 | `/git-status`, `/git-diff`, `/git-commit` |
| `docker` | Docker 操作 | `/docker-build`, `/docker-run`, `/docker-logs` |
| `k8s` | Kubernetes | `/k8s-pods`, `/k8s-logs`, `/k8s-deploy` |
| `aws` | AWS CLI | `/aws-s3`, `/aws-ec2`, `/aws-lambda` |
| `code-review` | 代码审查 | `/review`, `/review-pr` |
| `test-gen` | 测试生成 | `/test`, `/test-coverage` |
| `doc-gen` | 文档生成 | `/doc`, `/readme` |
| `refactor` | 重构建议 | `/refactor`, `/optimize` |

#### 创建自定义插件

```bash
# 创建插件目录
mkdir -p ~/.oh-my-opencode/plugins/my-plugin

# 创建插件文件
cat > ~/.oh-my-opencode/plugins/my-plugin/plugin.yaml << 'EOF'
name: my-plugin
description: 我的自定义插件
version: 1.0.0

commands:
  - name: my-cmd
    description: 我的命令
    prompt: |
      执行以下任务：
      $ARGUMENTS
EOF
```

---

## Aider 完全指南

[Aider](https://github.com/paul-gauthier/aider) 是一个强大的开源 AI 编程助手，以 Git 集成著称。

### 1. 安装

```bash
# pip 安装（推荐）
pip install aider-chat

# pipx 安装（隔离环境）
pipx install aider-chat

# Homebrew (macOS)
brew install aider

# 验证安装
aider --version
```

### 2. 配置 API Key

```bash
# Anthropic (Claude)
export ANTHROPIC_API_KEY="sk-ant-xxxxx"

# OpenAI
export OPENAI_API_KEY="sk-xxxxx"

# 或使用配置文件 ~/.aider.conf.yml
```

#### 配置文件示例

```yaml
# ~/.aider.conf.yml
model: claude-sonnet-4-20250514
auto-commits: true
dark-mode: true
stream: true
```

### 3. 基本使用

```bash
# 在 Git 仓库中启动
cd your-project
aider

# 指定文件
aider src/main.py src/utils.py

# 指定模型
aider --model claude-sonnet-4-20250514

# 使用 GPT-4
aider --model gpt-4o
```

### 4. 支持的模型

```bash
# Claude 系列
aider --model claude-sonnet-4-20250514
aider --model claude-opus-4-20250514

# GPT 系列
aider --model gpt-4o
aider --model gpt-4-turbo

# 本地模型 (Ollama)
aider --model ollama/codellama

# DeepSeek
aider --model deepseek/deepseek-coder
```

### 5. 内置斜杠命令

Aider 的斜杠命令完整列表及详细说明：

| 命令 | 作用 | 详细说明 | 示例 |
|------|------|---------|------|
| `/help` | 查看帮助 | 显示所有可用命令的帮助信息 | `/help` |
| `/add` | 添加文件 | 将文件添加到会话，AI 可以读取和修改这些文件 | `/add src/main.py` |
| `/drop` | 移除文件 | 从会话中移除文件，AI 将不再能访问该文件 | `/drop src/main.py` |
| `/ls` | 列出文件 | 显示当前会话中已添加的所有文件 | `/ls` |
| `/read-only` | 只读添加 | 添加文件供 AI 参考，但不允许修改（节省 token） | `/read-only config.py` |
| `/git` | Git 命令 | 在 aider 中直接执行任意 git 命令 | `/git status` |
| `/diff` | 显示差异 | 显示当前未提交的所有代码更改 | `/diff` |
| `/commit` | 提交更改 | 手动提交当前的更改到 Git 仓库 | `/commit` |
| `/undo` | 撤销更改 | 撤销 AI 最近一次的代码修改（git reset） | `/undo` |
| `/clear` | 清除历史 | 清除当前会话的对话历史，保留已添加的文件 | `/clear` |
| `/reset` | 重置会话 | 完全重置会话，包括对话历史和已添加的文件 | `/reset` |
| `/quit` | 退出程序 | 退出 aider | `/quit` |
| `/model` | 切换模型 | 在当前会话中切换到不同的 AI 模型 | `/model gpt-4o` |
| `/models` | 列出模型 | 显示所有可用的 AI 模型列表 | `/models` |
| `/settings` | 查看设置 | 显示当前的所有配置设置 | `/settings` |
| `/tokens` | Token 统计 | 显示当前会话的 token 使用情况和剩余额度 | `/tokens` |
| `/run` | 运行命令 | 执行 shell 命令并将输出显示给 AI | `/run python main.py` |
| `/test` | 运行测试 | 运行项目的测试套件 | `/test` |
| `/lint` | 代码检查 | 运行 linter 检查代码质量 | `/lint` |
| `/voice` | 语音输入 | 启用语音输入模式，用语音与 AI 对话 | `/voice` |
| `/copy` | 复制代码 | 将 AI 生成的代码复制到剪贴板 | `/copy` |
| `/paste` | 粘贴内容 | 将剪贴板内容粘贴到对话中 | `/paste` |
| `/web` | 添加网页 | 将网页内容添加到上下文供 AI 参考 | `/web https://...` |
| `/architect` | 架构模式 | 切换到架构师模式（先规划后执行） | `/architect` |
| `/chat-mode` | 聊天模式 | 切换聊天模式（code/ask/architect） | `/chat-mode ask` |
| `/editor` | 打开编辑器 | 用外部编辑器编写长消息 | `/editor` |
| `/map` | 代码地图 | 显示项目的代码结构地图 | `/map` |
| `/map-refresh` | 刷新地图 | 刷新代码结构地图 | `/map-refresh` |

### 6. Git 集成特性

Aider 的核心优势是与 Git 的深度集成：

```bash
# 自动提交（默认开启）
aider  # 每次 AI 修改后自动 commit

# 关闭自动提交
aider --no-auto-commits

# 查看 AI 生成的 commit 历史
git log --oneline

# 撤销 AI 的修改
/undo  # 在 aider 中
# 或
git reset --hard HEAD~1  # 在终端中
```

### 7. 多文件编辑

```bash
# 同时编辑多个文件
aider src/api.py src/models.py src/utils.py

# 在会话中添加更多文件
/add tests/test_api.py

# 添加整个目录
/add src/*.py
```

### 8. 架构师模式

```bash
# 使用架构师模式（先规划后执行）
aider --architect

# 架构师模式会：
# 1. 先用高级模型（如 opus）规划
# 2. 再用快速模型（如 sonnet）执行
```

### 9. 实用技巧

```bash
# 只看建议，不自动修改
aider --dry-run

# 显示详细的 diff
aider --show-diffs

# 使用 .aiderignore 排除文件
echo "node_modules/" >> .aiderignore
echo "*.log" >> .aiderignore

# 浏览器模式（Web UI）
aider --browser
```

---

## 开源 CLI 工具对比

| 特性 | OpenCode | Aider |
|------|----------|-------|
| **语言** | Go | Python |
| **启动速度** | 极快 | 较快 |
| **Git 集成** | 基础 | 深度集成 |
| **多文件编辑** | 支持 | 强项 |
| **LSP 支持** | ✅ | ❌ |
| **插件系统** | oh-my-opencode | ❌ |
| **语音输入** | ❌ | ✅ |
| **Web UI** | ❌ | ✅ |
| **架构师模式** | ❌ | ✅ |
| **本地模型** | Ollama | Ollama |

### 选择建议

```
追求速度 → OpenCode（Go 编写，启动快）
Git 重度用户 → Aider（Git 集成最强）
需要插件扩展 → OpenCode + oh-my-opencode
多文件重构 → Aider（多文件编辑强）
本地模型 → 两者都支持 Ollama
```

---

## 国产 CLI 工具

对于国内开发者，以下工具无需翻墙即可使用。

### Qoder CLI (阿里通义) ⭐ 推荐

[Qoder](https://qoder.com) 是阿里巴巴推出的 AI 编程 CLI 工具，基于通义千问 Qwen3-Coder 模型。

#### 1. 安装

```bash
# npm 安装
npm install -g @qoder-ai/qodercli

# 或使用 curl
curl -fsSL https://qoder.com/install | bash

# 验证安装
qoder --version
```

#### 2. 核心特性

| 特性 | 说明 |
|------|------|
| **Quest Mode** | 任务规格式编程，AI 自主完成开发任务 |
| **CodeReview** | 终端内高效代码审查 |
| **RepoWiki** | 自动生成代码库结构文档 |
| **MCP 支持** | 多代理协作协议，支持社区工具生态 |
| **低延迟** | 响应延迟低至 200ms |
| **中文优化** | 基于 Qwen3-Coder，中文场景适配更好 |

#### 3. 支持语言

JavaScript、TypeScript、Python、Go、C/C++、C#、Java

#### 4. 优势

- ✅ **国内访问无障碍**：无需翻墙
- ✅ **内存占用低**：比同类工具降低 70%
- ✅ **代码审查效率高**：提升 50%
- ✅ **Worktree 支持**：并行任务处理
- ✅ **IDE 集成**：支持 IntelliJ IDEA

#### 5. 基本使用

```bash
# 启动交互模式
qoder

# 直接提问
qoder "帮我分析这个项目"

# Quest Mode（任务模式）
qoder quest "实现用户登录功能"

# 代码审查
qoder review
```

### 其他国产方案

#### Kimi CLI (月之暗面)

```bash
# 安装 uv 包管理器
curl -LsSf https://astral.sh/uv/install.sh | sh

# 安装 Kimi CLI
uv tool install --python 3.13 kimi-cli

# 升级
uv tool upgrade kimi-cli --no-cache
```

#### GLM 4.7 (智谱 AI)

通过配置环境变量在 Claude Code 中使用智谱模型：

```bash
# macOS / Linux
export ANTHROPIC_AUTH_TOKEN="你的智谱API_Key"
export ANTHROPIC_BASE_URL="https://open.bigmodel.cn/api/anthropic"
```

> 💡 **提示**: GLM Coding Plan 已升级到 GLM-4.7 模型，性价比更高

### 国产 CLI 对比

| 特性 | Qoder CLI | Kimi CLI | GLM |
|------|-----------|----------|-----|
| **底层模型** | Qwen3-Coder | Moonshot | GLM-4.7 |
| **安装方式** | npm/curl | uv | 环境变量 |
| **独立 CLI** | ✅ | ✅ | ❌ (借用 CC) |
| **MCP 支持** | ✅ | ❌ | ❌ |
| **中文优化** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **代码审查** | ✅ | ❌ | ❌ |

---

## 代理配置（翻墙）

如果你在中国大陆，需要配置代理才能访问这些 AI 服务。

### 方式一：系统代理

```bash
# macOS/Linux - 添加到 ~/.bashrc 或 ~/.zshrc
export http_proxy="http://127.0.0.1:7890"
export https_proxy="http://127.0.0.1:7890"
export all_proxy="socks5://127.0.0.1:7890"

# Windows PowerShell
$env:http_proxy="http://127.0.0.1:7890"
$env:https_proxy="http://127.0.0.1:7890"

# 使配置生效
source ~/.zshrc
```

### 方式二：仅为 CLI 工具配置代理

```bash
# Claude Code
export ANTHROPIC_PROXY="http://127.0.0.1:7890"

# OpenAI
export OPENAI_PROXY="http://127.0.0.1:7890"

# 或在启动时指定
claude --proxy http://127.0.0.1:7890
```

### 方式三：使用 proxychains（Linux/macOS）

```bash
# 安装 proxychains
brew install proxychains-ng  # macOS
sudo apt install proxychains4  # Ubuntu

# 配置 /etc/proxychains.conf 或 ~/.proxychains/proxychains.conf
[ProxyList]
socks5 127.0.0.1 7890

# 使用
proxychains4 claude
```

### 方式四：配置文件方式

```json
// ~/.claude/settings.json
{
  "proxy": {
    "http": "http://127.0.0.1:7890",
    "https": "http://127.0.0.1:7890"
  }
}
```

### 常见代理软件端口

| 软件 | HTTP 端口 | SOCKS5 端口 |
|------|----------|-------------|
| Clash | 7890 | 7891 |
| V2Ray | 10809 | 10808 |
| Shadowsocks | 1087 | 1080 |
| Surge | 6152 | 6153 |

### 验证代理是否生效

```bash
# 测试连接
curl -I https://api.anthropic.com
curl -I https://api.openai.com
curl -I https://generativelanguage.googleapis.com

# 如果返回 200 或 301/302，说明代理正常
```

---

## 多任务并行工作流

CLI 最大的优势之一是可以轻松开启多个任务并行工作。

### 方式一：多终端窗口

```bash
# 终端 1 - 主开发任务
cd ~/project
claude "实现用户注册功能"

# 终端 2 - 测试任务
cd ~/project
claude "为用户注册写单元测试"

# 终端 3 - 文档任务
cd ~/project
claude "更新 API 文档"
```

### 方式二：使用 tmux（推荐）

```bash
# 安装 tmux
brew install tmux  # macOS
sudo apt install tmux  # Ubuntu

# 创建新会话
tmux new -s dev

# 分割窗口
Ctrl+b %    # 垂直分割
Ctrl+b "    # 水平分割

# 在不同窗格中运行不同的 claude 实例
# 窗格 1: claude "任务1"
# 窗格 2: claude "任务2"
# 窗格 3: claude "任务3"

# 切换窗格
Ctrl+b 方向键

# 常用 tmux 命令
Ctrl+b d    # 分离会话（后台运行）
tmux ls     # 列出会话
tmux a -t dev  # 重新连接会话
```

### 方式三：使用 screen

```bash
# 安装 screen
sudo apt install screen  # Ubuntu

# 创建会话
screen -S claude1

# 分离会话
Ctrl+a d

# 创建另一个会话
screen -S claude2

# 列出会话
screen -ls

# 重新连接
screen -r claude1
```

### 方式四：后台任务

```bash
# Claude Code 支持后台运行任务
claude "分析整个代码库的架构" --background

# 查看后台任务
/tasks

# 获取任务输出
/task-output <task-id>
```

### 方式五：Git Worktrees（高级）

```bash
# 创建 worktree 用于并行开发不同功能
git worktree add ../project-feature-a feature-a
git worktree add ../project-feature-b feature-b

# 在不同目录运行不同的 claude 实例
# 终端 1
cd ../project-feature-a && claude "实现功能 A"

# 终端 2
cd ../project-feature-b && claude "实现功能 B"

# 完成后合并
git worktree remove ../project-feature-a
```

### 并行工作最佳实践

```
1. 任务隔离：每个终端处理独立的功能模块
2. 避免冲突：不要同时修改同一个文件
3. 定期同步：使用 git 管理变更
4. 资源监控：注意 API 调用频率限制
```

---

## 多任务实战案例

以下是几个真实场景的多任务并行工作流示例，展示如何利用 CLI 的多窗口能力大幅提升开发效率。

### 案例一：全栈功能开发（3 窗口并行）

**场景**：开发一个用户管理功能，包含后端 API、前端页面、单元测试。

```
┌─────────────────────────────────────────────────────────────────┐
│                        tmux 会话布局                              │
├─────────────────────────┬───────────────────────────────────────┤
│                         │                                       │
│   窗格 1: 后端 API       │   窗格 2: 前端页面                      │
│                         │                                       │
│   claude               │   claude                              │
│   > 实现用户 CRUD API    │   > 实现用户管理页面                    │
│     使用 RESTful 规范    │     使用 React + TypeScript            │
│                         │                                       │
├─────────────────────────┴───────────────────────────────────────┤
│                                                                 │
│   窗格 3: 测试编写                                                │
│                                                                 │
│   claude                                                        │
│   > 为用户 API 编写单元测试，覆盖所有边界情况                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

**操作步骤**：

```bash
# 1. 创建 tmux 会话
tmux new -s user-feature

# 2. 分割窗口
Ctrl+b %      # 垂直分割（创建窗格 2）
Ctrl+b "      # 水平分割（创建窗格 3）

# 3. 在窗格 1 启动后端任务
cd ~/project/backend
claude "实现用户 CRUD API，包含：
1. GET /api/users - 获取用户列表（支持分页）
2. POST /api/users - 创建用户
3. PUT /api/users/:id - 更新用户
4. DELETE /api/users/:id - 删除用户
使用 RESTful 规范，添加参数验证"

# 4. 切换到窗格 2（Ctrl+b →），启动前端任务
cd ~/project/frontend
claude "实现用户管理页面，包含：
1. 用户列表表格（支持分页、搜索）
2. 新增/编辑用户弹窗
3. 删除确认对话框
使用 React + Ant Design"

# 5. 切换到窗格 3（Ctrl+b ↓），启动测试任务
cd ~/project/backend
claude "为用户 API 编写单元测试：
1. 正常 CRUD 操作测试
2. 参数验证测试
3. 权限验证测试
4. 边界情况测试"
```

**效率提升**：原本串行需要 3 小时的工作，并行只需 1 小时。

---

### 案例二：Bug 修复 + 代码审查（2 窗口并行）

**场景**：一边修复紧急 bug，一边审查同事的 PR。

```bash
# 终端 1：修复 bug
cd ~/project
claude "修复登录超时问题：
- 用户反馈：登录后 5 分钟就被踢出
- 预期行为：应该保持 24 小时登录状态
- 相关文件：src/auth/session.ts

请分析问题原因并修复"

# 终端 2：审查 PR
cd ~/project
claude "审查 PR #123 的代码改动：
$(gh pr diff 123)

请检查：
1. 代码质量和可读性
2. 潜在的 bug
3. 性能问题
4. 安全隐患"
```

---

### 案例三：重构项目（4 窗口流水线）

**场景**：大规模重构，需要分析、规划、执行、验证。

```
┌───────────────────────────┬───────────────────────────┐
│ 窗格 1: 代码分析           │ 窗格 2: 重构规划           │
│                           │                           │
│ claude                    │ claude                    │
│ > 分析 src/legacy 目录     │ > 基于分析结果制定         │
│   的代码质量问题           │   重构计划和优先级         │
├───────────────────────────┼───────────────────────────┤
│ 窗格 3: 执行重构           │ 窗格 4: 测试验证           │
│                           │                           │
│ claude                    │ claude                    │
│ > 按计划重构第一个模块     │ > 运行测试，验证重构       │
│                           │   没有破坏现有功能         │
└───────────────────────────┴───────────────────────────┘
```

**操作流程**：

```bash
# 窗格 1：分析阶段
claude "分析 src/legacy 目录的代码问题：
1. 识别重复代码
2. 找出过长的函数
3. 检查循环依赖
4. 评估测试覆盖率
输出问题清单和严重程度"

# 窗格 2：等窗格 1 有初步结果后开始规划
claude "基于代码分析结果，制定重构计划：
1. 按模块划分重构任务
2. 确定优先级（高风险先测试覆盖）
3. 估算每个任务的影响范围
4. 设计回滚方案"

# 窗格 3：执行重构
claude "重构 UserService 模块：
1. 拆分过长的 processUser 函数
2. 提取公共逻辑到 utils
3. 添加类型定义
4. 保持 API 兼容"

# 窗格 4：持续验证
watch -n 30 'npm test'  # 每 30 秒运行测试
# 或
claude "运行测试并分析失败原因：$(npm test 2>&1)"
```

---

### 案例四：多模型对比（3 窗口同时提问）

**场景**：对比不同 AI 模型的回答质量。

```bash
# 终端 1：Claude Opus（最强推理）
claude --model opus "设计一个高并发订单系统的架构"

# 终端 2：Claude Sonnet（平衡）
claude --model sonnet "设计一个高并发订单系统的架构"

# 终端 3：Claude Haiku（快速）
claude --model haiku "设计一个高并发订单系统的架构"

# 对比三个模型的回答，选择最佳方案
```

---

### 案例五：跨项目同步开发（Git Worktrees）

**场景**：同时开发主项目的两个独立功能分支。

```bash
# 1. 创建 worktrees
cd ~/main-project
git worktree add ../feature-payment feature/payment
git worktree add ../feature-notification feature/notification

# 2. 终端 1：支付功能
cd ../feature-payment
claude "实现支付模块：
1. 集成 Stripe API
2. 支持多种支付方式
3. 添加支付回调处理"

# 3. 终端 2：通知功能
cd ../feature-notification
claude "实现通知模块：
1. 支持邮件、短信、推送
2. 通知模板管理
3. 发送队列和重试机制"

# 4. 两个功能独立开发，互不干扰
# 完成后分别提交 PR

# 5. 清理 worktrees
git worktree remove ../feature-payment
git worktree remove ../feature-notification
```

---

### 案例六：文档 + 代码同步更新

**场景**：修改 API 的同时更新文档。

```bash
# 终端 1：修改 API
claude "给 /api/users 接口添加 filter 参数：
- 支持按 status 筛选
- 支持按 role 筛选
- 支持按 created_at 范围筛选"

# 终端 2：同步更新文档
claude "更新 API 文档 docs/api/users.md：
- 添加新的 filter 参数说明
- 更新请求示例
- 添加筛选使用案例"

# 终端 3：更新测试
claude "为 /api/users 的 filter 功能添加测试：
- 单个筛选条件测试
- 多个筛选条件组合测试
- 边界情况测试"
```

---

### 多任务效率对比

| 任务类型 | 串行耗时 | 并行耗时 | 效率提升 |
|---------|---------|---------|---------|
| 全栈功能开发 | 3 小时 | 1 小时 | **3x** |
| Bug 修复 + PR 审查 | 2 小时 | 1 小时 | **2x** |
| 大规模重构 | 8 小时 | 3 小时 | **2.7x** |
| API + 文档 + 测试 | 2 小时 | 45 分钟 | **2.7x** |

### 多任务注意事项

```
⚠️ 避免的情况：
1. 多个窗口同时修改同一个文件（会产生冲突）
2. 超过 API 频率限制（可能被限流）
3. 任务之间有强依赖（应该串行执行）

✅ 推荐的做法：
1. 按模块/功能划分任务
2. 使用 Git 分支隔离变更
3. 定期同步和合并
4. 监控 API 使用量
```

---

## CLI 进阶技巧

### 1. 管道操作

```bash
# 将 git diff 传给 Claude 分析
git diff | claude "解释这些改动并检查潜在问题"

# 将错误日志传给 Claude
cat error.log | claude "分析这些错误的原因"

# 将命令输出传给 Claude
npm test 2>&1 | claude "分析测试失败的原因"
```

### 2. 脚本自动化

```bash
#!/bin/bash
# auto-review.sh - 自动代码审查脚本

# 获取最近的改动
CHANGES=$(git diff HEAD~1)

# 让 Claude 审查
claude --print "审查以下代码改动，指出问题：

$CHANGES" > review-report.md

echo "审查报告已生成：review-report.md"
```

### 3. 自定义别名

```bash
# 添加到 ~/.bashrc 或 ~/.zshrc

# 快速启动
alias cc="claude"
alias ccp="claude --print"

# 常用任务
alias cc-review="claude '审查当前目录的代码质量'"
alias cc-test="claude '为当前文件生成单元测试'"
alias cc-doc="claude '为当前项目生成 README'"
alias cc-fix="claude '修复当前文件的所有 lint 错误'"

# 带上下文启动
alias cc-react="claude --context 'React TypeScript 项目'"
alias cc-python="claude --context 'Python 项目，使用 pytest'"
```

### 4. 项目模板

```bash
# 创建项目初始化脚本
#!/bin/bash
# init-claude-project.sh

mkdir -p .claude

cat > .claude/settings.json << 'EOF'
{
  "model": "claude-sonnet-4-20250514",
  "permissions": {
    "allow": ["Read", "Write", "Edit", "Bash", "Glob", "Grep"]
  }
}
EOF

cat > CLAUDE.md << 'EOF'
# 项目说明

## 技术栈
-

## 代码规范
-

## 注意事项
-
EOF

echo "Claude Code 项目配置已初始化"
```

### 5. 上下文管理技巧

```bash
# 只加载必要的文件
claude --include "src/api/**/*.ts" "优化 API 层代码"

# 排除不需要的文件
claude --exclude "node_modules,dist,*.log" "分析项目"

# 使用 .claudeignore 文件
cat > .claudeignore << EOF
node_modules/
dist/
*.log
*.lock
EOF
```

### 6. 费用控制

```bash
# 查看当前会话费用
/cost

# 使用更便宜的模型处理简单任务
/model haiku
"格式化这段 JSON"

# 切回高级模型处理复杂任务
/model opus
"重构这个复杂的算法"

# 压缩上下文节省 token
/compact
```

---

## 常见问题解答

### Q1: API Key 无效或认证失败？

```bash
# 检查环境变量是否设置
echo $ANTHROPIC_API_KEY
echo $OPENAI_API_KEY

# 检查 Key 格式
# Anthropic: sk-ant-api03-xxxxx
# OpenAI: sk-xxxxx

# 检查 Key 是否有余额
# 访问对应的控制台查看
```

### Q2: 连接超时或网络错误？

```bash
# 检查代理是否正常
curl -I https://api.anthropic.com

# 检查代理配置
echo $http_proxy
echo $https_proxy

# 尝试直接设置代理
export https_proxy="http://127.0.0.1:7890"
claude
```

### Q3: 命令找不到（command not found）？

```bash
# 检查是否安装成功
npm list -g @anthropic-ai/claude-code

# 检查 npm 全局路径是否在 PATH 中
npm config get prefix
echo $PATH

# 手动添加到 PATH
export PATH="$PATH:$(npm config get prefix)/bin"
```

### Q4: 权限被拒绝？

```bash
# Claude Code 默认需要确认危险操作
# 可以在配置中预先授权
{
  "permissions": {
    "allow": ["Read", "Write", "Edit", "Bash"]
  }
}

# 或使用 --dangerously-skip-permissions（不推荐）
claude --dangerously-skip-permissions
```

### Q5: 上下文太长/Token 超限？

```bash
# 压缩当前上下文
/compact

# 清除上下文重新开始
/clear

# 使用 .claudeignore 排除大文件
echo "*.min.js" >> .claudeignore
echo "package-lock.json" >> .claudeignore
```

### Q6: 如何降低费用？

```
1. 使用更便宜的模型（Haiku < Sonnet < Opus）
2. 及时压缩上下文（/compact）
3. 使用 .claudeignore 排除不必要的文件
4. 简单任务用简单模型
5. 善用 --print 模式避免不必要的交互
```

### Q7: 多个项目如何管理不同配置？

```bash
# 每个项目使用独立的 .claude 目录
project-a/.claude/settings.json  # 项目 A 配置
project-b/.claude/settings.json  # 项目 B 配置

# 全局配置作为默认值
~/.claude/settings.json

# 项目配置会覆盖全局配置
```

### Q8: 如何查看 CLI 的所有可用选项？

```bash
# 查看帮助
claude --help
codex --help
gemini --help

# 查看版本
claude --version
```

---

## 快速参考卡片

### Claude Code

```bash
# 安装
npm install -g @anthropic-ai/claude-code

# 配置
export ANTHROPIC_API_KEY="sk-ant-xxxxx"

# 启动
claude

# 常用命令
/help          # 帮助
/clear         # 清除上下文
/compact       # 压缩上下文
/cost          # 查看费用
/model <name>  # 切换模型
```

### Codex CLI

```bash
# 安装
npm install -g @openai/codex

# 配置
export OPENAI_API_KEY="sk-xxxxx"

# 启动
codex

# 常用选项
--model gpt-4o    # 指定模型
--auto            # 自动执行模式
```

### Gemini CLI

```bash
# 安装
npm install -g @google/gemini-cli

# 配置（使用 Google 账号登录）
gemini auth login

# 启动
gemini

# 常用选项
--sandbox         # 沙盒模式
--image file.jpg  # 多模态输入
```

### Kiro CLI

```bash
# 安装
brew install kiro-cli
# 或
curl -fsSL https://cli.kiro.dev/install | bash

# 配置（浏览器登录 AWS 账号）
kiro-cli auth login

# 启动
kiro-cli

# 常用命令
/help            # 帮助
/context         # 查看上下文
/spec            # 创建规格文档
/hooks           # 管理自动化钩子
```

### 代理配置

```bash
# 通用代理设置
export http_proxy="http://127.0.0.1:7890"
export https_proxy="http://127.0.0.1:7890"
```

---

## 🎯 分享版：AI CLI 工具速查表

> 以下是精炼版内容，适合快速分享和参考。

### 一句话选型

```
💡 新手免费体验 → Gemini CLI
💼 专业开发首选 → Claude Code
☁️ AWS 生态用户 → Kiro CLI
🐙 GitHub 用户 → Copilot CLI
🔧 多模型玩家 → OpenCode / Aider
💰 预算有限 → Gemini CLI（免费）+ Aider（开源）
```

### 30 秒安装

```bash
# Claude Code
npm i -g @anthropic-ai/claude-code && export ANTHROPIC_API_KEY="sk-ant-xxx"

# Codex CLI
npm i -g @openai/codex && export OPENAI_API_KEY="sk-xxx"

# Gemini CLI
npm i -g @google/gemini-cli && gemini auth login

# Kiro CLI
curl -fsSL https://cli.kiro.dev/install | bash && kiro-cli auth login

# Copilot CLI
npm i -g @github/copilot && copilot

# OpenCode
brew install opencode-ai/tap/opencode

# Aider
pip install aider-chat
```

### 核心命令速记

| 操作 | Claude | Codex | Gemini | Kiro | Copilot | OpenCode | Aider |
|------|--------|-------|--------|------|---------|----------|-------|
| 帮助 | `/help` | `/help` | `/help` | `/help` | `/help` | `/help` | `/help` |
| 清除 | `/clear` | `/clear` | `/clear` | `/clear` | `/clear` | `/clear` | `/clear` |
| 切换模型 | `/model` | `/model` | `/model` | `/model` | `/model` | `/model` | `/model` |
| 添加文件 | `/add` | `/add` | `/add` | - | `/add-dir` | `/add` | `/add` |
| 撤销 | - | `/undo` | - | - | - | `/undo` | `/undo` |
| 退出 | `/quit` | `/exit` | `/quit` | `/quit` | `/exit` | `/quit` | `/quit` |

### 关键差异一览

| | Claude | Codex | Gemini | Kiro | Copilot | OpenCode | Aider |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 免费 | ❌ | ❌ | ✅ | 试用 | ❌ | 取决于模型 | 取决于模型 |
| 开源 | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ |
| 本地模型 | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ |
| Git 集成 | ⭐⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| 代码理解 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |

### 代理配置（国内必备）

```bash
# 添加到 ~/.zshrc 或 ~/.bashrc
export http_proxy="http://127.0.0.1:7890"
export https_proxy="http://127.0.0.1:7890"
```

### 多任务并行（CLI 核心优势）

```bash
# 用 tmux 开 3 个窗格同时工作
tmux new -s dev
Ctrl+b %  # 垂直分割
Ctrl+b "  # 水平分割

# 窗格1: claude "实现后端 API"
# 窗格2: claude "实现前端页面"
# 窗格3: claude "编写单元测试"
# 效率提升 3x！
```

### API Key 管理

```bash
# 方式1: ccuswitcher（推荐）
npm i -g ccuswitcher
ccswitch add work sk-ant-xxx
ccswitch use work

# 方式2: 自定义函数
claude-key() { export ANTHROPIC_API_KEY=$1; }
```

### 费用控制技巧

```
1. 简单任务用 Haiku/GPT-4o-mini（便宜 10x）
2. /compact 压缩上下文（省 token）
3. .claudeignore 排除大文件
4. Gemini CLI 免费额度：1500次/天
```

---

## 总结

| 工具 | 最适合场景 | 上手难度 | 费用 |
|------|-----------|---------|------|
| **Claude Code** | 专业开发、复杂代码理解 | ⭐⭐⭐ | 按量付费 |
| **Codex CLI** | 多模型需求、OpenAI 生态 | ⭐⭐ | 按量付费 |
| **Gemini CLI** | 新手入门、预算有限 | ⭐ | 免费额度大 |
| **Kiro CLI** | AWS 项目、Spec 驱动开发 | ⭐⭐ | AWS 订阅 |
| **OpenCode** | 多模型、本地部署 | ⭐⭐ | 仅 API 费用 |
| **Aider** | Git 重度用户、多文件重构 | ⭐⭐ | 仅 API 费用 |

**新手建议路径**：
1. 从 Gemini CLI 开始（免费）
2. 熟悉后尝试 Claude Code（更强大）
3. AWS 用户可考虑 Kiro CLI
4. 根据需求选择长期使用的工具

**记住**：CLI 的核心优势是**轻量、可控、可自动化**。一旦掌握，你会发现它比 IDE 更高效。

---

*最后更新：2025-02*
