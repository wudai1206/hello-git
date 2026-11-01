# Git & GitHub 日常使用速查

> 配套仓库：https://github.com/wudai1206/hello-git
> 环境：Windows + Git 2.56（安装路径 `C:\Program Files\Git`）+ 已配好的 ed25519 SSH 密钥

---

## 一、先记住这张图

```
工作区(你改的文件)  --git add-->  暂存区(准备提交)  --git commit-->  本地仓库(快照)  --git push-->  GitHub(云端)
                                                                          ↑                          |
                                                                          └-------- git pull --------┘
```

四个地方，四条命令，就是 Git 的全部日常。

---

## 二、每天开工的 5 步

```bash
cd C:\Users\13305\Desktop\hello-git

git pull                     # 1. 先拉：别人可能改过了
git status                   # 2. 看：改了哪些文件（红色=没暂存，绿色=已暂存）

# 3. 干活……改文件……

git add .                    # 4. 暂存全部改动（只想提交某个文件就写 git add 文件名）
git commit -m "feat: 增加了登录页"   # 5. 拍快照（说清"做了什么"）
git push                     # 6. 推上 GitHub（SSH 免密，不输密码）
```

**口诀：先拉 → 改 → 加 → 提交 → 推。**

---

## 三、场景速查：我想……

| 我想… | 命令 |
|---|---|
| 看改了什么（还没暂存） | `git diff` |
| 看改了什么（已暂存） | `git diff --staged` |
| 看状态 / 当前分支 | `git status` / `git branch` |
| 看历史（一行一条） | `git log --oneline` |
| 看带分支图的历史 | `git log --oneline --graph --all` |
| 看某次提交改了什么 | `git show 提交号` |
| 开新分支干活 | `git switch -c feature/login` |
| 切回主分支 | `git switch main` |
| 列出所有分支（含远程） | `git branch -a` |
| 删掉已合并的分支 | `git branch -d feature/login` |
| 删远程分支 | `git push origin --delete feature/login` |
| 把别人的改动拉下来 | `git pull` |
| 只拉不改工作区 | `git fetch` + `git log origin/main` |
| 临时藏起改动去干别的 | `git stash` → 回来 `git stash pop` |

---

## 四、撤销专题（最容易踩坑，按"改到哪一步"对症下药）

| 情况 | 命令 | 危险度 |
|---|---|---|
| 文件改乱了，还没 add，想还原 | `git restore 文件名` | 安全（丢掉你的修改） |
| 已 `git add`，想取消暂存（不丢改动） | `git restore --staged 文件名` | 安全 |
| 刚 commit 了，但信息写错 / 漏了文件 | `git commit --amend` | 安全（只在没 push 时用） |
| commit 想整个撤掉，改动留在工作区 | `git reset --soft HEAD~1` | 较安全 |
| commit 和改动都不要了 | `git reset --hard HEAD~1` | ⚠️ 危险，改动真没了 |
| 已经 push 了，想撤销这次提交 | `git revert 提交号`（产生一次反向提交，再 push） | 安全（推荐给已推送的情况） |

> 铁律：**已经 push 到公共分支的历史，不要用 `reset --hard` 硬改**，用 `revert`。

---

## 五、冲突怎么解决

`git pull` 或 `git merge` 时报 `CONFLICT`：

```bash
git status                  # 看哪些文件冲突（both modified）
```

打开冲突文件，会看到：

```
<<<<<<< HEAD
你本地的内容
=======
远端/别人的内容
>>>>>>> origin/main
```

1. 手动编辑成最终想要的样子，**把 `<<<<<<<`、`=======`、`>>>>>>>` 三行标记全删掉**；
2. `git add 冲突文件`
3. `git commit`（信息默认即可）
4. `git push`

怕搞坏就 `git merge --abort` 放弃这次合并。

---

## 六、GitHub 网页端各区块是干什么的

| 区域 | 用途 |
|---|---|
| **Code** | 看文件、复制 clone 地址、下载 ZIP、切换分支 |
| **Issues** | 记待办、提 bug、讨论需求 |
| **Pull requests** | 提代码改动请求，走 review 再合并（协作核心） |
| **Actions** | 自动化（CI：每次 push 自动跑测试/构建） |
| **Projects** | 看板式任务管理 |
| **Wiki** | 项目文档 |
| **Settings** | 改仓库名、可见性、协作者、分支保护、删除仓库 |
| **个人 Settings → SSH and GPG keys** | 管理这台电脑的免密钥匙 |

---

## 七、和别人协作的标准姿势（给别人的项目提改动）

```bash
# 1. 网页上对别人的仓库点 Fork（复制一份到你账号下）
git clone git@github.com:你的用户名/某项目.git
cd 某项目

# 2. 开分支改，别直接改 main
git switch -c fix/typo

# 3. 改完提交推送
git add . && git commit -m "fix: 修正文档错别字"
git push -u origin fix/typo

# 4. 网页上点 Compare & pull request → 写清改了什么 → Create pull request
# 5. 维护者合并后，同步上游：
git remote add upstream git@github.com:原作者/某项目.git
git fetch upstream
git switch main && git merge upstream/main
```

---

## 八、`.gitignore`：这些东西永远别提交

在仓库根目录建 `.gitignore`：

```gitignore
# 依赖
node_modules/
venv/

# 编译产物
*.exe
*.class
target/
dist/

# 环境变量与密钥（最重要！）
.env
*.pem
*.key
id_rsa*

# 系统/编辑器杂项
.DS_Store
Thumbs.db
.idea/
.vscode/
```

提交前用 `git status` 扫一眼，别把密码、密钥推上公开仓库。

---

## 九、常见报错对照

| 报错 | 原因 | 解决 |
|---|---|---|
| `Permission denied (publickey)` | SSH 密钥没配好 / 没加到 GitHub | `ssh -T git@github.com` 自测；没通过就重新加公钥 |
| `Updates were rejected / non-fast-forward` | 远端有你本地没有的提交 | 先 `git pull --rebase`，再 `git push` |
| `refusing to merge unrelated histories` | 两个仓库历史不相干（本地 init 后接远程） | `git pull origin main --allow-unrelated-histories` |
| `fatal: not a git repository` | 当前目录不在仓库里 | `cd` 到仓库目录，或 `git init` |
| `src refspec main does not match any` | 还没有任何提交 / 分支名不对 | 先 `git add` + `git commit` |
| `error: Your local changes would be overwritten` | 拉取会覆盖你未提交的改动 | 先 `git stash`，pull 完 `git stash pop` |
| 网页打不开 / 超时 | 网络问题 | F5 多等 20 秒，或开 Watt Toolkit 加速；`git push` 走 SSH 通常不受影响 |

---

## 十、下一步练什么

1. 在 `hello-git` 里造一次**冲突**：两个分支改同一行 → 合并 → 手动解决。
2. 用 `git revert` 撤销一次已推送的提交，观察历史和 `reset` 的区别。
3. 找个小开源项目 Fork，改个文档错别字，走一遍真 PR。
4. 学 `git rebase -i`（整理提交历史），进阶再碰。
