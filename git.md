问题： 视频中老师用了这个命令：`git checkout -b v8-4 v8-4`,什么意思?
答：

下面对一些分支和标签相关的操作，作一下总结

## git分支相关的命令

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


## git标签相关的命令 
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

* 给自定的commit打标签   
打标签不一定打在当前的head上，如果想打在以前的提交上，可以使用以下命令   
`git tag -a "mytag" "commitId"`

* 将标签提交到git服务器    
通常git push不会将标签提交到git服务器，我们需要显式操作     
`git push origin "mytag"` #将mytag这个标签提交到git服务器    
`git push origin -tags`   #将本地所有的标签全部提交到git服务器    

* 删除远程的标签    
如果标签已推送到远程，要删除远程标签，需要：        
先从本地删除： `git tag -d "mytag"`    
再从远程删除： `git push orign :refs/tags/"mytag"`  #注意这里删除命令也是push，你没有看错


详细的git教程参见:[廖雪峰Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013743862006503a1c5bf5a783434581661a3cc2084efa000)

