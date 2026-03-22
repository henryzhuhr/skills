# gh issue 使用说明与最佳实践

当任务涉及 issue 列表、issue 详情、状态、标签和负责人时，优先读取本文件。

## 常用命令

```bash
gh issue list --repo <owner>/<repo> --limit 30
gh issue view <number> --repo <owner>/<repo>
```

## 适用场景

- 查看 issue 列表
- 查看某个 issue 的详情
- 检查 issue 当前状态、标签和 assignee

## 最佳实践

- 查询 issue 时，先确认目标仓库，不要把 issue 编号跨仓库复用
- 列表场景下优先限制数量，避免一次返回过多结果
- 汇报时至少说明编号、标题、状态和链接
- 用户问“谁在负责”或“有没有标签”时，再补充 assignee 和 labels
