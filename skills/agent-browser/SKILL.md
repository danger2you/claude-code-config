---
name: agent-browser
description: |
  浏览器自动化 CLI 工具。支持打开网页、截图、抓取内容、点击元素、填写表单、文件上传、会话持久化。
  当用户需要浏览器自动化、网页操作、内容抓取时触发。
  核心功能：(1) 网页控制 (2) 元素交互 (3) 内容提取 (4) 截图录制 (5) 文件上传 (6) 会话持久化
license: Apache-2.0
metadata:
  author: Vercel Labs + Claude Code Community
  version: 3.0.0
  categories: [browser-automation, web-scraping]
  keywords: [browser, automation, cli, screenshot, web, scraping, agent-browser, session-persistence]
---

# agent-browser - 浏览器自动化 Skill

## 🎯 概述

agent-browser 是 AI-native 的浏览器自动化 CLI 工具，基于 Playwright，使用 Refs 机制简化元素选择，上下文消耗比传统方案少 93%。当用户需要浏览器自动化操作时，**立即使用 agent-browser CLI 工具**完成任务。

## ⚡ 执行规则（重要）

**当此 Skill 被调用时，你必须：**

1. **立即执行**，不要只说"我要做什么"
2. **使用 agent-browser 命令**执行操作
3. **通过 Bash tool** 执行命令
4. **显示结果**给用户
5. 🔥 **强制使用 Sessions 或 Profiles**：
   - **必须使用 `--session` 或 `--profile` 参数**
   - 不要每次都开新的浏览器会话
   - 保持登录状态，避免重复登录

### 推荐使用方式

#### 方案 1: 使用 Sessions（会话管理）
```bash
# 使用命名会话
agent-browser --session my-session open <URL>

# 后续操作使用同一个会话
agent-browser --session my-session click @e1
```

#### 方案 2: 使用 Persistent Profiles（持久化配置文件）⭐推荐
```bash
# 使用持久化配置文件
agent-browser --profile ~/.my-profile open <URL>

# 登录状态会自动保存，下次使用同一个 profile 时会自动恢复
agent-browser --profile ~/.my-profile click @e1
```

#### 方案 3: 组合使用（最佳实践）
```bash
agent-browser --session my-session --profile ~/.my-profile open <URL>
```

## 🔧 核心命令模板

### 导航命令
```bash
agent-browser open <url>          # 导航到 URL
agent-browser back                # 后退一页
agent-browser forward             # 前进一页
agent-browser reload              # 刷新当前页
```

### 元素交互
```bash
agent-browser snapshot -i         # 获取可交互元素
agent-browser click @ref          # 点击元素
agent-browser fill @ref <text>    # 填写输入框
agent-browser type @ref <text>    # 输入文本
agent-browser hover @ref          # 悬停元素
```

### 信息获取
```bash
agent-browser get text <selector> # 获取文本内容
agent-browser get title           # 获取页面标题
agent-browser get url             # 获取当前 URL
agent-browser screenshot [path]   # 截图
```

### 文件操作
```bash
agent-browser upload @ref <filepath>  # 上传文件
```

### 浏览器控制
```bash
agent-browser close               # 关闭浏览器
agent-browser connect [endpoint]  # 连接到 Chrome 调试端口
```

## 📋 标准工作流程

当用户要求浏览网页或抓取内容时，按以下步骤执行：

### 步骤 1：打开网页（使用 Profile）
```bash
agent-browser --profile ~/.my-profile open <用户提供的URL>
```

### 步骤 2：获取页面信息
根据用户需求选择：
- 获取可交互元素：`snapshot -i`
- 获取页面文本：`get text body`
- 截图：`screenshot <路径>`

### 步骤 3：交互操作（如需要）
- 点击链接/按钮：`click @ref`
- 填写表单：`fill @ref "内容"`
- 上传文件：`upload @ref "文件路径"`
- 导航：`back` / `forward` / `reload`

### 步骤 4：完成后关闭
```bash
agent-browser close
```

## 💡 使用示例

### 示例 1：简单导航和截图
用户说："打开百度首页并截图"

**立即执行：**
```bash
# 1. 打开网页
agent-browser open https://www.baidu.com

# 2. 截图
agent-browser screenshot ~/Desktop/baidu.png

# 3. 关闭
agent-browser close
```

### 示例 2：网页抓取
用户说："帮我看看 example.com 上有什么内容"

**立即执行：**
```bash
# 1. 打开网页
agent-browser open https://example.com

# 2. 获取内容
agent-browser get text body

# 3. 关闭
agent-browser close
```

### 示例 3：会话持久化（保存登录状态）
用户说："登录 GitHub 并保存会话以便将来使用"

**立即执行：**
```bash
# 第一次：使用可见浏览器登录
agent-browser --profile ~/.agent-browser/github --headed open https://github.com/login

# 用户在浏览器窗口中手动登录
# 会话自动保存到 profile

# 下次：自动登录
agent-browser --profile ~/.agent-browser/github open https://github.com/settings
agent-browser screenshot ~/Desktop/github-settings.png
agent-browser close
```

### 示例 4：文件上传
用户说："上传我的头像到 GitHub 个人资料"

