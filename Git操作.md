## 问题：视频中老师用到`git reset HEAD`命令，什么意思
## 问题：视频中老师用了这个命令：`git checkout -b v8-4 v8-4`,什么意思?

这里写一些我对相关命令的探究，主要结合了菜鸟教程，廖雪峰Git教程，并加入了自己的理解。

### Git工作区，暂存区和版本库

我们先来理解下Git 工作区、暂存区和版本库概念（**非常重要，对许多命令的理解起着非常重要的作用**）

* 工作区：就是你在电脑里能看到的目录。
* 暂存区：英文叫stage, 或index。一般存放在 ".git目录下" 下的index文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。
* 版本库：工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。

下面这个图展示了工作区、版本库中的暂存区和版本库之间的关系：  
![](https://www.runoob.com/wp-content/uploads/2015/02/1352126739_7909.jpg)

图中左侧为工作区，右侧为版本库。在版本库中标记为 "index" 的区域是暂存区（stage, index），标记为 "master" 的是 master 分支所代表的目录树。
图中我们可以看出此时 "HEAD" 实际是指向 master 分支的一个"游标"。所以图示的命令中出现 HEAD 的地方可以用 master 来替换。
图中的 objects 标识的区域为 Git 的对象库，实际位于 ".git/objects" 目录下，里面包含了创建的各种对象及内容。

* 当对工作区修改（或新增）的文件执行 `git add` 命令时，暂存区的目录树被更新，同时工作区修改（或新增）的文件内容被写入到对象库中的一个新的对象中，而该对象的ID被记录在暂存区的文件索引中。  
* 当执行提交操作（`git commit`）时，暂存区的目录树写到版本库（对象库）中，master 分支会做相应的更新。即 master 指向的目录树就是提交时暂存区的目录树。 
* 当执行 `git checkout .` 或者 `git checkout -- <file>` 命令时，会用暂存区全部或指定的文件替换工作区的文件。这个操作很危险，会清除工作区中未添加到暂存区的改动。
* 当执行 `git reset HEAD ` 命令时，暂存区的目录树会被重写，被 master 分支指向的目录树所替换，但是工作区不受影响。
>上面两个命令就对应以下两种情景  
场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。  
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。
  
* 当执行 `git checkout HEAD .` 或者 `git checkout HEAD <file>` 命令时，会用 HEAD 指向的 master 分支中的全部或者部分文件替换暂存区和以及工作区中的文件。这个命令也是极具危险性的，因为不但会清除工作区中未提交的改动，也会清除暂存区中未提交的改动。 
>所以总结起来就是， `git checkout -- <file>`是从缓存区到工作区，`git reset HEAD`是从版本库到缓存区，`git checkout HEAD <file>`是从版本库直接到工作区。可以看出`git checkout HEAD <file>`命令是上面`git reset HEAD`和`git checkout -- <file>`两个命令的结合 。而且这里HEAD可以换作任意的commitid。我们通常关注的是工作区和版本库，所以我觉得最常用的命令就是`git checkout <commitid> <file>/.`,这样我们的工作区就可以在不同的提交之间切换。而且如果我们给每个commit打上标签（tag）我们给每个提交起个有意义的tag,我们就可以使用`git checkout <tag> <file>/.`在不同的commit之间切换。比如我这里有两次commit,分别打标签为one,two,当我想去第一次commit的地方，使用`git checkout one .`,这时工作区的内容就是第一次是的状态，再用`git checkout two .`,工作区就变成了第二次提交的状态，这里注意`git checkout one .`和`git checkout one`的不同，第二个命令是切换分支（如果有one分支，就切换到其上，当然我们这里没有创建one分支）。这里我试验一下，当使用`git checkout one`也会将工作区变为第一次提交的状态，而且HEAD指向也变为one了。又牵扯出一些我不太了解的情况，暂时先探讨到这里。    
接下来还可以探讨`git reset --hard <tag>`命令，不仅可以将工作区在不同commit之间切换，而且HEAD的指向也会在变换。


* 当执行 `git rm --cached <file>` 命令时，会直接从暂存区删除文件，工作区则不做出改变。  

### HEAD是什么？

上面是为了说明工作区、缓存区、版本库的概念，作了简化，针对只有一个master分支而言，实际上HEAD指向当前活动分支的当前活动版本，比如我的项目有两个分支dev,master,我现在`git checkout dev`到dev分支上，然后dev分支有3次提交（按提交顺序记commitid分别为c1,c2,c3），这时我当前版本就是commitid为最新提交的c3,所以HEAD就指向c3,当我用`git reset --hard c1`命令回到第一次提交的版本时，这时HEAD就指向c1。

https://cdn.liaoxuefeng.com/cdn/files/attachments/001384907584977fc9d4b96c99f4b5f8e448fbd8589d0b2000/0
https://cdn.liaoxuefeng.com/cdn/files/attachments/001384907594057a873c79f14184b45a1a66b1509f90b7a000/0

## 下面对一些分支和标签相关的操作，作一下总结

### git分支相关的命令

* 创建一个新的分支         
`git branch dev` #注意是从当前分支创建

* 切换到一个分支       
`git checkout dev`

* 创建并切换到一个新的分支     
`git checkout -b dev`

* 查看所有分支         
`git branch`

* 将dev分支合并到当前分支     
如果当前处于master分支，要把dev分支合并到master分支   
`git merge dev`

* 删除分支    
`git branch -d dev`


### git标签相关的命令 
>注意：以下"mytag"都是自己起的标签名

* 列出当前分支下的所有标签     
`git tag`

* 创建标签    
`git tag "mytag"`

* 创建标签时写注释    
`git tag -a "mytag" -m "注释"`

* 切换标签（与切换分支命令相同）     
`git checkout "mytag"`

* 查看一个标签的信息     
`git show "mytag"`

* 删除标签      
`git tag -d 'mytag'`

* 给指定的commit打标签   
打标签不一定打在当前的head上，如果想打在以前的提交上，可以使用以下命令   
`git tag -a "mytag" "commitId"`

* 给指定commitid打标签时写注释
`git tag -a "mytag" -m "commitId"`

* 将标签提交到git服务器    
通常git push不会将标签提交到git服务器，我们需要显式操作     
`git push origin "mytag"` #将mytag这个标签提交到git服务器    
`git push origin -tags`   #将本地所有的标签全部提交到git服务器    

* 删除远程的标签    
如果标签已推送到远程，要删除远程标签，需要：        
先从本地删除： `git tag -d "mytag"`    
再从远程删除： `git push orign :refs/tags/"mytag"`  #注意这里删除命令也是push，你没有看错


详细的git教程参见:[廖雪峰Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013743862006503a1c5bf5a783434581661a3cc2084efa000)
[菜鸟教程](http://www.runoob.com/git/git-workspace-index-repo.html)

