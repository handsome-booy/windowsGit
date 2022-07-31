# Git和GitHub

## 1.Git

### 1.1 创建版本库

新建一个包，然后在这个包目录里 git init

### 1.2 往库里存东西

git add xxx.xxx

git commit - m “xxxx”

### 1.3 时光穿梭

git status 查看当前命令的状态

git diff xxx.xxx 查看当前文件的修改情况

### 1.4 版本回退

git log 查看所有往版本库里更新的情况

git reset --hard HEAD^ 回退到上一次修改

git reset --hard XXX 回退到XXX的这一版本

### 1.5 工作区

.git文件所在的目录就是工作区，工作区里包含的是上传到.git版本库的文件。工作区里隐藏的目录.git不算工作区，而是git的版本库。版本库里存了很多东西，其中最重要的是称为index的暂存区，还有git为我们自动创建的第一个分支master，以及指向master的一个指针HEAD。

把文件往Git版本库里添加的时候，是分两步执行的：第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区；第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支。因为我们创建Git版本库时，Git自动为我们创建了唯一一个master分支，所以，git commit就是往master分支上提交更改。

工作区就是.git所在的目录，就是包含了.git目录的那个大文件夹；暂存区就是git add之后文件的所在地，也是在版本库中；git commit之后，文件就到了版本库中，版本库就是.git目录。

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20211102095604185.png" alt="image-20211102095604185" style="zoom:67%;" />

### 1.6 查看工作区和版本库文件差别

git diff HEAD -- xxx.xxx

### 1.7 撤销修改

git checkout -- xx.xx 意思是把xx.xx文件在工作区的修改全部撤销，或者把在暂存区的文件的修改全部撤销，全部回到修改前的状态。总之就是让一个文件回到上一次git commit或者上一次git add的状态。**git checkout其实是使用版本库里的版本替换工作区的版本，无论工作区是修改还删除，都可以一件还原。**

git reset HEAD xxx.xxx 可以把暂存区的修改重新放回工作区。使用HEAD表示的是最新版本

### 1.8 删除文件

rm xxx.xxx  +  git rm xxx.xxx   +   git commit -m“xxx”

如果删除错误（只是rm了文件），可以使用git checkout来从版本库里恢复回来。‘

### 1.9 远程仓库（杀手级功能）

连接github上的远程仓库 ：git remote add origin git@github.com:handsome-booy/learngit.git，添加后远程库的名字就是origin；

把本地库所有内容推送到远程库上：git push -u origin master，git push命令实际上是把当前分支master推送到远程。由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。以后的推送可以使用git push origin master

删除远程仓库：可以先用git remote -v来查看远程仓库的信息，然后使用git remote rm origin来删除远程库。注意：这里的删除只是把二者关联断了，真的要删除得去github上删除。

### 1.10 从远程库克隆

git clone git@ github.com:handsome-booy/newexercise.git

### 1.11 创建与合并分支

创建分支： git checkout -b dev，这一命令相当于创建一个dev分支，并切换到dev分支，等同于以下两个命令：git branch dev + git checkout dev

查看分支：使用git branch可以查看当前的分支

合并分支：git merge dev 将dev分支的修改合并到master分支（在什么分支下合并，就是拿dev和什么分支合并）

删除分支：git branch -d dev

创建并切换分支还可以使用switch命令，git switch -c dev，git switch master

### 1.12 解决冲突

无法使用git merge自动合并时，需要手动更改内容

### 1.13 分支管理策略

如果强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样就可以看出分支信息。git merge --no-ff -m“merge with nno-ff”  dev表示禁用Fast forward模式。

### 1.14 Bug分支

当你在一个分支上工作到一半，并且不想马上提交时，可以使用git stash命令，把工作现场存储起来，等之后继续工作。当需要继续工作时，可以使用git stash list命令查看，然后需要把这个内容恢复起来，恢复的方法有两个：一是用git stash apply恢复，但是恢复之后stash内容并不会被删除，需要用git stash drop手动删除；二是用git stash pop，恢复的同时把stash内容也删除了。当stash内容多次时，恢复的时候就要带上stash list中的定位“stash@{0}”。

首先要确定在哪个分支上修复bug，如果要在dev分支上修复，就需要在dev分支上创建一个临时bug修复分支，使用git checkout -b issue-101表示创建一个issue-101分支。在这个新分支上修复完后，切换到原来的dev分支，并完成合并（只是合并dev和issue-101这两个分支），最后删除bug修复分支。

假设一个分子dex是从dev分支上分出来的，所以同样的bug也在dex上，在dev上修复了这个bug后，怎么能在dex上也修改呢？要想在dex上也修改，我们只需要把dev上负责修复的分支issue-101上的提交编号复制到dex分支，这个过程只是把对应的提交修改了，而不是合并dex和dev，这个操作可以使用git cherry-pick xxxx。

### 1.15 feature分支

通过feature分支来添加新功能。

如果要丢弃一个没有被合并过的分支，可以通过git branch -D xxx来完成。

### 1.16 多人协作

当从远程仓库clone时，git会自动把本地的master分支和远程的master分支对应起来，远程仓库的默认名是origin。

查看远程仓库的信息，可以使用git remote。

使用git push origin master把本地master分支推送到远程origin，如果要推送其他分支，可以把master改掉。

如果远程仓库有多个分支，但是git clone只能把master分支克隆下来，如果要把其他分支也调出来，需要使用命令git checkout -b origin/dev，这样就把远程的dev分支也clone下来了。

当两个人同时提交时，如果有一方有错误，可以使用git pull把最新的提交抓下来，然后在本地合并和解决冲突，再推送。

### 1.17 rebase操作

使用git rebase可以把本地未push的分叉提交历史整理成直线。

### 1.18 创建标签

在需要打标签的分支上使用git tag tagname。

查看标签可以使用git tag。

标签是打在最新提交的commit上的，如果需要对历史的commit打标签，可以找到历史的commit id，然后使用git tag tagname f52ss即可。

可以使用git tag tagname来查看标签信息。

使用git tag -a tagname -m“xxxx"创建带有说明的标签。

### 1.19 操作标签

git tag -d tagname来删除标签。标签只保存在本地，不会推送到远程仓库。

如果要把标签推送到远程仓库，可以使用git push origin tagname，或者一次性推送所有未推送的标签git push origin --tags。

如果标签已经推送到远程，还想要删除，可以使用以下方法：首先在本地删除git tag -d tagname，然后使用git push origin：refs/tags/tagname远程删除。
