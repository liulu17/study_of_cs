1. 创建分支
   git branch test
2. 进入分支
   git checkout test
3. 本地分支关联远程分支
   git branch --set-upstream-to=origin/test test
4. 本地直接创建test1并与remote关联
   git checkout -b test1 origin/test1
5. 本地直接与remote关联，但是处于游离状态
   git checkout origin/test1
6. 查看远程仓库信息
   git remote show origin