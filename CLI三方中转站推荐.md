# CLI 三方中转站推荐

> 本文档整理了各类 AI CLI 工具的第三方 API 服务和订阅方式

---

## 1. CodeX (OpenAI)

### 安装方式

```bash
# Homebrew (推荐)
brew install --cask codex

# npm
npm install -g @openai/codex
```

### 官方福利 🎉

> ✅ **目前 CodeX 免费开放！** 仅限 Mac 用户

| 用户类型 | 额度 |
|----------|------|
| 免费用户 | Plus 级别额度 |
| Plus 用户 | 双倍额度 |
| Pro 用户 | 双倍额度 |

**CodeX Mac App**: 除了 CLI，还有桌面应用，适合产品经理和设计师使用

**扩展支持**: ✅ MCP | ✅ Skills

### 第三方推荐 - 银河录像局

- **链接**: https://nf.video/wYsab/?gid=105
- **稳定性**: ⭐⭐⭐⭐⭐ 大半年无问题
- **特点**: 量大管饱
- **缺点**: 暂不支持opencode正在适配中说是

> ⚠️ **警告**: 不建议在银河购买 Claude Code 服务！走的是官方 API，容易被 Anthropic 封号导致不可用。CC 建议通过 Apple ID 官方订阅更稳妥。

---

## 2. Claude Code 推荐 - ByteCatCode

### 安装方式

```bash
# Homebrew (推荐)
brew install --cask claude-code

# npm
npm install -g @anthropic-ai/claude-code
```

- **链接**: https://bytecatcode.org/register?aff=4SX3
- **服务方式**: 逆向 Kiro 反代提供服务
- **稳定性**: ⭐⭐⭐⭐ 使用两周暂时稳定
- **福利**: 不定期群里分发千刀 API Key 使用
- **分组**: 最好付费使用建议super或者aws那个分组，token设置中最下面那个开关记着打开.

### 付费方式

| 方式 | 说明 |
|------|------|
| 月度付费 | 按月订阅 |
| 按量付费 | 1 RMB = 1$ 额度 |

### 价格优势

- 倍率约为官方 **0.2x**，非常划算
- 建议：新手可以先充 10-20 块试试水

## https://hongmacc.com/signup?ref=HONGMACC-377DA889

### 其他服务

- Gemini CLI API
- CodeX CLI API

---

## 3. Gemini CLI (Google)

### 安装方式

```bash
# Homebrew
brew install gemini-cli

# npm
npm install -g @google/gemini-cli

# 免安装试用
npx @google/gemini-cli
```

> 💡 **免费额度**: 登录 Google 账号后，免费用户可享 60 次/分钟、1000 次/天的请求额度

支持 Gemini 3 系列模型：

- Gemini 3 Flash
- Gemini 3 Pro
- Gemini 3 Preview

**扩展支持**: ✅ MCP | ✅ Skills

---

## 4. Kiro (AWS/Amazon)

AWS 推出的 AI 驱动 IDE，支持 CLI 模式。

### 安装方式

- **Linux**: 下载 AppImage 或 zip 文件
- **Windows**: 通过 WSL 安装
- **要求**: glibc 2.34+（旧系统可用 musl 版本）

### 特性

- Spec-Driven Development（规格驱动开发）
- Vibe Coding（对话式快速原型）
- Agent Hooks（自动化钩子）
- Steering Rules（开发风格定义）

> 💡 **提示**: ByteCatCode 的反代服务就是基于逆向 Kiro 实现的

---

## 5. GitHub Copilot CLI

GitHub 官方推出的 AI 编码 CLI 工具。

### 安装方式

```bash
# npm 安装
npm install -g @github/copilot

# 启动
copilot

# 首次使用需登录
/login
```

> ⚠️ **注意**: 旧版 `gh copilot` 命令将于 2025年10月25日 停用，请迁移到新版 npm 包

### 特性

- 支持多模型（包括 Claude Sonnet 4）
- MCP 支持
- Git/GitHub 深度集成（PR、Issues 管理）
- 支持读取 CLAUDE.md、GEMINI.md 等指令文件

**扩展支持**: ✅ MCP

---

## 6. OpenRouter

通用 API 路由服务，支持多种模型接入

---

## 7. 国产 CLI 方案 🇨🇳

