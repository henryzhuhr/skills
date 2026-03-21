# skills

🎉 My personal directory of skills, hope it can be helpful for your work.

## Get Started

Find all available skills in this repository

```bash
npx skills add henryzhuhr/skills --list
```

Check updates for all installed skills in the current project

```bash
npx skills check
```

Update skills in this repository

```bash
# default to update all
npx skills update --project

# Update specific skill
npx skills update --project <skill-name>
```

## Install List

> Notes for installing skills command:
>
> - set `Installation scope` to `Project` by without `-g` flag which means the skill will be installed in the current project.
> - set `Installation method` to `Copy` by using `--copy` flag which means the skill will be copied to the current project.

- the install directory auto set to `./claude/skills` by `--agent claude-code`

### git-commit

A skill for generating commit messages and committing code to git repositories. See [`skills/git-commit/SKILL.md`](./skills/git-commit/SKILL.md)

```bash
npx skills add henryzhuhr/skills --copy --agent claude-code --skill git-commit -y
```
