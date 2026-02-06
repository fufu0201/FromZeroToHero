# 第 3 课：版本控制入门 — Git 使用详解

## 本课目标
- 理解版本控制（VCS）与 Git 的基本概念
- 能完成本地仓库的初始化、常见工作流（add/commit/push/pull）
- 掌握分支管理、冲突处理与常用恢复命令
- 了解与远程托管（GitHub/GitLab）交互的基本流程
- 熟悉 `.gitignore`、stash、tag 等实用功能


---

## 1. 什么是版本控制（VCS）与 Git
- 版本控制是记录文件随时间变化历史的系统，可回溯、比较、恢复历史版本。
- Git 是分布式版本控制系统（DVCS）：每个克隆的仓库都有完整历史，本地即可提交与查看历史。
- 常见远程托管服务：GitHub、GitLab、Bitbucket（用于协作和代码托管）。

---

## 2. 安装与配置（快速）
- Windows 推荐安装 Git for Windows（含 Git Bash）；macOS 使用 Homebrew：`brew install git`；Linux 用包管理器安装。
- 基本配置（只需执行一次）：

```bash
git config --global user.name "你的名字"
git config --global user.email "you@example.com"
# 可选：设置常用别名
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
```

- 查看配置：`git config --list`。

---

## 3. 本地仓库的基本操作

1) 在现有项目中初始化仓库：

```bash
cd path/to/your/project
git init
```

2) 克隆远程仓库：

```bash
git clone https://github.com/username/repo.git
# 或使用 SSH
git clone git@github.com:username/repo.git
```

3) 查看当前状态：

```bash
git status
```

4) 把修改加入暂存区（stage）：

```bash
git add file.py
# 或添加全部改动
git add .
```

5) 提交到本地仓库：

```bash
git commit -m "简短但有意义的提交信息"
```

6) 查看提交历史：

```bash
git log --oneline --graph --decorate --all
```

7) 查看未提交的改动：

```bash
git diff        # 工作区 vs 暂存区
git diff --staged # 暂存区 vs 最后一次提交
```

---

## 4. 与远程仓库交互

- 查看远程：`git remote -v`
- 添加远程：`git remote add origin <url>`
- 推送（本地分支推送到远程）：

```bash
git push origin main    # 或 master / 你的分支名
# 若远程不存在该分支，可以创建并推送：
git push -u origin my-branch
```

- 拉取远程改动并合并到当前分支：

```bash
git pull origin main
```

- 更推荐的方式：先 `git fetch` 再 `git merge` 或 `git rebase`（更干净的历史）：

```bash
git fetch origin
git merge origin/main
# 或
git rebase origin/main
```

---

## 5. 分支管理（Branching）

- 列出分支：`git branch`
- 创建分支：`git branch feature-x`
- 切换分支（旧）：`git checkout feature-x`
- 切换/创建分支（新）：`git switch -c feature-x` 或 `git checkout -b feature-x`
- 合并分支：

```bash
# 在目标分支上（例如 main）执行：
git merge feature-x
```

- 常见流程：
  1. 基于 `main` 创建 feature 分支
  2. 在 feature 分支开发并多次 commit
  3. 将 feature 合并回 `main`（通过 PR 或本地 merge）

- 处理冲突（conflict）：当两处修改相同文件相同位置时会产生冲突。Git 会在冲突位置标注：

```
<<<<<<< HEAD
本地版本
=======
远程/其他分支的版本
>>>>>>> feature-x
```

手动编辑解决冲突后：

```bash
git add resolved_file
git commit   # 完成合并提交
```

---

## 6. 常用恢复与撤销命令（谨慎使用）

- 丢弃工作区改动（未暂存）：

```bash
git checkout -- file         # 恢复到上次提交的版本（工作区）
# 或（较新命令）
git restore file
```

- 撤销已暂存但未提交的改动：

```bash
git restore --staged file
```

- 撤销最后一次提交（保留修改到工作区）：

```bash
git reset --soft HEAD~1
```

- 撤销最后一次提交且丢弃修改（危险）：

```bash
git reset --hard HEAD~1
```

- 恢复到某个历史提交（使用 commit id）：

```bash
git checkout <commit-hash>  # 查看某个提交快照（处于分离 HEAD 状态）
# 或创建基于该提交的新分支：
git switch -c temp <commit-hash>
```

- 使用 `git revert <commit>` 可以生成一个新的提交来反做历史提交（安全用于公共分支）。

---

## 7. 暂存操作（stash）

当你在某个分支有未完成改动，但需要切换分支临时处理别的事务时：

```bash
git stash save "work-in-progress"
# 切回后恢复：
git stash list
git stash apply stash@{0}   # 应用但保留 stash
# 或
git stash pop               # 应用并移除 stash
```

---

## 8. .gitignore 与忽略文件

在项目根目录创建 `.gitignore` 列出不需要跟踪的文件/目录，例如：

```
# Python 常见忽略项
__pycache__/
*.py[cod]
venv/
.env
*.log
```

修改 `.gitignore` 仅影响未被 Git 跟踪的文件；已被跟踪的文件需要先 `git rm --cached file` 才会停止被跟踪。

---

## 9. 标签（Tag）

用于给 release 打标签：

```bash
git tag -a v1.0 -m "release v1.0"
git push origin v1.0
```

---

## 10. 在 GitHub 上的基本协作流程（简述）

1. Fork + Clone（非本仓库贡献者）：Fork 仓库到自己帐号 -> clone 到本地
2. 新建 feature 分支，完成开发并 commit
3. push 到自己的远程 Fork
4. 在 GitHub 上发起 Pull Request（PR），描述变更并请求合并
5. 代码评审（Review）-> 修正 -> 合并（由仓库维护者）

PR 流程帮助多人协作、代码审查与 CI（持续集成）自动化。

---

## 11. 在 VS Code 中使用 Git（快速提示）

- VS Code 自带 Git 支持：侧边栏有 Source Control 面板，可以完成 `stage` / `commit` / `push` / `pull` / `branch` 等操作。
- 推荐在命令行和 GUI 之间结合使用：命令行用于复杂操作，GUI 用于日常查看和快速提交。

---

## 12. 常见问题与最佳实践
- 提交信息要简洁有意义：首行概括（50 字符以内），可选空行后附加详细说明，常用说明有新功能：feat，修复bug：fix。例：`feat：Add user login validation`。
- 小步提交（小功能/小修复多次提交），便于回滚和审查。
- 在合并到主分支前保持主分支绿色（CI 通过）并先拉取远程最新代码再合并。`
- 避免在公共分支用 `git reset --hard` 强行改写历史。

---
