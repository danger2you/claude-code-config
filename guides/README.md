# Guides Directory

详细指南文档，按需查阅。这些文件不会默认加载到对话上下文中。

## 📚 Available Guides

### [git-operations.md](./git-operations.md)
**使用场景：** 执行 Git 操作时参考

**内容：**
- Git 安全协议（禁止操作、必须操作）
- 提交工作流（状态检查、消息格式、分步流程）
- PR 创建流程
- 危险操作说明（reset、force push）
- 文件暂存最佳实践

**何时查阅：**
- 创建 commit 前
- 创建 PR 前
- Pre-commit hook 失败时
- 需要执行 reset/force push 等危险操作时

---

### [pre-analysis.md](./pre-analysis.md)
**使用场景：** 分析问题、定位 bug、提出方案前参考

**内容：**
- Mermaid 图表绘制指南
- 代码阅读清单（核心实现、调用链、配置、测试）
- JAR 依赖反编译流程
- 证据链完整性要求
- 假设标注格式

**何时查阅：**
- 分析复杂 bug 前
- 提出技术方案前
- 需要追踪依赖库行为时
- 证据不完整需要标注假设时

---

## 🔗 Cross-References

这些指南在 CLAUDE.md 中被引用：

- **CLAUDE.md:56** → `guides/pre-analysis.md`
- **CLAUDE.md:100** → `guides/git-operations.md`

## 📝 Usage Pattern

```
用户请求 → 识别场景 → 使用 Read 工具读取对应指南 → 遵循指南执行
```

**示例：**
- 用户："帮我提交代码" → 读取 `git-operations.md` → 按流程执行
- 用户："分析这个 bug" → 读取 `pre-analysis.md` → 绘制 Mermaid 图 → 完整代码阅读
