# Git基本操作



## 一、基本指令

##### 1. git init 

本地库初始化

```gas
gzc@DELL MINGW64 /f/WeChat
$ git init
Initialized empty Git repository in F:/WeChat/.git/
```



##### 2. git config

设置签名，作用是区别不同开发人员的身份；和代码托管中心的账号，密码没有任何关系；

**项目级别/仓库级别** （仅在当前本地库范围内有效）

```gas
gzc@DELL MINGW64 /f/WeChat (master)
$ git config user.name gzc

gzc@DELL MINGW64 /f/WeChat (master)
$ git config user.email gzc@163.COM

gzc@DELL MINGW64 /f/WeChat (master)
$ cat .git/config
[core]
        repositoryformatversion = 0
        filemode = false
        bare = false
        logallrefupdates = true
        symlinks = false
        ignorecase = true
[user]
        name = gzc
        email = gzc@163.COM
```

**系统用户级别** （登录当前操作系统的用户范围）

git config --global

```markdown
gzc@DELL MINGW64 ~
$ cat .gitconfig
[user]
        name = JachinGao
        email = winter_2013@163.com
[gui]
[credential]
        helper = store
```

**级别优先级：**就近原则，项目级别优先于系统用户级别；如果只有系统用户级别的签名，就以系统用户级别的签名为准；



##### 3. git status

查看工作区、暂存区的装填

```markdown
$ git status
On branch master

Initial commit

nothing to commit (create/copy files and use "git add" to track)
```



##### 4. git add

将工作区新建/修改的文件，添加到暂存区；（这里添加了一个hello.txt文件）

```markdown
gzc@DELL MINGW64 /f/WeChat (master)
$ git status
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        hello.txt

nothing added to commit but untracked files present (use "git add" to track)
```

使用add指令添加到暂存区

```
gzc@DELL MINGW64 /f/WeChat (master)
$ git add hello.txt

Gaozhaochen@DELL MINGW64 /f/WeChat (master)
$ git status
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   hello.txt
```

**git rm --cached** 从暂存区撤回，（使用该指令，又将该文件变为未追踪的状态）

```
gzc@DELL MINGW64 /f/WeChat (master)
$ git rm --cached hello.txt
rm 'hello.txt'

gzc@DELL MINGW64 /f/WeChat (master)
$ git status
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        hello.txt

nothing added to commit but untracked files present (use "git add" to track)
```



##### 5. git commit

将暂存区的内容提交到本地库

```
gzc@DELL MINGW64 /f/WeChat (master)
$ git commit -m "first commit"
[master (root-commit) f25aac0] first commit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 hello.txt

gzc@DELL MINGW64 /f/WeChat (master)
$ git status
On branch master
nothing to commit, working tree clean
```

**修改文件**，（这里修改hello.txt中的内容）

```gas
Gaozhaochen@DELL MINGW64 /f/WeChat (master)
$ git status
'On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

然后再使用git add，此时状态为：(工作区→暂存区)

```
Gaozhaochen@DELL MINGW64 /f/WeChat (master)
$ git add hello.txt

Gaozhaochen@DELL MINGW64 /f/WeChat (master)
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   hello.txt
```

使用git commit提交 （暂存区→本地库）

```
gzc@DELL MINGW64 /f/WeChat (master)
$ git commit -m "second commit"
[master 839db5f] second commit
 1 file changed, 3 insertions(+)
```

**修改提交信息（添加amend参数）**

git commit -amend

**git commit -m 和 git commit -am 区别**

```
git add .
git commit -m "some str"
```

```
git commit -am "some str"
```

* 针对第一步中的git  add .命令的作用就是将本地修改过的文件且已经追踪的文件添加到本地的暂存区，  然后使用git commit -m "str"命令将暂存区的代码提交到本地仓库；

* 第二步其实就相当于第一步的结合，但是有区别的是git commit -am 'str'命令只能提交已经追踪过且修改了的文件，去过是新增文件就必须使用第一步的命令；若文件没有被git监视（如新增的一个文件），此时使用该命令会报错。

  

##### 6. git log

查看历史记录

```
gzc@DELL MINGW64 /f/WeChat (master)
$ git log
commit 839db5f4366c69abe197fe9f3c82731a234033ac
Author: gzc <gaozhaochen@163.COM>
Date:   Sat Jul 25 11:18:43 2020 +0800

    second commit

commit f25aac003a1bb46a2677580cd2ded1948c8e73ba
Author: gzc <gaozhaochen@163.COM>
Date:   Sat Jul 25 11:07:48 2020 +0800

    first commit
