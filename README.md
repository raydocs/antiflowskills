# Flow-Next Skills for Droid AI

Flow-Next 任务追踪和工作流技能，专为 Factory/Droid AI 代理设计。

## 快速开始

### 1. 克隆仓库

```bash
git clone https://github.com/raydocs/antiflowskills.git
cd antiflowskills
```

### 2. 验证安装

```bash
REPO_ROOT="$(git rev-parse --show-toplevel)"
"$REPO_ROOT/.flow/bin/flowctl" list
```

### 3. 开始使用

直接用自然语言与 Droid AI 对话，技能会自动触发：

- "帮我规划一个用户认证功能" → 触发 `flow-plan`
- "显示所有任务" → 触发 `flow-next`
- "开始实现 fn-1-abc.2" → 触发 `flow-work`

## 技能列表

### 核心技能

| 技能 | 用途 | 触发示例 |
|------|------|----------|
| `flow-next` | 任务管理（查看、创建、更新） | "显示任务"、"列出 epics"、"添加任务" |
| `flow-plan` | 规划功能，创建结构化任务 | "规划一个..."、"设计..." |
| `flow-work` | 执行任务，包含 git 工作流 | "实现 fn-1-abc"、"开始工作" |
| `flow-interview` | 深入需求访谈 | "访谈我关于..."、"细化需求" |

### 审查技能

| 技能 | 用途 | 触发示例 |
|------|------|----------|
| `flow-plan-review` | 方案审查（Carmack 级别） | "审查方案"、"检查 epic 规格" |
| `flow-impl-review` | 代码审查（Carmack 级别） | "审查实现"、"检查代码变更" |

### 研究技能（Scouts）

| 技能 | 用途 | 触发示例 |
|------|------|----------|
| `repo-scout` | 查找代码库中的模式和约定 | "查找现有模式"、"这个仓库用什么约定" |
| `context-scout` | 深度代码库探索 | "探索代码库"、"理解 X 如何工作" |
| `docs-scout` | 查找框架/库文档 | "查找文档"、"API 是什么样的" |
| `practice-scout` | 查找行业最佳实践 | "最佳实践是什么"、"应该如何实现" |
| `github-scout` | 搜索 GitHub 代码/issues | "在 GitHub 上找例子" |
| `memory-scout` | 搜索项目记忆 | "之前做了什么决定" |

## 使用流程

### 典型工作流

```
1. 规划阶段
   用户: "规划一个用户注册功能"
   → flow-plan 创建 epic 和任务

2. 执行阶段
   用户: "开始实现 fn-1-abc"
   → flow-work 逐个执行任务

3. 审查阶段
   用户: "审查实现"
   → flow-impl-review 进行代码审查
```

### 任务管理命令

```bash
# 设置（每个 shell 会话运行一次）
REPO_ROOT="$(git rev-parse --show-toplevel)"
FLOWCTL="$REPO_ROOT/.flow/bin/flowctl"

# 查看
$FLOWCTL list                    # 所有 epics + 任务
$FLOWCTL show fn-1-abc.2         # 查看单个任务
$FLOWCTL ready --epic fn-1-abc   # 哪些任务可以开始

# 工作
$FLOWCTL start fn-1-abc.2        # 开始任务
$FLOWCTL done fn-1-abc.2 --summary-file s.md --evidence-json e.json
```

## ID 格式

- Epic: `fn-N-xxx`（如 `fn-1-abc`）
- Task: `fn-N-xxx.M`（如 `fn-1-abc.2`）

## 语义触发 vs 斜杠命令

Factory/Droid 技能通过**自然语言**触发，不是斜杠命令：

| 平台 | 触发方式 | 示例 |
|------|----------|------|
| Claude Code | 斜杠命令 | `/flow-next:plan Add OAuth` |
| **Droid AI** | **自然语言** | **"规划一个 OAuth 功能"** |

## 平台限制

### rp-cli (仅 macOS)

RepoPrompt CLI 仅在 macOS 上可用，影响以下功能：

| 功能 | macOS | 非 macOS |
|------|-------|----------|
| `context-scout` | 完整功能 | 降级为 `repo-scout` |
| `flow-impl-review` | 完整功能 | 需手动审查 |
| `flow-plan-review` | 完整功能 | 需手动审查 |

**非 macOS 解决方案：**
- 使用 `repo-scout` 替代 `context-scout`
- 审查技能选择 "无审查" 或手动审查

### 不可用功能

以下 Claude Code 功能在 Droid 中不可用：

- Ralph 自主模式（需要 hooks 系统）
- Pre/post 任务钩子
- 自定义自动化触发

## 文件结构

```
.factory/
  skills/
    flow-next/SKILL.md       # 任务管理
    flow-plan/SKILL.md       # 规划
    flow-work/SKILL.md       # 执行
    flow-interview/SKILL.md  # 访谈
    flow-impl-review/        # 代码审查
    flow-plan-review/        # 方案审查
    repo-scout/SKILL.md      # 代码库探索
    context-scout/SKILL.md   # 深度探索
    docs-scout/SKILL.md      # 文档查找
    practice-scout/SKILL.md  # 最佳实践
    github-scout/SKILL.md    # GitHub 搜索
    ...

.flow/
  bin/flowctl               # CLI 工具
  epics/                    # Epic 元数据
  specs/                    # Epic 规格
  tasks/                    # 任务文件
```

## 常见问题

### "git rev-parse failed" 错误

**原因：** 命令在 git 仓库外运行

**解决：** 进入仓库目录，或设置环境变量：
```bash
export REPO_ROOT=/path/to/repo
```

### 技能没有触发

**原因：** 表述不够明确

**解决：** 更明确地描述意图：
- ❌ "帮帮我"
- ✅ "规划一个用户登录功能"
- ✅ "实现任务 fn-1-abc.2"

### 审查技能不工作（非 macOS）

**原因：** rp-cli 仅支持 macOS

**解决：**
- 跳过审查：回答 "无审查"
- 手动审查代码
- 在 macOS 机器上运行审查

## 更多资源

- [Factory 技能完整指南](docs/factory-skills-guide.md)
- [flowctl 使用说明](.flow/usage.md)
- [flowctl CLI 帮助](.flow/bin/flowctl --help)

## License

MIT
