---
name: github-helper
description: 使用 GitHub CLI（gh）帮助查看和管理 GitHub 账号、仓库、Pull Request 等信息。适用于查看当前认证用户有哪些仓库、查询某个 PR 的状态、检查 review / CI / mergeability、查看当前仓库元信息，或在执行 GitHub 操作前先确认鉴权与仓库上下文的场景。
---

# 🐙 GitHub Helper

用这个 skill 通过 `gh` 安全地查看和管理 GitHub 信息。默认优先执行只读操作；涉及创建、修改、关闭、合并、评论、打标签等写操作时，必须先得到用户明确指令。

如果任务超出这里列出的核心工作流，或需要更完整的 `gh` 命令参考、输出格式建议、`gh api` 用法和通用最佳实践，读取 [references/gh-cli.md](references/gh-cli.md)。

## 前置检查

1. 先执行 `gh auth status`，确认 `gh` 已安装、已登录，以及当前使用的 GitHub Host 和账号。
2. 如果用户说“当前用户”，默认指当前 `gh` 认证账号，可用 `gh api user --jq '.login'` 获取 login。
3. 如果任务与“当前仓库”相关，优先在当前 Git 仓库中推导仓库上下文：
   - 先用 `gh repo view --json nameWithOwner,url,defaultBranchRef,isPrivate`
   - 如果失败，再看 `git remote -v`
4. 如果用户给的是 PR 链接，先从 URL 提取 `<owner>/<repo>` 和 PR 编号；如果只给了 PR 编号但没给仓库，要先补全仓库上下文。
5. 如果 `gh` 未登录、仓库不存在、或当前目录不是目标仓库，要直接说明阻塞点，不要猜测目标。

## 标准工作流

### 1. 查看当前认证用户的仓库

1. 运行 `gh api user --jq '.login'` 获取当前账号。
2. 运行 `gh repo list <login> --limit 100 --json name,nameWithOwner,description,isPrivate,isFork,isArchived,url,updatedAt`。
3. 默认按 `updatedAt` 倒序整理结果，至少汇报：
   - 仓库名
   - 可见性（public/private）
   - 是否 fork / archived
   - 最近更新时间
   - URL
4. 如果用户只想看某一类仓库，优先在输出中筛选并说明筛选条件，例如仅 private 仓库、最近更新的前 N 个、某个 org 下的仓库。
5. 如果用户要看 organization 的仓库，不要把“当前用户仓库”和“org 仓库”混为一谈；显式使用 `gh repo list <org>`。

### 2. 查看某个 PR 的状态

在拿到 `<owner>/<repo>` 和 PR 编号后，优先执行：

```bash
gh pr view <number> --repo <owner>/<repo> \
  --json number,title,state,isDraft,reviewDecision,mergeStateStatus,author,headRefName,baseRefName,url,createdAt,updatedAt
gh pr checks <number> --repo <owner>/<repo>
```

需要时再补充：

```bash
gh pr view <number> --repo <owner>/<repo> --comments
```

汇报时至少包含：

- PR 标题与链接
- 当前状态：`OPEN` / `MERGED` / `CLOSED`
- 是否 Draft
- review 状态：`APPROVED` / `REVIEW_REQUIRED` / `CHANGES_REQUESTED`
- checks 状态：通过 / 失败 / 进行中
- mergeability 信号：`mergeStateStatus`
- 目标分支和来源分支

如果用户问“这个 PR 现在什么状态”，优先给一段摘要，例如：

```text
PR #123 当前为 OPEN，非 Draft。
Review: CHANGES_REQUESTED
Checks: 1 failed, 3 passed
Merge: BLOCKED
```

### 3. 查看当前仓库信息

用户问“这个仓库是什么”“当前仓库有哪些 PR”之类的问题时，优先使用：

```bash
gh repo view --json nameWithOwner,description,url,defaultBranchRef,isPrivate
gh pr list --repo <owner>/<repo> --limit 30
```

只有在用户明确要求时再进一步查看 issue、release、workflow run 等扩展信息。

## 输出要求

- 先说清楚当前操作基于哪个 GitHub 账号、哪个仓库。
- 对结构化结果优先整理成简短表格或要点，不要原样倾倒 JSON。
- 如果字段为空或接口没返回，明确写“未返回”或“当前无数据”，不要自行脑补。
- 用户问“状态”时，优先给结论，再给支持该结论的关键字段。

## 安全规则

- 默认只做只读查询。
- 未经用户明确要求，不要执行 `gh pr merge`、`gh pr close`、`gh repo delete`、`gh issue close`、`gh pr review`、`gh pr comment` 等写操作。
- 涉及可能影响远端状态的操作时，先复述将要执行的动作，再执行。
- 如果一个请求会同时影响多个仓库或多个 PR，先汇总目标列表，避免误操作。
