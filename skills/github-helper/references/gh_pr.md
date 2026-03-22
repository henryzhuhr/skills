# gh pr 使用说明与最佳实践

当任务涉及 Pull Request 状态、review、checks、mergeability 和 diff 时，优先读取本文件。

## 常用命令

```bash
gh pr list --repo <owner>/<repo> --limit 30
gh pr view <number> --repo <owner>/<repo>
gh pr view <number> --repo <owner>/<repo> --json number,title,state,isDraft,reviewDecision,mergeStateStatus,headRefName,baseRefName,url
gh pr checks <number> --repo <owner>/<repo>
gh pr diff <number> --repo <owner>/<repo>
```

## 适用场景

- 查看 PR 当前状态
- 检查 review 决策、CI 状态、是否可合并
- 查看 PR 改动内容

## 最佳实践

- 查询 PR 时，优先同时查看 `gh pr view --json ...` 和 `gh pr checks`
- 输出时先给结论，再给字段，例如 `OPEN`、`APPROVED`、`BLOCKED`
- 用户只给 PR 编号时，先补全仓库上下文，不要在错误仓库里查询
- 用户给的是 PR 链接时，先从链接提取仓库和编号
- 需要解释“为什么不能合并”时，重点看 `reviewDecision`、`mergeStateStatus` 和 checks 结果
