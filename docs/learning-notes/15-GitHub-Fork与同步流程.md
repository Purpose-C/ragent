# GitHub Fork 与同步流程

## 是否要放到自己的仓库

建议先 Fork 到自己的 GitHub，再在个人分支保存学习笔记和改造。Fork 保留与原项目的关系，便于同步和提交 Pull Request；“重新上传”会丢失这种关系和原提交历史语义。

当前本地仓库已配置：`origin` 为 `git@github.com:Purpose-C/ragent.git`，`upstream` 为 `git@github.com:nageoffer/ragent.git`，这已经是推荐结构。

- `origin`：你默认推送的个人 Fork。
- `upstream`：原作者仓库，只用于获取更新，通常不直接推送。

## 推荐方式：先 Fork 再克隆

```bash
git clone git@github.com:你的用户名/ragent.git
cd ragent
git remote add upstream git@github.com:nageoffer/ragent.git
git remote -v
```

## 已经 clone 原仓库

```bash
git remote rename origin upstream
git remote add origin git@github.com:你的用户名/ragent.git
git push -u origin main
```

## 同步原仓库

```bash
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

若团队要求线性历史，可在理解 rebase 后使用 rebase；初学阶段优先 merge，更容易看清发生了什么。

## 新建学习分支

```bash
git checkout main
git pull origin main
git checkout -b learn/day01-notes
```

```bash
git add learning-notes/
git commit -m "docs: add ragent learning notes"
git push -u origin learn/day01-notes
```

本次任务明确禁止 commit/push，因此以上仅是后续学习命令，本次没有执行。

## 分支建议

- `main`：尽量跟随 upstream，不直接做实验。
- `learn/notes-*`：学习笔记。
- `feat/<name>`：小改造代码。
- 一个分支只处理一个主题，降低冲突。

## 减少和解决冲突

同步前先提交或暂存自己的工作；频繁从 upstream 合并；不要大范围格式化；文档和代码分开提交。冲突时打开标记文件，理解 `<<<<<<<` 两侧内容，手工合并后运行测试，再 `git add` 和继续 merge。不要为了“消除冲突”直接删除不理解的一侧。

## 不应提交

- API Key、Token、数据库生产密码、私有地址。
- `.env`、IDE 临时文件、构建产物、日志。
- 上传的真实业务文档和用户数据。
- 与当前任务无关的大量格式化改动。

## 本章复习问题

1. origin 与 upstream 的角色是什么？
2. Fork 相比重新上传保留了什么？
3. 为什么 main 不适合作为日常实验分支？

## 下一步建议

每次开始学习前运行 `git status` 和 `git remote -v`，确认分支、未提交改动和远端方向后再操作。
