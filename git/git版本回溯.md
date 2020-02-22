当我们从远程仓库将master下载到本地后，你每使用一次commit将代码提交到版本库后，你的文档就会被记录、保存。比如我写了一个a.txt文件，内容为‘111’，然后git add，git commit后修改了它，内容为‘222’，然后又 git add，git commit，随后又把内容修改了，改为‘333’。

某一时刻，我突然想到，我真正想要的内容是‘111’，那么，我可以通过git log命令查看我所有的提交命令。

```
commit d840a16c0a3819d3da16c16ac75aa5abfe06afa9
Author: xwx <1232312@qq.com>
Date:   Tue Sep 10 15:51:05 2019 +0800

    ok

commit b1156ffd26a5413a2cde0c704717b7a1f5954cd0
Author: xwx <1232312@qq.com>
Date:   Tue Sep 10 15:50:52 2019 +0800

    ok

commit 03469dcb1904fadacbd48de5171bf5249a9e6319
Author: xwx <1232312@qq.com>
Date:   Tue Sep 10 15:50:17 2019 +0800

    ok

```
能看到我提交了三次，并在commit后面对应着一个码，我想退回第一次提交那么

```
git reset --hard 03469dcb1904fadacbd48de5171bf5249a9e6319
```
那么文件内容就变回‘aaa’了。
当然git所记录的不只是你的文件内容，也包括目录，比如你第一次提交时，库中还有两个文件目录，之后删除了，当退回版本时，这两个被删除的文件目录也会回来。
通过上面的例子，我们了解了git每一次的提交，都是被记录下来的，我们也可以随时回到某一个版本。