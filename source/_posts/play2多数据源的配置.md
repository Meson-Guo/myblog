---
title: play2 多数据源的配置
date: 2016-08-28 20:35:56
categories: 技术类
tags: 
- mysql
- play
- postgresql
decription: 在项目中我们必然会碰到多数据源联合开发的问题，本篇文章讲述play2对Mysql和Postgresql的多数据源配置
---
<!-- more -->
## 前瞻
   在平时我么做项目时，因为可能会碰到各种各样的需求，所以必然会碰到多数据源协同开发的情况，
   play是一种高效快速开发的开源框架，目前在业内也得到了一定认可，但国内使用的不多，大部分使用者都在国外，所以你懂得，国内的资料并不是很多。
   play的多数据源配置主要分为两个模块：
           一、application.conf的配置
		       主要针对数据源的配置
		   二、Model的配置
		      主要针对所使用哪一个数据源进行配置
1、application.conf的配置
  不多说了直接上代码：
```
  #mysql connection
  db.mysql.driver=com.mysql.jdbc.Driver
  db.mysql.url="mysql://username:password@localhost/test?characterEncoding=utf-8"

  #postgresql  connection
  db.default.driver=org.postgresql.Driver
  db.default.url="jdbc:postgresql://localhost:5432/test?characterEncoding=utf-8"
  db.default.user="postgres"  
  db.default.password="password" 
```
  这里注意一般我们使用db.default.*来进行数据源的配置，其实这里的default就是数据源的名字，系统默认叫default 也可叫其他的
  就像这里我叫它mysql一样。这样就可以了吗 当然不行了，因为play的数据操作是通过model和Ebean来实现的，所以必须要给新加的数据源指向model
  即为：
       ebean.default="models.*"
       ebean.mysql="models.*"
	   到此配置文件的配置就结束了
	   
2、对Model的配置
  
  现在两个数据源都已经配置好了，单独使用任意一个都没有问题，但是你改怎么告诉它使用哪一个数据源呢
  public static Finder<Long,ColumnInfo> find = new Finder<Long,ColumnInfo>("mysql",Long.class,ColumnInfo.class);
  我们可以在Model中在生成Finder对象时指定，如上：
  其实从Finder的一个构造函数也可以看出来
```
        /**
          * Creates a finder for entity of type <code>T</code> with ID of type <code>I</code>, using a specific Ebean server.
          */
        public Finder(String serverName, Class<I> idType, Class<T> type) {
            this.type = type;
            this.idType = idType;
            this.serverName = serverName;
        }
```
   当然如果你没有指定，如下：
```
  public static Finder<Long,ColumnInfo> find = new Finder<Long,ColumnInfo>(Long.class,ColumnInfo.class);
```
  系统就默认的访问default，这里就是Postgresql。  好了，接下来你就可以在play框架中愉快的使用多数据源了
