




## 配置user信息
```
$ git config --local user.name '用户名'	//local只对当前仓库有效
$ git config --global user.email '邮箱地址'  //global对当前用户所有仓库有效
$ git config --system    //对登录的电脑用户都有用
$ git config --list      //列出全部配置
```

## 建立Git仓库
1. 把现有的代码所在文件夹
```
$ cd 项目代码所在的文件夹
$ git init
```

2. 新建的项目直接用Git管理
```
$ cd 某个文件夹
$ git init  your_project   //当前路径下创建和项目名称同名的文件夹
$ cd your_project
```

3. 往仓库里添加文件
```
工作目录 > git add files >暂存区 > git commit > 历史版本
```

**Git add 添加文件**

```
gir add  //提交文件
git add -u 	//把已经关联的文件全部提交
```

**git rm 移除文件**

```
git rm  <fileName> //移除文件
```

**Git commit 提交记录**

```
git commit -m '消息'  //提交更改
git commit --amend    //修改最新的commit 的message
```

4. 变成Git仓库内文件名
```
git mv <oldfileName> <newfileName>
```

5. 查看状态
**Git log 打印提交日志**

```
git log   //查看提交日志
git log -n(number) //查看最近number次的提交日志
git log --oneline //简洁查看
git log --all	//查看所有分支
git log --graph 查看图形化的 log 地址
git help --web log //跳转到git log 的帮助文档网页
```

**git status 查看 git工作状态**

```
git status
```

**gitk 图形界面工具查看版本历史**

```
gitk
```

6. commit/tree和blob三个对象之间的关系
**1个commit对应一棵树,这棵树的对应的视图,存储commit的所有文件快照** 

tree里面含有所有文件夹(文件树tree)或文件(blob)、hash值·文件（夹）名字,blob只有一份,只要文件内容相同则blob相同


7. 查看文件类型
 ```
find .git/objects -type f		//找出所有索引存储在git 目录的文件
git cat-file -t <hash>			//查看文件类型
```

8. 创建分支
**git checkout 重写工作区**

```
git chehckout	//把当前目录所有修改的文件 从HEAD中签出并且把它恢复成未修改时的样子
git checkout -b //创建新分支并切换到对应分支上
git checkout master     //取出master版本的head
git checkout master file_name  //放弃当前对文件file_name的修改
git checkout tag_name    //在当前分支上 取出 tag_name 的版本
```

**git branch 创建与删除分支**

```
git branch
git branch -av  //列出全部分支
git branch -d <分支名> //清除分支
git branch -D <分支名> //(强制)清除分支
```

9. 比较差异
```
git diff commit1 commit2   //比较commit1和commit2的差异
git diff --cached //比较变更差异
git diff branch1 branch2 filename //比较分支1和分支2关于filename文件的差异
```

10. 复位HEAD指定状态
```
git reset HEAD	//暂存区恢复成HEAD的一样
git reset --hard HEAD //恢复到之前的状态
git reset  --hard hash	//消除最近的几次提交(commit后想取消)
```

11. 命令用于将更改储藏在脏工作目录中
```
git stash 把当前工作区的内容放入暂存区
git stash pop 把暂存区的内容恢复到工作区，且删除
git stash apply把暂存区的内容恢复到工作区，且保留
```

12. 本地与远程仓库关联
```
git remote add github github地址	
```

13. 本地与远程仓库操作
```
git fetch githup 拉取远程版本库
git merge -h 查看合并帮助信息
git merge --allow-unrelated-histories githup/master 合并githup上的master分支（两分支不是父子关系，所以合并需要添加 --allow-unrelated-histories）
git push githup 推送同步到githup仓库
```


## 常用Git场景
1. 删除不需要的分支
```
git branch -d <分支名> //清除分支
git branch -D <分支名> //(强制)清除分支
```
2. 修改最新commit的message
```
git commit --amend	//进入变更页  用i写入 :wq!保存并退出
```
3. 修改老旧的commit的message
```
git rebase -i 进入交互界面 根据提示输入内容 选择r命令
```
4. 把多个commit整理成1个
```
git rebase -i 进入交互界面 根据提示输入内容 选择s命令
```
5. 暂存区恢复成HEAD的一样
```
git reset HEAD
```
6. 工作区恢复成暂存区
```
git checkout
```
7. 取消暂存区部分文件的更改(add后想取消)
```
git reset Head <fileName>
```
8. 消除最近的几次提交(commit后想取消)
```
git reset  --hard hash
```
9. 比较两个不同commit的文件的差异
```
git diff branch1 branch2 filename //比较分支1和分支2关于filename文件的差异
```
10. 恢复文件原来的状态
```
git reset --hard HEAD 恢复到之前的状态
```
11. 之前任务修改,暂缓此时任务
```
git stash 把当前工作区的内容放入暂存区
git stash pop 把暂存区的内容恢复到工作区，且删除
git stash apply把暂存区的内容恢复到工作区，且保留
```
12. 将Git仓库备份到本地
```
git clone --bare <文件路径>协议
```
13. 和远端仓库发生关联
```
git remote add 
```
14. 将本地仓库同步到Github
```
git remote add github github地址			//从本地和远程仓库关联
git fetch githup 拉取远程版本库
git merge -h 查看合并帮助信息
git merge --allow-unrelated-histories githup/master 合并githup上的master分支（两分支不是父子关系，所以合并需要添加 --allow-unrelated-histories）
git push githup 推送同步到githup仓库
```

15. 使用规则
```
1：push前一定先pull
2：合并代码必须两人结对
3：合并冲突，非自己的变动保持原样，和自己冲突的代码找相应的代码提交人确认如何解决冲突
4：合并完成后，保证本地能编译能运行再push
5：合并到主干的代码必须通过测试，必须通过代码review
6：不同的功能从主干上拉新分支进行开发工作
7：分支的命名需要加上，拉取人＋拉取说明
8：上完线的分支要及时清理
```