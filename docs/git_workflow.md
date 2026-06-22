# Git/GitHub 学习与维护流程

本文档用于把本工程作为 Git/GitHub 实践案例。目标不是一次性学完 Git，而是在真实维修项目中逐步掌握最小、安全、可回退的工作流。

## 1. 当前状态

截至 2026-06-22 主对话与 Git 子对话同步后：

- 当前目录已经执行过 `git init`。
- 远程仓库已经配置：
  - `origin = https://github.com/onlymrwan/MFLI-Fix.git`
- 当前分支为 `main`，并跟踪 `origin/main`。
- 当前本地与远端已对齐：
  - `HEAD -> main`
  - `origin/main`
  - `origin/HEAD`
  - latest commit: `624f2eb Add repair image records`
- 当前 `git status --short --branch --ignored` 仅显示：
  - `!! archives/`
  - `!! media/`
- 这表示工作区没有未提交修改，`archives/` 和 `media/` 被忽略是预期行为。
- Git 全局配置来源为 `C:/Users/zewen Wan/.gitconfig`。
- 已配置：
  - `user.name=onlymrwan`
  - `user.email=1528153670@qq.com`
  - `init.defaultbranch=main`
  - `core.autocrlf=true`
  - `core.longpaths=true`
  - `core.editor=code --wait`
- GitHub CLI `gh` 当前不可用，PowerShell 提示 `gh` 未识别；本项目目前通过 Git HTTPS 远程地址完成推送。

已完成的关键 Git 节点：

```text
a956f70 Initial commit
d67d07f Initialize MFLI repair project structure and records
7c676b8 Resolve merge conflict in README by keeping local version
624f2eb Add repair image records
```

备注：曾出现 `Failed to write item to store [0x8]` / `内存资源不足` 的凭据写入提示，但后续推送实际成功。若后续反复出现，再单独处理 Git 凭据管理。

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

## 4. 日常版本同步流程

本项目已经完成第一次提交和远程推送，后续进入日常同步阶段。

每次开始工作前先确认状态：

```powershell
git status --short --branch --ignored
git log --oneline --decorate -3
```

含义：

- 如果显示 `## main...origin/main` 且没有 `M`、`??`，说明本地与远端对齐且无待提交修改。
- 如果显示 `M <file>`，说明已有跟踪文件被修改。
- 如果显示 `?? <file>`，说明有新文件尚未纳入 Git。
- 如果显示 `!! archives/`、`!! media/`，这是预期忽略项。

### 4.1 小步提交原则

建议按任务类型小步提交，不把测量记录、文档修改、图片整理和 Git 规则修改混成一个大提交。

推荐提交粒度：

| 场景 | 建议提交信息 |
|---|---|
| 新增测量结果 | `Record AD9269 OR and rail measurements` |
| 更新维修判断 | `Update MFLI repair status after OR measurements` |
| 新增或整理照片 | `Add repair image records` |
| 更新任务计划 | `Update first-round measurement checklist` |
| 更新 Git/项目管理文档 | `Update project coordination workflow` |

### 4.2 提交流程

每次提交按以下顺序：

```powershell
git status --short --branch --ignored
git diff
```

确认修改范围合理后，按文件或目录分批暂存：

```powershell
git add <path>
```

检查暂存内容：

```powershell
git diff --cached --stat
git diff --cached --name-only
```

确认无误后提交并推送：

```powershell
git commit -m "短句描述本次修改"
git push
```

推送后确认：

```powershell
git status --short --branch --ignored
git log --oneline --decorate -3
```

看到 `HEAD -> main, origin/main` 指向同一个最新提交，表示本地和 GitHub 已同步。

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

当前远程仓库已经建立：

```text
origin https://github.com/onlymrwan/MFLI-Fix.git
```

后续 GitHub 同步策略：

1. 本地先完成修改和验证。
2. 主对话或 Git 子对话检查 `git status` 和 `git diff`。
3. 按任务类型分批 `git add`。
4. 用 `git diff --cached --stat` 检查暂存范围。
5. `git commit`。
6. `git push` 到 `origin/main`。

公开风险提醒：

- 如果仓库是 private，维修照片和仪器内部结构资料风险较低，但仍应避免上传密钥、账号、个人隐私和未确认可共享的商业资料。
- 如果未来改为 public，需要先审查 `images/`、`docs/reports/`、`measurements/` 和 PDF/Word 文件是否适合公开。

不建议在当前阶段引入复杂分支流。除非出现多人协作或高风险大改动，默认只维护 `main`。

## 7. 主对话与 Git 子对话的版本同步方案

### 7.1 分工

主对话：

- 决定哪些维修结论、测量结果和项目管理规则写入文件。
- 决定是否提交、何时推送。
- 对涉及敏感资料或公开风险的上传做最终确认。

Git 子对话：

- 解释 Git 命令。
- 检查状态、diff、暂存范围和远端同步状态。
- 给出 PowerShell 命令和安全回退方式。
- 处理冲突、non-fast-forward、凭据提示等 Git 问题。
- 在主对话确认变更范围后，直接执行 `git add`、`git commit`、`git push`，用户不需要手动操作 Git 或 PowerShell。

执行边界：

- 主对话负责确认本次提交包含哪些文件。
- Git 子对话负责执行命令并回报结果。
- 用户无需逐条复制命令，除非认证、权限或网络问题必须人工介入。
- 仍禁止 `git reset --hard`、`git clean -fd`、强制推送和未确认的敏感资料上传。

### 7.2 每次同步的交接格式

Git 子对话向主对话同步时使用：

```text
当前分支：
本地/远端关系：
未提交修改：
已忽略项目：
最近提交：
是否需要提交：
是否需要 push：
风险提醒：
建议命令：
```

主对话收到后，应把关键状态更新到本文件或 `docs/project_coordination.md`，但不需要每次都记录完整命令输出。

### 7.3 日常节奏

建议采用“三段式”：

1. 开始任务前：`git status --short --branch --ignored`
2. 写入文件后：`git diff` 和必要的内容检查
3. 任务完成后：提交并 `git push`

对于维修测量项目，建议每完成一个可复核节点就提交一次：

- 完成一轮测量计划：提交。
- 写入一组实测数据：提交。
- 更新一次故障判断：提交。
- 上传一批照片或标注图：提交。

这样每个提交都能对应一个科研或维修动作，便于以后回溯。

## 8. 常用命令速查

```powershell
git status --short --branch --ignored
git diff
git diff --cached
git add <path>
git restore --staged <path>
git commit -m "message"
git push
git log --oneline --decorate -5
git remote -v
```

## 9. 本项目 Git 维护子对话

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
