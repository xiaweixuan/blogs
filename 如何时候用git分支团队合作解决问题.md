## git分支的理解
git之所有受广大开发人员欢迎，其原因之一就是因为他强大的分支功能，那么分支到底是怎样一个功能呢，接下来向大家介绍。

首先大家可以了解一下[git的版本回溯](https://blog.csdn.net/just_a_bad_guy/article/details/100970678)这一功能，在此基础上，接下来我们来说分支。

我们常常使用的master就是一个默认分支，只不过我们每次在创建的时候没有改名字，都使用‘master’这个默认的名字。大家可以以平行宇宙的概念去理解分支，刚刚进入了分支，他的东西和主干的上的东西是完全一样的，但是你在分支中的操作却完全不会影响主干的东西。直到某一刻，你把分支合并到主干中，这样主干就会和分支的内容完全相同了。所以，分支常常是我们探索未知的好途径。
分支是如何实现的呢
比如我们发布一个项目，项目要不断的优化，我们将它上传到github上面，每提交一次，github都会记录下来你的提交，例如我们最早开发了版本v1，之后完善了一下再次提交称为v1.1版本，再完善、上传作为v1.2版本，每次上传后，git所呈现给我们的都是最新的，但其实，每一次上传的记录都被保存，我们随时可以调出来看，只不过git默认把最新一次展现给我们。我们将上述描述画成图像来呈现给大家：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190918110028577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p1c3RfYV9iYWRfZ3V5,size_16,color_FFFFFF,t_70)
这个时候我们先要开发v2版本了，但是github是开源社区，任何人都可以下载你的代码，如果这个时候别人在使用你的代码，你如果将为完成的代码上传就会造成别人用了你错误的代码。那么怎么办呢，可以用git的分支解决。
别人会默认下载你主分支上的代码，所以你可以创建一个分支去修改你的代码，当代码彻底修改好以后，在合并到主分支。这样在你眼中，代码是在不断修改的，但在其他人眼中，只有每次修改完善后的代码。
所以我们创建一个分支比如叫foo，我们在他的上面提交，直至修改完成。![在这里插入图片描述](https://img-blog.csdnimg.cn/20190918110947204.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p1c3RfYV9iYWRfZ3V5,size_16,color_FFFFFF,t_70)
这个时候我们可以发现，每个版本依旧会存储在github上面，只不过每个分支的指向不同，master分支始终指向v1.2版本，但是foo已经指向了版本v2.2，这个时候修改完后，我们在将foo合并到master，这样master呈现的也变成了v2.2![在这里插入图片描述](https://img-blog.csdnimg.cn/2019091811124775.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p1c3RfYV9iYWRfZ3V5,size_16,color_FFFFFF,t_70)
这个时候再把foo删除，我们就可以再不影响他人使用的情况下，完成了项目的修改与上传。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190918111355131.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p1c3RfYV9iYWRfZ3V5,size_16,color_FFFFFF,t_70)


## 团队如何使用git分支开发项目
git有四种常见的工作模式，也就是四种工作流。他们分别是==集中式工作流==，==功能分支工作流==，==gitflow工作流==，==forking工作流==。

其中==功能分支工作流==是在==集中式工作流==基础上加以丰富，==gitflow工作流==又是在==功能分支工作流==基础上加以丰富的，前三种多用于某一集体的一个项目，而==forking工作流==多用于开源社区上的项目研发。所以，在本章文章中，主要详细介绍一下==集中式工作流==，和==功能分支工作流==的流程。（gitflow和forking工作流笔者会在后续文章中说明）
###### 集中式工作流
集中式工作流比较简单，他只有一条分支。开始，队伍的所有成员将远程库clone下载到本地，假设有p1和p2两个人分别是队中的成员，之后他们分别对库中的文件作出了更改，如图：

![分支描述](https://img-blog.csdnimg.cn/20190915183848840.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p1c3RfYV9iYWRfZ3V5,size_16,color_FFFFFF,t_70)
假设p1对项目增加了一个文件a.txt，然后push到了远程仓库,p2也对其更改了两回。第一次增加了文件b.txt，第二次增加了c.txt。当p2想要push到远程仓库的时候，是会报错的，因为远程仓库现在的内容已经和你刚刚clone下来的内容不一样了（因为p1对其提交了a.txt），这个时候如果你能提交上去，那将会覆盖p1的提交。那么这个时候我们要先将远程上的东西git pull下来，再进行git push。如果中途出现vim文档可直接输入`:wq`退出就好。

当然如果说p1和p2下载到本地后修改的是同一个文件，那这个时候就是不单纯的git pull能解决的了，那个我们还在稍后的冲突问题中讲解
###### 功能分支工作流
这个是在前一个工作流的基础上的，功能分支工作流是将整个项目放在主分支上，每当你想要为你的项目增加一个功能，那么开出一个分支，在分支上去做，等功能写好以后，在合并到主分支上。在这个过程中在功能分支上做任何事情是不会干扰到主分支的，与此同时其他人可以同时更改项目的其他内容而互不干扰。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190918095424100.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p1c3RfYV9iYWRfZ3V5,size_16,color_FFFFFF,t_70)
接下来展示一下具体的工作流程。
1）每个人下载全部的远程仓库
2）下载后在本地创建一个功能分支，将分支提交到远程
3）修改分支内容，提交
4）将分支内容合并到主分支
## git分支的操作指令
以功能分支工作流为例，给大家介绍一下全程的指令。
第一步将远程仓库的库克隆大本地
`````git
$ git clone https://github.com/xiaweixuan/test.git
Cloning into 'test'...
remote: Enumerating objects: 6, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 6 (delta 0), reused 6 (delta 0), pack-reused 0
Unpacking objects: 100% (6/6), done.

`````
下载后可以查看一下文件，这时候用linux的命令进入文件并查看就可以，也可以直接在文件夹中点击

```git
$ cd test
$ ls
a.txt  b.txt
```
可以看到有a.txt b.txt两个文件，假设这就是我们的一个项目。记下来我们要对项目进行操作，要注意的是如果你没有使用上述命令进入test文件夹，那么你要关掉git bash窗口，去test文件夹里重新git bash here

假设现在的项目需要我们添加一个新功能，因为新功能我不知道写完能不能用，如果在原先的代码上写，写完后发现写的不对，那么原来的代码就相当于也被毁了，所以这个时候我们创建分支
```git
$ git branch foo
$ git checkout foo
Switched to branch 'foo'
```
上面分别是创建分支foo和进入分支foo，可以用git branch查看当前分支
```git
$ git branch
* foo
  master
```
上图显示了本库所有分分支，并在当前分支上用*标记上了。
现在我们所在的就是foo分支了，也就可以大胆的去写代码了。我们在b.txt文件中加上几句话表示我们写了一个新功能，这个时候我们可能中途有事，需要把这个分支保存并上传到远程仓库，以免代码丢失。
```git
$ git add .

$ git commit -m 'foo分支的第一次上传'
[foo 6709aa1] foo分支的第一次上传
 1 file changed, 4 insertions(+), 1 deletion(-)

$ git push -u origin foo
Fatal: HttpRequestException encountered.
   ▒▒▒▒▒▒▒▒ʱ▒▒▒▒
git: 'credential-cache' is not a git command. See 'git --help'.
Username for 'https://github.com': xiaweixuan
git: 'credential-cache' is not a git command. See 'git --help'.
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 311 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
remote:
remote: Create a pull request for 'foo' on GitHub by visiting:
remote:      https://github.com/xiaweixuan/test/pull/new/foo
remote:
To https://github.com/xiaweixuan/test.git
 * [new branch]      foo -> foo
Branch foo set up to track remote branch foo from origin.

```
这样就把你在本地的分支上传到了远程仓库，可以再github上查看。
接下来我们可以继续修改本地项目，比如再加一个c.txt，然后上传。
```git
$ git add .

$ git commit -m '再一次修改'
[foo c05004a] 再一次修改
 1 file changed, 1 insertion(+)
 create mode 100644 c.txt

$ git push
Fatal: HttpRequestException encountered.
   ▒▒▒▒▒▒▒▒ʱ▒▒▒▒
git: 'credential-cache' is not a git command. See 'git --help'.
Username for 'https://github.com': xiaweixuan
git: 'credential-cache' is not a git command. See 'git --help'.
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 327 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/xiaweixuan/test.git
   6709aa1..c05004a  foo -> foo

```
终于我们的项目写完了，并且没有任何错误，那么现在面临的问题就是将foo分支合并到主分支，然后删除foo分支。
因为github是多人合作平台，合并后，主分支将会被分支内容覆盖，所以要谨慎，必须经过小组中所有人批准
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190918093844236.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p1c3RfYV9iYWRfZ3V5,size_16,color_FFFFFF,t_70)
点击foo分支后面的 Compare&pull request 的绿色按钮，然后出现下图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190918094013200.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p1c3RfYV9iYWRfZ3V5,size_16,color_FFFFFF,t_70)
在里面写上对本次分支的描述，然后点击 create pull request
接下来就是等待其他人的同意，同意后就会自动将foo分支合并到主分支上。
然后再github上删除foo分支。
然后在本地切换到master删除foo分支
```git
$ git checkout master
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.

$ git branch -d foo
warning: deleting branch 'foo' that has been merged to
         'refs/remotes/origin/foo', but not yet merged to HEAD.
Deleted branch foo (was c05004a).

```
利用 git pull将远程上主分支的内容拉取下来
```git
$ git pull
remote: Enumerating objects: 1, done.
remote: Counting objects: 100% (1/1), done.
remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (1/1), done.
From https://github.com/xiaweixuan/test
   43fb1df..d475bb4  master     -> origin/master
Updating 43fb1df..d475bb4
Fast-forward
 b.txt | 5 ++++-
 c.txt | 1 +
 2 files changed, 5 insertions(+), 1 deletion(-)
 create mode 100644 c.txt

```
这样利用分支去写功能块就结束了。

