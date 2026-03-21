# skills

## 目录

- [快速开始](#快速开始)
- [技能列表](#技能列表)
  - [git-commit-helper](#-git-commit-helper)
  - [git-worktree-helper](#-git-worktree-helper)

## 快速开始

查看此仓库中所有可用的技能

```bash
npx skills add henryzhuhr/skills --list
```

检查当前项目中已安装技能的更新

```bash
npx skills check
```

更新此仓库中的技能

```bash
# 默认更新所有技能
npx skills update --project

# 更新指定技能
npx skills update --project <skill-name>
```

## 技能列表

> 💡 安装技能的注意事项：
>
> - 不加 `-g` 参数将 `Installation scope` 设置为 `Project`，表示技能将安装在当前项目中
> - 使用 `--copy` 参数将 `Installation method` 设置为 `Copy`，表示技能将被复制到当前项目

- 使用 `--agent claude-code` 参数将自动设置安装目录为 `./claude/skills`

### 📝 git-commit-helper

> [`SKILL.md`](./skills/git-commit-helper/SKILL.md)

Use When 提交代码到 git 仓库，使用 commit message

```bash
npx skills add henryzhuhr/skills --copy --agent claude-code --skill git-commit-helper -y
```

### 🌳 git-worktree-helper

> [`SKILL.md`](./skills/git-worktree-helper/SKILL.md)

检查、创建和管理 Git worktree，用于并行分支开发。适用于需要按本地分支模式批量创建 worktree、把 worktree 放到仓库同级目录、避免为已在其他目录检出的分支重复创建 worktree、核对分支与 worktree 路径映射，或在多个分支上修改前先准备隔离工作区的场景。

```bash
npx skills add henryzhuhr/skills --copy --agent claude-code --skill git-worktree-helper -y
```
