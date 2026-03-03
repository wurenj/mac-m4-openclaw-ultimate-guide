# 第二章：Ollama 完全部署指南（超详细版）

> **⏱️ 预计时间**：60-90 分钟  
> **📦 所需空间**：约 50-100GB（取决于下载的模型数量）  
> **⚠️ 难度等级**：⭐⭐（需要基础命令行操作）  
> **🎯 适合人群**：零基础电脑小白（每个命令都有解释）

---

## 2.1 什么是 Ollama？

### 简单理解

```
Ollama = AI 模型运行器

就像：
- VLC 是视频播放器（播放各种视频文件）
- Ollama 是模型播放器（运行各种 AI 模型）
```

### 为什么选择 Ollama？

| 优势 | 说明 |
|------|------|
| ✅ **简单易用** | 一条命令下载模型，一条命令运行 |
| ✅ **自动优化** | 自动适配 M4 芯片，性能最佳 |
| ✅ **模型丰富** | 支持 100+ 种开源模型 |
| ✅ **免费开源** | 完全免费，无需订阅 |
| ✅ **本地运行** | 数据不出电脑，隐私安全 |
| ✅ **中文支持** | 完美支持中文对话 |

### Ollama vs 其他框架

```
Ollama：
优点：简单、快速、自动优化
缺点：自定义选项较少
适合：新手、日常使用

MLX（Apple 官方）：
优点：Apple 原生、性能最好
缺点：安装复杂、模型少
适合：开发者、高级用户

llama.cpp：
优点：最轻量、最灵活
缺点：需要手动配置
适合：极客、研究者

⭐ 推荐：新手用 Ollama，有经验后再尝试其他
```

---

## 2.2 安装 Homebrew（Mac 包管理器）

### 什么是 Homebrew？

```
Homebrew = Mac 的应用商店（命令行版）

就像：
- App Store 可以图形化安装应用
- Homebrew 可以用命令安装开发工具
```

### 安装步骤

**步骤 1：打开终端**
```
方法 1：
1. 打开"启动台"（Dock 栏的火箭图标）
2. 找到"其他"文件夹
3. 点击"终端"

方法 2：
1. 按 Cmd+Space 打开 Spotlight
2. 输入"终端"
3. 按回车

方法 3：
1. 打开 Finder
2. 应用程序 > 实用工具 > 终端
```

**步骤 2：复制安装命令**
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

**步骤 3：粘贴并执行**
```
1. 在终端窗口点击右键
2. 选择"粘贴"
3. 按回车执行
```

**步骤 4：输入密码**
```
1. 终端会提示输入密码
2. 输入你的开机密码（输入时看不到字符，正常）
3. 按回车
```

**步骤 5：等待安装**
```
安装过程：
✓ 检查系统兼容性
✓ 下载 Homebrew
✓ 安装依赖
✓ 配置环境

约需 3-5 分钟
```

**步骤 6：安装完成**
```
看到以下提示表示成功：

==> Installation successful!
==> Homebrew has enabled anonymous aggregate formula and cask analytics.

==> Next steps:
- Run "brew help" to get started
- Further documentation: https://docs.brew.sh
```

**步骤 7：配置环境变量**
```bash
# M4 芯片（Apple Silicon）运行以下命令：
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"

# 验证安装
brew --version
# 应该显示：Homebrew 4.x.x
```

---

## 2.3 安装 Ollama

### 方法 1：使用 Homebrew（推荐）

```bash
# 1. 安装 Ollama
brew install ollama

# 2. 等待安装完成
# 约需 2-3 分钟

# 3. 验证安装
ollama --version
# 应该显示：ollama version 0.x.x
```

### 方法 2：官网下载

```
1. 访问官网：https://ollama.com
2. 点击"Download"
3. 选择"macOS"
4. 下载完成后打开 DMG 文件
5. 拖拽 Ollama 到应用程序文件夹
6. 打开应用程序 > Ollama
```

---

## 2.4 启动 Ollama 服务

### 方法 1：后台运行（推荐）

```bash
# 启动 Ollama 服务
ollama serve

# 保持这个终端窗口开启
# 或者按 Ctrl+C 停止，用方法 2
```

