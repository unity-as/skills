---
name: git-collab-docs
version: 1
description: >-
  Manages git for collaborative work: when to branch vs commit, branch naming,
  stage-commit format, what counts as shared files, and when a branch may merge
  to main. Use when the user mentions 提交、commit、push、推送、pull、拉取、同步、
  合作、协作、仓库、分支、branch、PR、合并, or after a stage of work is saved.
---

# Git 协作

默认正本分支：**`main`**。只收已经能用的代码。不对 `main` `push --force`。不要改 remote、不要 `git config`。改仓库前 `git pull`，推前再 pull。

多人时默认开多条分支，不要都堆在 `main` 上改。

## 合作文件 vs 不提交

**要跟踪（合作）：**

- 项目相关代码，以及辅助脚本
- `.claude/`、`.cursor/` 等 Agent 配置，以及 `CLAUDE.md` / `AGENTS.md` 指明的工作产物
- 根 `README.md`（或 `README`）以及其中链接、列出的文档、目录、配置包
- 已指定为永久资料的目录

**不提交：**

- 临时资料（未指定为永久目录的）
- 明确为个人文件的
- 密钥、`.env`、本机私密配置

含图 Markdown（`![`、`<img`、`data:image`）：算合作文件，但 **Agent 禁止编辑**（会卡死）。人改完可以 commit。

## 大更新：开分支

发现程序有问题、模块要优化、新功能、改现有逻辑 → **开新分支**。

命名：`类型/短名`

| 类型 | 何时 |
|------|------|
| `feat/` | 新功能 |
| `fix/` | 修问题 |
| `opt/` | 优化 |

例：`feat/chassis`、`fix/openocd-path`。

大更新里可再开一层：从**当前分支该点**分出，不必先回 `main`。例：`feat/chassis` 上方案 1 做完，要试方案 2 → `feat/chassis-scheme2`。

```text
git pull
git checkout -b feat/短名
git push -u origin HEAD
```

不要 `git rebase -i`。

## 小更新：只 commit

仍在当前分支。下列情况 **一次 commit**，不开新分支：

- 改完一堆，需要保存
- 阶段成果要存档
- 要转方向，但还继承前面工作

**提交说明：**

1. 第一行：这阶段做了什么。  
2. 需要时加注释：后人要接着用的注意点。  
3. 写明 **何时**、**谁**（GitHub 用户名）。

```text
feat: 底盘方案1 能过编译

这里用了指针，后面改 xxx 时注意 yyy。
作者: github-username
时间: 2026-08-31
```

用户说「提交 / commit / 推送」时执行。一次小更新完成（能用上面第一行说清、没有半截）时也可以提交。一条 commit 一件事。

## 分支完成与合并

完成标志：**这件事做完，且与用户一致认定已经测过、可以成熟使用。**  
`main` 只合能用的结果。Agent 说「改完了」不算完成。

未完成：还在讨论、用户说先放着、没测过、和 `main` 冲突未解、没 push。

合并前 **再问一遍** 是否合并。若尚未走 Pull Request、用户却要求直接合，问的时候 **提醒可以用 PR**，用户仍要直接合则按其说的做。

未确认不要 merge。不要 force 掉别人的分支。

```text
git checkout main
git pull
git merge feat/短名
git push
git branch -d feat/短名
```

## 日常

先 pull，再改，阶段到了就 commit，大改动走分支。同一文件避免两人并行大改。冲突搜 `<<<<<<<`，留该留的，再 commit + push。
