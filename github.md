**GitHub的本地结构**

```markdown
工作区：git add 命令提交到暂存区
暂存区：git commit命令提交的本地库
临时存储区域，可以提交的本地库，也可以撤回的工作区
本地库：真正存储每个历史版本的信息
```

**代码托管中心是干嘛的**

他的任务是帮我们维护远程库

本地库与远程库的交互方式，两种方式

1.团队内部协作

2.跨团对协作

**托管中心种类**

```markdown
局域网环境下：可以搭建Gitlib服务器作为代码托管中心，GitLab可以自己搭建
外网环境下：可以有github或gitee作为代码托管中心，
```

**初始化本地创建**

1.创建一个文件夹：GitResponse

2.打开github终端 git bean hare

进去之后先把编码设置为UTF-8

![image-20201121212518787](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121212518787.png)

git中命令与Linux是一样的

![image-20201121212716948](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121212716948.png)

设置签名

![image-20201121212921280](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121212921280.png)

本地仓库初始化：

进入GitResponse文件，在通过git init 命初始化空的repository

![image-20201121213233474](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121213233474.png)

git目录是隐藏的

```markdown
ll -la 命令 查询隐藏文件
```

注意事项：git目录下的本地库相关的子目录和子文件不要删除和乱改

## **Git常用命令**

### **add和commit命令**

添加文件 add  提交文件  commit

展示：

创建一个文件在GitRespnonse仓库文件夹下:Demo.txt

通过git add 把文件放到暂存区中

![image-20201121214521919](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121214521919.png)

最终通过 git commit -m "备注" 加入到本地仓库

![image-20201121215542430](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121215542430.png)

### **status命令**

git status 看的是工作区和暂存区的状态

创建一个文件，查看状态

![image-20201121215647989](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121215647989.png)

红色字体表示此文件还有进行管理

然后将Demo2.txt通过 git add命令提交：暂存区，再次查看

![image-20201121215903338](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121215903338.png)

changes to be committed 表示Demo2.txt 文件还有加入到本地库

![image-20201121220310375](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121220310375.png)

现在修改了Demo2.txt文件,并查看状态

![image-20201121225303600](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121225303600.png)

modified：Demo2.txt ：表示demo2.txt 已经修改过了

重新添加到：暂时区

![image-20201121225502864](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121225502864.png)

然后将暂时区的文件提交的本地库

![image-20201121225605628](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121225605628.png)

### **log命令**

**git log 命令可以让我们查看提交的，显示从最近到最远的日志。**

![image-20201121225943275](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121225943275.png)

当历史记录过多的时候，查询日志的时候，有分页效果

下一页：空格

上一页：b

日志展示方式

```markdown
方式1 git log ———》分页方式
方式2 git log --pretty=oneline
方式3	git log --oneline
方式4	git reflog  多了信息HEAD@数字  这个数字的含义 ：表示指定会到当前这个历史版本需要走多少步
```

### **reset命令**

reset命令：前进或者后退历史版本

复制：在终端中可以选中复制

![image-20201121231840176](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121231840176.png)

【1】hard参数：

git reset --hard[索引]

本地库的指针移动的同时 重置暂存区  重置工作区

![image-20201121233212343](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121233212343.png)

【2】mixed参数：

本地库的指针移动的同时  重置暂存区  但是工作区不动

![image-20201121233312238](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121233312238.png)

【3】soft参数：

本地库的指针移动的时候 暂存区、工作区都不动

![image-20201121233417478](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121233417478.png)

总结：以后用的最多的就是 第一种hard参数

### **删除文件_找会本地库删除的文件**

【1】新建一个Test2.txt文件

【2】将它add到暂存区中

【3】再通过commit提交到本地区中

【4】删除工作区中的Test2.txt

![image-20201121233839717](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121233839717.png)

【5】将删除操作同步到暂存区

![image-20201121234435957](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121234435957.png)

![image-20201121234453776](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121234453776.png)

【6】将删除操作同步到本地库

![image-20201121234557348](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121234557348.png)

【7】.查看日志

![image-20201121234644543](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121234644543.png)

【8】找会本地中删除的文件，实际上就是将历史版本切换到刚才添加文件的那个版本

### **找回暂存区删除的文件**

【1】删除工作区数据

![image-20201121235106583](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121235106583.png)

【2】同步到暂存区

![image-20201121235155209](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121235155209.png)

后悔了，恢复暂存区的数据

![image-20201121235343974](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201121235343974.png)

### **diff命令**

【1】先创建文件，添加到暂存区，再提交到本地库

![image-20201122092933013](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122092933013.png)

【2】更改工作区的Test3.txt中的内容，增加内容

导致：工作区与暂存区 不一致 ，比对

![image-20201122093125897](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122093125897.png)

总结：git diff[方法名] --->将工作区的文件与暂存区的文件进行比较

多个文件的比对

![image-20201122093353590](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122093353590.png)

总结：git diff ---》比较工作区中暂存区中 所有文件的差异

比较暂存区与工作区中差别

git diff【历史版本】【文件名】--->比较暂存区与工作区中内容

## 分支

什么是分支：

在版本控制过程中， 使用多条线同时推进多个任务，这里面说的多条线，就是多个分支

![image-20201122100136923](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122100136923.png)

分支好处：
同时多个分支可以并行开发，互相不耽误，互相不影响，提高开发效率

如果有一个分支功能开发失败，直接删除这个分支就可以了，不会对其他分支产生任何影响

### **查看、创建、切换 分支**

【1】在工作区创建一个Test4.txt,然后提交到暂存区，提交到本地库

![image-20201122100837684](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122100837684.png)

![image-20201122100909766](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122100909766.png)

[2]查看分支

![image-20201122100931054](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122100931054.png)

【3】创建分支，再查看

![image-20201122101120555](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122101120555.png)

【4】切换分区

![image-20201122101304431](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122101304431.png)

创建远程库

远程库的地址：

![image-20201122111654358](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122111654358.png)

远程库地址比较长，每次复制比较麻烦

https://github.com/lianqingsen/GitResp1.git

再Git本地将地址保存，通过别名

查看别名:

![image-20201122111824774](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122111824774.png)

起别名

![image-20201122111941953](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122111941953.png)

推送操作

![image-20201122114000914](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122114000914.png)

克隆操作

![image-20201122115128597](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122115128597.png)

远程库修改的拉取操作

拉取操作pull操作，相当于fetch+merge

先是抓取操作：fetch

![image-20201122145815725](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122145815725.png)

在通过merge合并到工作区中。

远程库的拉取可以直接使用pull

SSH免密登陆

1进入用户的主目录 

```linux
cd ~
```

执行命令，生成一个.ssh的目录

![image-20201122153338518](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122153338518.png)

keygen ---> keygeneration

三次回车确定默认值

![image-20201122154108332](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122154108332.png)

打开id_rsa.pub文件，进行复制

打开githubz账号 点击头像 选择setting  再点击SSH and GPGkeys--->new SSH kye

![image-20201122154338371](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122154338371.png)

![image-20201122154514489](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122154514489.png)

IDEA继承Git

![image-20201122160803675](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122160803675.png)

在IDEA中初始化本地仓库

![image-20201122161053379](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122161053379.png)

![image-20201122165803327](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122165803327.png)

一般开发中先pull操作，在push操作，不会直接push操作

## **使用IDEA克隆远程库到本地**

![image-20201122171330133](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122171330133.png)

![image-20201122171424756](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201122171424756.png)

【1】团队开的的时候避免在一个文件中改代码

【2】在修改一个文件前，在push之前，先pull操作