**立即执行：**
```bash
# 使用保存的会话打开个人资料设置
agent-browser --profile ~/.agent-browser/github open https://github.com/settings/profile

# 获取可交互元素以找到文件输入框
agent-browser snapshot -i

# 上传文件（假设文件输入框的 ref 是 @e68）
agent-browser upload @e68 "~/Pictures/avatar.jpg"

# 点击保存按钮（假设保存按钮的 ref 是 @e65）
agent-browser click @e65

# 截图确认
agent-browser screenshot ~/Desktop/profile-updated.png
agent-browser close
```

### 示例 5：表单填写
用户说："填写 example.com 上的联系表单"

**立即执行：**
```bash
# 打开联系页面
agent-browser open https://example.com/contact

# 获取表单元素
agent-browser snapshot -i

# 填写表单字段（从 snapshot 获取 refs）
agent-browser fill @e1 "张三"
agent-browser fill @e2 "zhangsan@example.com"
agent-browser fill @e3 "你好，这是一条测试消息"

# 提交表单
agent-browser click @e4

# 等待确认
agent-browser wait 2000
agent-browser screenshot ~/Desktop/form-submitted.png
agent-browser close
```

### 示例 6：批量操作
用户说："浏览多个页面并截图"

**立即执行：**
```bash
# 打开网页
agent-browser open https://example.com

# 批量执行多个命令
agent-browser batch "snapshot -i" "screenshot /tmp/page1.png" "click @e1" "wait 1000" "screenshot /tmp/page2.png"

agent-browser close
```

## 🔧 高级功能

### 连接到现有 Chrome 浏览器

使用你现有的 Chrome 浏览器及其所有登录和扩展：

```bash
# 步骤 1：启动带调试端口的 Chrome
# Windows:
"C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222

# macOS:
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222

# Linux:
google-chrome --remote-debugging-port=9222

# 步骤 2：连接 agent-browser
agent-browser connect http://127.0.0.1:9222

# 步骤 3：正常使用（已登录所有网站！）
agent-browser open https://gmail.com
agent-browser get title
agent-browser close
```

### Profile 管理

为不同账户或用途创建独立的 profiles：

```bash
# 工作 GitHub 账户
agent-browser --profile ~/.agent-browser/github-work open https://github.com

# 个人 GitHub 账户
agent-browser --profile ~/.agent-browser/github-personal open https://github.com

# Twitter 账户
agent-browser --profile ~/.agent-browser/twitter open https://twitter.com
```

### Headless vs Headed 模式

```bash
# Headless 模式（默认，无可见浏览器）
agent-browser open https://example.com

# Headed 模式（可见浏览器，用于调试或手动登录）
agent-browser --headed open https://example.com
```

## ⚠️ 重要提醒

1. **立即执行**：不要只说"我要做什么"，直接调用 Bash tool
2. **显示结果**：将命令输出展示给用户
3. 🔥 **保持浏览器会话 - 强制规则**：
   - **不要每次任务都关闭浏览器**
   - 如果需要保持登录状态（如已登录的网站），**不要执行 close 命令**
   - 只有在确定不再需要登录状态时才关闭浏览器
   - 原因：关闭浏览器会丢失所有登录状态，下次需要重新登录
4. **交互前获取 refs**：使用 `click`、`fill` 或 `upload` 前先运行 `snapshot -i`
5. **使用绝对路径**：截图和文件上传始终使用完整路径
6. **Profile 用于持久化**：使用 `--profile` 标志保存登录状态和 cookies
7. **等待页面加载**：如果元素未就绪，添加 `wait <ms>`
8. **Refs 是临时的**：页面导航后元素 refs 会改变，需要重新运行 `snapshot`

## 🎯 触发场景

当用户说以下内容时，立即使用此 Skill：
- "打开网页..." / "浏览..."
- "截图..." / "捕获..."
- "抓取网页内容..." / "提取数据..."
- "点击..." / "填写..."
- "上传文件..." / "提交表单..."
- "浏览器自动化..." / "网页自动化..."
- "登录到..." / "保存会话..."
- WebFetch 失败时的备用方案

## 🔍 故障排除

### 问题：找不到可交互元素

**解决方案**：等待页面完全加载
```bash
agent-browser open https://example.com
agent-browser wait 3000  # 等待 3 秒
agent-browser snapshot -i
```

### 问题：找不到元素

**解决方案**：导航后 refs 会改变，获取新的 snapshot
```bash
agent-browser snapshot -i  # 获取新的 refs
agent-browser click @e1
```

### 问题：Profile 未持久化

**解决方案**：使用绝对路径
```bash
# ❌ 错误（相对路径）
agent-browser --profile ./profile open https://example.com

# ✅ 正确（绝对路径）
agent-browser --profile ~/.agent-browser/myprofile open https://example.com
```

### 问题：找不到命令

**解决方案**：验证安装
```bash
# 检查是否安装
which agent-browser

# 如果未找到，重新安装
npm install -g agent-browser
```

### 问题：Daemon 启动失败

**原因**：浏览器二进制文件未安装或版本不匹配

**解决方案**：
```bash
# 重新安装 Playwright 浏览器
npx playwright install chromium
```

## 📚 参考资源

- 官方 GitHub: https://github.com/vercel-labs/agent-browser
- 官网: https://agent-browser.dev/
- Playwright 文档: https://playwright.dev/
- 安装指南: ../agent-browser-installer/SKILL.md

---

**版本**: v3.0（整合版）
**最后更新**: 2026-01-28
**状态**: ✅ 已测试可用
**贡献者**: Vercel Labs + Claude Code Community
