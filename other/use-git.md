# git 一般的使用操作
1. 先在github上建立自己的repository，取名为yourRepo

2. 创建本地库

```
ssh -T git@github.com # 在初始化版本库之前，先要确认认证的公钥是否正确
git init # 初始化版本库
git remote add origin git@github.com:yourname/yourRepo.git # 进入要上传的仓库，添加远程地址

git add -A # 或者使用 git add * 添加所有仓库
git commit -m 'first commit' # 提交并添加注释
git push origin master # 上传到github，如果git仓库中已经有一部分代码，会报（Non-fast-forward）错误
```
或者
```
git init
git add -A
git commit -m "first commit"
git remote add origin https://github.com/xxx/xxx.git
git push -u origin master
```

3. 可能出现的问题及解决办法

* 如果git仓库中已经有一部分代码，是不允许直接覆盖代码上去，会出现（Non-fast-forward/error:failed to push som refs to）错误，解决办法如下：

>（1）强推，强制使用本地的代码覆盖git仓库中的内容git push -f

>（2）先把git仓库中的内容fetch到本地然后merge后，再push，#git fetch #git merge，这2句命令等价于git pull

        合并pull两个不同的项目出现：fatal: refusing to merge unrelated histories
        
        解决办法：git pull origin master --allow-unrelated-histories，添加--allow-unrelated-histories参数

* git pull 是可能会报错，解决办法如下：

>（1）修改(.git/config)的内容如下
         [branch "master"]
         remote = origin
         merge = refs/heads/master

>（2）如果不编辑config文件的话，可以使用如下命令行：

         git config branch.master.remote origin 
         git config branch.master.merge refs/heads/master

        然后再pull下再push就OK了，或者直接git pull origin master

 

4. 在第一次使用git push代码前需要先配置Git
>（1）首先在本地创建ssh key；

        $ ssh-keygen -t rsa -C "your_email@youremail.com"

        后面的your_email@youremail.com改为你的邮箱，之后会要求确认路径和输入密码，我们这使用默认的一路回车就行。成功的话会在~/下生成.ssh文件夹，进去，打开id_rsa.pub，复制里面的key。回到github，进入Account Settings，左边选择SSH Keys，Add SSH Key,title随便填，粘贴key。

>（2）为了验证是否成功，在git bash下输入：

        $ ssh -T git@github.com

        如果是第一次的会提示是否continue，输入yes就会看到：You’ve successfully authenticated, but GitHub does not provide shell access 。这就表示已成功连上github。

>（3）把本地仓库传到github上去之前还需要设置username和email，因为github每次commit都会记录他们。

        $ git config --global user.name "your name"

        $ git config --global user.email "your_email@youremail.com"

# Git基本常用命令如下：

```
   git init          把当前的目录变成可以管理的git仓库，生成隐藏.git文件。

   git add XX       把xx文件添加到暂存区去。

   git commit –m "xx"  提交文件 –m 后面的是注释。

   git status        查看仓库状态

   git diff  xx      查看xx文件修改了那些内容

   git log          查看历史记录

   git reset  –hard HEAD^ 或者 git reset  –hard HEAD~ 回退到上一个版本(如果想回退到100个版本，使用git reset –hard HEAD~100 )

   git reflog       查看历史记录的版本号id

   git checkout -- xx  把xx文件在工作区的修改全部撤销。

   git checkout -b branchname 创建并切换到分支
　　
   git checkout -b branchname origin/branchname 把远程origin的branchname分支创建到本地

   git rm XX          删除XX文件

   git remote add origin https://github.com/benlightning/webpack-vue-sample.git 关联一个远程库

   git push –u(第一次要用-u 以后不需要) origin master 把当前master分支推送到远程库

   git clone https://github.com/benlightning/webpack-vue-sample.git 从远程库中克隆

   git checkout –b dev  创建dev分支 并切换到dev分支上

   git branch  查看当前所有的分支

   git checkout master 切换回master分支

   git merge dev    在当前的分支上合并dev分支

   git branch –d dev 删除dev分支

   git branch name  创建分支

   git stash 把当前的工作隐藏起来 等以后恢复现场后继续工作(生成stash内容)

   git stash list 查看所有被隐藏的stash内容列表

   git stash apply 恢复被隐藏的文件（即工作区），但是stash内容不删除

   git stash drop 删除一条stash内容

   git stash pop 恢复工作现场的同时 也删除stash的内容

   git remote 查看远程库的信息

   git remote –v 查看远程库的详细信息

   git push origin master  git会把master分支推送到远程库对应的远程分支上
```
 

```
# 用github上的代码把本地的替换掉
git reset --hard
git pull

# 本地修改未提交，服务器上修改
git stash #保存现场 git stash list可以看到保存的信息
git pull
git stash pop #然后可以使用git diff -w +文件名 来确认代码自动合并的情况
```

当上传较大文件是报错：Git, fatal: The remote end hung up unexpectedly

git config --global http.postBuffer 524288000

或者在.git/config文件中添加：

```
[http]
postBuffer = 524288000
```

git中有部分内容始终提交不上去，或许git库中加入了clone到的其他的git库，使用git add -f dir后报错fatal: Pathspec 'dir' is in submodule 'dir'

解决办法是：删掉文件中的.git目录，git rm -r --cached dir然后按正常的add commit执行就可以了