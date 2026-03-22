# gh repo 使用说明与最佳实践

当任务涉及仓库列表、仓库元信息、默认分支、可见性和仓库链接时，优先读取本文件。

## 常用命令

```bash
gh repo list <owner> --limit 100
gh repo list <owner> --limit 100 --json name,nameWithOwner,isPrivate,isFork,isArchived,url,updatedAt
gh repo view <owner>/<repo>
gh repo view <owner>/<repo> --json nameWithOwner,description,url,defaultBranchRef,isPrivate
```

## 适用场景

- 查看当前用户有哪些仓库
- 查看某个 organization 的仓库
- 获取仓库简介、默认分支、可见性和链接

## 最佳实践

- 用户只问“有哪些仓库”时，默认按最近更新时间排序汇报
- 仓库较多时，先做筛选，例如 private、archived、fork、最近更新前 N 个
- 结果里至少保留仓库名、可见性、更新时间和 URL
- “当前仓库”不明确时，先确认当前目录或显式指定 `<owner>/<repo>`
- 需要结构化输出时，优先 `--json` 配合 `--jq`
