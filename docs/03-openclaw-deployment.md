# 第三章：OpenClaw 完全部署指南

> **⏱️ 预计时间**：60-90 分钟  
> **📦 所需工具**：Node.js, Python, Git  
> **⚠️ 风险等级**：低

---

## 3.1 什么是 OpenClaw？

### 核心概念

```
OpenClaw = AI 大脑 + 手和脚

AI 大脑：大语言模型（如 Qwen, Llama）
手和脚：工具系统（文件操作、网页搜索、定时任务等）
```

### OpenClaw 能做什么？

| 功能 | 说明 | 示例 |
|------|------|------|
| 📝 内容创作 | 写文章、文案、脚本 | 小红书文案、公众号文章 |
| 💬 自动回复 | 客服、评论回复 | 淘宝客服、社交媒体 |
| 📊 数据处理 | Excel 整理、报表生成 | 销售数据汇总 |
| 🔍 市场调研 | 竞品信息收集 | 竞品价格监控 |
| ⏰ 定时任务 | 自动执行计划任务 | 每天生成日报 |
| 🧠 多模型协作 | 按任务切换模型 | 简单任务用小模型 |

---

## 3.2 前置依赖安装

### 步骤 1：安装 Homebrew（Mac 包管理器）

```bash
# 打开终端（应用程序 > 实用工具 > 终端）

# 安装 Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 按提示操作：
# 1. 按回车开始
# 2. 输入 Mac 密码（输入时不显示，正常）
# 3. 等待安装完成（约 3-5 分钟）

# 安装完成后，运行提示的命令：
# M4 Mac 运行：
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"

# 验证安装
brew --version
# 应显示：Homebrew 4.x.x
```

### 步骤 2：安装 Node.js 20

```bash
# 使用 Homebrew 安装
brew install node@20

# 验证
node --version
# 应显示：v20.x.x

npm --version
# 应显示：10.x.x
```

### 步骤 3：安装 Python 3.11

```bash
brew install python@3.11

# 验证
python3 --version
# 应显示：Python 3.11.x
```

### 步骤 4：安装 Git

```bash
brew install git

# 验证
git --version
# 应显示：git version 2.x.x
```

---

## 3.3 安装 OpenClaw

### 方法 1：全局安装（推荐）

```bash
# 使用 npm 全局安装
npm install -g openclaw

# 验证安装
openclaw --version

# 查看帮助
openclaw --help
```

### 方法 2：源码安装（高级用户）

```bash
# 克隆仓库
git clone https://github.com/openclaw/openclaw.git
cd openclaw

# 安装依赖
npm install

# 链接到全局
npm link
```

---

## 3.4 初始化工作空间

### 步骤 1：创建工作空间目录

```bash
# 在外置 SSD 上创建工作空间
mkdir -p /Volumes/MacOS-SSD/openclaw-workspace
cd /Volumes/MacOS-SSD/openclaw-workspace
```

### 步骤 2：初始化 OpenClaw

```bash
# 初始化工作空间
openclaw init

# 或使用自定义路径
openclaw init --workspace /Volumes/MacOS-SSD/openclaw-workspace
```

### 步骤 3：查看目录结构

```bash
ls -la

# 应看到以下结构：
# ├── AGENTS.md          # 代理配置
# ├── SOUL.md            # 人格配置
# ├── USER.md            # 用户信息
# ├── TOOLS.md           # 工具配置
# ├── HEARTBEAT.md       # 心跳任务
# ├── memory/            # 记忆文件
# ├── skills/            # 技能文件
# └── openclaw.json      # 主配置文件
```

---

## 3.5 配置文件详解

### openclaw.json 完整配置

```json
{
  "meta": {
    "name": "Mac-M4-Ultimate",
    "version": "2.0",
    "createdAt": "2026-03-03"
  },
  
  "env": {
    "OLLAMA_MODELS": "/Volumes/MacOS-SSD/ollama-models",
    "OPENCLAW_WORKSPACE": "/Volumes/MacOS-SSD/openclaw-workspace"
  },
  
  "models": {
    "mode": "merge",
    "providers": {
      "ollama": {
        "baseUrl": "http://localhost:11434",
        "api": "openai-completions",
        "models": [
          {
            "id": "qwen2.5:7b",
            "name": "Qwen2.5-7B (中文主力)",
            "contextWindow": 32768,
            "maxTokens": 8192
          },
          {
            "id": "llama3.2:3b",
            "name": "Llama3.2-3B (轻量快速)",
            "contextWindow": 8192,
            "maxTokens": 2048
          },
          {
            "id": "codellama:7b",
            "name": "CodeLlama-7B (编程专用)",
            "contextWindow": 16384,
            "maxTokens": 4096
          }
        ]
      }
    }
  },
  
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/qwen2.5:7b",
        "fallbacks": [
          "ollama/llama3.2:3b"
        ]
      },
      "workspace": "/Volumes/MacOS-SSD/openclaw-workspace",
      "maxConcurrent": 2,
      "subagents": {
        "maxConcurrent": 4
      }
    }
  },
  
  "tools": {
    "allow": [
      "web_search",
      "web_fetch",
      "exec",
      "read",
      "write",
      "edit",
      "browser"
    ],
    "deny": []
  },
  
  "cron": {
    "enabled": true
  },
  
  "gateway": {
    "port": 15252,
    "mode": "local",
    "bind": "localhost",
    "auth": {
      "mode": "token",
      "token": "your-secure-token-here"
    }
  }
}
```

