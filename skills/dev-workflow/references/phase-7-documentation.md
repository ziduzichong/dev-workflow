# 阶段7：文档生成#

## 输入#

前面所有阶段的产物。

## 执行步骤#

生成以下 5 份文件，全部使用中文：

| # | 文件 | 路径 | 内容 |
|---|------|------|------|
| 1 | README | `$README_PATH` | 简介、快速开始、项目结构、依赖、FAQ |
| 2 | 工作流程报告 | `$REPORT_DIR/workflow-report.md` | 项目信息、设计方案、技术路线、编码问题、命令日志、审查记录 |
| 3 | 使用手册 | `$USER_DOC_DIR/user-manual.md` | 系统要求、安装、快速入门、功能详解、配置、故障排除 |
| 4 | 移植方案 | `$USER_DOC_DIR/porting-guide.md` | 架构抽象层、移植步骤、平台差异、依赖替换 |
| 5 | 遥测摘要 | `$REPORT_DIR/workflow-telemetry.json` | （已有，持续更新到最终状态） |

## 检查点#

| # | 类型 | 验证内容 | 操作 |
|---|------|---------|------|
| 1 | 🔧 | 5 份产出全部存在 | `Glob` 搜索 `$REPORT_DIR/*.md` + `$README_PATH` |
| 2 | 🔧 | 每个文档 ≥200 字 | `Read` 每个文件前 50 行 |
| 3 | 🔧 | Shell 命令语法正确 | `Bash` 执行 `bash -n` 语法检查 |
| 4 | 🔧 | 路径引用有效 | `Glob` 验证文档中引用的文件路径 |
| 5 | 🔧 | 全中文 | `Read` 抽查 3 个文件 |

全部通过 → `git commit -m "dev-workflow: 阶段7 文档生成 — 全部文档完成"` → 更新 telemetry（status: completed）→ 向用户汇报#
