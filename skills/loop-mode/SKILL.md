---
name: "loop-mode"
description: "Runs tasks in loop mode with todo tracking, one-step iterations, status summaries, resume guidance, and failure stop rules. Invoke when user prompt starts with /loop."
---

# Loop Mode

当用户的提示词以 `/loop` 开头时，必须进入 loop 模式执行任务。

## 执行规则

1. 先拆解任务并维护 todo。
2. 每轮只完成一个小步骤。
3. 每步完成后总结当前状态。
4. 如果中断，下次从最后状态继续。
5. 遇到连续失败 3 次就停止并说明原因。

## 使用方式

用户输入格式：

```text
/loop <具体任务>
```

执行时去掉 `/loop` 前缀，将后面的内容作为实际任务目标。

## 行为要求

- 开始执行前，先明确本轮目标和待办事项。
- 一次只推进一个明确、可验证的小步骤。
- 完成当前步骤后，立即更新 todo 状态。
- 每轮结束时输出简短状态总结，包含：
  - 已完成内容
  - 当前进度
  - 下一步计划
- 如果工具调用、验证或实现连续失败 3 次，停止继续尝试，并说明失败点和需要用户确认的信息。
- 如果任务已完成，说明最终结果和关键变更。

## 恢复约定

如果会话中断，恢复时应优先根据已有 todo、最近一次状态总结、已修改文件和终端结果继续执行，不要从头重复已完成步骤。
