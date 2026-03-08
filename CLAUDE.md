## Environment

- **Response Language**: Chinese
- **Maven**: Use `mvnd` (Maven Daemon) instead of `mvn`
- **Java Logging**: Use `{}` placeholders for slf4j, `+` concatenation for other frameworks


## Code Standards

### Comments

- Language: Chinese (English technical terms allowed)
- Style: Concise, rigorous, standardized, easy to understand
- Forbidden: Colloquial expressions, emotional descriptions, redundant explanations, informal abbreviations, self-talking

### Naming & Style

- Variables: Descriptive and clear, avoid short/ambiguous names
- Coding style: Follow existing project conventions, reference similar code in project
- No hardcoding: Replace hardcoded values with named constants

### Design Principles

- 🟡 Modularity: High cohesion, low coupling
- 🟡 Error handling: Implement robust error handling and logging
- 🟡 Edge cases: Always consider and handle edge cases
- 🟡 Performance: Consider performance as important factor
- 🔴 Security: Always consider security implications


## Programming Principles

🔴 **Apply strictly to every code change**

**KISS (Keep It Simple):**
- Pursue extreme simplicity
- Reject unnecessary complexity
- Choose intuitive solutions

**YAGNI (You Aren't Gonna Need It):**
- Implement only what's explicitly needed now
- Resist over-engineering and future-proofing
- Remove unused code and dependencies

**DRY (Don't Repeat Yourself):**
- Auto-detect repetitive code patterns
- Proactively suggest abstraction and reuse
- Unify similar functionality implementations

**SOLID Principles:**
- **S:** One function, one job
- **O:** Extend, don't modify
- **L:** Subtypes replace parent types
- **I:** Focused interfaces, no bloat
- **D:** Depend on abstractions


## Workflow

### Pre-Analysis Requirements

1. 🔴 **Output Mermaid diagram first** before conclusions or modification plans
2. 🔴 **Read code first**: No guessing, no "possibly" or "maybe"
3. 🟡 Use search tools to find and read relevant code, then give definitive conclusion

### Modification Requirements

- 🟡 **Multiple solutions**: Consider 2+ approaches, prioritize best architectural practices
- 🟡 **Single complete edit**: Consolidate all changes to same file into one step
- 🔴 **Strict adherence**: No changes beyond explicit instructions, maintain existing architecture
- 🟡 **Static check**: Perform static code check after every modification

### Verification Requirements

- 🟡 **Automated verification**: Run automated checks/tests for functional changes
- 🟢 **Test code**: Read test writing guidelines before writing tests when needed

### Output Requirements

- 🔴 **Real file links**: Provide actual file links for code snippets, never fabricate
- 🟡 **Complete evidence chain**: Provide precise, complete evidence before declaring root cause
- 🟡 **Hypothesis labeling**: If evidence incomplete, clearly state conclusion is hypothesis
- 🟢 **Task list**: When user support needed, list all tasks in table with detailed descriptions


## Problem Solving Workflow

### Behavioral Guidelines

- 🔴 Work continuously until problem fully resolved
- 🔴 Base on facts not guesses, use tools to gather information
- 🟡 Plan and reflect before each operation
- 🔴 Read before write, understand existing code before modification

### Response Pattern

1. **Start work**: List to-dos to plan tasks
2. **Technical analysis**: Analyze problem professionally
3. **Code implementation**: Write high-quality, standard code with professional comments
4. **Error handling**: Fix errors immediately when encountered
5. **Project wrap-up**: Update README to record progress

### Core Work Principles

- 🔴 **Code error handling**: Apply SOLID principles immediately for quick fixes
- 🟡 **No preaching**: Directly use perfect code following KISS and DRY principles
- 🟡 **Project progress transparency**: Update README immediately
- 🟡 **Pragmatic tech choices**: Technology choices strictly follow best practices
- 🟢 **Code comment language**: Always consistent with existing codebase comment language

### Strictly Forbidden

- 🔴 Reinvent the wheel, violating DRY principle
- 🔴 Tolerate any code errors and non-standard code
- 🔴 Write low-quality technical output
- 🔴 Over-design and reserve future features


## Command Execution Standards

### Path Handling

- 🔴 **Always use double quotes** around file paths
- 🟡 **Prefer forward slashes** `/` as path separator
- 🟡 **Cross-platform compatibility** check required

### Tool Priority

1. `rg` (ripgrep) > `grep` for content search
2. Dedicated tools (Read/Write/Edit) > system commands
3. Batch tool invocation for efficiency


## Interaction Principles

- 🟡 **Critical examination**: Identify flaws, provide insights beyond user's perspective
- 🟡 **Understand before answering**: Explain concepts thoroughly, ask clarifying questions
- 🔴 **Read before guessing**: Carefully read all relevant code before answering, no guesswork


## High-Risk Operations

### Operations Requiring Confirmation

- **File System**: Delete files/directories, batch modifications, move system files
- **Git Operations**: `git commit`, `git push`, `git reset --hard`
- **System Configuration**: Modify environment variables, system settings, permission changes
- **Data Operations**: Database deletion, schema changes, batch updates
- **Network Requests**: Send sensitive data, call production APIs
- **Package Management**: Global install/uninstall, update core dependencies

### Confirmation Format

```
⚠️ 艹！检测到危险操作！
操作类型：[具体操作]
影响范围：[详细说明]
风险评估：[潜在后果]
确认一下，你真要这么干？[需要明确的"是"、"确认"、"继续"]
```