### 关键配置说明

```json
// 模型配置
"models": {
  "primary": "ollama/qwen2.5:7b",  // 默认使用 Qwen2.5 7B
  "fallbacks": [                   // 如果失败，切换到
    "ollama/llama3.2:3b"           // Llama3.2 3B（更快）
  ]
}

// 工具权限
"tools": {
  "allow": [           // 允许使用的工具
    "web_search",      // 网页搜索
    "exec",            // 执行命令
    "read", "write"    // 文件读写
  ]
}

// 网关配置
"gateway": {
  "port": 15252,       // 访问端口
  "bind": "localhost", // 只允许本地访问（安全）
  "auth": {
    "mode": "token"    // 需要令牌认证
  }
}
```

---

## 3.6 启动 OpenClaw

### 步骤 1：启动 Gateway 服务

```bash
# 进入工作空间
cd /Volumes/MacOS-SSD/openclaw-workspace

# 启动 Gateway
openclaw gateway start

# 查看状态
openclaw status

# 查看日志
openclaw logs --follow
```

### 步骤 2：访问控制界面

```
浏览器访问：http://localhost:15252

如果看到 OpenClaw 界面，说明启动成功！✅
```

### 步骤 3：测试对话

```bash
# 通过 CLI 测试
openclaw chat "你好，请用中文自我介绍"

# 应该得到中文回复
```

---

## 3.7 配置多模型自动切换

### 创建模型路由技能

```bash
# 创建技能目录
mkdir -p /Volumes/MacOS-SSD/openclaw-workspace/skills/model-router

# 创建技能文件
cat > /Volumes/MacOS-SSD/openclaw-workspace/skills/model-router/SKILL.md << 'EOF'
# 模型自动路由技能

## 路由规则

| 任务类型 | 关键词 | 使用模型 | 原因 |
|----------|--------|----------|------|
| 编程开发 | 代码、python、函数、bug | codellama:7b | 编程专用 |
| 简单问答 | 是什么、为什么、多少 | llama3.2:3b | 速度最快 |
| 中文写作 | 文章、文案、故事 | qwen2.5:7b | 中文能力强 |
| 数据分析 | 表格、excel、统计 | qwen2.5:7b | 理解力强 |

## 实现逻辑

1. 分析用户输入，提取关键词
2. 匹配任务类型
3. 选择对应模型
4. 执行任务
5. 如果失败，自动切换到 fallback 模型
EOF
```

### 配置自动切换

编辑 `openclaw.json`：

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/qwen2.5:7b",
        "fallbacks": [
          "ollama/llama3.2:3b",
          "ollama/codellama:7b"
        ]
      }
    }
  }
}
```

---

## 3.8 创建第一个技能

### 内容创作技能

```bash
mkdir -p /Volumes/MacOS-SSD/openclaw-workspace/skills/content-writer

cat > /Volumes/MacOS-SSD/openclaw-workspace/skills/content-writer/SKILL.md << 'EOF'
# 内容创作技能

## 触发条件
用户说"写文章"、"写文案"、"写脚本"

## 工作流程

### 步骤 1：需求收集
询问：
1. 主题是什么？
2. 目标平台？（公众号/小红书/知乎）
3. 字数要求？
4. 风格偏好？

### 步骤 2：大纲生成
使用模型：ollama/qwen2.5:7b
- 生成文章大纲
- 展示给用户确认

### 步骤 3：分段写作
- 按大纲分段写作
- 每段 300-500 字

### 步骤 4：保存文件
保存到：/Volumes/MacOS-SSD/openclaw-workspace/projects/content/
EOF
```

---

## 3.9 配置自动化任务

### 配置 Heartbeat

编辑 `HEARTBEAT.md`：

```markdown
# HEARTBEAT.md - 自动检查任务

## 每天检查（9:00, 14:00, 20:00）
- [ ] 检查是否有新任务
- [ ] 检查社交媒体评论
- [ ] 检查待处理数据
- [ ] 检查模型运行状态

## 每周一检查（9:00）
- [ ] 总结上周任务
- [ ] 生成周报
- [ ] 更新客户跟进列表
```

### 配置 Cron 定时任务

```bash
# 查看 cron 状态
openclaw cron status

# 添加定时任务
openclaw cron add --schedule "0 9 * * *" --command "check-tasks"
```

---

## 3.10 故障排查

### 问题 1：Gateway 无法启动

```bash
# 查看日志
openclaw logs

# 检查端口是否被占用
lsof -i :15252

# 如果端口被占用，修改 openclaw.json 中的端口
```

### 问题 2：模型无法连接

```bash
# 检查 Ollama 是否运行
ollama list

# 如果没有运行，启动 Ollama
ollama serve

# 或设置开机自启
brew services start ollama
```

### 问题 3：权限错误

```bash
# 检查工作空间权限
ls -la /Volumes/MacOS-SSD/openclaw-workspace

# 修复权限
chmod -R 755 /Volumes/MacOS-SSD/openclaw-workspace
```

---

## 3.11 下一步

- 📖 **第四章**：内容创作自动化实战
- 📖 **第五章**：客户服务自动化
- 📖 **第六章**：数据分析自动化

---

*由 Ni 为你编写* 👁️‍🗨️⚡
