




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
git status //查看 git工作状态
git log   //查看提交日志
```

Git add参数
```
gir add -m (message) //提交时的信息
git add -u 	//把已经关联的文件全部提交
```