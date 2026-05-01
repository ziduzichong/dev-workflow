# 用户扩展机制（Hooks）

用户可在 `$HOOKS_DIR` 下放置自定义脚本，在工作流特定节点自动执行：

| Hook 点 | 触发时机 | 脚本路径 |
|---------|---------|---------|
| `pre_stage_<N>` | 阶段 N 开始前 | `$HOOKS_DIR/pre-stage-<N>.sh` |
| `post_stage_<N>` | 阶段 N 完成后 | `$HOOKS_DIR/post-stage-<N>.sh` |
| `on_abort` | 工作流中止时 | `$HOOKS_DIR/on-abort.sh` |
| `on_complete` | 工作流完成时 | `$HOOKS_DIR/on-complete.sh` |

Hook 为可选功能，无脚本存在则跳过。Hook 失败不影响工作流继续。

### Hook 脚本示例

`post-stage-3.sh`：自动运行全量测试并通知。

```bash
#!/bin/bash
# post-stage-3.sh
# 阶段3完成后自动运行全量测试

echo "🔍 开始运行全量测试..."
测试结果=$(npm test 2>&1)
if [ $? -eq 0 ]; then
  echo "✅ 阶段3完成，全部测试通过"
else
  echo "⚠️ 阶段3完成，但测试有失败："
  echo "$测试结果"
fi
```

`pre-stage-1.sh`：阶段1开始前检查必要工具。

```bash
#!/bin/bash
# pre-stage-1.sh
# 检查 git 和基本构建工具

if ! command -v git &> /dev/null; then
  echo "错误：未安装 git，请先安装 git"
  exit 1
fi

echo "✅ 阶段1前置检查通过"
```
