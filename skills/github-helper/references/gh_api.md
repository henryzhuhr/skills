# gh api 使用说明与最佳实践

当高层 `gh` 子命令不够用，或需要直接访问 REST / GraphQL、分页和字段筛选时，优先读取本文件。

## 常用示例

```bash
gh api user
gh api user --jq '.login'
gh api repos/<owner>/<repo>
gh api repos/<owner>/<repo>/pulls/<number>
gh api repos/<owner>/<repo>/pulls --paginate
gh api repos/<owner>/<repo>/pulls/<number> -H 'Accept: application/vnd.github+json'
gh api graphql -f query='query { viewer { login } }'
```

## 适用场景

- 高层命令无法覆盖的查询
- 需要直接访问 REST / GraphQL
- 需要分页、筛字段或定制响应格式

## 最佳实践

- 需要结构化字段时优先用 `--jq`
- 列表接口超过单页结果时，使用 `--paginate`
- 需要特定响应格式时，显式加 `-H 'Accept: application/vnd.github+json'`
- 除非用户明确要求，否则不要把完整 API 原始 JSON 直接贴给用户
- 面向用户汇报时，先提炼成表格或要点，再补关键字段
