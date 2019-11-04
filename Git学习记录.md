### 1.Git与SVN的区别

##### 我来解读一下读完后理解的Git与SVN的主要差别 我觉得这两个工具主要的区别在于历史版本维护的位置Git本地仓库包含代码库还有历史库，在本地的环境开发就可以记录历史 而SVN的历史库存在于中央仓库，每次对比与提交代码都必须连接到中央仓库才能进行这样的好处在于： 
1、自己可以在脱机环境查看开发的版本历史 
2、多人开发时如果充当中央仓库的Git仓库挂了，任何一个开发者的仓库都可以作为中央仓库进行服务

### 2. 创建版本库

##### 什么是版本库呢？版本库又名仓库，英文名repository，你可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。所以，创建一个版本库非常简单，
##### 首先，选择一个合适的地方，创建一个空目录：
```
$ mkdir learngit
$ cd learngit
$ pwd
/Users/michael/learngit
```
##### 第二步，通过git init命令把这个目录变成Git可以管理的仓库：

```
$ git init
Initialized empty Git repository in /Users/michael/learngit/.git/
```
##### `瞬间Git就把仓库建好了，而且告诉你是一个空的仓库（empty Git repository），细心的读者可以发现当前目录下多了一个.git的目录，这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。`
如果你没有看到.git目录，那是因为这个目录默认是隐藏的，用ls -ah命令就可以看见。
##### 第三步，在之前创建的learngit目录下(子目录也可以),创建文件

用命令git add告诉Git，把文件添加到仓库：

```
$ git add readme.txt
```

用命令git commit告诉Git，把文件提交到仓库：
```
$ git commit -m "wrote a readme file"
[master (root-commit) eaadf4e] wrote a readme file
 1 file changed, 2 insertions(+)
 create mode 100644 readme.txt
```

##### git commit -m <message>

简单解释一下git commit命令，-m后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。嫌麻烦不想输入-m "xxx"行不行？确实有办法可以这么干，但是强烈不建议你这么干，因为输入说明对自己对别人阅读都很重要。实在不想输入说明的童鞋请自行Google，我不告诉你这个参数。

git commit命令执行成功后会告诉你，1 file changed：1个文件被改动（我们新添加的readme.txt文件）；2 insertions：插入了两行内容（readme.txt有两行内容）。

----
### 版本回退

##### 在Git中，我们用git log命令查看：

git log命令显示从最近到最远的提交日志
##### 现在我们要把版本回退到上一次修改的版本，使用命令 git reset 
将版本回退到当前版本
```
$ git reset --hard HEAD
HEAD is now at 24d4967 第二次修改
```

将版本回退到上个版本
```
$ git reset --hard HEAD~1
HEAD is now at e4f3b25 俺的第一次
```

* `首先，Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本，也就是最新的提交1094adb...（注意我的提交ID和你的肯定不一样），上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。
`
##### 如果想要回到新版本，好比你从21世纪坐时光穿梭机来到了19世纪，想再回去已经回不去了，肿么办？
* 办法其实还是有的，只要上面的命令行窗口还没有被关掉，你就可以顺着往上找啊找啊，找到那个append GPL的commit id是1094adb...，于是就可以指定回到未来的某个版本：(版本号没必要写全，前几位就可以了，Git会自动去找)
```
$ git reset --hard 24d4
HEAD is now at 24d4967 第二次修改
```
##### 在Git中，总是有后悔药可以吃的。当你用$ git reset --hard HEAD^回退到add distributed版本时，再想恢复到append GPL，就必须找到append GPL的commit id。Git提供了一个命令git reflog用来记录你的每一次命令：

```
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```
------
### 工作区和暂存区

 ##### 工作区（Working Directory）
>* 就是你在电脑里能看到的目录，比如我的learngit文件夹就是一个工作区：
![f0324fc089e3a2e113a0df79f514645c.png](en-resource://database/838:1)


#####  版本库（Repository）
>* 工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库
>* Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。
![2fcd9145a58280f58b3932ff83caaf0f.png](en-resource://database/840:1)
>* 前面讲了我们把文件往Git版本库里添加的时候，是分两步执行的：
>* 第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区
>* 第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支。
>* 因为我们创建Git版本库时，Git自动为我们创建了唯一一个master分支，所以，现在，git commit就是往master分支上提交更改。
##### git show commit_id 可以查看提交内容
----
### 撤销修改

##### git checkout -- file可以丢弃工作区的修改：

```
$ git checkout -- readme.txt
```

命令git checkout -- readme.txt意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：

* 一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

* 一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。总之，就是让这个文件回到最近一次git commit或git add时的状态。
##### 假设你将错误的信息以及git add 到缓存区中，

* 用命令git reset HEAD <file>可以把暂存区的修改撤销掉（unstage），重新放回工作区：

```
$ git reset HEAD readme.txt
Unstaged changes after reset:
M       readme.txt
```

##### `git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本`
##### 小结

* 又到了小结时间。
* 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。
* 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD <file>，就回到了场景1，第二步按场景1操作。
* 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

### 删除文件

一般情况下，你通常直接在文件管理器中把没用的文件删了，或者用rm命令删了：

```
$ rm test.txt
```

现在你有两个选择，一是确实要从版本库中删除该文件，那就用命令git rm删掉，并且git commit：

```
$ git rm test.txt
rm 'test.txt'
$ git commit -m "remove test.txt"
[master d46f35e] remove test.txt
 1 file changed, 1 deletion(-)
 delete mode 100644 test.txt
```

另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：

```
$ git checkout -- test.txt
```
-----
### 远程仓库

##### 要关联一个远程库，使用命令
```
git remote add origin git@server-name:path/repo-name.git
```
##### 关联后，使用命令第一次推送master分支的所有内容；
```
git push -u origin master
```

此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改
### 从远程库克隆

###### 上次我们讲了先有本地库，后有远程库的时候，如何关联远程库。
###### 现在，假设我们从零开发，那么最好的方式是先创建远程库，然后，从远程库克隆。
###### 首先，登陆GitHub，创建一个新的仓库，名字叫gitskills：
![ee57edf05e5797bce4f4946c9fb56732.png](en-resource://database/842:1)

##### 现在，远程库已经准备好了，下一步是用命令git clone克隆一个本地库：
```
$ git clone git@github.com:yshdxm/gitskills.git

Cloning into 'gitskills'...

remote: Enumerating objects: 3, done.

remote: Counting objects: 100% (3/3), done.

remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0

Receiving objects: 100% (3/3), done.
```

##### 小结
要克隆一个仓库，首先必须知道仓库的地址，然后使用git clone命令克隆。Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。

-----
### 分支管理
#### 创建与合并分支

查看分支：git branch

创建分支：git branch <name>

切换分支：git checkout <name>或者git switch <name>

创建+切换分支：git checkout -b <name>或者git switch -c <name>

合并某分支到当前分支：git merge <name>

删除分支：git branch -d <name>