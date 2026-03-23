---
name: git-tag-helper
description: 管理 Git tag（标签），包括创建、查看、删除和发布 tag。适用于需要给版本打标签、管理发布版本、同步 tag 到远端等场景。
---

# 🏷️ Git Tag Helper

用这个 skill 以安全、规范的方式管理 Git tag。

## 标准工作流程

前置约束：

- 创建 tag 前必须先检查是否已存在同名 tag
- 删除 tag 前必须确认该 tag 是否已推送到远端
- 强制推送 tag 属于危险操作，必须由用户明确确认
- 禁止在未确认的情况下删除远端 tag

### 1. 查看当前 tag 状态

1. 执行命令 `git tag --list '<pattern>'` 查看本地 tag，按语义版本排序（如 v1.0.0, v1.2.3）。
2. 执行命令 `git ls-remote --tags origin` 查看远端 tag。
3. 汇总本地和远端 tag，输出格式示例：

    ```bash
    🏷️ Tag 列表

    | Tag | 本地 | 远端 | 类型 |
    | ---- | ---- | ---- | ---- |
    | v1.0.0 | ✅ | ✅ | annotated |
    | v1.1.0 | ✅ | ✅ | annotated |
    | v1.2.0 | ✅ | ❌ | annotated |
    | v2.0.0-beta | ✅ | ✅ | lightweight |
    ```

4. 如果用户只要求查看 tag 状态，到这里即可，不进入后续步骤。
5. 如果用户要求创建、删除或推送 tag，继续执行后续步骤。

### 2. 创建新 tag

#### 2.1 确定 tag 信息

- 询问用户想要创建的 tag 名称（如 `v1.2.3`）
- 确认 tag 类型：
  - **Annotated tag**（推荐用于版本发布）：包含 tagger 信息、日期、注释，使用 `-a` 参数创建
  - **Lightweight tag**：仅指向特定 commit 的引用，适合临时标记
- 确认目标 commit（默认是当前 HEAD）
- 如果是 annotated tag，询问是否需要添加 tag message

#### 2.2 检查 tag 是否已存在

- 运行 `git tag -l <tag-name>` 检查 tag 是否已存在
- 如果 tag 已存在，询问用户是要覆盖还是使用新名称

#### 2.3 创建 tag

根据用户选择执行相应命令：

```bash
# Annotated tag（推荐）
git tag -a v1.2.3 -m "Release version 1.2.3"

# Annotated tag with custom message
git tag -a v1.2.3 -m "Release version 1.2.3

This release includes:
- Feature A
- Bug fix B
"

# Lightweight tag
git tag v1.2.3

# Tag specific commit
git tag -a v1.2.3 <commit-hash> -m "Release version 1.2.3"
```

#### 2.4 验证创建结果

- 运行 `git tag -l v1.2.3` 确认 tag 已创建
- 运行 `git show v1.2.3` 显示 tag 详情（类型、目标 commit、message）
- 告诉用户 tag 创建成功，并显示简要信息

### 3. 推送 tag 到远端

#### 3.1 推送单个 tag

```bash
git push origin v1.2.3
```

#### 3.2 推送所有本地 tag

```bash
git push origin --tags
```

#### 3.3 强制推送（覆盖远端 tag）

⚠️ **警告**：只有在你确定需要覆盖远端 tag 时才使用此操作

```bash
git push origin v1.2.3 --force
```

#### 3.4 验证推送结果

- 运行 `git ls-remote --tags origin` 确认 tag 已推送到远端
- 告诉用户推送成功

### 4. 删除 tag

#### 4.1 删除本地 tag

```bash
git tag -d v1.2.3
```

#### 4.2 删除远端 tag

```bash
git push origin --delete v1.2.3
```

#### 4.3 同时删除本地和远端 tag

```bash
git tag -d v1.2.3 && git push origin --delete v1.2.3
```

#### 4.4 验证删除结果

- 运行 `git tag -l <tag-name>` 确认本地 tag 已删除
- 运行 `git ls-remote --tags origin` 确认远端 tag 已删除
- 告诉用户删除成功

### 5. 同步远端 tag

如果远端有新的 tag 需要拉到本地：

```bash
# 获取所有远端 tag
git fetch origin --tags

# 或者获取特定 tag
git fetch origin tag v1.2.3
```

## 安全规则

- 创建 annotated tag 时，必须提供有意义的 message
- 推送 tag 前，必须确认 tag 指向正确的 commit
- 删除 tag 前，必须确认该 tag 是否已经推送到远端
- 强制推送 tag 属于危险操作，必须由用户明确确认
- 不要随意删除远端 tag，除非用户明确要求

## 执行要求

- 创建 tag 前，先检查是否已存在同名 tag
- 如果 tag 已存在，询问用户是要覆盖还是使用新名称
- 推送 tag 后，验证远端是否已成功接收
- 汇报结果时明确区分：本地操作、远端操作
- 批量操作时，先汇总计划，再执行
- 非 Git 仓库、tag 不存在、命令执行失败：属于失败，直接停止并暴露错误

## 常见场景

### 发布新版本

1. 确认当前分支是 main/master 或 release 分支
2. 检查 CHANGELOG 或 release notes
3. 创建 annotated tag：`git tag -a v1.2.3 -m "Release version 1.2.3"`
4. 推送 tag：`git push origin v1.2.3`
5. 可选：创建 GitHub release

### 标记临时版本

1. 创建 lightweight tag：`git tag temp-test-1`
2. 使用后及时删除：`git tag -d temp-test-1`

### 清理本地 tag

1. 获取远端 tag 列表：`git ls-remote --tags origin`
2. 对比本地和远端，找出本地多余的 tag
3. 删除本地多余的 tag：`git tag -d <tag>`
