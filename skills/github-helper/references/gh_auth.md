# gh auth 使用说明与最佳实践

当任务涉及登录、切换账号、token、scope、权限不足、Host 选择或 Git 凭证集成时，优先读取本文件。

## 常用命令

```bash
gh auth status
gh auth status --active --json hosts
gh auth login
gh auth login --hostname <hostname>
gh auth switch --hostname <hostname> --user <login>
gh auth refresh --scopes <scope1,scope2>
gh auth setup-git
gh auth logout
gh auth token
```

## `gh auth status`

适用场景：

- 确认当前激活账号
- 检查某个 Host 是否已登录
- 在执行 GitHub 操作前做鉴权健康检查

最佳实践：

- 多 Host 或多账号场景下，优先用 `gh auth status --active --json hosts`
- 只检查企业实例时，使用 `--hostname <hostname>`
- `--show-token` 和 `gh auth token` 都会暴露敏感信息，除非用户明确要求，否则不要执行
- 根据官方手册，`--json` 模式下即使某些账号存在认证问题也可能返回退出码 `0`，所以要检查输出字段，而不是只看退出码

## `gh auth login`

适用场景：

- 首次登录 `github.com`
- 登录 GitHub Enterprise Host
- 修复本地缺失或过期的认证配置

最佳实践：

- 默认使用浏览器登录流；企业实例用 `gh auth login --hostname <hostname>`
- 无头环境优先使用环境变量，而不是交互式登录
- 用 `--with-token` 时，标准输入应提供 classic PAT，最小 scope 为 `repo`、`read:org`、`gist`
- 对 fine-grained PAT，优先使用 `GH_TOKEN`
- 只有用户明确要配置 Git 走 SSH 时才处理 `--git-protocol ssh` 和 SSH key 上传

## `gh auth switch`

常用命令：

```bash
gh auth switch
gh auth switch --hostname <hostname> --user <login>
```

最佳实践：

- 同一 Host 下有多个账号时，不要假设当前活跃账号；先看 `gh auth status`
- 需要切到非活跃账号刷新 scope 时，先 `gh auth switch`，再执行 `gh auth refresh`
- 查询结果里要明确当前基于哪个 Host、哪个 login

## `gh auth refresh`

常用命令：

```bash
gh auth refresh
gh auth refresh --scopes project
gh auth refresh --remove-scopes admin:org
gh auth refresh --reset-scopes
```

适用场景：

- `gh project` 等命令提示 scope 不足
- 需要补充或收缩现有 token 的权限

最佳实践：

- 缺权限时优先补最小 scope，不要盲目扩大权限
- `repo`、`read:org`、`gist` 是最小基础 scope，不能通过移除命令去掉
- 用户只报“权限不够”时，先指出缺的具体 scope，再建议 `gh auth refresh --scopes <scope>`

## `gh auth setup-git`

常用命令：

```bash
gh auth setup-git
gh auth setup-git --hostname <hostname>
```

最佳实践：

- 需要让 `git` 复用 `gh` 凭证时再执行 `gh auth setup-git`
- 默认会为所有已认证 Host 配置 credential helper；如果只想处理一个 Host，就显式带 `--hostname`
- 这个命令会改本地 Git 凭证配置，属于会影响本机环境的操作，执行前应先说明

## `gh auth logout`

常用命令：

```bash
gh auth logout --hostname <hostname> --user <login>
```

最佳实践：

- 这个命令只删除本地存储的认证配置，不会撤销 GitHub 上已签发的 token
- 需要真正撤销 token 时，应到 GitHub 的授权应用页面处理
- 多账号场景下，显式带 `--hostname` 和 `--user`，避免登错账号

## `gh auth token`

常用命令：

```bash
gh auth token --hostname <hostname> --user <login>
```

最佳实践：

- 只在用户明确要求查看 token，或必须把 token 传给其他安全流程时才使用
- 不要把 token 写进文档、日志、commit message 或命令回显
- 如果只是判断当前账号是否可用，优先 `gh auth status` 或 `gh api user`

## 自动化与环境变量

常用环境变量：

```bash
export GH_TOKEN=<token>
export GH_ENTERPRISE_TOKEN=<token>
export GH_HOST=<hostname>
export GH_REPO=<owner>/<repo>
```

最佳实践：

- 自动化和 CI 优先使用环境变量认证，而不是交互式 `gh auth login`
- `GH_TOKEN` / `GITHUB_TOKEN` 会优先于已存储凭证，用于 `github.com` 或 `ghe.com` 子域
- 企业实例优先使用 `GH_ENTERPRISE_TOKEN`，并配合 `GH_HOST`
- GitHub Actions 里优先设置 `GH_TOKEN: ${{ github.token }}`
- 需要脱离当前工作目录执行命令时，可用 `GH_REPO` 明确目标仓库，减少上下文歧义
