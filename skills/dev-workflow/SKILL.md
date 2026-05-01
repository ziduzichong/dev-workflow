---
name: dev-workflow
description: This skill should be used when the user asks to "start a workflow", "启动工作流", "begin development", "从零开发", "full development workflow", "完整开发流程", "start from phase N", "从阶段N开始", "skip architecture", "跳过架构". It orchestrates a 7-phase development pipeline from planning to documentation with TDD, systematic debugging, code review, and automated telemetry.
---

# 开发工作流 (Dev Workflow)

全流程开发编排器。严格按阶段推进，每阶段验证后才进入下一阶段。

## 适用判断（Scope Gate）

**启动前必须判断。** 50行法则：≤50行单文件→直接编码不启动；≤50行多文件→简化（阶段1+3+5）；50-200行→标准（可跳过2/4/6）；≥200行→完整流程（不可跳过）。

### 苏格拉底追问（阶段1前必须执行）

4维度：目标（解决什么问题/谁用）→边界（不触发条件/最大量级）→约束（技术/非技术限制）→验收（完成标准）。追问后回述确认，不得假设用户已同意。

## 依赖 Skill

启动前逐一检查，缺失核心≥2个则中止。完整依赖表见 `references/rules-global.md`。

核心：brainstorming、writing-plans、executing-plans、requesting-code-review、receiving-code-review
可选/推荐：test-driven-development、subagent-driven-development、using-git-worktrees、systematic-debugging、code-simplifier

## 7阶段流程

| 阶段 | 核心动作 |
|------|---------|
| 1 规划与设计 | brainstorming + writing-plans + 苏格拉底追问 |
| 2 架构审查 | 独立 sub-agent 8维度审查 + 替代路线推荐 |
| 3 TDD编码调试 | 微任务拆分(2-5分钟) + TDD循环 + 编译验证 |
| 4 系统化调试 | 复现→定位→修复→验证（无待调bug则跳过） |
| 5 代码审查 | requesting + receiving code review + 修改任务表 |
| 6 代码优化 | code-simplifier + lint + 全量验证 |
| 7 文档生成 | README + 工作流程报告 + 使用手册 + 移植方案 |

## 检查点规则

🔧 工具验证：用 Read/Bash/Glob/Grep 执行，根据返回值判断。**不接受AI口头声明，必须附带工具实际输出。**
👤 人工确认：向用户展示内容，等待确认回复。

## 快速命令

启动/开始+工作流→阶段1；从阶段N开始→指定阶段；跳过+架构/调试/审查→跳过对应阶段；只执行阶段N→仅指定阶段；重新+文档→仅阶段7。

## 文档输出路径

`$DESIGN_DIR`（默认docs/superpowers/specs/）、`$REPORT_DIR`（默认docs/）、`$USER_DOC_DIR`（默认docs/）、`$README_PATH`（默认README.md）、`$HOOKS_DIR`（默认.claude/hooks/）

## 详细内容索引

各阶段完整步骤、检查点、全局规则见 references/ 目录：

- `references/rules-global.md` — 失败处理、修复权限、Git规范、Context管理
- `references/phase-1-planning.md` — 阶段1完整步骤
- `references/phase-2-architecture.md` — 阶段2架构审查
- `references/phase-3-tdd-coding.md` — 阶段3 TDD编码
- `references/phase-4-debugging.md` — 阶段4系统化调试
- `references/phase-5-review.md` — 阶段5代码审查
- `references/phase-6-optimization.md` — 阶段6代码优化
- `references/phase-7-documentation.md` — 阶段7文档生成
- `references/rules-telemetry.md` — 遥测格式
- `references/rules-hooks.md` — Hooks机制
- `references/build-commands.md` — 多语言构建命令表
