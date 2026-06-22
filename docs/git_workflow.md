# Git/GitHub 学习与维护流程

本文档用于把本工程作为 Git/GitHub 实践案例。目标不是一次性学完 Git，而是在真实维修项目中逐步掌握最小、安全、可回退的工作流。

## 1. 当前状态

截至 2026-06-22：

- 当前目录已经执行过 `git init`。
- 还没有执行 `git add`、`git commit`、`git push`。
- 还没有配置远程仓库，`git remote -v` 为空。
- Git 全局配置来源为 `C:/Users/zewen Wan/.gitconfig`。
- 已配置：
  - `user.name=onlymrwan`
  - `user.email=1528153670@qq.com`
  - `init.defaultbranch=main`
  - `core.autocrlf=true`
  - `core.longpaths=true`
  - `core.editor=code --wait`
- GitHub CLI `gh` 当前不可用，PowerShell 提示 `gh` 未识别。

## 2. 本项目的 Git 边界

默认应纳入 Git：

- `README.md`
- `.gitignore`
- `.gitattributes`
- `docs/`
- `tasks/`
- `measurements/`
- `images/diagrams/`
- 经过筛选后需要保留的维修照片

默认不纳入 Git：

- `media/videos/`
- `archives/source_packages/*.zip`
- Office 临时文件，例如 `~$*.docx`
- 系统缓存、Python 缓存、LaTeX 编译中间文件

注意：维修照片和仪器内部结构可能属于敏感资料。如果未来创建 GitHub 远程仓库，默认建议先使用 private repository。

## 3. 每次修改后的固定检查

在 PowerShell 中执行：

```powershell
git status --short --ignored
```

含义：

- `??` 表示 Git 还没有跟踪的新文件。
- `M` 表示 Git 已跟踪文件发生修改。
- `!!` 表示被 `.gitignore` 忽略的文件。

本项目中看到 `!! archives/` 和 `!! media/` 是预期结果，因为视频和 zip 默认不进入 Git。

## 4. 第一次提交建议流程

第一次提交前先检查将要纳入 Git 的范围：

```powershell
git status --short --ignored
```

确认没有视频、zip、临时文件或敏感凭据后，再分批暂存。

建议第一批只暂存项目骨架和文本管理文件：

```powershell
git add .gitattributes .gitignore README.md docs tasks measurements
git status --short
```

检查暂存内容：

```powershell
git diff --cached --stat
```

如果统计结果合理，再提交：

```powershell
git commit -m "Initialize MFLI repair project structure"
```

提交后检查：

```powershell
git status --short --ignored
git log --oneline -1
```

## 5. 暂存与回退

如果 `git add` 后发现暂存错了，可以取消暂存，但不删除文件内容：

```powershell
git restore --staged <path>
```

例如：

```powershell
git restore --staged images/raw
```

不要使用 `git reset --hard` 或 `git clean -fd`，除非已经非常明确知道后果并且有备份。

## 6. GitHub 远程仓库计划

当前还没有远程仓库。后续若要推送到 GitHub，需要先确认：

1. 仓库名，例如 `zurich-mfli-input-repair`。
2. 仓库可见性：建议 `private`。
3. 是否上传维修照片和 Word/Excel/PDF。
4. 使用网页创建仓库，还是安装 GitHub CLI 后创建。
5. 远程地址使用 HTTPS 还是 SSH。

在确认前，不执行 `git remote add` 或 `git push`。

## 7. 常用命令速查

```powershell
git status --short --ignored
git diff
git diff --cached
git add <path>
git restore --staged <path>
git commit -m "message"
git log --oneline --decorate -5
git remote -v
```

## 8. 本项目 Git 维护子对话

长期子对话：

```text
MFLI维修-Git与工程维护
thread id: 019eed09-243e-7a70-8dfc-2da16fb59d91
```

用途：

- 给出适合 PowerShell 的 Git 命令。
- 帮助解释每一步 Git 操作的影响和回退方式。
- 提交前检查状态、暂存范围和忽略规则。
- 需要连接 GitHub 时，先辅助制定公开/私有和远程仓库方案。
