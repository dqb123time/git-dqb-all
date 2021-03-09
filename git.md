`git push` 命令使用两个参数：

- 远程命令，如 `origin`
- 分支名称，如 `main`

例如：

```shell
git push  <REMOTENAME> <BRANCHNAME> 
```

例如，您通常运行 `git push origin main` 来推送本地更改到在线仓库。



### [重命名分支](https://docs.github.com/cn/github/using-git/pushing-commits-to-a-remote-repository#renaming-branches)

要重命名分支，同样使用 `git push` 命令，但要加上一个或多个参数：新分支的名称。 例如：

```shell
git push  <REMOTENAME> <LOCALBRANCHNAME>:<REMOTEBRANCHNAME> 
```

这会将 `LOCALBRANCHNAME` 推送到 `REMOTENAME`，但其名称将改为 `REMOTEBRANCHNAME`。



### [删除远程分支或标记](https://docs.github.com/cn/github/using-git/pushing-commits-to-a-remote-repository#deleting-a-remote-branch-or-tag)

删除分支的语法初看有点神秘：

```shell
git push  <REMOTENAME> :<BRANCHNAME> 
```

请注意，冒号前有一个空格。 命令与重命名分支的步骤类似。 但这里是告诉 Git *不要*推送任何内容到 `REMOTENAME` 上的 `BRANCHNAME`。 因此，`git push` 会删除远程仓库上的分支。





### 创建本地分支

git branch 分支名

例如：git branch dev，这条命令是基于当前分支创建的本地分支，假设当前分支是master(远程分支)，则是基于master分支创建的本地分支dev。

### 切换到本地分支

git checkout 分支名

例如：git checkout dev，这条命令表示从当前master分支切换到dev分支。

### 创建本地分支并切换

git checkout -b 分支名
例如：git checkout -b dev，这条命令把创建本地分支和切换到该分支的功能结合起来了，即基于当前分支master创建本地分支dev并切换到该分支下。

### 提交本地分支到远程仓库

git push origin 本地分支名
例如：git push origin dev，这条命令表示把本地dev分支提交到远程仓库，即创建了远程分支dev。

注：要想和其他人分享某个本地分支，你需要把它推送到一个你拥有写权限的远程仓库。你创建的本地分支不会因为你的写入操作而被自动同步到你引入的远程服务器上，你需要明确地执行推送分支的操作。换句话说，对于无意分享的分支，你尽管保留为私人分支好了，而只推送那些协同工作要用到的特性分支。

原文链接：https://blog.csdn.net/huangjw_806/article/details/78297851



### 新建本地分支与远程分支关联

git branch –set-upstream 本地新建分支名 origin/远程分支名

例如：git branch –set-upstream dev origin/dev，把本地dev分支和远程dev分支相关联。

注：本地新建分支， push到远程服务器上之后，使用git pull或者git pull 拉取或提交数据时会报错，必须使用命令：git pull origin dev（指定远程分支）；如果想直接使用git pull或git push拉去提交数据就必须创建本地分支与远程分支的关联。
————————————————
原文链接：https://blog.csdn.net/huangjw_806/article/details/78297851



#### 让本地的local分支跟踪远程的local分支

具体方法
`git branch --set-upstream-to=远程分支 本地分支`
具体示例
`git branch --set-upstream-to=origin/local local`

#### 查看本地分支和远程分支的对应情况

```
git branch -vv 注意是两个v, 不是一个w!
```

#### 细节

对于github上的新仓库, 得先用git push -u origin master这种方式指定上游并提交一次后, 才能使用git branch --set-upstream-to=origin/master master
**试错过程:**
github上创建一个新仓库, 将本地仓库的master分支提交到远程新仓库,
我先用git remote add 添加远程仓库, 此时不能用git branch --set-upstream-to=origin/master master, 会报错,如下图所示, 它提示我先使用git push -u
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191018170332778.png)
所以得先用git push -u origin master这种方式指定上游并提交一次后, 才能使用git branch --set-upstream-to=origin/master master

### git 根据tag创建分支

在项目中我们需要根据tag创建分支.现将创建步骤总结一下.假设在你的主分支上有一个tag为v1.0,主分支的名字为master.

1.执行:git origin fetch 获得最新.

2.通过:git branch <new-branch-name> <tag-name> 会根据tag创建新的分支.

例如:git branch newbranch v1.0 . 会以tag v1.0创建新的分支newbranch;

3.可以通过git checkout newbranch 切换到新的分支.

4.通过 git push origin newbranch 把本地创建的分支提交到远程仓库.

现在远程仓库也会有新创建的分支啦.
————————————————

原文链接：https://blog.csdn.net/lhcxwjh/article/details/51083249



### git rebase -i

```
$ git rebase -i [startpoint] [endpoint] 

// 注意：这里的区间是一个前开后闭的区间。
```

![img](https://img2018.cnblogs.com/blog/737276/201903/737276-20190313224133023-864699101.png)

 ps: Commands 说明，以下单字符命令为简写命令。

- p, pick: 保留该 commit。
- r, reword: 保留该 commit，可以修改 commit 的注释。
- e, eidt: 保留该 commit，但停下来修改该 commit （不仅仅是注释），可以用来解决 merge 冲突。
- s, squash: 将该 commit 和 前面一个 commit 合并。
- f, fixup: 将该 commit 和 前面一个 commit 合并，但不保留该提交的注释信息。
- x, exec: 执行 shell 命令。
- d, drop: 丢弃该 commit。



### git 修改commit注释

不过在git中，其commit提供了一个--amend参数，可以修改最后一次提交的信息.但是如果你已经push过了，那么其历史最后一次，永远也不能修改了。 
 我使用git commit --amend已经push过的

```markdown
# git commit --amend
```

然后在出来的编辑界面，直接编辑注释的信息,保存退出



 git使用amend选项提供了最后一次commit的反悔。但是对于历史提交呢，就必须使用rebase了。

​    git rebase -i HEAD~3

​    表示要修改当前版本的倒数第三次状态。

​    这个命令出来之后，会出来三行东东：

​    pick:*******

​    pick:*******

​    pick:*******

​    如果你要修改哪个，就把那行的pick改成edit，然后保存退出。

​    这时通过git log你可以发现，git的最后一次提交已经变成你选的那个了，这时再使用：

​    git commit --amend

​    来对commit进行修改。

修改完了之后，要回来对不对？

​    使用git rebase --continue

​    OK，一切都搞定了。



git常用命令https://juejin.cn/post/6844903933350002701