也可以支持国产大模型的 CLI 工具：

### Kimi CLI (月之暗面)

```bash
# 安装 uv 包管理器
curl -LsSf https://astral.sh/uv/install.sh | sh

# 安装 Kimi CLI
uv tool install --python 3.13 kimi-cli

# 升级
uv tool upgrade kimi-cli --no-cache
```

### GLM 4.7 (智谱 AI)

通过配置环境变量在 Claude Code 中使用：

```bash
# macOS / Linux
export ANTHROPIC_AUTH_TOKEN="你的智谱API_Key"
export ANTHROPIC_BASE_URL="https://open.bigmodel.cn/api/anthropic"
```

> 💡 **提示**: GLM Coding Plan 已升级到 GLM-4.7 模型，性价比更高

### Qoder CLI (阿里通义) ⭐ 推荐

阿里巴巴推出的 AI 编程 CLI，基于通义千问 Qwen3-Coder 模型。

```bash
# 安装方式
npm install -g @qoder-ai/qodercli

# 或使用 curl
curl -fsSL https://qoder.com/install | bash
```

**核心特性**：
- **Quest Mode**: 任务规格式编程，AI 自主完成开发
- **CodeReview**: 终端内高效代码审查
- **RepoWiki**: 自动生成代码库文档
- **MCP 支持**: 多代理协作协议
- **低延迟**: 响应延迟低至 200ms
- **中文优化**: 对中文场景适配更好

**支持语言**: JavaScript、TypeScript、Python、Go、C/C++、C#、Java

**优势**: 内存占用降低 70%，代码审查效率提升 50%，国内访问无障碍

### MiniMax

MiniMax 暂无独立 CLI，主要通过 API Key 接入其他 CLI 工具（如 OpenClaw）使用，或使用其桌面客户端 MiniMax Cowork。

**实测参考**: [Linux.do 社区帖子](https://linux.do/t/topic/1544250)

---

## 8. 官方订阅替代方案

### 方案 A：外区 Apple ID + 礼品卡 ⭐ 推荐

| 优点 | 缺点 |
|------|------|
| 被封号可申诉退款 | 25% 苹果税 |
| 1个月内苹果会退全款 | 需要外区账号 |
| 官方渠道最稳定 | - |

> ✅ **好消息**: 目前官方对于封号会及时退款，风险可控

**省钱技巧**: 可选尼日利亚区，价格更便宜

**Claude Code 最佳方案**: 强烈建议通过 Apple ID 官方订阅，比第三方 API 更稳定安全

### 方案 B：Google Play 礼品卡

适用于 Gemini CLI 等 Google 服务订阅：

| 优点 | 缺点 |
|------|------|
| 无苹果税 | 需要外区 Google 账号 |
| 支持 Android 设备 | - |

### 方案 C：代充服务

- 需要代充可以加微信联系
- 已验证：Pro 订阅可代充

### 方案 D：U 卡虚拟信用卡

**操作步骤**:

1. 使用外区 Apple ID 下载 **Bitget**
2. 充值 USDT
3. 开通虚拟信用卡
4. 使用该卡去官方网页版订阅
5. 地址填写：搜索「地址生成器」生成即可

> ⚠️ **注意**: 不推荐炒币，仅用于开卡订阅

---

## 🏆 终极推荐

> 如果追求 **稳定 + 性能 + 体验**，建议上：
>
> **OpenCode + OhMyOpenCode + CodeX API**

这套组合兼顾了稳定性、性能和使用体验，是目前较为理想的方案。

---

## 总结对比

| 方案 | 成本 | 稳定性 | 推荐指数 |
|------|------|--------|----------|
| 银河录像局 (CodeX) | 中 | 高 | ⭐⭐⭐⭐⭐ |
| ByteCatCode (CC) | 低 | 中高 | ⭐⭐⭐⭐ |
| OpenRouter | 中 | 高 | ⭐⭐⭐⭐ |
| Apple 礼品卡 | 高(含税) | 官方 | ⭐⭐⭐ |
| Google Play 礼品卡 | 中 | 官方 | ⭐⭐⭐⭐ |
| U 卡虚拟卡 | 中 | 官方 | ⭐⭐⭐⭐ |

---

*最后更新: 2025年2月*
