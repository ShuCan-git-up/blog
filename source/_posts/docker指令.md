---
title: docker指令
date: 2018-07-09 11:46:21
tags: [docker] [jenkins]
---



##### RUN

构建容器时执行的命令

有两种格式

+ RUN <command>  			--基本的linux指令
+ RUN ["executable", "param1", "param2"]  		--executable可以理解为可行执行文件，后面就是两个参数

##### CMD

功能为容器启动时要运行的命令，在构件时并不运行

传递的参数一定要是双引号。docker解析的是一个JSON array



##### LABLE

功能是为镜像指定标签

+ LABLE <key> = <value> <key> = <value> ...

换行可以使用\符号

LABEL multi.label1="value1" \

multi.label2="value2" 

说明：LABEL会继承基础镜像种的LABEL，如遇到key相同，则值覆盖

 ##### ENV

设置环境变量

+ ENV <key> <value>
+ ENV <key>=<value> ...

两者的区别就是第一种是一次设置一个，第二种是一次设置多个

##### ADD

一个复制命令，把文件复制到镜像中

如果把虚拟机与容器当做两台linux服务器的话，命令就类似于scp，只是scp需要用户名和密码验证，而ADD不用

+ ADD <src>…..<dest>
+ ADD ["<src>",…"<dest>"]

<dest>路径的填写可以是容器内的绝对路径，也可以是相对于工作目录的相对路径

 <src>可以是一个本地文件或者是一个本地压缩文件，还可以是一个url

 如果把<src>写成一个url，那么ADD就类似于wget命令

 

##### COPY

复制

+ COPY <src>…..<dest>
+ COPY ["<src>",…"<dest>"]

与ADD的区别

COPY的<src>只能是本地文件，其他用法一致

##### ENTRYPOINT

功能是启动时的默认命令

+  ENTRYPOINT ["executable", "param1", "param2"]
+ ENTRYPOINT command param1 param2





##### VOLUME

可实现挂载功能,可以将内地文件夹或者其他容器得文件夹挂在到这个容器中

+ VOLUME ["/data"]





### jenkins实现docker项目的自动部署

Poll SCM的功能是每隔一定时间检查源码是否更新。有的话就自动构建

*/60 * * * *

含义是每隔60分钟检查一次git源码