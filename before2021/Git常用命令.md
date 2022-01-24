# <center>2019-8-8

## git命令
- 初始化：git init:初始化当前目录
- 提交修改
  - git add <文件名>:将改动提交
  - git commit -m <消息>:将改动统一上传，并加上消息
- 查看
  - git status：查看当前仓库的状态
  - git diff <文件名>:查看改动
      - 1. 比较工作区与暂存区
          git diff 不加参数即默认比较工作区与暂存区
      - 2. 比较暂存区与最新本地版本库(本地库中最近一次commit的内容)
          git diff --cached <文件名>
      - 3. 比较工作区与最新本地版本库
          git diff HEAD <文件名>  如果HEAD指向的是master分支，那么HEAD还可以换成master
      - 4. 比较工作区与指定commit-id的差异
          git diff commit-id <文件名>
      - 5. 比较暂存区与指定commit-id的差异
          git diff --cached commit-id <文件名>
      - 6. 比较两个commit-id之间的差异
          git diff commit-id commit-id
  - git log：查看日志
- 版本更改
  - git reset --hard <版本号或者head^>:版本回退
  - git checkout -- < file >:返回最近一次commit或者add的状态，即撤销未被提交的修改
- 连接github
  - git remote add origin git@github.com:< 账户 >/< 库名 >.git
  - git push:将本地库提交到github
  - git clone：将github库下载到本地
  - git clone -b <分支名> <仓库地址>：下载指定分支
- 分支：
  - git branch < 分支名 >:创建分支
  - git checkout < 分支名 >:切换分支
  - git checkout -b < 分支名 >:创建并切换分支
  - git merge < 分支名 >:合并给定分支到当前分支
  - git branch -d < 分支名 >:删除分支
  - git push origin :< 分支名 >：删除远程分支
- 标签
  - git tag '标签名' < 版本号 >：打标签，无版本号默认给最新提交的commit打标签
  - git show
  - git tag -d '标签名':删除标签
