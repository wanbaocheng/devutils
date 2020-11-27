# git常用命令
- 本地库
  - 克隆远程库
  ```
  $ git clone 地址  # 克隆远程仓库
  $ git clone -b 分支名 地址  # 克隆分支的代码到本地
  ```
  - 创建
  ```
  $ mkdir repo && cd repo
  $ git init  # 初始化 在工作路径上创建主分支
  $ git init --bare
  ```
  - 删除  
  删除本地库所在的整个目录即可。
- 远程库
  - 创建
  ```
  $ git remote add mathsai root@IP地址或域名:/root/repos/urdf  # 添加其名字为mathsai的远程库，并为其指定地址
  $ git remote set-url --add origin root@IP地址或域名:/root/repos/urdf  # 在远程库origin中添加新地址
  ```
  - 查看
  ```
  $ git remote -v
  ```
  - 删除
  ```
  $ git remote remove rrepo  # 删除远程库 rrepo
  $ git remote set-url --delete origin root@IP地址或域名:/root/repos/urdf  # 在远程库origin中删除地址
  ```
- 分支
  - 查看  
  ```
  $ git branch -a  # 查看所有分支
  $ git branch  # 查看本地分支
  $ git branch -r  # 查看远程分支
  $ git branch -vv  # 查看本地分支及其上游分支
  ```
  - 创建本地库的分支
  ```
  $ git checkout -b bname  # 创建本地分支bname，检出当前分支的到该分支，并切换到它
  $ git checkout -b bname commitid   # 创建本地分支bname，检出当前分支的提交commitid到该分支，并切换到它
  $ git checkout -b bname1 bname2  # 创建分支 bname1，并检出 bname2 到它
  $ git checkout -b bname rbname  # 创建分支 bname，检出 rbname 到它，并把 rbname 设置为 bname 的上游分支
  $ git checkout --track rbname  # 在本地库创建与 rbname 同名的分支，并 rbname 作为它的上游分支
  ```
  - 切换或检出
  ```
  $ git checkout bname  # 如果存在本地分支 bname，则切换到该分支，否则检查远程库中有无分支 bname，
                        # 若有，则创建同名本地分支，并检出远程分支到本地，否则输出失败信息
  $ git checkout bname f.txt  # 检出分支 bname 当前提交中的f.txt文件到当前分支
  $ git checkout commitid f.txt  # 检出提交commitid的f.txt文件到当前分支
  ```
  - 创建远程库的分支
  ```
  $ git push rrepo bname  # 在远程仓库 rrepo 中创建分支 bname，并把本地分支 bname 的内容推上去
  ```
  - 设置上游分支
  ```
  $ git branch -u rbname  # 把 rbname 设置当前分支的上游分支
  $ git branch --set-upstream-to rbname  # 同上
  $ git branch -u rbname bname  # 把 rbname设置为bname的上游分支
  $ git branch --set-upstream-to rbname bname  # 同上
  ```
  - 删除
  ```
  $ git branch -d bname  # 删除本地分支 bname
  $ git branch -r d rbname  # 删除远程分支 rbname
  ```
- 本地库的可视化查看
```
$ gitk --all
```
- 在本地库的当前分支上的操作
  - 查看相关信息
  ```
  $ git status  # 查看状态
  $ git log
  $ git reflog
  $ git ls-files
  $ git ls-files|awk 'system("ls -l "$0)' |awk '{count++; sum+=$5} {print "Count:" count,"Sum: " sum" bytes"}'\
  $ git status --untracked-files=all
  $ git ls-files --others --exclude-standard
  ```
  - 将文件加入暂存区
  ```
  $ git add 文件名 // 将某个文件存入暂存区
  $ git add b c //把b和c存入暂存区
  $ git add . // 将所有文件提交到暂存区
  ```
  - 提交
  ```
  $ git commit -m "提交的备注信息"
  $ git commit -am "备注信息"
  $ git commit --amend -m "提交信息" // 回撤上一次提交并与本次工作区一起提交
  ```
  - [打标签](https://www.jianshu.com/p/154d58451ef7)
  ```
  $ git tag commitid tagname
  ```
  - 打包
  ```
  $ git archive commitidORtagname -o repo.tar
  ```
  - 回撤
  ```
  $ git reset HEAD~2 --hard // 回撤2步
  $ git reset --files // 从仓库回撤到暂存区
  $ git reset HEAD // 回撤暂存区内容到工作目录
  $ git reset HEAD --soft 回撤提交到暂存区
  $ git reset HEAD --hard // 回撤提交 放弃变更 (慎用)
  $ git reset HEAD^  // 回撤仓库最后一次提交
  $ git reset --hard commitid // 回撤到该次提交id的位置
  ```
  - 子模块
    - 在git库中添加子模块
    ```
    $ git submodule add 子模块URL
    ```
    - 更新子模块  
    在克隆git库后需如下操作
    ```
    $ git submodule init
    $ git submodule update
    ```
    - [.gitmodule中没有为非子模块的路径找到子模块映射](https://stackoom.com/question/HYnt/gitmodule%E4%B8%AD%E6%B2%A1%E6%9C%89%E4%B8%BA%E9%9D%9E%E5%AD%90%E6%A8%A1%E5%9D%97%E7%9A%84%E8%B7%AF%E5%BE%84%E6%89%BE%E5%88%B0%E5%AD%90%E6%A8%A1%E5%9D%97%E6%98%A0%E5%B0%84)
    ```shell script
    $ git ls-files --stage | grep 160000    # 罗列索引中引用的所有子模块
    $ git rm --cached 子模块                 # 根据上面所列的子模块逐个删除
    ```
    

### 从仓库中彻底删除文件或文件夹
```
$ git filter-branch --force --index-filter \
'git rm -r --cached --ignore-unmatch 文件夹或文件名' \
--prune-empty --tag-name-filter cat -- --all
$ rm -rf .git/refs/original/
$ git reflog expire --expire=now --all
$ git gc --prune=now
$ git push --force --all
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

### git diff提示dirty问题
```shell script
$ git diff
```
输出类似
```shell script
-Subproject commit 545qiwukjkdsjf345qjj54555432ksg444jj4467
+Subproject commit 545qiwukjkdsjf345qjj54555432ksg444jj4467-dirty
```
解决办法:
```shell script
$ git submodule foreach --recursive git checkout .
```
