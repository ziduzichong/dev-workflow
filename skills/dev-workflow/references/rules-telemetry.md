# 可观测性（Telemetry）

每个阶段的开始/结束时，更新 `$REPORT_DIR/workflow-telemetry.json`：

```json
{
  "session_id": "<session-id>",
  "start_time": "<ISO8601>",
  "stages": [
    {
      "stage": 1,
      "name": "规划与设计",
      "start_time": "<ISO8601>",
      "end_time": "<ISO8601>",
      "checkpoints": {"total": 3, "passed": 3, "failed": 0},
      "retries": 0,
      "status": "completed"
    }
  ],
  "total_stages_completed": 1,
  "overall_status": "in_progress"
}
```

## 完成汇报

全部阶段完成后，向用户汇报：

1. **交付物清单** — 代码、文档、构建产物、遥测数据
2. **各阶段检查点通过情况** — 汇总（含重试次数）
3. **已知问题和限制** — 未采纳的建议、未消除的 warning
4. **建议的后续工作** — 下一步行动项
