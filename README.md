# skills

## 目录

- [快速开始](#快速开始)
- [编写规范](#编写规范)
- [技能列表](#技能列表)
  - [git-commit-helper](#-git-commit-helper)
  - [git-worktree-helper](#-git-worktree-helper)

## 快速开始

查看此仓库中所有可用的技能

```bash
npx skills add henryzhuhr/skills --list
```

通过命令安装到当前项目

```bash
npx skills add henryzhuhr/skills --copy --agent claude-code -y --skill <skill-name>
```

更新技能只能通过重新安装的方式更新，安装时会覆盖之前的技能文件，无法使用 `npx skills update` 的原因参考 PR： [#544](https://github.com/vercel-labs/skills/pull/544)、[#637](https://github.com/vercel-labs/skills/pull/637)、[#690](https://github.com/vercel-labs/skills/issues/690)，这是原项目 [vercel-labs/skills](https://github.com/vercel-labs/skills) 没有支持的功能。

## 编写规范

- [Skill 创建与编写规范](./docs/skill-authoring.md)
- [Starter 模板](./templates/skill-starter/SKILL.md.template)
- 评测资产建议按 `evals/<skill-name>/` 组织，不放入 skill 安装目录

## 技能列表

> 💡 安装技能的注意事项：
>
> - 不加 `-g` 参数将 `Installation scope` 设置为 `Project`，表示技能将安装在当前项目中
> - 使用 `--copy` 参数将 `Installation method` 设置为 `Copy`，表示技能将被复制到当前项目

- 使用 `--agent claude-code` 参数将自动设置安装目录为 `./.claude/skills`

### 📝 git-commit-helper

[`SKILL.md`](./skills/git-commit-helper/SKILL.md)：Use When 提交代码到 git 仓库，使用 commit message

```bash
npx skills add henryzhuhr/skills --copy --agent claude-code -y --skill git-commit-helper
```

### 🌳 git-worktree-helper

[`SKILL.md`](./skills/git-worktree-helper/SKILL.md)：检查、创建和管理 Git worktree，用于并行分支开发。适用于需要按本地分支模式批量创建 worktree、把 worktree 放到仓库同级目录、避免为已在其他目录检出的分支重复创建 worktree、核对分支与 worktree 路径映射，或在多个分支上修改前先准备隔离工作区的场景。

```bash
npx skills add henryzhuhr/skills --copy --agent claude-code -y --skill git-worktree-helper
```
