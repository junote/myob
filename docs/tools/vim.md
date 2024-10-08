
#### 无插件Vim编程技巧

##### 目录  
* 【 :E 】 浏览目录
* 【 – 】 到上级目录
* 【D】删除文件（大写）
* 【R】改文件名（大写）
* 【s】对文件排序（小写）
* 【x】执行文件
#####  缓冲区
* 【:ls】查看缓冲区
* :bnext      缩写 :bn  
* :bprevious   缩写 :bp  
* :blast  缩写 :bl  
* :bfirst 缩写 :bf
##### 窗口分屏浏览
- :He   全称为 :Hexplore  （在下边分屏浏览目录）
- :Ve 全称为 :Vexplore （在左边分屏间浏览目录，要在右边则是 :Ve!）

##### Tab页浏览目录
- :Te  全称是 :Texplorer 
- gt   – 到下一个页
- gT  – 到前一个页
- {i} gt   – i是数字，到指定页，比如：5 gt 就是到第5页
- :tabclose [i] – 如果后面指定了数字，那就关闭指定页，如果没有就关闭当前页
- vim 的 -p 参数来用Tab页的方式打开多个文件

##### Quickfix
比如我们正在浏览nginx的代码，这时，我想看看哪里用到了nginx的NGX_HTTP_VAR_INDEXED宏。于是，我可以在vim里输入如下的命令：

> **:grep -r –include=”*.[ch]” NGX_HTTP_VAR_INDEXED src/**

按回车返回vim后，用 【:cw 】把grep的输出取回

##### 关键字补全
【**Ctrl +N**】  – 当你按下这它时，你会发现Vim就开始搜索你这个目录下的代码，搜索完成了就会出现一个下拉列表
【**Ctrl + P**】 – 接下来你可以按这个键，于是回到原点，然后你可以按上下光标键来选择相应的Word。

##### 字符相关
【ga】 –  查看光标处字符的ascii码
【g8】 – 查看光标处字符的utf-8编码
【gf】  – 打开光标处所指的文件 （这个命令在打到#include头文件时挺好用的，当然，仅限于有路径的）
【\*】或【#】在当前文件中搜索当前光标的单词

##### 缩进相关
【>>】向右给它进当前行 【<<】向左缩进当前行
【=】  – 缩进当前行 （和上面不一样的是，它会对齐缩进）

##### 读取Shell命令相关
【:r!date】 插入日期


## **indent折叠命令**

操作：za，打开或关闭当前折叠；zM，关闭所有折叠；zR，打开所有折叠
`set  foldmethod=indent`
indent方式，vim会自动利用缩进进行折叠，我们可以使用现成的折叠成果．  
我们可以在(括号)折叠处输入以下命令：

zc 折叠  
zC 对所在范围内所有嵌套的折叠点进行折叠  
zo 展开折叠  
zO 对所在范围内所有嵌套的折叠点展开  
\[z 到当前打开的折叠的开始处。  
]z 到当前打开的折叠的末尾处。  
zj 向下移动。到达下一个折叠的开始处。关闭的折叠也被计入。  
zk 向上移动到前一折叠的结束处。关闭的折叠也被计入。

[无插件Vim编程技巧 | 酷 壳 - CoolShell](https://coolshell.cn/articles/11312.html)

