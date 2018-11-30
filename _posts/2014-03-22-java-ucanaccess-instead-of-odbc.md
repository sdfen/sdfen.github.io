---
layout: post
title:  "Java 抛弃Odbc,使用UCanAccess连接Access数据库"
date:   2014-03-22
image: password.jpg
description: 快速连接Access数据库
category: java
tags: [java, jdbc, odbc, UCanAccess, Access, mdb, accdb]

---

UCanAccess是一个开源的JDBC驱动，可以方便的读写access数据库，而且还跨平台，不用再依赖繁琐的odbc去连接了

## 添加支持库
- [UCanAccess](http://ucanaccess.sourceforge.net/site.html) (ucanaccess-2.x.x.jar)
- [HSQLDB](http://hsqldb.org/) (hsqldb.jar, version 2.2.5 or newer)
- [Jackcess](http://jackcess.sourceforge.net/) (jackcess-2.x.x.jar)
- [commons-lang](http://commons.apache.org/proper/commons-lang/) (commons-lang-2.4.jar, or newer)
- [commons-logging](http://commons.apache.org/proper/commons-logging/) (commons-logging-1.0.4.jar, or newer)

commons-lang建议使用2.6版，最新版运行时会缺少类

至于导入jar就不啰嗦了

## 代码实现
支持mdb和accdb

{% highlight java %}
Connection conn=DriverManager.getConnection(
        "jdbc:ucanaccess://C:/__tmp/test/zzz.accdb");
Statement s = conn.createStatement();
ResultSet rs = s.executeQuery("SELECT * FROM Person");
while (rs.next()) {
    System.out.println(rs.getString(1));
}
{% endhighlight %}

