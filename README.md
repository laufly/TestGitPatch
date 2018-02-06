# TestGitPatch

此项目用来测试 git 打 patch 和恢复 patch

博客

个人理解android开发接触到的patch，一般可以分为两种，

一种是UNIX下的patch，可以使用git diff或者其他UNIX的diff命令生成（本人只用过git diff命令，后面的看书有提到过但是自己木有用过），

一种是git 工具生成的patch，也可以说是git专用的patch，使用git format-patch命令生成。先来看看git diff生成patch的过程。

一般常用命令如下：
        git diff  > patch
        git diff  --cached > patch
        git diff  branchname --cached > patch
        
这个时候当前目录下就会有一个patch文件，这是一个非git环境也可以使用的patch。对于这种patch，在git上使用要用git apply命令，如下：

        git apply patch
        
由于这是一个类似UNIX下更新文件的操作，所以执行完上述操作之后，实际上是等于手动修改了文件，还要做一些git commit之类的操作。

另外请注意，git apply 是一个事务性操作的命令，也就是说，要么所有补丁都打上去，要么全部放弃。

git工具上说明在实际打补丁之前，可以先用git apply --check 查看补丁是否能够干净顺利地应用到当前分支中：

git apply --check patch，如果执行完该命令之后没有任何输出，表示我们可以顺利采纳该补丁，接下来就是git上的提交了。

在接着说说git format-patch生成的补丁，这是git专用的，也是日常工作中最常接触到的补丁类型。常用命令如下：

        1)两个节点之间的提交： git format-patch  节点A   节点B
        
        2)单个节点： git format-patch -1 节点A （-n就表示要生成几个节点的提交）
        
        3)最近一次提交节点的patch ：git format-patch HEAD^ 依次类推……
        
使用git format-patch命令生成的patch文件，包含了提交的附加信息：比如作者，时间等。

再次基础上使用git am命令即可将此补丁应用到当前分支。

注意应用完之后，你会发现当前分支多了一次提交记录，并且有完整的信息，而不是简单的修改文件。


在对比一下，git diff 和git format-patch生成的patch一个重要不同之处，实际使用中会发现git diff一次只会生成一个patch文件，

不管差别了多少个提交，都是一个；而git format-patch是根据提交的节点来的，一个节点一个patch。

应用patch：

先检查patch文件：# git apply --stat newpatch.patch

检查能否应用成功：# git apply --check  newpatch.patch

打补丁：# git am --signoff < newpatch.patch



思路:

在 test1 分之修改内容,然后执行 
```
git add . 
git commit -m "a test text file"
git log   # 用来开看要打patch的提交标识

git format-patch -s xxxx(commit 提交标识)
```
生成patch文件,切换到master分之

执行以下代码:
```
git apply 0001-a-test-text-file.patch

```
出现添加的text文件

期间因为是先创建的text文件然后切换的test1分之,所以切回master把text文件删除,然后通过应用 patch 返回来