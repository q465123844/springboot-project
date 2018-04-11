## 问题：视频中老师用到`git reset HEAD`命令，什么意思
## 问题：视频中老师用了这个命令：`git checkout -b v8-4 v8-4`,什么意思?

* 答：`git reset HEAD`命令是`git reset HEAD .`的缩写，执行这个命令，暂存区的目录树会被重写，被 HEAD指向的目录树所替换，但是工作区不受影响，如果要让工作区也被重写，可以加上--hard参数，即`git reset --hard HEAD`，也可以用`git checkout HEAD .`命令（下面对其有进一步的讲解）。再进一步，以上的HEAD都可以换成任意的commitid或tag，例如`git reset two`,暂存区内容将被标签为two的提交所重写，并且将HEAD指向two标签处的提交。
  >对于`git reset <commitid/tag/HEAD> <./file>`		  
我们把命令分解为三个部分，`reset`、`<commitid/tag/HEAD`、`<./file>`。     
第一部分`reset`：这个命令有两个功能：1.重置`HEAD`的指向，使`HEAD`指向指定的`<commitid/tag>`。 2.用`<commitid/tag>`指向的提交重写缓存区。    
第二部分`<commitid/tag>`：它是`git reset`作用的对象，指任意一次提交(或其tag)，特殊情况这个作用对象是`HEAD`（因为`HEAD`也指向一次提交的）,即`git reset HEAD`,这时`HEAD`是`git reset`作用的对象，就是将`HEAD`指向`HEAD`,并用`HEAD`指向的提交重写缓存区。      
第三部分`<./file>`: 它是指定要被重写的范围，可以指定某一个文件，也可以用`.`指定重写所有文件。    

