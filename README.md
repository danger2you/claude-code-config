# Claude Code 配置集合

这是一个 Claude Code 的自定义配置仓库，包含 Agents、Commands 和 Skills，用于增强 AI 编程助手的能力。

## 📁 目录结构

```
.
├── agents/      # 专业化 AI 代理
├── commands/    # 自定义命令
└── skills/      # 技能模块
```

## 🤖 Agents (代理)

专业化的 AI 代理，用于特定任务的自动化处理。

| 名称 | 描述 |
|------|------|
| **architect** | 软件架构专家，用于系统设计、可扩展性和技术决策 |
| **code-reviewer** | 代码审查专家，主动审查代码质量、安全性和可维护性 |
| **e2e-runner** | E2E 测试专家，使用 Playwright 生成、维护和运行端到端测试 |
| **get-current-datetime** | 执行日期命令并返回原始输出 |
| **init-architect** | 自适应初始化：根级简明 + 模块级详尽 |
| **planner** | 规划专家，用于复杂功能和重构的规划 |
| **security-reviewer** | 安全漏洞检测和修复专家，检查 OWASP Top 10 漏洞 |
| **tdd-guide** | 测试驱动开发专家，强制执行测试优先方法论 |
| **ui-ux-designer** | UI/UX 设计专家，提供界面设计指导 |

## 🔧 Commands (命令)

自定义命令，用于简化常见开发任务。

| 命令 | 描述 |
|------|------|
| **code-review** | 代码审查命令 |
| **e2e** | 生成并运行 Playwright E2E 测试 |
| **feat** | 新增功能开发命令，支持完整开发流程 |
| **git-cleanBranches** | 安全清理已合并或过期的 Git 分支 |
| **git-commit** | 自动生成 Conventional Commits 格式的提交信息 |
| **git-rollback** | 交互式回滚 Git 分支到历史版本 |
| **git-worktree** | 管理 Git worktree，支持智能默认和 IDE 集成 |
| **init-project** | 初始化项目 AI 上下文，生成 CLAUDE.md 索引 |
| **orchestrate** | 编排命令 |
| **plan** | 重述需求、评估风险并创建实施计划 |
| **tdd** | 强制执行测试驱动开发工作流 |
| **verify** | 验证命令 |
| **workflow** | 六阶段开发工作流（研究→构思→计划→执行→优化→评审） |

## 🎯 Skills (技能)

技能模块，为特定领域提供最佳实践和模式。

| 技能 | 描述 |
|------|------|
| **api-design** | REST API 设计模式，包括资源命名、状态码、分页、过滤等 |
| **article-writing** | 撰写文章、指南、博客、教程等长篇内容 |
| **coding-standards** | TypeScript、JavaScript、React 和 Node.js 的编码标准 |
| **content-engine** | 为 X、LinkedIn、TikTok、YouTube 等平台创建内容 |
| **e2e-testing** | Playwright E2E 测试模式和最佳实践 |
| **frontend-slides** | 创建动画丰富的 HTML 演示文稿 |
| **security-review** | 安全审查清单，用于认证、用户输入、API 端点等 |
| **security-scan** | 扫描 Claude Code 配置的安全漏洞 |
| **tdd-workflow** | 测试驱动开发工作流，确保 80%+ 测试覆盖率 |
| **verification-loop** | Claude Code 会话的综合验证系统 |
| **work-log** | 工作日志管理 |

## 🚀 使用方法

### 安装

将此仓库克隆到你的 Claude Code 配置目录：

```bash
git clone https://github.com/danger2you/claude-code-config.git ~/.claude
```

### 使用 Agents

Agents 会根据任务自动激活，或者可以通过 Agent 工具手动调用。

### 使用 Commands

在 Claude Code 中使用 `/` 前缀调用命令：

```bash
/git-commit --emoji
/feat
/tdd
```

### 使用 Skills

Skills 会根据上下文自动激活，提供相关的最佳实践和指导。

## 📝 许可

本配置集合供个人使用和学习参考。

## 🤝 贡献

欢迎提交 Issue 和 Pull Request 来改进这些配置。

---

**仓库地址**: https://github.com/danger2you/claude-code-config.git
