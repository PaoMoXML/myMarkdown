![image-20230209111437597](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20230209111437597.png)





1. git add -A  添加所有变化
2. git add -u  添加被修改(modified)和被删除(deleted)文件，不包括新文件(new)
3. git add .   添加新文件(new)和被修改(modified)文件，不包括被删除(deleted)文件
4. git commit -u "xxxx描述提交内容"
5. git push `仓库名别名` `分支名` 将修改内容提交到远程仓库
6. git pull  `仓库名别名` `分支名` `分支名`  拉取最新内容并自动合并到本地
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