## git冲突问题的讲解
在团队合作的时候难免会有pull不下来或者push不上去的情况，这个时候证明你遇到了冲突问题。
举个例子，你们团队在使用集中式工作流的时候，还是这个图
![](https://img-blog.csdnimg.cn/20190918100059221.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p1c3RfYV9iYWRfZ3V5,size_16,color_FFFFFF,t_70)
假设两个人把仓库下载下来时，就有一个文件a.txt。这个时候p1的任务是修改a.txt（内容修改为‘p1的修改’）,修改后上传了，p2的任务是写b.txt，写完后，p2突然发现，a.txt写的有点不对吧，于是p2也修改了a.txt（修改内容为‘发现了bug’），然后git push，结果发现报错无法上传。和之前一样，我们要先pull一下，但是这个时候，就不会像之前那样简单了。因为同样是a.txt文件，远程的是一个内容，而本地的已经被p2修改成另一个内容了，这个时候a.txt的内容就发生了冲突，那要怎么办呢，接下来，我们实施一下。
```git
$ git push
Fatal: HttpRequestException encountered.
   ▒▒▒▒▒▒▒▒ʱ▒▒▒▒
git: 'credential-cache' is not a git command. See 'git --help'.
Username for 'https://github.com': xiaweixuan
git: 'credential-cache' is not a git command. See 'git --help'.
To https://github.com/xiaweixuan/test.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://github.com/xiaweixuan/test.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
作为p2，我们发现自己push的时候报错了，这个时候我们已经知道是因为p1修改了a.txt文件，这个时候我们这么输入
```git
$ git pull --rebase
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From https://github.com/xiaweixuan/test
   9ce584b..939bb69  master     -> origin/master
First, rewinding head to replay your work on top of it...
Applying: ok
Using index info to reconstruct a base tree...
M       a.txt
Falling back to patching base and 3-way merge...
Auto-merging a.txt
CONFLICT (content): Merge conflict in a.txt
error: Failed to merge in the changes.
Patch failed at 0001 ok
The copy of the patch that failed is found in: .git/rebase-apply/patch

When you have resolved this problem, run "git rebase --continue".
If you prefer to skip this patch, run "git rebase --skip" instead.
To check out the original branch and stop rebasing, run "git rebase --abort".
```
这个时候我们进入了修复模式，我们去文件夹里打开a.txt发现内容改变为这样![在这里插入图片描述](https://img-blog.csdnimg.cn/20190918102809868.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p1c3RfYV9iYWRfZ3V5,size_16,color_FFFFFF,t_70)

git用<<< === >>>区分开了文件不同的地方，你要自己去修改，比如我们只想让‘发现了bug’这句话保留，就他其他的都删掉，操作完以后我们继续去命令行
```git
$ git add .
$ git rebase --continue
Applying: ok
```
这样就退出了修复模式，然后git push
```git
$ git push
Fatal: HttpRequestException encountered.
   ▒▒▒▒▒▒▒▒ʱ▒▒▒▒
git: 'credential-cache' is not a git command. See 'git --help'.
Username for 'https://github.com': xiaweixuan
git: 'credential-cache' is not a git command. See 'git --help'.
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 266 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/xiaweixuan/test.git
   939bb69..3b584f7  master -> master

```
至此，冲突修复完成