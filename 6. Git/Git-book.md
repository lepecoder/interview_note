# 分支

```Bash
# 创建分支
git branch b1
# 切换分支
git checkout b1
# 创建并切换
git checkout -b b1
# 展示所有分支
git branch -a
# 删除远程分支 remotes/origin/dev
git push origin --delete dev
# 删除本地分支
git branch -d dev # 此命令不能在dev分支中运行
```

## 从版本控制中删除

```bash
git rm file --cached  # 从版本控制中删除文件，保留物理文件
git rm -r dir --cached  # 从版本控制中删除文件夹
git rm -rf file/dir --cached # 从版本控制和本地都删除
删除完成后记得添加到.gitignore
commit后push才会在远程生效
```