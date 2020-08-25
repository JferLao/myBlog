




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
**git checkout 用于切换分支或恢复工作树文件**

```
git chehckout
```

**git branch 创建与删除分支**
```
git branch
```
