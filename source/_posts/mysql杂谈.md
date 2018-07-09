---
title: mysql杂谈
date: 2018-04-03 14:36:34
tags: [mysql]
---

常用的进程间通信方式有:管道，命名管道，命名字，TCP套接字，Unix域套接字。而Mysql提供的连接方式从本质上看都是上述提及的进程通信方式

1. TCP/IP

   TCP/IP套接字的方式是MySQL在任何平台上都提供的连接方式，也是用的最多的。一般情况客户端一台机器去连接服务器另一台机器。两台机器之间就是通过TCP/IP连接。客户端会向服务器Mysql实例发出TCP/IP连接请求，连接成功

2. 命名管道和共享内存

   Windows2003，vista及在此之上的平台，如果两个需要进程通信的进程在同一台机器上，那么可以使用命名管道，配置文件启用—enable-named-pipe。也可以使用共享内存的连接方式，只需要进行配置



在与数据库进行交互操作的时候一些时间资源的比较

~~~java
        /**
         * 测试数据库连接对象消耗的时间
         */
        String sql = "select * from mybatis WHERE id = ?";
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;

        long beforeTimeOffset = 0; //创建Connection对象前时间
        long afterTimeOffset = 0L; //创建Connection对象后时间
        long executeTimeOffset = 0; //创建Connection对象后时间

        // 这是和数据库连接的对象，这就是资源
        Connection connection = null;
        Class.forName("com.mysql.jdbc.Driver");
        beforeTimeOffset = System.currentTimeMillis();
        System.out.println(beforeTimeOffset);

        connection = DriverManager.getConnection("jdbc:mysql://rm-bp18t9bzt23446tlvo.mysql.rds.aliyuncs.com:3306/activemq_authorization", "root", "Lgeekrds2017");
        afterTimeOffset = System.currentTimeMillis();
        System.out.println(afterTimeOffset);
        System.out.println("create cost:" + (afterTimeOffset - beforeTimeOffset));

        preparedStatement = connection.prepareStatement(sql);
        System.out.println("precompile cost:" + (System.currentTimeMillis() - afterTimeOffset));
        preparedStatement.setObject(1, 1L);
        resultSet = preparedStatement.executeQuery();
        System.out.println("execute cost:" + (System.currentTimeMillis()-afterTimeOffset));
~~~

从此图可知，创建一个Connection对象，用了毫秒，而真正执行sql的时间却只有毫秒

一个connection对象的创建代价可以说是相当大，试想一下，如果我们在web应用中，为用户的每一个请求就操作一次数据库，当有10000个在线用户并发操作之后，对计算机而言，仅仅是创建connection对象的时间就要