# git 常用命令

#### 同步远程仓库
创建一个空的远程仓库：登录github =》 new repository
初始化本地仓库 git init
#### 编辑文件
编辑区添加到暂存区：git add .
暂存区提交到分支：git commit -m "备注"
创建远程主机名：git remote
同步远程仓库：git push -u origin master
克隆项目：git clone url
拉取项目代码: git pull
#### 团队协作
项目拥有者进入项目的settings选项 =》 Collaborators =》 添加项目成员
给成员发送邀请链接，程序需要确认。此后项目成员可以使用自己的用户名和密码同步此项目。
在开发web项目的过程中，每个成员需要创建一个自己的分支，在自己分支上开发，没有问题在合并到master分支。
#### 分支管理
查看分支:git branch，默认只有master分支
创建分支 git branch teacher，创建teacher分支
切换分支：git checkout teacher
在自己分支上修改文件并提交。
合并分支：切换至master分支，git merge dev
本地分支推送至远程分支:git push origin feature-branch:feature-branch
远程分支拉倒本地：git checkout -b feature-branch origin/feature-branch