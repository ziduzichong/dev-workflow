# dev-workflow — Claude Code 全流程开发编排 Skill

> 一个覆盖「规划 → 编码 → 调试 → 审查 → 优化 → 文档」7 阶段的完整开发工作流 Skill，专为 Claude Code / WorkBuddy 设计。

## 一句话介绍

dev-workflow 将 AI 辅助开发从「单轮对话」升级为「多阶段可验证的工作流」，每个阶段都有明确的入口/出口条件和检查点，让 AI 真正交付可用代码，而不仅仅是生成片段。

---

## 核心优势

| 优势 | 说明 |
|------|------|
| **7阶段强制流水线** | 规划→架构审查→TDD编码→系统化调试→代码审查→优化→文档，阶段间有桥接检查，无法跳过 |
| **50行法则智能裁剪** | 根据改动规模自动选择执行深度：≤50行单文件直接编码，≥200行强制执行完整7阶段 |
| **苏格拉底追问** | 阶段1前强制4维度追问（目标/边界/约束/验收），追问后回述确认，杜绝模糊需求 |
| **证据强制规则** | 所有检查点不接受AI口头声明，必须贴出工具实际输出（exit code/文件内容/匹配结果）作为通过证据 |
| **TDD微任务拆分** | 每个编码任务粒度控制在2~5分钟，含精确文件路径、完整步骤、验证命令 |
| **多语言构建自动检测** | 支持 C/C++/C#/Python/Go/Rust/Java/TypeScript，自动识别项目类型并执行对应构建和测试命令 |
| **嵌入式降级方案** | 无法写自动化测试时，用 cppcheck/clang-tidy/ruff/mypy 静态分析替代，TDD循环自动简化 |
| **可观测性内置** | 每个阶段的开始/结束自动写入 workflow-telemetry.json，包含检查点通过率、重试次数、状态 |
| **Git 安全提交** | 每阶段自动 commit，自动排除 .env/*.key/secrets 等敏感文件，commit message 规范化 |
| **用户 Hooks 扩展** | 支持在阶段前后、中止、完成时注入自定义脚本，实现团队标准化 |

---

## 已知局限（劣势）

| 局限 | 影响 | 缓解方式 |
|------|------|---------|
| **强依赖 Superpowers Skill 生态** | 需要同时安装 `superpowers:brainstorming`、`writing-plans`、`test-driven-development` 等 8 个外部 skill，缺失 ≥2 个核心 skill 会中止 | README 中提供一键安装命令，或手动补装缺失 skill |
| **Sub-Agent 编排依赖平台能力** | 阶段2（架构审查）和阶段3（并行微任务）依赖 Agent/Sub-Agent 工具，在 Cursor/Windsurf/OpenCode 等平台无法使用 | 可降级运行（无 sub-agent 时主 agent 自行审查），或按平台适配指南改造 |
| **SKILL.md 对 Claude Code 耦合较高** | 使用了 Claude Code 专有工具名（Read/Write/Bash/Glob/Grep/Agent），其他平台需适配 | 参考 `adaptation-guide.md`（待写）或联系作者获取适配版 |
| **无内置 Web UI** | 所有交互通过命令行，进度和报告以 Markdown 文件输出 | 配合 VS Code 侧边栏或 Claude Code 终端使用，输出文件可用任何 Markdown 编辑器查看 |
| **嵌入式项目烧录需手动配置** | ST-Link/OpenOCD 烧录命令需用户自行确认路径 | 阶段3 构建验证步骤中会提示用户确认，不会自动执行烧录 |
| **Context 管理需人工介入** | 超大项目（>10个阶段轮次）可能触达 context 上限，需开启新会话恢复 | 阶段产出自动写入文件，支持从任意阶段恢复，README 中有恢复指引 |

---

## 使用方法

### 安装

**方式一：从 GitHub 安装（推荐）**

```bash
# 添加 marketplace（首次）
claude plugin marketplace add ziduzichong/ziduzichong-tools

# 安装插件
claude plugin install dev-workflow@ziduzichong-tools

# 或在项目目录中本地加载（开发调试用）
claude --plugin-dir ./dev-workflow-plugin
```

**方式二：安装依赖 Skill**

本 Skill 依赖以下 Superpowers Skill（已安装可跳过）：

```bash
# 安装 Superpowers（如未安装）
# 参考：https://github.com/anthropics/superpowers
```

### 快速开始

在 Claude Code 中直接对话启动：

```
用户：启动工作流，我要开发一个 STM32 的串口命令解析器
```

或指定从某个阶段开始：

```
用户：从阶段3开始
用户：跳过架构审查，直接开始编码
用户：只执行阶段7，重新生成文档
```

### 各阶段说明

| 阶段 | 名称 | 做什么 | 输出 |
|------|------|-------|------|
| 1 | 规划与设计 | 苏格拉底追问 → brainstorming → writing-plans | 设计文档 + 实现计划 |
| 2 | 架构审查 | 独立 sub-agent 8维度评分 + 替代路线推荐 | 架构审查报告 |
| 3 | TDD编码调试 | 微任务拆分 → TDD循环 → 编译验证 | 全部测试通过的代码 |
| 4 | 系统化调试 | 复现→定位→修复→验证（无待调bug则跳过） | bug修复日志 |
| 5 | 代码审查 | requesting + receiving code review → 修改任务表 | 审查问题全部修复的代码 |
| 6 | 代码优化 | code-simplifier + lint 检查 + 全量验证 | 优化后的代码 |
| 7 | 文档生成 | README + 工作流程报告 + 使用手册 + 移植方案 | 5份中文文档 |

---

## 与同类型产品对比

### vs Superpowers（独立 Skill 集合）

| 维度 | dev-workflow | Superpowers |
|------|-------|--------------|
| 编排方式 | 7阶段强制流水线，阶段间有桥接检查 | 每个 Skill 独立调用，无强制顺序 |
| 流程保障 | 有（检查点 + 证据强制 + 重试上限） | 无（依赖用户自行判断） |
| 适用场景 | 完整功能开发、多文件改动、团队协作 | 单任务（如只做代码审查或只做调试） |
| 学习曲线 | 中（需理解7阶段） | 低（每个 Skill 单独使用） |
| 可裁剪性 | 高（50行法则自动裁剪） | 高（按需调用） |
| Token 消耗 | 中（L2/L3分层加载，SKILL.md仅~1500字） | 低~中（取决于调用哪个 Skill） |

**结论**：Superpowers 是「工具箱」，dev-workflow 是「生产线」。两者可共存，dev-workflow 内部也调用了多个 Superpowers Skill。

### vs Cursor / Windsurf 自带的 Agent 模式

| 维度 | dev-workflow | Cursor Agent / Windsurf |
|------|-------|----------------------|
| 流程可控性 | 高（阶段、检查点、重试上限全部可配置） | 低（黑盒，无法干预中间步骤） |
| 可复现性 | 高（每个阶段产出文件，可从任意阶段恢复） | 低（会话结束后无法精确复现） |
| 证据机制 | 有（强制贴出工具输出） | 无（AI自说自话"已通过"） |
| 多阶段编排 | 原生支持7阶段流水线 | 需手动分段对话 |
| 平台锁定 | Claude Code / WorkBuddy 专属 | 各自平台锁定 |

### vs 手动对话式开发

| 维度 | dev-workflow | 手动对话 |
|------|-------|---------|
| 需求遗漏 | 低（苏格拉底追问强制澄清） | 高（容易假设AI已理解） |
| 测试覆盖 | 强制TDD（测试先行） | 可选（容易偷懒不写测试） |
| 代码质量 | 有审查+优化两道关卡 | 取决于用户主动要求 |
| 文档完整性 | 阶段7强制生成5份文档 | 容易遗漏 |
| 时间成本 | 首次使用需学习流程 | 低（无需学习） |

---

## 支持的编程语言

| 语言 | 构建命令 | 测试命令 | 静态分析降级 |
|------|---------|---------|------------|
| C (Make) | `make -j4` | `make test` | cppcheck / clang-tidy |
| C (CMake) | `cmake --build build` | `ctest` | cppcheck / clang-tidy |
| C++ | `cmake --build build` | `ctest` | cppcheck / clang-tidy |
| C# | `dotnet build` | `dotnet test` | `dotnet format` |
| Python | `python -m py_compile` | `pytest` | ruff / mypy |
| JavaScript | `npm run build` | `npm test` | eslint |
| TypeScript | `npx tsc --noEmit` | `npm test` | eslint |
| Go | `go build ./...` | `go test ./...` | `go vet` |
| Rust | `cargo build` | `cargo test` | `cargo clippy` |
| Java (Maven) | `mvn compile` | `mvn test` | — |
| Java (Gradle) | `gradle build` | `gradle test` | — |
| STM32 | CubeIDE CLI | — | cppcheck |

---

## 第三方市场收录

本 Skill 已发布到 GitHub 公开仓库，以下平台会通过爬虫自动收录（无需手动提交）：

- **skillsmp.com** — 自动抓取含 `SKILL.md` 的公开仓库（需 ≥2 GitHub Stars）🔗 https://skillsmp.com
- **claudate.com** — AI Skills Marketplace，自动同步 GitHub 公开仓库 🔗 https://claudate.com

> ⚠️ **注意**：skillsmp.com 要求仓库至少有 **2 个 GitHub Star** 才会被收录。欢迎为本仓库 Star 以支持收录！

---

## 作者与许可

- **作者**：子都子充
- **许可**：MIT License
- **GitHub**：https://github.com/ziduzichong/dev-workflow
- **Issue 反馈**：欢迎在 GitHub Issues 提交 bug 报告和功能建议

---

## 版本记录

| 版本 | 日期 | 核心变化 |
|------|------|---------|
| v1.0.0 | 2026-05 | 首次发布，7阶段完整流程，L2/L3分层架构 |
