
###  Auto commit

```shell
crontab -e


//`0 1 * * *` 表示每天的第1小时（凌晨1点）执行。
0 1 * * * cd /path/to/repository && git push origin master

```

将”prepare-commit-msg.sample”文件复制为”prepare-commit-msg”，去掉文件名中的”.sample”后缀，这样Git就会在每次提交之前运行该脚本。确保脚本具有可执行权限，可以使用chmod命令给脚本添加权限。
然后，打开脚本文件，在文件的合适位置添加以下代码：

```
#!/bin/sh

# Add date to git commit message automatically
DATE=(date "+%Y-%m-%d")
echo "DATE 1">"1"

```

[Git 自动将日期添加到git提交消息中|极客教程 (geek-docs.com)](https://geek-docs.com/git/git-questions/40_git_add_date_to_git_commit_message_automatically.html)
[Git 定时任务中进行 Git push|极客教程 (geek-docs.com)](https://geek-docs.com/git/git-questions/23_git_git_push_via_cron.html#ftoc-heading-3)

