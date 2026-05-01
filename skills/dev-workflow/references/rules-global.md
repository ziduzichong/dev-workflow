# 全局规则

## 失败处理策略

| 规则 | 说明 |
|------|------|
| 阶段内重试上限 | 同一问题的修复尝试不超过 **3 次** |
| 阶段回退上限 | 同一阶段最多执行 **2 轮** |
| 超过上限 | 立即中止，生成「工作流中止报告」 |
| 编译失败 | 不得跳过，必须在当前任务内修复 |
| 测试失败 | TDD 红灯阶段正常现象，绿灯前不得进入下一任务 |
| 用户中断 | 保存当前进度到 `$REPORT_DIR/workflow-progress.md` |

**中止报告**保存至 `$REPORT_DIR/abort-report.md`，格式：

```markdown
# 工作流中止报告
## 中止原因
- 阶段：<N>
- 原因：重试3次仍失败 / 阶段回退2轮仍失败 / 用户主动中断
- 失败详情：<具体错误信息>

## 已完成的工作
| 阶段 | 状态 | 产出文件 |
|------|------|---------|

## 已尝试的方案
1. 方案A: <描述> → <失败原因>

## 建议的人工介入方向

## 恢复指引
从阶段<N>开始，参考 $REPORT_DIR/workflow-progress.md
```

## 修复操作权限边界

- 编译/测试失败时，只允许修改当前任务涉及的、报错位置所在的文件
- **禁止**自动安装系统级依赖。如需安装，须向用户说明理由并获确认
- 修改构建配置文件（Makefile、CMakeLists.txt、.csproj 等）前，须向用户展示 diff 并获确认

## 证据强制规则

**所有检查点的 🔧 工具验证项不接受 AI 口头声明，必须附带工具执行的实际输出作为通过证据。**

- `Bash` 检查点 → 必须贴出 exit code 和关键输出行
- `Read`/`Glob` 检查点 → 必须贴出读取到的文件内容或搜索结果
- `Grep` 检查点 → 必须贴出匹配行和匹配数量
- 不得使用"已通过"、"看起来没问题"、"测试全部绿灯"等未经证实的表述
- 如工具输出为空或异常，视为未通过

## 依赖检查步骤（阶段1开始前必须执行）

1. 检查上述 skill 是否在可用 skill 列表中
2. 缺失的 🔴 核心 skill ≥2 个 → **中止**，告知用户补齐
3. 缺失的 🟡/🟢 skill → 列出清单并询问是否跳过对应子流程
4. 所有依赖确认后，进入阶段1

## Git 版本控制

**执行 commit 前必须先检查：**
```bash
git rev-parse --is-inside-work-tree 2>/dev/null && echo "IN_REPO" || echo "NOT_REPO"
```

- 如果 `NOT_REPO`：跳过所有 commit 步骤，提示用户初始化 git repo
- 如果 `IN_REPO`：正常执行 commit 流程

**安全提交流程：**
```bash
git status --short
# 排除敏感文件：.env / *.key / secrets / credentials* / *.pem / *.pfx
git add --all -- ':!.env' ':!*.key' ':!secrets' ':!credentials*' ':!*.pem' ':!*.pfx'
git diff --cached --stat
git commit -m "<message>"
```

每完成一个阶段（以及阶段3 中每完成一个子任务），执行一次 commit。

commit message 规范：
- 阶段1: `dev-workflow: 阶段1 规划与设计 — 设计文档完成`
- 阶段2: `dev-workflow: 阶段2 架构审查 — 审查报告完成`
- 阶段3: `dev-workflow: 阶段3 任务<N>/<任务名> — TDD通过+编译通过`
- 阶段4: `dev-workflow: 阶段4 系统化调试 — 问题全部修复`
- 阶段5: `dev-workflow: 阶段5 代码审查 — 审查问题全部修复`
- 阶段6: `dev-workflow: 阶段6 代码优化 — 优化完成`
- 阶段7: `dev-workflow: 阶段7 文档生成 — 全部文档完成`

## Context 管理策略

1. 每完成一个阶段，将阶段产出写入文件
2. 进入下一阶段时，用 `Read` 工具读取上阶段文件的关键部分（`offset=1, limit=200`）
3. 阶段3 每个子任务完成后，输出摘要（≤50 行）写入 `$REPORT_DIR/task-summary.md`，下一子任务读取摘要
4. 如果预计 context 将超出限制，将当前状态写入 `$REPORT_DIR/workflow-progress.md`，**建议用户开启新会话并从对应阶段恢复**