### 方法 2：开机自启

```bash
# 设置开机自动启动
brew services start ollama

# 查看状态
brew services list

# 应该显示：
# ollama  started  admin  /Users/admin/Library/LaunchAgents/homebrew.mxcl.ollama.plist
```

### 方法 3：手动启动

```
1. 打开应用程序
2. 找到 Ollama
3. 双击打开
4. 菜单栏会出现 Ollama 图标
```

---

## 2.5 配置模型存储位置（重要！）

### 为什么要改存储位置？

```
默认情况：
- 模型存在内置硬盘：~/.ollama/models
- 占用空间：每个模型 4-10GB
- 256GB 硬盘很快就不够用了

我们的目标：
- 模型存在外置 SSD：/Volumes/MacOS-SSD/ollama-models
- 节省内置空间
- 方便管理和备份
```

### 步骤 1：创建存储目录

```bash
# 在外置 SSD 创建模型目录
mkdir -p /Volumes/MacOS-SSD/ollama-models

# 验证
ls -la /Volumes/MacOS-SSD/ollama-models
# 应该显示空目录
```

### 步骤 2：设置环境变量

```bash
# 编辑配置文件
nano ~/.zshrc
```

**添加以下内容：**
```bash
# Ollama 配置
export OLLAMA_MODELS=/Volumes/MacOS-SSD/ollama-models
```

**保存退出：**
```
1. 按 Ctrl+O 保存
2. 按 Enter 确认
3. 按 Ctrl+X 退出
```

### 步骤 3：使配置生效

```bash
# 重新加载配置
source ~/.zshrc

# 验证
echo $OLLAMA_MODELS
# 应该显示：/Volumes/MacOS-SSD/ollama-models
```

### 步骤 4：重启 Ollama 服务

```bash
# 停止服务
brew services stop ollama

# 启动服务
brew services start ollama

# 验证
ollama list
```

---

## 2.6 下载推荐模型

### 模型选择策略

根据你的 16GB 内存，我精心选择了以下模型组合：

```
日常使用（必装）：
├── Qwen2.5 7B - 中文能力强，速度快 ⭐主力
└── Llama 3.2 3B - 超快，简单任务

编程开发（可选）：
└── CodeLlama 7B - 写代码、调试

专业任务（可选）：
├── Qwen2.5 14B - 更强的中文理解
└── Llama 3.1 8B - 通用推理

⚠️ 注意：
- 每个 7B 模型约 4-5GB
- 14B 模型约 8-9GB
- 16GB 内存建议同时只运行 1 个模型
```

### 下载主力模型

**步骤 1：下载 Qwen2.5 7B**
```bash
# 这是你的主力模型，中文能力最强
ollama pull qwen2.5:7b

# 下载过程：
# downloading qwen2.5:7b ...
# [================================] 100% 4.2 GB
# verifying sha256 digest
# writing manifest
# removing any unused layers
# success
```

**步骤 2：下载 Llama 3.2 3B**
```bash
# 轻量级模型，速度快
ollama pull llama3.2:3b

# 约 2GB，下载更快
```

**步骤 3：下载 CodeLlama 7B（可选）**
```bash
# 编程专用模型
ollama pull codellama:7b
```

### 下载进度查看

```bash
# 查看下载进度
ollama list

# 输出示例：
# NAME              ID              SIZE      MODIFIED
# qwen2.5:7b        a8d4a8e8f8c8    4.2 GB    2 minutes ago
# llama3.2:3b       b9e5b9f9g9d9    2.0 GB    5 minutes ago
```

### 下载时间估算

```
网络速度     下载 7B 模型    下载 3B 模型
10 MB/s      约 7 分钟      约 3 分钟
50 MB/s      约 1.5 分钟    约 40 秒
100 MB/s     约 45 秒       约 20 秒

⚠️ 首次下载较慢，后续会使用缓存
```

---

## 2.7 测试模型

### 测试 Qwen2.5 7B

```bash
# 运行模型
ollama run qwen2.5:7b

# 看到提示符后，输入测试问题：
>>> 你好，请用中文简单自我介绍

# 预期回复：
你好！我是 Qwen2.5，一个 7B 参数的语言模型。
我擅长中文理解和生成，可以帮你写作、翻译、回答问题等。
有什么我可以帮你的吗？

# 退出模型
>>> /bye
```

