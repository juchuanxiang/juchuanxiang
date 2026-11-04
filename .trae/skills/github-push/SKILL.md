---
name: github-push
description: 将本项目（高级网页设计课堂实践）提交 commit 并推送到 GitHub 仓库 juchuanxiang/juchuanxiang，使用本机已配置的 SSH 公钥。当用户说「提交到github」「commit 并 push」「推送代码到远程仓库」时使用。
---

# 提交到 GitHub

## 目标仓库

- SSH 地址：`git@github.com:juchuanxiang/juchuanxiang.git`
- 网页地址：`https://github.com/juchuanxiang/juchuanxiang`
- 使用本机密钥 `~/.ssh/id_ed25519`（公钥已在 GitHub 账户中登记）

## 环境前提

- Git 安装路径（Windows）：`C:\Program Files\Git\cmd\git.exe`
- 若当前终端 PATH 未刷新，先执行：
  `$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")`
- GitHub 的 22 端口在部分网络下会被拒绝，此时通过 443 端口连接：

```text
Host github.com
  HostName ssh.github.com
  Port 443
  User git
  IdentityFile ~/.ssh/id_ed25519
```

写入 `~/.ssh/config` 后用 `ssh -T git@github.com` 验证，出现
`Hi <user>! You've successfully authenticated` 即成功。

## 关键约束

1. **嵌套仓库**：`the-best-ever/` 是从 Gitee 克隆的独立仓库。若要把它作为普通目录提交，
   先把 `the-best-ever/.git` 重命名为 `.git_backup`（保留备份，不删除）。
2. **忽略产物**：`.trae-html-share-packages/` 与 `.screenshots/` 属于工具生成的中间产物，
   已写入 `.gitignore`，不提交。
3. 仓库初始状态可能为 `No commits yet on master`，首次推送使用 `git push -u origin master`。
4. 提交前必须检查 `git status`，只提交与课堂实践相关的内容。

## 执行步骤

```powershell
# 1. 进入项目根目录
Set-Location "c:\Users\juchu\Desktop\高级网页设计"

# 2. 关联远程（已存在则先 remote set-url）
git remote add origin git@github.com:juchuanxiang/juchuanxiang.git

# 3. 暂存并提交
git add -A
git commit -m "feat: 新增图标字体与 SVG 实践页面"

# 4. 首次推送并建立跟踪
git push -u origin master
```

## 验证

- `git log --oneline -3` 能看到本次提交
- `git ls-remote origin` 能看到远端分支与提交号
- 浏览器打开仓库地址确认文件已上传

## 常见问题

| 现象 | 处理 |
| --- | --- |
| `Connection refused`（端口 22） | 按上文改用 `ssh.github.com:443` |
| `Permission denied (publickey)` | 确认 `~/.ssh/id_ed25519.pub` 已添加到 GitHub 账户 |
| `src refspec master does not match any` | 说明还没有提交，先执行 `git commit` |
| 提示 `adding embedded git repository` | `the-best-ever/.git` 尚未重命名 |
