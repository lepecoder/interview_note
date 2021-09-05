提交pull request已经有很多教程了，这里讲如何处理别人提交的pull request。

如果没有冲突、不需要在本地检查，可以直接在网页端合并请求。

![Git处理pullrequest.png](assets/20201214211839-wwk7yf5-Git处理pull request.png)

如果想要在本地debug那就需要pull到本地，一般有如下流程。

```Bash
git checkout -b pr_10 master  # 从master分支新建出一个分支
# 获取对方fork的远程仓库的master分支
git pull https://gitee.com/user/project.git master 
# 也可以直接pull本次的pr
git pull https://gitee.com/you_name/you_project.git pull/10/head:pr_10
git checkout master  # 切换回master分支
git merge pr_10  # 合并pr_10
git push origin master  # 推送的远程仓库
```

除此之外，我们还想准确看到对方本次pr改了什么，虽然在Web端也可以看到，但总是不如IDE里方便。

![Git处理pullrequest1.png](assets/20201214211901-4p4w7y7-Git处理pull request_1.png)

在pycharm里可以安装Git插件，右键master之后show diff，

![Git处理pullrequest2.png](assets/20201214211911-izswxmq-Git处理pull request_2.png)

之后就可以看到pr_10分支和master分支的差异，蓝色是修改过的文件，灰色是新增的文件。

![Git处理pullrequest3.png](assets/20201214211921-k9duwzn-Git处理pull request_3.png)