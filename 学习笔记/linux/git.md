![image-20230209111437597](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20230209111437597.png)





1. git add -A  添加所有变化
2. git add -u  添加被修改(modified)和被删除(deleted)文件，不包括新文件(new)
3. git add .   添加新文件(new)和被修改(modified)文件，不包括被删除(deleted)文件
4. git commit -u "xxxx描述提交内容"
5. git push `仓库名别名` `分支名` 将修改内容提交到远程仓库
6. git pull  `仓库名别名` `分支名`  拉取最新内容并自动合并到本地
7. git log
8. git init：初始化一个git仓库
9. git clone：clone一个git仓库



### 分支管理

- **git branch**：查看分支命令
- **git branch (branchname)**：创建分支命令
- **git checkout (branchname)**：切换分支命令
- **git merge**：合并分支命令
- **git branch -d (branchname)**：删除分支命令



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
- **git fetch**、**git pull**：提取远程仓仓库
- **git push [alias] [branch]**：推送到远程仓库
- **git remote rm**：删除远程仓库
- **git pull [options] [<repository> [<refspec>…]]**



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