### 测试 Llama 3.2 3B

```bash
# 运行模型
ollama run llama3.2:3b

# 测试问题：
>>> 1+1 等于几？

# 预期快速回复：
1+1 等于 2。

# 退出
>>> /bye
```

### 测试 CodeLlama 7B

```bash
# 运行模型
ollama run codellama:7b

# 测试问题：
>>> 用 Python 写一个计算斐波那契数列的函数

# 预期回复：
def fibonacci(n):
    if n <= 0:
        return []
    elif n == 1:
        return [0]
    elif n == 2:
        return [0, 1]
    
    fib = [0, 1]
    for i in range(2, n):
        fib.append(fib[i-1] + fib[i-2])
    return fib

# 退出
>>> /bye
```

---

## 2.8 模型性能基准测试

### 创建测试脚本

```bash
# 创建测试脚本
cat > /Volumes/MacOS-SSD/test-models.sh << 'EOF'
#!/bin/bash

echo "=== 模型性能测试 ==="
echo ""
echo "测试时间：$(date)"
echo ""

# 测试函数
test_model() {
    local model=$1
    local prompt=$2
    
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "测试模型：$model"
    echo "提示词：$prompt"
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    
    # 记录开始时间
    start_time=$(date +%s%N)
    
    # 运行模型并获取回复
    response=$(ollama run $model "$prompt" 2>&1)
    
    # 记录结束时间
    end_time=$(date +%s%N)
    
    # 计算耗时（毫秒）
    elapsed=$(( (end_time - start_time) / 1000000 ))
    
    echo "响应时间：${elapsed}ms"
    echo ""
    echo "回复内容："
    echo "$response"
    echo ""
}

# 执行测试
test_model "qwen2.5:7b" "用 50 字总结人工智能的发展"
test_model "llama3.2:3b" "用 50 字总结人工智能的发展"
test_model "codellama:7b" "用 Python 写一个 hello world"

echo "=== 测试完成 ==="
EOF

# 赋予执行权限
chmod +x /Volumes/MacOS-SSD/test-models.sh
```

### 运行测试

```bash
# 运行测试脚本
/Volumes/MacOS-SSD/test-models.sh

# 输出示例：
# === 模型性能测试 ===
# 测试时间：Tue Mar 3 12:00:00 2026
# 
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
# 测试模型：qwen2.5:7b
# 提示词：用 50 字总结人工智能的发展
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
# 响应时间：3500ms
# 
# 回复内容：
# 人工智能从 1950 年代图灵测试开始，经历符号主义、连接主义，
# 到深度学习革命，如今在大模型时代实现突破性进展。
# 
# === 测试完成 ===
```

### 性能预期（Mac M4 16GB）

```
模型           首次加载    后续加载    生成速度    内存占用
Llama 3.2 3B   2-3 秒     1-2 秒     80+ t/s    ~2GB
Qwen2.5 7B     3-5 秒     2-3 秒     40+ t/s    ~5GB
CodeLlama 7B   3-5 秒     2-3 秒     35+ t/s    ~5GB
Qwen2.5 14B    5-8 秒     3-5 秒     20+ t/s    ~9GB

t/s = tokens per second（每秒生成的 token 数）
```

---

## 2.9 常用命令速查

### 模型管理

```bash
# 列出已安装的模型
ollama list

# 下载模型
ollama pull 模型名

# 删除模型
ollama rm 模型名

# 复制模型
ollama cp 源模型 目标模型

# 查看模型信息
ollama show 模型名
```

### 运行模型

```bash
# 运行模型（交互模式）
ollama run 模型名

# 运行模型（单次问答）
ollama run 模型名 "问题内容"

# 运行模型（从文件读取提示词）
ollama run 模型名 < prompt.txt

# 运行模型（保存回复）
ollama run 模型名 "问题" > response.txt
```

### 服务管理

```bash
# 启动服务
ollama serve

# 后台启动
brew services start ollama

# 停止服务
brew services stop ollama

# 重启服务
brew services restart ollama

# 查看状态
brew services list
```

---