* 答：`git checkout -b 8-4 8-4`这个命令中第一个8-4是指当前分支的8-4标签，第二个8-4是指一个新的分支，这个命令的意义是，在当前分支的8-4标签处新建一个新的分支8-4，并切换到8-4分支上去，并且这时HEAD是指向8-4分支的8-4标签对应的提交上。      
下面用一系列命令说明了这个过程。（为了便于演示，我之前已经在上面三次提交上分别打了one,two,three三个标签）     
![](https://github.com/sqmax/springboot-project/blob/blog/blog/pic/3.png)      

下面写一些我对相关命令的探究，主要结合了菜鸟教程，廖雪峰Git教程，并加入了自己的理解和实验。   

### Git工作区，暂存区和版本库

我们先来理解下Git 工作区、暂存区和版本库概念（**非常重要，对许多命令的理解起着非常重要的作用**）

* 工作区：就是你在电脑里能看到的目录。
* 暂存区：英文叫stage, 或index。一般存放在 ".git目录下" 下的index文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。
* 版本库：工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。

>我们可以用下面的命令查看三个地方文件的区别：    
`git diff HEAD --` #查看版本库和工作区的区别    
`git diff --staged` #查看版本库和缓存区的区别     

下面这个图展示了工作区、版本库中的暂存区和版本库之间的关系：  
![](https://github.com/sqmax/springboot-project/blob/blog/blog/pic/pic.png)

图中左侧为工作区，右侧为版本库。在版本库中标记为 "index" 的区域是暂存区（stage, index），标记为 "master" 的是 master 分支所代表的目录树。
图中我们可以看出此时 "HEAD" 实际是指向 master 分支的一个"游标"。所以图示的命令中出现 HEAD 的地方可以用 master 来替换。
图中的 objects 标识的区域为 Git 的对象库，实际位于 ".git/objects" 目录下，里面包含了创建的各种对象及内容。

* 当对工作区修改（或新增）的文件执行 `git add` 命令时，暂存区的目录树被更新，同时工作区修改（或新增）的文件内容被写入到对象库中的一个新的对象中，而该对象的ID被记录在暂存区的文件索引中。  
* 当执行提交操作（`git commit`）时，暂存区的目录树写到版本库（对象库）中，master 分支会做相应的更新。即 master 指向的目录树就是提交时暂存区的目录树。 
* 当执行`git checkout -- .`(可以简写为`git checkout .`) 或者 `git checkout -- <file>`（可以简写为`git checkout <file>`） 命令时，会用暂存区全部或指定的文件替换工作区的文件。这个操作很危险，会清除工作区中未添加到暂存区的改动。
* 当执行`git reset HEAD .`（简写为`git reset HEAD `） 命令时，暂存区的目录树会被重写，被 master 分支指向的目录树所替换，但是工作区不受影响。
>上面两个命令就对应以下两种情景  
场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。  
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。
  
* 当执行 `git checkout HEAD .` 或者 `git checkout HEAD <file>` 命令时，会用 HEAD 指向的 master 分支中的全部或者部分文件替换暂存区和以及工作区中的文件。这个命令也是极具危险性的，因为不但会清除工作区中未提交的改动，也会清除暂存区中未提交的改动。 
>所以总结起来就是， `git checkout -- <file>`是从缓存区到工作区，`git reset HEAD`是从版本库到缓存区，`git checkout HEAD <file>`是从版本库直接到工作区。可以看出`git checkout HEAD <file>`命令是上面`git reset HEAD`和`git checkout -- <file>`两个命令的结合 。而且这里HEAD可以换作任意的commitid。我们通常关注的是工作区和版本库，所以我觉得最常用的命令就是`git checkout <commitid> <file>/.`,这样我们的工作区就可以在不同的提交之间切换。而且如果我们给每个commit打上标签（tag）我们给每个提交起个有意义的tag,我们就可以使用`git checkout <tag> <file>/.`在不同的commit之间切换。比如我这里有两次commit,分别打标签为one,two,当我想去第一次commit的地方，使用`git checkout one .`,这时工作区的内容就是第一次是的状态，再用`git checkout two .`,工作区就变成了第二次提交的状态，这里注意`git checkout one .`和`git checkout one`的不同，第二个命令是切换分支（如果有one分支，就切换到其上，当然我们这里没有创建one分支）。这里我试验一下，当使用`git checkout one`也会将工作区变为第一次提交的状态，而且HEAD指向也变为one了。又牵扯出一些我不太了解的情况，暂时先探讨到这里。    
接下来还可以探讨`git reset --hard <tag>`命令，不仅可以将工作区在不同commit之间切换，而且HEAD的指向也会在变换。


* 当执行 `git rm --cached <file>` 命令时，会直接从暂存区删除文件，工作区则不做出改变。  

### HEAD是什么？

上面是为了说明工作区、缓存区、版本库的概念，作了简化，针对只有一个master分支而言，实际上HEAD指向当前活动分支的当前活动版本，比如我的项目有两个分支dev,master,我现在`git checkout dev`到dev分支上，然后dev分支有3次提交（按提交顺序记commitid分别为c1,c2,c3），这时我当前版本就是commitid为最新提交的c3,所以HEAD就指向c3,当我用`git reset --hard c1`命令回到第一次提交的版本时，这时HEAD就指向c1。    
HEAD默认值想最新提交的，我们可以用`git reset`改变HEAD的指向，来指向曾经的提交，我们不要随便改变HEAD的指向，因为很多命令都是以HEAD的指向为参考标准的，例如下面的`git log`(注意我对`git log`起了别名`git lg`,并进行了格式化)。     
![](https://github.com/sqmax/springboot-project/blob/blog/blog/pic/6.png)

下面是一些相关的命令：   
`git show HEAD` #查看HEAD指向的版本库的信息      
`git show HEAD^^` #查看HEAD指向版本的上一个版本的信息      
`git show HEAD~4` #查看HEAD上溯4代的信息    
注意：windows下shell把`^`当做换行转义了，类似于`\`,可以这样使用`git show "HEAD^"`，也可以这样`git show HEAD~1`。

### git学习方法总结
* 很多命名我们只有时间后才能够对其真正地理解，我的做法是，建立一个文件夹，`git init`成一个版本库，建一个.txt文件，并一直打开，修改几次并提交，然后探究各种命令，期间需要不断地`git status`来查看文件的提交状态、NotePad++查看工作区.txt文件（或者用`git diff HEAD .`命令来查看工作区和版本库里面最新版本的区别），来观察工作区，暂存区，版本库的变化，还可以`git log`,`git reflog`,`git branch`,`git tag`,`git show HEAD`查看各种信息。
* 学会把一个命令分解开来看，很多命令都是这么一种形式的，谓语（动作）、宾语（作用对象）、状语（作用范围），并且，很多命令是简写的（把状语省略了，常见的就是当作用于所有文件时，省略`.`），我们要学会把它补全,这样我们才能够弄清楚一个命令的本质，熟练记忆。
>下面几个命令就可以这样来分解：    
* `git reset <commitid/tag/HEAD>`(`git reset <commitid/tag/HEAD> .`的简写)。
  翻译一下就是：对所有的文件将HEAD重指向指定的提交。
* `git diff <commitid/tag/branch> <commitid/tag/branch>`(`git diff <commitid/tag/branch> <commitid/tag/branch>`的简写)。
  翻译一下就是：对所有的文件，比较不同提交或分支的不同。
* `git checkout <commitid/tag> .`(这个命令的特殊情况（`git checkout HEAD .`）的意义上面有介绍，注意这个命令不能省略后面的状语，我试验过)。
  翻译一下就是：对所有文件，用指定的提交重写工作区和缓存区。

------------------------

## 下面对一些分支和标签相关的操作，作一下复习

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
`git tag -a "mytag" "commitId"` #输入这个命令后终端会变成一个vim编辑器，让你输入对tag的注释。

* 将标签提交到git服务器    
通常git push不会将标签提交到git服务器，我们需要显式操作     
`git push origin "mytag"` #将mytag这个标签提交到git服务器    
`git push origin -tags`   #将本地所有的标签全部提交到git服务器    

* 删除远程的标签    
如果标签已推送到远程，要删除远程标签，需要：        
先从本地删除： `git tag -d "mytag"`    
再从远程删除： `git push orign :refs/tags/"mytag"`  #注意这里删除命令也是push，你没有看错

### 一些使用小技巧     
* 使用`git config --global alias.<别名> 命令`给git命令起别名。
> 如`git config --global alias.br branch` #以后就可以用`git br`代替`git branch`

* 查看配置信息
`git config -l`

* 格式化`git log`查看日志的方式       
`git log –graph –pretty=format:’%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset’ –abbrev-commit –date=relative`          
我们可以为这个长命令它起个别名       
`git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative"`    
下面格式化后的日志看起来是不是清爽很多。        
![](https://github.com/sqmax/springboot-project/blob/blog/blog/pic/4.PNG)



详细的git入门教程参见:[廖雪峰Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013743862006503a1c5bf5a783434581661a3cc2084efa000)
[菜鸟教程](http://www.runoob.com/git/git-workspace-index-repo.html)

