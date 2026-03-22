# gh run 使用说明与最佳实践

当任务涉及 GitHub Actions workflow run、CI 状态、执行结论和持续观察时，优先读取本文件。

## 常用命令

```bash
gh run list --repo <owner>/<repo> --limit 20
gh run view <run-id> --repo <owner>/<repo>
gh run watch <run-id> --repo <owner>/<repo>
```

## 适用场景

- 查看 GitHub Actions workflow run 列表
- 查看某次 run 的状态和结论
- 跟踪某次 CI 执行

## 最佳实践

- 查询 CI 时，优先说明 workflow 名称、状态、结论和触发分支
- `gh run watch` 是长时间命令，只有在用户明确要持续观察时才使用
- 不知道 `run-id` 时，先 `gh run list` 再 `gh run view`
- 输出时区分 `status` 和 `conclusion`，避免把“仍在运行”和“已失败”混在一起
