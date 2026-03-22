# AGENTS

## ‼️ 项目约束

⚠️ 必须严格遵守以下规范，否则可能导致技能无法被正确加载和使用。

## 技能规范

## 添加技能

可以使用下面的命令添加技能：

```bash
npx skills init skills/<skill-name>
```

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

### 技能说明文档同步

> ⚠️ 每次都需要检查

项目根目录下的 `README.md` 文件里的 `## 技能列表` 部分列举了所有技能的简介和安装命令，你需要自动检查 `./skills` 目录下的技能列表，并将它们的信息同步到 `README.md` 文件中，保持技能列表的更新和准确。

- 技能简介应该简洁明了，突出技能的核心功能和适用场景

- 每一个技能标题都需要添加一个适合 emoji 来增加视觉吸引力

- 在标题后，需要添加一个链接，指向技能文件，例如

  ```markdown
  ### 📝 <skill-name>

  [`SKILL.md`](./skills/<skill-name>/SKILL.md)：<技能简介>
  ```

- 你需要检查 `README.md` 是否符合上述规范，如果不符合，你需要自动修正它，使其符合规范。但是需要注意，自动修正只能修改 [`## 技能列表`](./README.md#技能列表) 部分
