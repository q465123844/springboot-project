项目中遇到的一些分支和标签相关的操作，这里做一下总结

## git分支相关的命令







## git标签相关的命令

* 列出当前分支下的所有标签 
git tag

* 创建标签
git tag "mytag"

* 创建标签时写注释
git tag -a "mytag" -m '注释'

* 切换标签（与切换分支命令相同）
git checkout "mytag"

* 查看一个标签的信息
git show "mytag"

* 删除标签
git tag -d 'mytag'

* 给自定的commit打标签
打标签不一定打在当前的head上，如果想打在以前的提交上，可以使用以下命令
git tag -a "mytag" "commitId"

* 将标签提交到git服务器
通常git push不会将标签提交到git服务器，我们需要显式操作
git push origin "mytag" #将mytag这个标签提交到git服务器
git push origin -tags   #将本地所有的标签全部提交到git服务器
