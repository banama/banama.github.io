---
layout: default_post
title: javabean实例及数据库点滴
categories: share
permalink: /share/javabeanFir.html
---

Javabean可以对数据进行良好的封装，可以理解微MVC模式的model，这篇文章属于HelloWorld级别，没有什么高深内容，只有点滴。

与数据库打过一些交道，甚至配置过略微高大上的Mysql-Cluster，但是从心底里对数据库操作不是那么知根知底，不算一个合格的DBer。我一直认为，作为一个程序员，三天不敲代码就会手生，数据库的东西又是琐碎，更是如此，许久不碰，不生才怪。

###数据库
安装mysql
其中包括两部分，mysql-server和mysql-client， apt-get install * 很简单
安装成功，首先启动mysql-server，执行命令  `/etc/init.d/mysql start`
mysql-client是mysql的命令行管理，执行命令  `mysql -u root -p`,即可进入root用户管理,默认状态下，root用户是没有密码的。
为了安全，数据库一定要有密码，初始状态下设置密码，可使用命令`mysqladmin -u root -p password newpasswd `,也可使用此命令修改密码，按提示输入原来密码后设置成功。
数据库的用户信息都存在mysql库的user表中，更新表中内容也可修改密码
`UPDATE user SET Password = PASSWORD('newpass') WHERE user = 'root';`
`flush privileges；`
以上两条命令即可更改表中密码信息，如果忘记了密码呢？也是通过修改表中信息来修改，可是没有密码无法登录数据库怎么办？
有办法的，执行命令`mysqld_safe -skip-grant-tables &`  `mysql -u root`即可进入数据库，像风儿一样自由...

在命令行执行命令非常方便，也有助于自己对数据库的了解，不过归根到底效率还是不高，对于mysql，相信有一个工具你会非常喜欢，phpmyadmin,这是一个网页管理工具，所以也是跨平台的，它依赖与apache，当然叶依赖与php.
apache2使用也非常方便，执行命令`/etc/init.d/apache2 start`即可启动，打开浏览器，输入127.0.0.1，一切像想象的一样美丽，不会出现什么问题。

phpmyadmin可以理解成一个网站，一个安装几乎不用做什么配置的网站，你只需要下载phpmyadmin，并解压到apache2的web根目录在/var/www/下即可，为了方便，建议改个名字，比如我的phpmyadmin的目录名就是phpmyadmin，那么在开启apache的情况下，直接访问127.0.0.1/phpmyadmin即可访问，输入帐号密码即可进行数据库操作。但在安装phpmyadmin之前也可能遇到什么问题，phpmyadmin混迹江湖这么多年，早已被吃透，如果你会google(我可不相信你不会)...相信没什么能难得倒你。

###Javabean
javabean为数据做了良好的封装，下面通过实例来看看是怎么封装的
首先在beans包建立Student.class

<pre><code>
package beans;
public class Student {
	private String stuno;
	private String stuname;
	private String stutest;
	public String getStuno() {
		return stuno;
	}
	public String getStuname() {
		return stuname;
	}
	public String getStutest() {
		return stutest;
	}
	public void setStuno(String stuno) {
		this.stuno = stuno;
	}	
	public void setStuname(String stuname) {
		this.stuname = stunama;
	}
	public void setStutest(String stutest) {
		this.stutest = stutest;
	}
}</code> </pre>

代码很简单，都是面向对象最最基本的东西，我可以看到分别有三个成员，以及他们的set和get方法。数据封装好了，怎么用？新建一个stu.jsp来调用。jsp的冗杂代码就不写了，写一针见血的。




首先看到`jsp:useBean`标签，他指明了调用javabean,class指向了我们的javabean，id你可以理解为实例化的一个对象。
代码中有三个对成员操作的例子
第一个，一目了然了，不过不推荐这种写法，因为jsp中包含了太多的java代码，这部是我们想要的。
第二个，运用`jsp:setProperty`标签执行了封装好的数据类中的set方法，其中`property`为成员名，`name`为实例化的对象的名字，`value`为值。
第三个其实和第二个差不多，不过这个扩展了request请求的值，这才是web开发的意义所在。

到此为止，javabean对于数据封装及调用的一些内容已经了解了，不过貌似没有涉及到数据库，别急，我们有专门对数据库操作的DAO(Date Access Object)类。