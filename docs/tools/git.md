
###  git commit hook

Cron表达式由6个字段组成，分别表示分钟、小时、日、月、周、命令

```shell
[Minute] [hour] [Day_of_the_Month] [Day_of_the_Year] [Day_of_the_Week] [command]

crontab -e


//`0 1 * * *` 表示每天的第1小时（凌晨1点）执行。
0 1 * * * cd /path/to/repository && git push origin master

sudo systemctl restart cron

```

将”prepare-commit-msg.sample”文件复制为”prepare-commit-msg”，去掉文件名中的”.sample”后缀，这样Git就会在每次提交之前运行该脚本。确保脚本具有可执行权限，可以使用chmod命令给脚本添加权限。
然后，打开脚本文件，在文件添加以下代码：

```
#!/bin/bash

# Add date to git commit message automatically
git status --short > $1

```

[Git 自动将日期添加到git提交消息中|极客教程 (geek-docs.com)](https://geek-docs.com/git/git-questions/40_git_add_date_to_git_commit_message_automatically.html)
[Git 定时任务中进行 Git push|极客教程 (geek-docs.com)](https://geek-docs.com/git/git-questions/23_git_git_push_via_cron.html#ftoc-heading-3)


### show_single_file_log

```
git log -p --follow -- <filename>
$ git log -- divLhsSkyAd.scala.html    # one-line summary info
$ git log -p divLhsSkyAd.scala.html    # detail about each “patch”
git diff --cached   Show git changes that has been added but not committed
git diff --cached src/main/scala/agt/Main.scala
```



### update submoudle

```
// idea 1
git submodule foreach --recursive git checkout .

idea2
rm submodule
git submodule update --init --recursive

idea3
git submodule update --remote --merge

```

### rm_submodule

```
# 逆初始化模块，其中{MOD_NAME}为模块目录，执行后可发现模块目录被清空
git submodule deinit {MOD_NAME} 
# 删除.gitmodules中记录的模块信息（--cached选项清除.git/modules中的缓存）
git rm --cached {MOD_NAME} 
# 提交更改到代码库，可观察到'.gitmodules'内容发生变更
git commit -am "Remove a submodule."

```

### delet_change
- 删除已有文件上的更改 `git reset --hard`
- 删除添加的文件 `git clean -f`