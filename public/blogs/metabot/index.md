# MetaBot 配置指南：从 0 到 1 实现飞书远程控制 Claude Code

## 目录

1. [环境准备](#1-环境准备)
2. [创建飞书应用](#2-创建飞书应用)
3. [安装 MetaBot](#3-安装-metabot)
4. [配置文件](#4-配置文件)
5. [启动 MetaBot](#5-启动-metabot)
6. [飞书事件订阅](#6-飞书事件订阅)
7. [测试验证](#7-测试验证)
8. [常用命令](#8-常用命令)
   - [接力对话功能](#接力对话功能)
9. [多 Bot 配置](#9-多-bot-配置)
10. [常见问题](#10-常见问题)

---

## 1. 环境准备

### 必需条件

| 工具 | 最低版本 | 检查命令 | 安装方式 |
|------|---------|---------|---------|
| **Node.js** | 20+ | `node --version` | [nodejs.org](https://nodejs.org/) |
| **Claude Code CLI** | 最新 | `claude --version` | `npm install -g @anthropic-ai/claude-code` |
| **Git** | 任意 | `git --version` | [git-scm.com](https://git-scm.com/) |

### 可选条件

- **Anthropic 官方订阅**：通过 `claude login` OAuth 登录
- **第三方 API**：支持 Anthropic 兼容 API（如 DeepSeek、Kimi、小米等）

---

## 2. 创建飞书应用

### 2.1 登录飞书开放平台

访问 [open.feishu.cn](https://open.feishu.cn/)，使用飞书账号登录。

### 2.2 创建应用

1. 点击「创建企业自建应用」
2. 填写应用名称（如：`shanbot`）
3. 填写应用描述
4. 点击「创建」

### 2.3 添加机器人能力

1. 进入应用详情页
2. 左侧菜单 → 「添加应用能力」
3. 选择「机器人」→ 点击「添加」

### 2.4 开通权限

进入「权限管理」页面，开通以下权限：

| 权限 | 说明 |
|------|------|
| `im:message` | 发送消息 |
| `im:message:readonly` | 接收消息 |
| `im:resource` | 获取资源（图片、文件） |
| `im:chat:readonly` | 读取群信息 |

### 2.5 记录凭证

在「凭证与基础信息」页面，记录：

- **App ID**（格式：`cli_xxxxxxxxxxxxxxxx`）
- **App Secret**

---

## 3. 安装 MetaBot

### 方式一：一键安装（推荐）

**Linux/macOS：**
```bash
curl -fsSL https://raw.githubusercontent.com/xvirobotics/metabot/main/install.sh | bash
```

**Windows PowerShell：**
```powershell
.\install.ps1 -Dir C:\opt\metabot
```

安装器会引导完成所有配置。

### 方式二：手动安装

```bash
# 1. 克隆项目
git clone https://github.com/xvirobotics/metabot.git ~/metabot
cd ~/metabot

# 2. 安装依赖
npm install

# 3. 复制配置模板
cp bots.example.json bots.json
cp .env.example .env

# 4. 编辑配置文件（见下一步）
```

---

## 4. 配置文件

### 4.1 bots.json（Bot 配置）

编辑 `bots.json`，配置你的飞书 Bot：

```json
{
  "feishuBots": [
    {
      "name": "shanbot",
      "description": "我的 AI 助手",
      "feishuAppId": "cli_a977c9f850b89cc7",
      "feishuAppSecret": "piDG2FFA9Yhe3hrItJsJ0gWGVEBzeFus",
      "defaultWorkingDirectory": "D:/projects/shanshi-ai"
    }
  ]
}
```

**字段说明：**

| 字段 | 必填 | 说明 |
|------|------|------|
| `name` | ✅ | Bot 标识名，用于飞书里 `/bot name` 切换 |
| `description` | ❌ | 描述信息 |
| `feishuAppId` | ✅ | 飞书 App ID |
| `feishuAppSecret` | ✅ | 飞书 App Secret |
| `defaultWorkingDirectory` | ✅ | Claude Code 工作目录（绝对路径） |
| `model` | ❌ | Claude 模型，默认使用 SDK 默认值 |
| `maxTurns` | ❌ | 单次会话最大轮数 |
| `maxBudgetUsd` | ❌ | 单次会话最大花费（美元） |

### 4.2 .env（环境变量配置）

编辑 `.env`，配置全局设置：

```bash
# =============================================================================
# MetaBot 配置
# =============================================================================

# 多 Bot 配置文件路径
BOTS_CONFIG=./bots.json

# =============================================================================
# Claude Code 配置
# =============================================================================

# 方式一：使用官方订阅（先运行 claude login）
# 无需配置 API Key

# 方式二：使用第三方 Anthropic 兼容 API
ANTHROPIC_BASE_URL=https://your-third-party-api.com/anthropic
ANTHROPIC_API_KEY=your-api-key

# 方式三：使用官方 API Key
# ANTHROPIC_API_KEY=sk-ant-xxx

# =============================================================================
# 工作目录（单 Bot 模式时使用，多 Bot 模式在 bots.json 中配置）
# =============================================================================
CLAUDE_DEFAULT_WORKING_DIRECTORY=/path/to/your/project

# =============================================================================
# API 服务
# =============================================================================
API_PORT=9100
API_SECRET=        # 设置后启用 Bearer 认证

# =============================================================================
# MetaMemory（知识库）
# =============================================================================
MEMORY_ENABLED=true
MEMORY_PORT=8100
META_MEMORY_URL=http://localhost:8100

# =============================================================================
# 日志
# =============================================================================
LOG_LEVEL=info

# =============================================================================
# 时区（定时任务使用）
# =============================================================================
SCHEDULE_TIMEZONE=Asia/Shanghai
```

### 4.3 第三方 API 配置示例

**DeepSeek：**
```bash
ANTHROPIC_BASE_URL=https://api.deepseek.com/anthropic
ANTHROPIC_API_KEY=sk-xxx
```

**Kimi（月之暗面）：**
```bash
ANTHROPIC_BASE_URL=https://api.moonshot.ai/anthropic
ANTHROPIC_API_KEY=sk-xxx
```

**小米：**
```bash
ANTHROPIC_BASE_URL=https://token-plan-cn.xiaomimimo.com/anthropic
ANTHROPIC_API_KEY=tp-xxx
```

**智谱 GLM：**
```bash
ANTHROPIC_BASE_URL=https://api.z.ai/api/anthropic
ANTHROPIC_API_KEY=xxx
```

---

## 5. 启动 MetaBot

### 开发模式（推荐调试）

```bash
cd ~/metabot
npm run dev
```

### 生产模式（PM2 守护进程）

```bash
# 使用一键安装脚本会自动配置 PM2
# 手动安装需要自己配置：
npm install -g pm2
pm2 start ecosystem.config.cjs
pm2 save
pm2 startup
```

### 检查启动状态

```bash
# 健康检查
curl http://localhost:9100/api/health

# 期望输出：
# {
#   "status": "ok",
#   "uptime": 123,
#   "bots": 1,
#   ...
# }
```

---

## 6. 飞书事件订阅

**重要：MetaBot 启动后，再配置事件订阅！**

### 6.1 进入事件订阅页面

1. 打开 [open.feishu.cn](https://open.feishu.cn/)
2. 进入你的应用
3. 左侧菜单 → 「事件与回调」

### 6.2 选择订阅方式

选择 **「使用长连接接收事件」**

> 不要选「将事件发送至开发者服务器」，那需要公网 IP。

### 6.3 添加事件

点击「添加事件」，搜索并添加：

- `im.message.receive_v1`（接收消息）

### 6.4 发布应用

1. 左侧菜单 → 「版本管理与发布」
2. 点击「创建版本」
3. 填写版本号和更新说明
4. 点击「提交审核」

个人应用一般秒过。

---

## 7. 测试验证

### 7.1 基础测试

在飞书里找到你的机器人，发送：

```
你好，请介绍一下自己
```

### 7.2 功能测试

```
# 查看当前目录
列出当前目录的文件

# 读取文件
读取 README.md 的前 20 行

# 运行命令
运行 node --version
```

### 7.3 多会话测试

1. 新建一个和机器人的私聊
2. 发送 `/status` 查看会话状态
3. 验证两个私聊是独立会话

---

## 8. 常用命令

### 聊天命令

| 命令 | 说明 |
|------|------|
| `/reset` | 清除当前会话，重新开始 |
| `/stop` | 中止当前正在执行的任务 |
| `/status` | 查看会话状态（模型、token 使用等） |
| `/sessions` | 查看历史会话列表 |
| `/resume <n>` | 继续第 n 个历史会话 |
| `/model` | 查看当前模型 |
| `/model list` | 列出可用模型 |
| `/model <name>` | 切换模型 |
| `/model reset` | 恢复默认模型 |
| `/help` | 查看帮助 |

### 接力对话功能

接力对话允许你在不同的飞书聊天窗口中继续之前的对话。

**使用场景：**
- 在手机上继续电脑端未完成的工作
- 在不同的群聊/私聊中切换对话
- 恢复意外中断的会话

**使用方法：**

1. **查看历史会话：**
   ```
   /sessions
   ```
   显示最近 20 个会话，包含预览、时间和平台信息。

2. **继续某个会话：**
   ```
   /resume 1        # 继续最近的会话
   /resume 2        # 继续第 2 个会话
   /resume abc123   # 通过会话 ID 前缀继续
   ```

3. **切换会话：**
   在不同的飞书私聊/群聊窗口中使用 `/resume`，每个窗口可以独立切换到不同的历史会话。

**注意事项：**
- 继续会话后，Claude 会恢复之前的上下文
- 每个聊天窗口的会话是独立的
- 使用 `/reset` 可以清除当前会话，重新开始

### 管理命令（CLI）

```bash
# 进入 MetaBot 目录
cd ~/metabot

# 查看实时日志
npm run dev

# 使用 PM2 管理
pm2 start metabot    # 启动
pm2 stop metabot     # 停止
pm2 restart metabot  # 重启
pm2 logs metabot     # 查看日志

# 使用 metabot CLI（一键安装才有）
metabot start        # 启动
metabot stop         # 停止
metabot restart      # 重启
metabot logs         # 查看日志
metabot update       # 更新
```

---

## 9. 多 Bot 配置

如果需要多个 Bot 对应不同项目，编辑 `bots.json`：

```json
{
  "feishuBots": [
    {
      "name": "shanbot",
      "description": "膳食健康项目",
      "feishuAppId": "cli_aaa",
      "feishuAppSecret": "secret_aaa",
      "defaultWorkingDirectory": "D:/projects/shanshi-ai"
    },
    {
      "name": "workbot",
      "description": "工作项目",
      "feishuAppId": "cli_bbb",
      "feishuAppSecret": "secret_bbb",
      "defaultWorkingDirectory": "D:/projects/work-project"
    },
    {
      "name": "studybot",
      "description": "学习项目",
      "feishuAppId": "cli_ccc",
      "feishuAppSecret": "secret_ccc",
      "defaultWorkingDirectory": "D:/projects/study"
    }
  ]
}
```

**使用方式：**

- 每个 Bot 需要单独创建飞书应用
- 在飞书里给不同 Bot 发消息，操作不同项目
- 用 `/bot workbot` 切换目标 Bot（在同一聊天窗口）

---

## 10. 常见问题

### Q1: 启动后飞书没反应

**检查清单：**

1. MetaBot 是否正在运行？
   ```bash
   curl http://localhost:9100/api/health
   ```

2. 飞书事件订阅是否配置？
   - 确认选择了「长连接」方式
   - 确认添加了 `im.message.receive_v1` 事件

3. 应用是否已发布？
   - 需要创建版本并提交审核

4. 权限是否开通？
   - 确认 `im:message`、`im:message:readonly` 等权限已开通

### Q2: Claude Code 无法启动

**检查：**

1. Claude Code CLI 是否安装？
   ```bash
   claude --version
   ```

2. 是否已认证？
   ```bash
   # 方式一：OAuth 登录（官方订阅）
   claude login

   # 方式二：配置 API Key（第三方）
   # 在 .env 中设置 ANTHROPIC_API_KEY
   ```

3. 工作目录是否存在？
   ```bash
   ls -la /your/working/directory
   ```

### Q3: 第三方 API 报错

**检查：**

1. Base URL 是否正确？
   - 必须是 Anthropic 兼容格式
   - 测试：`curl https://your-api.com/anthropic`

2. API Key 是否有效？
   - 检查是否过期
   - 检查余额是否充足

3. 模型是否支持？
   - 第三方可能不支持所有 Claude 模型
   - 在 `.env` 中指定模型：`CLAUDE_MODEL=claude-sonnet-4-6`

### Q4: 如何更新 MetaBot？

```bash
cd ~/metabot

# 拉取最新代码
git pull

# 安装新依赖
npm install

# 重启
npm run dev
# 或
metabot restart
```

### Q5: 如何查看日志？

```bash
# 开发模式：直接在终端看
npm run dev

# PM2 模式
pm2 logs metabot

# metabot CLI
metabot logs
```

### Q6: 工作目录可以改吗？

可以。编辑 `bots.json` 中的 `defaultWorkingDirectory` 字段，然后重启 MetaBot。

```json
{
  "name": "shanbot",
  "defaultWorkingDirectory": "新的/工作/目录/路径"
}
```

### Q7: 支持群聊吗？

支持。

| 场景 | @提及 | 说明 |
|------|-------|------|
| **私聊** | 不需要 | 所有消息直接发送给 Bot |
| **两人群**（你 + Bot） | 不需要 | 自动识别为类私聊 |
| **多人群聊** | 需要 @Bot | 只有 @Bot 的消息才会触发回复 |

**推荐**：建一个只有你和 Bot 的两人群聊，不需要每次 @Bot。

### Q8: 如何发送文件给 Bot？

**私聊 / 两人群**：直接发送文件或图片，Bot 自动处理。

**多人群聊**：
1. 先在群里上传文件
2. 5 分钟内 @Bot 说「分析一下」
3. Bot 自动处理之前上传的文件

---

## 附录：完整配置示例

### bots.json（使用第三方 API）

```json
{
  "feishuBots": [
    {
      "name": "shanbot",
      "description": "膳食健康项目 AI 助手",
      "feishuAppId": "cli_a977c9f850b89cc7",
      "feishuAppSecret": "piDG2FFA9Yhe3hrItJsJ0gWGVEBzeFus",
      "defaultWorkingDirectory": "D:/projects/shanshi-ai"
    }
  ]
}
```

### .env（使用第三方 API）

```bash
# Bot 配置
BOTS_CONFIG=./bots.json

# 第三方 API（小米）
ANTHROPIC_BASE_URL=https://token-plan-cn.xiaomimimo.com/anthropic
ANTHROPIC_API_KEY=tp-c7j1j5artxvj69mdzoyc365txj959bnu5yx9ryss9gilrkut

# API 服务
API_PORT=9100

# MetaMemory
MEMORY_ENABLED=true
MEMORY_PORT=8100
META_MEMORY_URL=http://localhost:8100

# 日志
LOG_LEVEL=info

# 时区
SCHEDULE_TIMEZONE=Asia/Shanghai
```

---

## 参考链接

- [MetaBot GitHub](https://github.com/xvirobotics/metabot)
- [MetaBot 文档站](https://xvirobotics.com/metabot/zh/)
- [飞书开放平台](https://open.feishu.cn/)
- [Claude Code 文档](https://docs.anthropic.com/claude-code)

---

**配置完成！现在可以在飞书里远程控制 Claude Code 了。**
