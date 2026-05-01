# 各语言构建命令自动检测表

扫描项目目录，根据以下特征文件判断语言，选择构建命令。如果用户手动指定了构建命令，优先使用用户指定。

| 特征文件 | 语言 | 默认构建命令 | 测试命令 |
|---------|------|-------------|---------|
| `*.csproj` / `*.sln` | C# | `dotnet build` | `dotnet test` |
| `Makefile` + `*.c` | C (Make) | `make -j4` | `make test` |
| `CMakeLists.txt` + `*.c` | C (CMake) | `cmake --build build` | `ctest` |
| `CMakeLists.txt` + `*.cpp` | C++ | `cmake --build build` | `ctest` |
| `package.json` + `*.ts` | TypeScript | `npx tsc --noEmit` | `npm test` |
| `package.json` (only) | JavaScript | `npm run build` (if exists) | `npm test` |
| `go.mod` | Go | `go build ./...` | `go test ./...` |
| `Cargo.toml` | Rust | `cargo build` | `cargo test` |
| `pom.xml` | Java (Maven) | `mvn compile` | `mvn test` |
| `build.gradle*` | Java (Gradle) | `gradle build` | `gradle test` |
| `pyproject.toml` / `setup.py` | Python | `python -m py_compile <files>` | `pytest` |
| `STM32*.ioc` | STM32 (CubeIDE) | `STM32CubeIDE --launcher.suppressErrors -nosplash -application org.eclipse.cdt.managedbuilder.core.headlessbuild -build <project>` | — |

## 嵌入式项目烧录验证（可选）

- ST-Link: `st-flash write <firmware.bin> 0x08000000`
- OpenOCD: `openocd -f board/stm32f103c8t6.cfg -c "program <firmware.elf> verify reset"`

## 无法写自动化测试时的替代方案

用编译检查 + 静态分析替代测试步骤，必须执行以下工具中的至少一种：

- **C/C++**: `cppcheck --enable=all --inconclusive --std=c11 .` 或 `clang-tidy *.c -- -I.`
- **C#**: `dotnet format --verify-no-changes`
- **Python**: `ruff check .` 或 `mypy .`

在任务摘要中标注"无自动化测试，已通过编译验证 + <静态分析工具名>"。TDD 循环简化为：写实现 → 编译 → 静态分析 → 通过 → 下一任务。
