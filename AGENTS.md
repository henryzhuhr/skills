# AGENTS

## ‼️ 项目约束

⚠️ 必须严格遵守以下规范，否则可能导致技能无法被正确加载和使用。

## 技能规范

## 添加技能

可以使用下面的命令添加技能：

```bash
npx skills init skills/<skill-name>
```

添加完成技能后，需要自动更新 `README.md` 中的技能列表，以便于面向 Github 用户通过 `README.md` 了解有哪些技能。

### 技能目录管理规范

技能目录的管理规范如下

```bash
/project-root
├── .agents
│   └── skills -> ../skills
├── .claude
│   └── skills -> ../skills
└── skills
    └── <skill-name>
```

1. 所有技能必须放在 `skills` 目录下，每个技能一个子目录
2. ⚠️ `.agents/skills` 和 `.claude/skills` 都以软链接的方式指向 `skills` 目录，绝对不允许直接修改 `.agents/skills` 和 `.claude/skills` 目录下的内容，应该通过修改 `skills` 目录来管理技能
