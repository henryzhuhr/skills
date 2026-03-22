# gh 命令行使用说明与最佳实践

这份参考用于补充 `github-helper` 的核心工作流。遇到以下情况时优先读取本文件：

- 需要查询仓库、PR 之外的 GitHub 信息
- 需要用 `gh api` 访问 REST / GraphQL
- 需要稳定地格式化输出，而不是直接展示原始结果
- 需要在多仓库、多 Host 或多账号场景下避免歧义

## 基本原则

- 先确认认证状态：`gh auth status`
- 先确认目标仓库：优先显式传 `--repo <owner>/<repo>`
- 默认只执行只读命令；写操作必须先得到用户明确授权
- 输出优先使用 `--json` 配合 `--jq` 或 `--template`，不要直接返回大段原始文本
- 用户给的是 URL 时，先解析出仓库和编号，再执行命令

## 认证与上下文

常用命令：

```bash
gh auth status
gh auth login
gh auth logout
gh api user --jq '.login'
gh repo view --json nameWithOwner,url,defaultBranchRef,isPrivate
```

最佳实践：

- 先看 `gh auth status`，确认当前 Host、账号和 token 状态
- “当前用户”默认指当前 `gh` 登录账号，不要猜测成 Git 本地作者
- “当前仓库”默认指当前目录对应的 GitHub 仓库；如果当前目录不明确，要求用户指定
- 在自动化或脚本场景下，尽量显式带上 `--repo`

## 常用只读命令

### 用户与仓库

```bash
gh repo list <owner> --limit 100
gh repo list <owner> --limit 100 --json name,nameWithOwner,isPrivate,isFork,isArchived,url,updatedAt
gh repo view <owner>/<repo>
gh repo view <owner>/<repo> --json nameWithOwner,description,url,defaultBranchRef,isPrivate
```

适用场景：

- 查看当前用户有哪些仓库
- 查看某个 org 的仓库
- 获取仓库简介、默认分支、可见性和链接

最佳实践：

- 用户只问“有哪些仓库”时，默认按最近更新时间排序汇报
- 仓库较多时，先做筛选，例如 private、archived、fork、最近更新前 N 个
- 结果里至少保留仓库名、可见性、更新时间和 URL

### Pull Request

```bash
gh pr list --repo <owner>/<repo> --limit 30
gh pr view <number> --repo <owner>/<repo>
gh pr view <number> --repo <owner>/<repo> --json number,title,state,isDraft,reviewDecision,mergeStateStatus,headRefName,baseRefName,url
gh pr checks <number> --repo <owner>/<repo>
gh pr diff <number> --repo <owner>/<repo>
```

适用场景：

- 查看 PR 当前状态
- 检查 review 决策、CI 状态、是否可合并
- 查看 PR 改动内容

最佳实践：

- 查询 PR 时，优先同时查看 `gh pr view --json ...` 和 `gh pr checks`
- 输出时先给结论，再给字段，例如 `OPEN`、`APPROVED`、`BLOCKED`
- 用户只给 PR 编号时，先补全仓库上下文，不要在错误仓库里查询
- 用户给的是 PR 链接时，先从链接提取仓库和编号

### Issue 与 Workflow

```bash
gh issue list --repo <owner>/<repo> --limit 30
gh issue view <number> --repo <owner>/<repo>
gh run list --repo <owner>/<repo> --limit 20
gh run view <run-id> --repo <owner>/<repo>
gh run watch <run-id> --repo <owner>/<repo>
```

适用场景：

- 查看 issue 列表和详情
- 查看 GitHub Actions workflow run 状态
- 跟踪某次 CI 执行

最佳实践：

- 查询 CI 时，优先说明 workflow 名称、状态、结论和触发分支
- `gh run watch` 是长时间命令，只有在用户明确要持续观察时才使用

## `gh api` 用法

当高层命令不够用时，使用 `gh api` 访问 GitHub API。

常用示例：

```bash
gh api user
gh api repos/<owner>/<repo>
gh api repos/<owner>/<repo>/pulls/<number>
gh api graphql -f query='query { viewer { login } }'
```

推荐参数：

```bash
gh api user --jq '.login'
gh api repos/<owner>/<repo>/pulls --paginate
gh api repos/<owner>/<repo>/pulls/<number> -H 'Accept: application/vnd.github+json'
```

最佳实践：

- 需要结构化字段时优先用 `--jq`
- 列表接口超过单页结果时，使用 `--paginate`
- 需要特定响应格式时，显式加 `-H 'Accept: application/vnd.github+json'`
- 除非用户明确要求，否则不要把完整 API 原始 JSON 直接贴给用户

## 输出格式建议

优先选择：

```bash
gh repo list <owner> --json name,isPrivate,updatedAt --jq '.[] | {name, isPrivate, updatedAt}'
gh pr view <number> --repo <owner>/<repo> --json title,state,reviewDecision,mergeStateStatus
```

也可以用模板：

```bash
gh pr view <number> --repo <owner>/<repo> --template '{{.title}} {{.state}}'
```

最佳实践：

- 面向用户汇报时，尽量先提炼成表格或要点
- 查询“状态”类问题时，先给一句摘要，再列关键字段
- 对空字段、缺失字段和无结果要明确写出，不要脑补

## 安全与稳定性建议

- 写操作前先复述将要执行的命令和目标对象
- 批量操作前先汇总目标列表，例如多个 PR、多个仓库、多个 issue
- 不要把“当前仓库”假定为远端唯一仓库；尽量显式写 `--repo`
- 网络失败、权限不足、仓库不存在时，直接暴露错误，不要编造结果
- 对可能耗时的命令，如 `gh run watch`、大范围 `gh api --paginate`，先说明预期行为