```

**git log --pretty=oneline** (显示一行信息)

```
gzc@DELL MINGW64 /f/WeChat (master)
$ git log --pretty=oneline
b12a563c75f21aa55150ce91d18b7e11a8e192da 10 commit
55ae44df1fed47d76e71cba4addfe0a3eaccacd8 9 commit
52c7466b9a387fcd1ee46cbd70a7d6f1a818a0f5 8 commit
29c183a7f6b419c676a9b2508f148ad5973021fc 7 commit
e4555ce168b95367a41213f06e711ae8cc827699 6 commit
2c27ffb23afcc8d1ad893968a5073ca1e8292244 5 commit
e46d0fcb2dfc6ed82b5e7aecfeacf64af286a401 4 commit
1b0c686c0c5bc8e6a6df5e8552ea0086f97b3abd 3 commit
839db5f4366c69abe197fe9f3c82731a234033ac second commit
f25aac003a1bb46a2677580cd2ded1948c8e73ba first commit
```

**git log --oneline** (显示简要的一行信息)

```
gzc@DELL MINGW64 /f/WeChat (master)
$ git log --oneline
b12a563 10 commit
55ae44d 9 commit
52c7466 8 commit
29c183a 7 commit
e4555ce 6 commit
2c27ffb 5 commit
e46d0fc 4 commit
1b0c686 3 commit
839db5f second commit
f25aac0 first commit
```

**git reflog** 显示历史，只显示一行，并且显示指针（移动到指针要多少步）

HEAD@｛移动到当前版本需要多少步｝

```
gzc@DELL MINGW64 /f/WeChat (master)
$ git reflog
b12a563 HEAD@{0}: commit: 10 commit
55ae44d HEAD@{1}: commit: 9 commit
52c7466 HEAD@{2}: commit: 8 commit
29c183a HEAD@{3}: commit: 7 commit
e4555ce HEAD@{4}: commit: 6 commit
2c27ffb HEAD@{5}: commit: 5 commit
e46d0fc HEAD@{6}: commit: 4 commit
1b0c686 HEAD@{7}: commit: 3 commit
839db5f HEAD@{8}: commit: second commit
f25aac0 HEAD@{9}: commit (initial): first commit
```

前进后退

```
gzc@DELL MINGW64 /f/WeChat (master)
$ git reset --hard 2c27ffb
HEAD is now at 2c27ffb 5 commit

gzc@DELL MINGW64 /f/WeChat (master)
$ git reflog
2c27ffb HEAD@{0}: reset: moving to 2c27ffb
52c7466 HEAD@{1}: reset: moving to 52c7466
b12a563 HEAD@{2}: commit: 10 commit
55ae44d HEAD@{3}: commit: 9 commit
52c7466 HEAD@{4}: commit: 8 commit
29c183a HEAD@{5}: commit: 7 commit
e4555ce HEAD@{6}: commit: 6 commit
2c27ffb HEAD@{7}: commit: 5 commit
e46d0fc HEAD@{8}: commit: 4 commit
1b0c686 HEAD@{9}: commit: 3 commit
839db5f HEAD@{10}: commit: second commit
f25aac0 HEAD@{11}: commit (initial): first commit
```

git reset --hard HEAD^^ (使用^符号) 该指令只能后退

```
gzc$ git reset --hard HEAD^^^
HEAD is now at e4555ce 6 commit

gzc@DELL MINGW64 /f/WeChat (master)
$ git log --oneline
e4555ce 6 commit
2c27ffb 5 commit
e46d0fc 4 commit
1b0c686 3 commit
839db5f second commit
f25aac0 first commit
```



##### 7. git reset

reset命令的三个参数

--soft  

* 仅在本地库移动HEAD指针

--mixed  

* 在本地库移动HEAD指针
* 重置暂存区

--hard  

* 在本地库移动指针
* 重置暂存区
* 重置工作区

删除文件找回

前提，删除前，文件存在时的状态提交到了本地库；



## 二、分支操作

##### 1. git branch [分支名]

创建一个分支

```
gzc@DELL MINGW64 /f/WeChat (master)
$  git branch gzc-dev

gzc@DELL MINGW64 /f/WeChat (master)
$ git branch
  gzc-dev
* master
```



##### 2. git branch -v

查看分支

```
Gaozhaochen@DELL MINGW64 /f/WeChat (master)
$ git branch -v
  gzc-dev 1b0c686 3 commit
* master  1b0c686 3 commit
```



##### 3. git checkout [切换分支]

```
gzc@DELL MINGW64 /f/WeChat (master)
$ git checkout gzc-dev
M       hello.txt
Switched to branch 'gzc-dev'

gzc@DELL MINGW64 /f/WeChat (gzc-dev)
$ git branch -v
* gzc-dev 1b0c686 3 commit
  master  1b0c686 3 commit
```

```
 //创建并切换分支
 git branch branch_1 
 git checkout branch_1

 //或者使用
 git checkout -b branch_1
```

删除分支

```
git branch -d iss53
```



##### 4. git merge

合并分支

* 第1步：切换到接受修改的分支

  git checkout [被合并分支名]

* 第2步：执行merge命令

  git merge [有新内容分支名]
  
  

## 三、远程库

##### 1. git pull

pull = fetch + merge

**解决冲突**

* 如果不是基于github远程库的最新版所做的修改，不能推送，必须先拉取；
* 拉取下来后如果进入冲突状态，则按照“分支冲突解决”操作解决即可；



2. git remote 

   git remote -v 查看当前状态

   ```
   git remote add origin https://gitee.com/JachinGao/GitTest.git
   git push -u origin master
   ```