## 2.10 高级用法

### 自定义 Modelfile

```bash
# 创建自定义模型
cat > /Volumes/MacOS-SSD/MyModel << 'EOF'
FROM qwen2.5:7b

# 设置系统提示词
SYSTEM """
你是一个专业的中文写作助手。
你的任务是帮助用户撰写文章、文案、邮件等。
回答要简洁、专业、有条理。
"""

# 设置参数
PARAMETER temperature 0.7
PARAMETER top_p 0.9
PARAMETER num_predict 2048
EOF

# 创建模型
ollama create mymodel -f /Volumes/MacOS-SSD/MyModel

# 测试
ollama run mymodel "帮我写一封请假邮件"
```

### API 调用

```bash
# 简单 API 调用
curl http://localhost:11434/api/generate -d '{
  "model": "qwen2.5:7b",
  "prompt": "你好",
  "stream": false
}'

# 聊天 API
curl http://localhost:11434/api/chat -d '{
  "model": "qwen2.5:7b",
  "messages": [
    {"role": "user", "content": "你好"}
  ],
  "stream": false
}'
```

### 多模型并发

```bash
# 同时运行多个模型（不推荐，内存可能不够）
ollama run qwen2.5:7b &
ollama run llama3.2:3b &

# 查看运行中的模型
ps aux | grep ollama

# 停止所有模型
killall ollama
```

---

## 2.11 故障排查

### 问题 1：下载失败

**症状：**
```
Error: connection refused
Error: timeout
```

**解决方案：**
```bash
# 1. 检查网络
ping ollama.com

# 2. 检查 Ollama 服务
ollama serve

# 3. 重试下载
ollama pull qwen2.5:7b

# 4. 如果还不行，使用镜像
export OLLAMA_ORIGINS=https://registry.ollama.ai
ollama pull qwen2.5:7b
```

### 问题 2：模型运行慢

**症状：**
```
生成速度很慢（< 10 t/s）
```

**解决方案：**
```bash
# 1. 检查内存使用
activity monitor

# 2. 关闭其他应用
# 释放内存

# 3. 使用更小的模型
ollama run llama3.2:3b

# 4. 检查温度
# 如果过热，改善散热
```

### 问题 3：找不到模型

**症状：**
```
Error: model 'qwen2.5:7b' not found
```

**解决方案：**
```bash
# 1. 检查模型列表
ollama list

# 2. 重新下载
ollama pull qwen2.5:7b

# 3. 检查存储位置
echo $OLLAMA_MODELS

# 4. 确保外置 SSD 已挂载
ls /Volumes/MacOS-SSD/ollama-models
```

### 问题 4：内存不足

**症状：**
```
Error: out of memory
模型崩溃
```

**解决方案：**
```bash
# 1. 使用更小的模型
ollama run llama3.2:3b

# 2. 关闭其他应用

# 3. 重启 Ollama
brew services restart ollama

# 4. 不要同时运行多个模型
```

---

## 2.12 检查清单

### 安装完成
- [ ] Homebrew 安装成功
- [ ] Ollama 安装成功
- [ ] 版本号正确

### 配置完成
- [ ] 模型存储位置已改到外置 SSD
- [ ] 环境变量已配置
- [ ] 服务已启动

### 模型下载
- [ ] Qwen2.5 7B 已下载
- [ ] Llama 3.2 3B 已下载
- [ ] CodeLlama 7B 已下载（可选）

### 测试完成
- [ ] 所有模型都能正常运行
- [ ] 性能测试完成
- [ ] 速度符合预期

### 故障排查
- [ ] 知道如何查看日志
- [ ] 知道如何重启服务
- [ ] 知道如何重新下载模型

---

## 2.13 下一步

完成 Ollama 部署后，继续学习：

- 📖 **第三章**：OpenClaw 完全部署指南
- 📖 **第四章**：多模型路由与自动切换
- 📖 **第五章**：自动化赚钱工作流

---

**恭喜你完成 Ollama 部署！** 🎉

现在你已经有了运行 AI 模型的能力，下一步是部署 OpenClaw 框架，让 AI 能够自动执行任务！

*由 Ni 为你精心编写* 👁️‍🗨️⚡
