# git常用命令
```shell script
$ git init // 初始化 在工作路径上创建主分支
$ git init --bare
$ git clone 地址 // 克隆远程仓库
$ git clone -b 分支名 地址 // 克隆分支的代码到本地
$ git status // 查看状态
$ git add 文件名 // 将某个文件存入暂存区
$ git add b c //把b和c存入暂存区
$ git add . // 将所有文件提交到暂存区
$ git add -p 文件名 // 一个文件分多次提交
$ git stash -u -k // 提交部分文件内容 到仓库
$ git commit -m "提交的备注信息" 
$ git commit -am "备注信息" 
$ git remote set-url --add origin root@116.196.114.244:/server/workspace/repo/5G_app # 增加远程库地址

$ git commit --amend -m "提交信息" // 回撤上一次提交并与本次工作区一起提交
$ git reset HEAD~2 --hard // 回撤2步
$ git reset --files // 从仓库回撤到暂存区
$ git reset HEAD // 回撤暂存区内容到工作目录
$ git reset HEAD --soft 回撤提交到暂存区
$ git reset HEAD --hard // 回撤提交 放弃变更 (慎用)
$ git reset HEAD^  // 回撤仓库最后一次提交
$ git reset --hard commitid // 回撤到该次提交id的位置
$ git push -f -u origin 分支名 所有内容都回撤完了 将回撤后的操作强制推送到远程分支
$ git cherry-pick commitid // 提取某次提交到当前分支
$ git checkout branchname f.txt  # 检出分支branchname的f.txt文件到当前分支
$ git ls-files
$ git tag commitid tagname
$ git archive commitidORtagname -o repo.tar
```
### 解决git status不能显示中文
- 原因  
  在默认设置下，中文文件名在工作区状态输出，中文名不能正确显示，而是显示为八进制的字符编码。
- 解决办法  
将git 配置文件 core.quotepath项设置为false。 
```shell script
$ git config --global core.quotepath false
```
其中quotepath表示引用路径，加上--global表示全局配置。
