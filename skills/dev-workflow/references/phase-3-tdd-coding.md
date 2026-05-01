# 阶段3：TDD 编码与调试

## 输入

阶段1 的实现计划 + 阶段2 的架构审查报告。

## 3.0 准备工作

**项目语言自动检测：**
扫描项目目录，根据 `build-commands.md` 中的特征文件判断语言，选择构建命令和测试命令。

如果用户手动指定了构建命令，优先使用用户指定。

**Git Worktree 隔离（推荐）：**
如果安装了 `superpowers:using-git-worktrees`，在当前阶段启动前调用它创建隔离工作树。所有编码在 worktree 中进行。完成后根据用户选择保留或丢弃 worktree。

## 3.1 微任务拆分（2~5 分钟粒度）

调用 `superpowers:executing-plans` 或 `superpowers:subagent-driven-development` 拆分任务。

**粒度标准：** 每个任务必须能在 **2~5 分钟**内完成，超过则继续拆分。

**每个微任务必须包含：**
- **精确文件路径** — 如 `src/auth/login.ts`，不得写"相关文件"
- **完整实现步骤** — 如"1. 定义 LoginRequest 接口，含 username/password 字段；2. 实现 validateLogin() 函数..."
- **验证命令** — 如 `npx jest login.test.ts`，不得写"运行测试验证"

**拆分优先级：**
- 数据模型和接口定义优先
- 核心逻辑次之
- UI 和集成最后
- 互不依赖的微任务可并行（调用 subagent-driven-development）
- 每个微任务必须可独立编译/测试

**示例（好的拆分）：**
```
任务 3/7: 创建 POST /login 路由
  文件: src/auth/login.ts（新建）、src/auth/router.ts（修改）
  步骤:
    1. 在 src/auth/login.ts 中定义 LoginRequest 接口（username, password）
    2. 实现 handleLogin(req: LoginRequest): Promise<LoginResponse>
    3. 在 src/auth/router.ts 中注册 POST /login 路由
    4. 运行 npx jest src/auth/login.test.ts 验证
  预计: 3 分钟
```

## 3.2 TDD 编码循环（每个任务内执行）

调用 `superpowers:test-driven-development` skill，按以下循环执行：

```
① 写失败测试 → ② 运行测试(红灯) → 已通过？
       ↑              ↓ 失败
       │        ③ 写最少实现代码
       │              ↓
       │        ④ 运行测试(绿灯？)
       │           ↓ 通过          ↓ 失败
       │     ⑤ 重构代码          回到③
       │           ↓
       └─── ⑥ 运行全部测试(仍绿灯)
                   ↓ 通过
              任务完成
```

**约束：**
- 未写测试前不得写实现代码
- 红灯才能写实现，绿灯立即停止写实现
- 重构不得改变外部行为（测试保持绿灯）
- 每个任务完成后运行全量测试确保无回归

**如果项目无法写自动化测试（如嵌入式裸机 C 代码）：**
- 用编译检查 + 静态分析替代测试步骤，见 `build-commands.md` 中的替代方案
- TDD 循环简化为：写实现 → 编译 → 静态分析 → 通过 → 下一任务

## 3.3 编译验证（每个任务后强制执行）

```
<根据 3.0 检测到的构建命令>
```
通过条件: `exit code == 0` 且无 `error:` 输出

## 3.4 编译/测试失败处理

```
失败 → 读取错误输出 → 定位问题 → 修复(仅修改报错文件) → 重新编译/测试
                                              ↓
                                        仍失败(第2次)
                                              ↓
                                        换方案 → 重新编译/测试
                                              ↓
                                        仍失败(第3次)
                                              ↓
                                    ┌─ 简单bug？──→ 进入阶段4 系统化调试
                                    │                    ↓
                                    │              修复后回到此处
                                    │
                                    └─ 无法修复？──→ 生成中止报告
```

## 检查点（每个子任务后强制执行）

| # | 类型 | 验证内容 | 操作 |
|---|------|---------|------|
| 1 | 🔧 | 测试绿灯（或编译通过） | `Bash` 执行测试/构建命令，确认 exit code 0 |
| 2 | 🔧 | 无新增 warning（或已记录） | 检查构建输出，写入 `$REPORT_DIR/build-log.md` |
| 3 | 🔧 | 接口一致性 | `Grep` 搜索新接口引用，抽样验证前 5 个 |
| 4 | 🔧 | 构建日志已保存 | `Read` 确认 `$REPORT_DIR/build-log.md` 含本次记录 |

每个子任务通过 → `git commit -m "dev-workflow: 阶段3 任务<N>/<任务名> — TDD通过+编译通过"` → 下一子任务

所有子任务完成 → `Write` 摘要到 `$REPORT_DIR/task-summary.md` → 执行阶段3→4 桥接检查 → 更新 telemetry → 进入阶段4

## 3.5 阶段3→4 桥接检查（阶段3 全部子任务完成后强制执行）

| # | 类型 | 验证内容 | 操作 |
|---|------|---------|------|
| 1 | 🔧 | 所有微任务 TDD 循环已闭合 | `Read` `$REPORT_DIR/task-summary.md`，逐一确认 |
| 2 | 🔧 | 构建日志无未处理失败 | `Read` `$REPORT_DIR/build-log.md`，检查无残留 `error:` |
| 3 | 🔧 | 全量测试/构建最后一次通过 | `Bash` 执行全量测试或构建命令 |
| 4 | 🔧 | 无 TODO/FIXME 残留（或已记录） | `Grep` 搜索 `TODO\|FIXME\|HACK\|XXX` |

全部通过后 → 进入阶段4。如有未通过项 → 回到对应微任务修复（最多 3 次重试）。
