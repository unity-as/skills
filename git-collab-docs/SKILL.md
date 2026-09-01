---
name: git-collab-docs
version: 1.1.0
description: >-
  Manages git for collaborative work: when to branch vs commit, branch naming,
  commit messages describe what was done only (author/time from git metadata),
  what counts as shared files, and when a branch may merge to main. Skips collab
  flow for non-collab docs and local-only (unpushed) repos. Use when the user
  mentions 提交、commit、push、推送、pull、拉取、同步、合作、协作、仓库、分支、
  branch、PR、合并, or after a stage of work is saved.
---

# Git 协作

不对 `main` `push --force`。不改 remote、不 `git config`、不用 `git rebase -i`。作者/时间用 git 元数据，不要写进 message。

## 先判

**非合作内容：** 不走协作。按用户指示改即可。

**仅本地仓库**（无 remote / 从未 push / 用户说只在本机）：只本地 checkout、commit、merge。不要强求 pull/push。

**已协作远程：** 走下面全文。`main` 只收能用的结果，只接受 merge，**任何改动（含一字）都开分支**，禁止在 `main` 上直接 commit。

**保护文件：** `CLAUDE.md` / `AGENTS.md` 列出的路径（未列则至少 `.claude/`、`CLAUDE.md`）。默认只读；用户点名才改；单独开分支；不和业务捆一次提交。

**含图 md**（`![`、`<img`、`data:image`）：合作文件，Agent 禁止编辑（会卡死）。人改完可 commit。

**竞态（同一条远程分支上云端已有「下一笔」，你还想当下一笔）：** 不要在这条路上接着 commit/push。记住本地 → 对齐云端 → 从云端新头开新分支 → 填回你的改动 → 推**新分支**。

**要跟踪：** 项目代码与辅助脚本；`.claude/` `.cursor/`；CLAUDE 指明的产物；README 及其链接内容；指定的永久目录。  
**不提交：** 临时资料、个人文件、密钥、`.env`。

## 开分支

**判：** 修问题 / 优化 / 新功能 / 改现有逻辑 → `feat/` `fix/` `opt/` + 短名。大更新里再分方案：从**当前分支该点**开，例如 `feat/chassis-scheme2`。已协作时从已更新的 `origin/main`（或当前共享分支的 `origin/...`）拉。

```text
git fetch
git checkout -b feat/短名 origin/main
git push -u origin HEAD
```

仅本地：`git checkout -b feat/短名`，不 push。

## 小更新（commit）

**判：** 已在功能分支上；阶段成果 / 改完要存 / 转向但仍继承前面。一条一事。用户说提交/推送，或一次小更新完成，才 commit。人在 `main` 上 → 先开分支再 commit。

第一行必须让没做这事的人知道**现在多了 / 修了 / 变成了什么**。写不出、或只能写「改了一下」→ 还不是一次小更新，先别 commit。

```text
git add <文件>
git commit -m "feat: 现在多了/修了/变成了什么"
```

需要后人注意时第二行再写。已协作则 `git push`。

## 云端抢先：本地改动挪到新分支

共享路以 `main` 为例；若撞的是 `feat/foo`，把 `origin/main` 换成 `origin/feat/foo`。

未 commit：

```text
git fetch
git stash -u
git checkout -b feat/短名 origin/main
git stash pop
git add <文件>
git commit -m "feat: …"
git push -u origin HEAD
```

已有一笔或多笔本地 commit（先钉住再 reset）：

```text
git fetch
git branch feat/短名
git reset --hard origin/main
git checkout feat/短名
git rebase origin/main
git push -u origin HEAD
```

不用 rebase 时：`git checkout -b feat/短名 origin/main` 再 `git cherry-pick 最早^..最晚`。冲突：解 → `git add` → `git rebase --continue` 或继续 cherry-pick。

## 分支完成与合并

**判完成：** 事做完，且与用户一致认定已测过、能成熟使用。Agent 说好了不算。未完成：讨论中、先放着、没测、和 `main` 冲突未解；已协作还包括没 push。

合并前再问一次。未走 PR、用户要直接合 → 提醒 PR，仍要合则按其说。未确认不 merge。

```text
git checkout main
git pull
git merge feat/短名
git push
git branch -d feat/短名
```

仅本地不 pull/push。冲突搜 `<<<<<<<`。
