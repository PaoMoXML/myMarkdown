![image-20230209111437597](https://cdn.jsdelivr.net/gh/PaoMoXML/image@main/img/image-20230209111437597.png)





1. git add -A  添加所有变化
2. git add -u  添加被修改(modified)和被删除(deleted)文件，不包括新文件(new)
3. git add .   添加新文件(new)和被修改(modified)文件，不包括被删除(deleted)文件
4. git commit -m "xxxx描述提交内容"
5. git push `仓库名别名` `分支名` 将修改内容提交到远程仓库
6. git pull  `仓库名别名` `分支名`  拉取最新内容并自动合并到本地
7. git log
8. git init：初始化一个git仓库
9. git clone：clone一个git仓库



提交基本流程

```shell
#提交修改 
git add -U
# 提交所有变化：git add -A
git commit -m "修改了xxx文档，新增了xxx文档"
git push origin master
#更新
git pull origin master
```

git解决提交乱码问题

```shell
git config --global core.quotepath false 
git config --global gui.encoding utf-8
git config --global i18n.commit.encoding utf-8 
git config --global i18n.logoutputencoding utf-8 
# bash 环境下(临时生效)：
export LESSCHARSET=utf-8
# cmd环境下(需要管理员权限)永久生效：
setx "LESSCHARSET" "utf-8" /m
```



### 账号密码自动保存

```shell
git config --global credential.helper store  // 自动保存账号密码
git config --system --unset credential.helper　　//重置验证设置
```



### 分支管理

- **git branch**：查看分支命令
- **git branch (branchname)**：创建分支命令
- **git checkout (branchname)**：切换分支命令
- **git merge**：合并分支命令
- **git branch -d (branchname)**：删除分支命令

```shell
git branch //查看本地所有分支 
git branch -r //查看远程所有分支
git branch -a //查看本地和远程的所有分支
git branch <branchname> //新建分支
git branch -d <branchname> //删除本地分支
git branch -d -r <branchname> //删除远程分支，删除后还需推送到服务器
git push origin:<branchname>  //删除后推送至服务器
git branch -m <oldbranch> <newbranch> //重命名本地分支
```

### fetch

```shell
$ git fetch <远程主机名> //这个命令将某个远程主机的更新全部取回本地
$ git fetch <远程主机名> <分支名> //注意之间有空格
```



### 提交历史查看 git log

- **--oneline** ：查看历史记录的简洁版本
- **--graph **：查看历史中什么时候出现了分支、合并
- **--reverse** ：逆向显示所有日志
- **--author **：查找指定用户的提交日志
- **--since、--before、 --until、--after**： 指定帅选日期
- **--no-merges** ：选项以隐藏合并提交



### Git 远程仓库

- **git remote add [alias] [url]**：添加远程仓库
- **git remote**：查看当前的远程仓库
- git remote -v：查看远程仓库地址
- **git fetch**、**git pull**：提取远程仓仓库
- **git push [alias] [branch]**：推送到远程仓库
- **git remote rm**：删除远程仓库
- **git pull [options] [<repository> [<refspec>…]]**

### git强制覆盖本地代码

1. **git fetch --all**：拉取所有更新，不同步；
2. **git reset --hard origin/master** ：本地代码同步线上最新版本(会覆盖本地所有与远程仓库上同名的文件)；
3. **git pull**：再更新一次（其实也可以不用，第二步命令做过了其实）

### 修改提交日志

[git 修改 commit log - 每天都要学进去一些 - 博客园 (cnblogs.com)](https://www.cnblogs.com/chenguangliang/p/13169738.html)

### 合并

[git rebase，看这一篇就够了 - 掘金 (juejin.cn)](https://juejin.cn/post/6969101234338791432)

### 将某文件的所有历史记录删除

1. 要删除的文件

   ```shell
   src/main/resources/application.yml
   ```

2. 进入到工程顶级目录

   输入命令（先修改`src/main/resources/application.yml`）

   ```shell
   git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch src/main/resources/application.yml' --prune-empty --tag-name-filter cat -- --all
   ```

3. 本地记录覆盖到Github,(所有branch以及所有tags)

   ```shell
   git push origin --force --all
   git push origin --force --tags
   ```

4. 确保没有什么问题之后,强制解除对本地存储库中的所有对象的引用和垃圾收集

   ```shell
   git for-each-ref --format='delete %(refname)' refs/original | git update-ref --stdin
   git reflog expire --expire=now --all
   git gc --prune=now
   ```

   

### 暂存工作区

```shell
// 暂存工作区，将修改未提交的内容暂存
$ git stash
Saved working directory and index state WIP on theBaseXmlDev: b93a88b98 【bug修改】textPlay功能bug修改

// 从暂存工作区中取出存储的内容，并将其从暂存中删除
$ git stash pop
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   epoint-etrading-web/src/main/resources/jdbc.properties

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (5908ae7ff9dd93be9a11f079f71013c9195dbfae)


// 标记暂存的内容
$ git stash save 'jdbc修改'
Saved working directory and index state On theBaseXmlDev: jdbc修改

// 查看暂存内容list
$ git stash list
stash@{0}: On theBaseXmlDev: jdbc修改

// 恢复到某一个暂存内容，注意：使用后并不会将暂存删除
$ git stash apply 0
On branch theBaseXmlDev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   epoint-etrading-web/src/main/resources/jdbc.properties

no changes added to commit (use "git add" and/or "git commit -a")

//手动删除暂存内容
$ git stash drop 0
Dropped refs/stash@{0} (d59d04c2a40979e1c46fade789e322faf80ea5cb)
```





### .gitignore

```bash
#               表示此为注释,将被Git忽略
*.a             表示忽略所有 .a 结尾的文件
!lib.a          表示但lib.a除外
/TODO           表示仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
build/          表示忽略 build/目录下的所有文件，过滤整个build文件夹；
doc/*.txt       表示会忽略doc/notes.txt但不包括 doc/server/arch.txt
 
bin/:           表示忽略当前路径下的bin文件夹，该文件夹下的所有内容都会被忽略，不忽略 bin 文件
/bin:           表示忽略根目录下的bin文件
/*.c:           表示忽略cat.c，不忽略 build/cat.c
debug/*.obj:    表示忽略debug/io.obj，不忽略 debug/common/io.obj和tools/debug/io.obj
**/foo:         表示忽略/foo,a/foo,a/b/foo等
a/**/b:         表示忽略a/b, a/x/b,a/x/y/b等
!/bin/run.sh    表示不忽略bin目录下的run.sh文件
*.log:          表示忽略所有 .log 文件
config.php:     表示忽略当前路径的 config.php 文件
 
/mtk/           表示过滤整个文件夹
*.zip           表示过滤所有.zip文件
/mtk/do.c       表示过滤某个具体文件
 
被过滤掉的文件就不会出现在git仓库中（gitlab或github）了，当然本地库中还有，只是push的时候不会上传。
 
需要注意的是，gitignore还可以指定要将哪些文件添加到版本管理中，如下：
!*.zip
!/mtk/one.txt
 
唯一的区别就是规则开头多了一个感叹号，Git会将满足这类规则的文件添加到版本管理中。为什么要有两种规则呢？
想象一个场景：假如我们只需要管理/mtk/目录中的one.txt文件，这个目录中的其他文件都不需要管理，那么.gitignore规则应写为：：
/mtk/*
!/mtk/one.txt
 
假设我们只有过滤规则，而没有添加规则，那么我们就需要把/mtk/目录下除了one.txt以外的所有文件都写出来！
注意上面的/mtk/*不能写为/mtk/，否则父目录被前面的规则排除掉了，one.txt文件虽然加了!过滤规则，也不会生效！
 
----------------------------------------------------------------------------------
还有一些规则如下：
fd1/*
说明：忽略目录 fd1 下的全部内容；注意，不管是根目录下的 /fd1/ 目录，还是某个子目录 /child/fd1/ 目录，都会被忽略；
 
/fd1/*
说明：忽略根目录下的 /fd1/ 目录的全部内容；
 
/*
!.gitignore
!/fw/ 
/fw/*
!/fw/bin/
!/fw/sf/
说明：忽略全部内容，但是不忽略 .gitignore 文件、根目录下的 /fw/bin/ 和 /fw/sf/ 目录；注意要先对bin/的父目录使用!规则，使其不被排除。
```

> .gitignore 前 push 了也可以忽略，使用 git rm -r --cached . 然后 git add . 再 commit, push

### 代理

```shell
//设置代理
//http || https
git config --global http.proxy 127.0.0.1:7890
git config --global https.proxy 127.0.0.1:7890

//sock5代理
git config --global http.proxy socks5 127.0.0.1:7891
git config --global https.proxy socks5 127.0.0.1:7891

//查看代理
git config --global --get http.proxy
git config --global --get https.proxy

//取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy

```

