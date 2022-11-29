# Git使用

## git常用命令

- git config --global user.name 用户名 ——设置用户签名
- git config --global user.email 邮箱——设置用户邮箱
- git init ——初始化本地库
- git status ——查看本地库状态
- git add xxx ——添加某文件到暂存区
- git rm --cached xxx ——将暂存区中某文件删除
- git commit -m "日志信息" 文件名 ——将暂存区的文件提交到本地库
- git reflog 查看版本信息
- git log 查看版本详细信息
- git reset --hard 版本号 ——版本穿梭，回到过去某个版本
- git branch 分支名 ——创建分支
- git branch -v  ——查看分支
- git checkout 分支名 ——切换分支
- git merge 分支名 —— 把指定的分支合并到当前分支上（正常合并）
- git remote -v ——查看当前所有远程地址别名
- git remote add 别名 远程地址——给某远程地址起别名
- git push 别名 分支——推送本地库到远程库
- git pull 别名 分支——拉取远程库到本地库
- ssh-keygen -t rsa -C git邮箱——生成本地ssh文件
- git push origin master -f ——强行让本地分支覆盖远程分支