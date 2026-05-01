# 阶段6：代码优化#

## 输入#

阶段5 审查通过后的代码。

## 执行步骤#

1. **调用 `code-simplifier` skill** — 简洁性、可读性、可维护性优化

2. **Lint 检查（如果项目支持）：**
   根据项目类型自动选择 lint 工具：
   | 语言 | Lint 命令 |
   |------|----------|
   | C# | `dotnet format --verify-no-changes` |
   | JavaScript/TS | `npx eslint .` |
   | Python | `flake8` / `ruff check` |
   | Go | `go vet ./...` |
   | Rust | `cargo clippy` |

3. **编译 + 全量测试最终验证**

## 检查点#

| # | 类型 | 验证内容 | 操作 |
|---|------|---------|------|
| 1 | 🔧 | 优化后编译通过 | `Bash` 构建命令，exit code 0 |
| 2 | 🔧 | 全量测试通过 | `Bash` 测试命令，exit code 0 |
| 3 | 🔧 | 代码风格一致 | `Read` 抽查 3+ 文件 |

全部通过 → `git commit` → 更新 telemetry → 进入阶段7
