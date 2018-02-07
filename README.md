## Java高并发秒杀系统API
## How to play

 1. git clone `https://github.com/codingXiaxw/seckill.git`
 2. open IDEA -->  File  -->  New  --> Open 
 3. choose seckill's pom.xml，open it
 4. update the `jdbc.properties` files about your mysql's username and password
 5. deploy the tomcat，and start up
 6. enter in the browser: `http://localhost:8080/seckill/list`
 7. enjoy it 
 

## Develop environment
IDEA+Maven+SSM框架。  

## Written in front of words

之前写了一个用[SSM框架搭建的商品查询系统](http://codingxiaxw.cn/2016/11/15/45-smm%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/),分两篇文章分别记录了自己整合SSM框架的过程以及利用SSM开发的一些基础知识，由于那时候刚学完SSM框架，所以自己觉得整合的过程总结的不够好。<!--more-->如今在有了一定的SSM框架开发经验后针对慕课网上对秒杀系统的讲解视频再写一个用Maven+SSM做的一个秒杀系统的文字讲解，从头到尾记录自己整合SSM框架以及用SS框架M开发这个秒杀系统的过程。maven的强大之处就是你不用再像以前那样，如果在项目中用到spring框架还要到spring官网上去下载一系列的jar包，用了maven对项目进行管理之后你就可以直接在它的pom.xml文件中添加jar包的相应坐标，这样maven就能自动从它的中央仓库中为我们将这些jar包下载到其本地仓库中供我们使用。  

用maven对项目进行管理的知识很简单，关于创建maven项目的知识大家看我的这篇文章便可以在几分钟内掌握:[Maven安装配置及创建你的第一个Maven项目](http://codingxiaxw.cn/2016/11/24/51-first-maven-project/)  

秒杀系统搭建环境:IDEA+Maven+SSM框架。详情讲解[请点击这里](http://codingxiaxw.cn/2016/11/27/53-maven-ssm-seckill-dao/)或是下面模块的链接前往我的博客观看。  

完成这个秒杀系统，需要完成四个模块的代码编写，分别是:  

- 1.[Java高并发秒杀APi之业务分析与DAO层代码编写](http://codingxiaxw.cn/2016/11/27/53-maven-ssm-seckill-dao/)。
- 2.[Java高并发秒杀APi之Service层代码编写](http://codingxiaxw.cn/2016/11/28/54-seckill-service/)。
- 3.[Java高并发秒杀APi之Web层代码编写](http://codingxiaxw.cn/2016/11/28/55-seckill-web/)。

其实完成这三个模块就可以完成我们的秒杀系统了，但对于我们的秒杀系统中一件秒杀商品，在秒杀的时候肯定会有成千上万的用户参与进来，通过上述三个模块完成的系统无法解决这么多用户的高并发操作，所以我们还需要第四个模块:  

- 4.Java高并发秒杀APi之高并发优化(待更新)。

该系统我将按照上述4个模块通过4篇文章来完成介绍，本篇文章进行第一个模块的讲解及项目的介绍以及Dao层编码的开发。首先看看我们项目的效果图:  

列表页:  

![](http://od2xrf8gr.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-11-27%20%E4%B8%8B%E5%8D%883.24.50.png)  

详情页:  

![](http://od2xrf8gr.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-11-27%20%E4%B8%8B%E5%8D%883.25.39.png)  

![](http://od2xrf8gr.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-11-27%20%E4%B8%8B%E5%8D%883.25.22.png)  

![](http://od2xrf8gr.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-11-27%20%E4%B8%8B%E5%8D%883.25.48.png)  

![](http://od2xrf8gr.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-11-27%20%E4%B8%8B%E5%8D%883.26.16.png)  

接下来我将从如何用maven创建我们的秒杀系统seckill项目开始到完成我们的秒杀系统，详细介绍自己完成它的过程。  

## 1.相关技术介绍
**MySQL:**1.这里我们采用手写代码创建相关表，掌握这种能力对我们以后的项目二次上线会有很大的帮助；2.SQL技巧；3.事务和行级锁的理解和一些应用。  

**MyBatis:**1.DAO层的设计与开发。2.MyBatis的合理使用，使用Mapper动态代理的方式进行数据库的访问。3.MyBatis和Spring框架的整合:如何高效的去整合MyBatis和Spring框架。  

**Spring:**1.Spring IOC帮我们整合Service以及Service所有的依赖。2.声明式事务。对Spring声明式事务做一些分析以及它的行为分析。  

**Spring MVC:**1.Restful接口设计和使用。Restful现在更多的被应用在一些互联网公司Web层接口的应用上。2.框架运作流程。3.Spring Controller的使用技巧。  

**前端:**1.交互设计。2.bootstrap。3.JQuery。设计到前端的页面代码我们直接拷贝即可，毕竟真正开发中这样一个项目是由产品经理、前端工程师、后端工程师一起完成的。  

**高并发:**1.高并发点和高并发分析。2.优化思路并实现。  

下面开始我们的项目的开发。  

## 2.Java高并发秒杀APi之业务分析与DAO层代码编写

### 2.1用Maven创建我们的项目seckill
在命令行中输入如下命令:  

```
mvn archetype:generate -DgroupId=cn.codingxiaxw.seckill -DartifactId=seckill -Dpackage=cn.codingxiaxw.seckill -Dversion=1.0-SNAPSHOT -DarchetypeArtifactId=maven-archetype-webapp
```

然后使用IDEA打开该项目，在IDEA中对项目按照Maven项目的标准骨架补全我们项目的相应文件包，最后的工程结构如下:  

<img src="http://od2xrf8gr.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-11-27%20%E4%B8%8B%E5%8D%883.53.43.png" width="50%" />   

main包下进行我们项目的代码编写及相关配置文件，test包下进行我们项目的测试。  

打开WEB-INF下的web.xml，它默认为我们创建servlet版本为2.3，需要修改它的根标签为:
```xml
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
                      http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         version="3.0"
         metadata-complete="true">
<!--用maven创建的web-app需要修改servlet的版本为3.0-->


</web-app>
```

然后打开pom.xml，在里面添加我们需要的第三方jar包的坐标配置信息，如SSM框架、数据库、日志，如下:
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>cn.codingxiaxw.seckill</groupId>
  <artifactId>seckill</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>seckill Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <!--3.0的junit是使用编程的方式来进行测试，而junit4是使用注解的方式来运行junit-->
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>


    <!--补全项目依赖-->
    <!--1.日志 java日志有:slf4j,log4j,logback,common-logging
        slf4j:是规范/接口
        日志实现:log4j,logback,common-logging
        使用:slf4j+logback
    -->
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.7.12</version>
    </dependency>
    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-core</artifactId>
      <version>1.1.1</version>
    </dependency>
    <!--实现slf4j接口并整合-->
    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
      <version>1.1.1</version>
    </dependency>


    <!--1.数据库相关依赖-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.35</version>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupId>c3p0</groupId>
      <artifactId>c3p0</artifactId>
      <version>0.9.1.1</version>
    </dependency>

    <!--2.dao框架:MyBatis依赖-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.3.0</version>
    </dependency>
    <!--mybatis自身实现的spring整合依赖-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.2.3</version>
    </dependency>

    <!--3.Servlet web相关依赖-->
    <dependency>
      <groupId>taglibs</groupId>
      <artifactId>standard</artifactId>
      <version>1.1.2</version>
    </dependency>
    <dependency>
      <groupId>jstl</groupId>
      <artifactId>jstl</artifactId>
      <version>1.2</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.5.4</version>
    </dependency>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
    </dependency>

    <!--4:spring依赖-->
    <!--1)spring核心依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>4.1.7.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-beans</artifactId>
      <version>4.1.7.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>4.1.7.RELEASE</version>
    </dependency>
    <!--2)spring dao层依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>4.1.7.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>4.1.7.RELEASE</version>
    </dependency>
    <!--3)springweb相关依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>4.1.7.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>4.1.7.RELEASE</version>
    </dependency>
    <!--4)spring test相关依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>4.1.7.RELEASE</version>
    </dependency>

  </dependencies>

  <build>
    <finalName>seckill</finalName>
  </build>
</project>
```

到此，我们项目的初始化工作完成。  

### 2.2秒杀系统业务分析
秒杀系统业务流程如下:  

![](http://od2xrf8gr.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-11-27%20%E4%B8%8B%E5%8D%884.07.38.png)  

由图可以发现，整个系统其实是针对库存做的系统。用户成功秒杀商品，对于我们系统的操作就是:1.减库存。2.记录用户的购买明细。下面看看我们用户对库存的业务分析:  

![](http://od2xrf8gr.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-11-27%20%E4%B8%8B%E5%8D%884.09.29.png)  

记录用户的秒杀成功信息，我们需要记录:1.谁购买成功了。2.购买成功的时间/有效期。3.付款/发货信息。这些数据组成了用户的秒杀成功信息，也就是用户的购买行为。  

为什么我们的系统需要事务?看如下这些故障:1.若是用户成功秒杀商品我们记录了其购买明细却没有减库存。导致商品的超卖。2.减了库存却没有记录用户的购买明细。导致商品的少卖。对于上述两个故障，若是没有事务的支持，损失最大的无疑是我们的用户和商家。在MySQL中，它内置的事务机制，可以准确的帮我们完成减库存和记录用户购买明细的过程。  

MySQL实现秒杀的难点分析:当用户A秒杀id为10的商品时，此时MySQL需要进行的操作是:1.开启事务。2.更新商品的库存信息。3.添加用户的购买明细，包括用户秒杀的商品id以及唯一标识用户身份的信息如电话号码等。4.提交事务。若此时有另一个用户B也在秒杀这件id为10的商品，他就需要等待，等待到用户A成功秒杀到这件商品然后MySQL成功的提交了事务他才能拿到这个id为10的商品的锁从而进行秒杀，而同一时间是不可能只有用户B在等待，肯定是有很多很多的用户都在等待拿到这个行级锁。秒杀的难点就在这里，如何高效的处理这些竞争？如何高效的完成事务?在后面第4个模块如何进行高并发的优化为大家讲解。  

我们这个系统需要完成秒杀的哪些功能?先来看看天猫的一个秒杀库存系统:  

![](http://od2xrf8gr.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-11-27%20%E4%B8%8B%E5%8D%884.30.36.png)  

大家看了是不是觉得很复杂?当然不用担心，我们只是实现秒杀的一些功能:1.秒杀接口的暴露。2.执行秒杀的操作。3.相关查询，比如说列表查询，详情页查询。我们实现这三个功能即可。接下来进行具体的编码工作，首先是Dao层的编码。  

### 2.3Dao层设计开发
首先创建数据库，相关表的sql语句我在main包下的sql包中已经给出。  

然后创建对应表的实体类，在java包下创建cn.codingxiaxw.entity包，创建一个Seckill.java实体类，代码如下:
```java
public class Seckill
{
    private long seckillId;
    private String name;
    private int number;
    private Date startTime;
    private Date endTime;
    private Date createTime;

    public long getSeckillId() {
        return seckillId;
    }

    public void setSeckillId(long seckillId) {
        this.seckillId = seckillId;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getNumber() {
        return number;
    }

    public void setNumber(int number) {
        this.number = number;
    }

    public Date getStartTime() {
        return startTime;
    }

    public void setStartTime(Date startTime) {
        this.startTime = startTime;
    }

    public Date getEndTime() {
        return endTime;
    }

    public void setEndTime(Date endTime) {
        this.endTime = endTime;
    }

    public Date getCreateTime() {
        return createTime;
    }

    public void setCreateTime(Date createTime) {
        this.createTime = createTime;
    }

    @Override
    public String toString() {
        return "Seckill{" +
                "seckillId=" + seckillId +
                ", name='" + name + '\'' +
                ", number=" + number +
                ", startTime=" + startTime +
                ", endTime=" + endTime +
                ", createTime=" + createTime +
                '}';
    }
}
```

和一个SuccessKilled.java，代码如下:
```java
public class SuccessKilled
{
    private long seckillId;
    private long userPhone;
    private short state;
    private Date createTime;

    //多对一，因为一件商品在库存中有很多数量，对应的购买明细也有很多。
    private Seckill seckill;

    public long getSeckillId() {
        return seckillId;
    }

    public void setSeckillId(long seckillId) {
        this.seckillId = seckillId;
    }

    public long getUserPhone() {
        return userPhone;
    }

    public void setUserPhone(long userPhone) {
        this.userPhone = userPhone;
    }

    public short getState() {
        return state;
    }

    public void setState(short state) {
        this.state = state;
    }

    public Date getCreateTime() {
        return createTime;
    }

    public void setCreateTime(Date createTime) {
        this.createTime = createTime;
    }

    public Seckill getSeckill() {
        return seckill;
    }

    public void setSeckill(Seckill seckill) {
        this.seckill = seckill;
    }

    @Override
    public String toString() {
        return "SuccessKilled{" +
                "seckillId=" + seckillId +
                ", userPhone=" + userPhone +
                ", state=" + state +
                ", createTime=" + createTime +
                '}';
    }
}
```

然后针对实体创建出对应dao层的接口，在cn.codingxiaxw.dao包下创建Seckill.java:
```java
public interface SeckillDao
{

    /**
     * 减库存
     * @param seckillId
     * @param killTime
     * @return 如果影响行数>1，表示更新库存的记录行数
     */
    int reduceNumber(long seckillId, Date killTime);

    /**
     * 根据id查询秒杀的商品信息
     * @param seckillId
     * @return
     */
    Seckill queryById(long seckillId);

    /**
     * 根据偏移量查询秒杀商品列表
     * @param off
     * @param limit
     * @return
     */
    List<Seckill> queryAll(int off,int limit);

}
```

和SuccessKilled.java:
```java
public interface SuccessKilledDao {

    /**
     * 插入购买明细,可过滤重复
     * @param seckillId
     * @param userPhone
     * @return插入的行数
     */
    int insertSuccessKilled(long seckillId,long userPhone);


    /**
     * 根据秒杀商品的id查询明细SuccessKilled对象(该对象携带了Seckill秒杀产品对象)
     * @param seckillId
     * @return
     */
    SuccessKilled queryByIdWithSeckill(long seckillId,long userPhone);
}
```

接下来基于MyBatis来实现我们之前设计的Dao层接口。首先需要配置我们的MyBatis，在resources包下创建MyBatis全局配置文件mybatis-config.xml文件，在浏览器中输入`http://mybatis.github.io/mybatis-3/zh/index.html`打开MyBatis的官网文档，点击左边的"入门"栏框，找到mybatis全局配置文件，在这里有xml的一个规范，也就是它的一个xml约束，拷贝:
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
```

到我们的项目mybatis全局配置文件中，然后在全局配置文件中加入如下配置信息:
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--配置全局属性-->
    <settings>
        <!--使用jdbc的getGeneratekeys获取自增主键值-->
        <setting name="useGeneratedKeys" value="true"/>
        <!--使用列别名替换列名　　默认值为true
        select name as title(实体中的属性名是title) form table;
        开启后mybatis会自动帮我们把表中name的值赋到对应实体的title属性中
        -->
        <setting name="useColumnLabel" value="true"/>

        <!--开启驼峰命名转换Table:create_time到 Entity(createTime)-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>

</configuration>
```

配置文件创建好后我们需要关注的是Dao接口该如何实现，mybatis为我们提供了mapper动态代理开发的方式为我们自动实现Dao的接口。在mapper包下创建对应Dao接口的xml映射文件，里面用于编写我们操作数据库的sql语句，SeckillDao.xml和SuccessKilledDao.xml。既然又是一个xml文件，我们肯定需要它的dtd文件，在官方文档中，点击左侧"XML配置"，在它的一些事例中，找到它的xml约束:
```xml
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
```

加入到两个mapper映射xml文件中，然后对照Dao层方法编写我们的映射文件内容如下:  

SeckillDao.xml:
```xml
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.codingxiaxw.dao.SeckillDao">
    <!--目的:为dao接口方法提供sql语句配置
    即针对dao接口中的方法编写我们的sql语句-->


    <update id="reduceNumber">
        UPDATE seckill
        SET number = number-1
        WHERE seckill_id=#{seckillId}
        AND start_time <![CDATA[ <= ]]> #{killTime}
        AND end_time >= #{killTime}
        AND number > 0;
    </update>

    <select id="queryById" resultType="Seckill" parameterType="long">
        SELECT *
        FROM seckill
        WHERE seckill_id=#{seckillId}
    </select>

    <select id="queryAll" resultType="Seckill">
        SELECT *
        FROM seckill
        ORDER BY create_time DESC
        limit #{offset},#{limit}
    </select>


</mapper>
```

SuccessKilledDao.xml:
```xml
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.codingxiaxw.dao.SuccessKilledDao">

    <insert id="insertSuccessKilled">
        <!--当出现主键冲突时(即重复秒杀时)，会报错;不想让程序报错，加入ignore-->
        INSERT ignore INTO success_killed(seckill_id,user_phone,state)
        VALUES (#{seckillId},#{userPhone},0)
    </insert>

    <select id="queryByIdWithSeckill" resultType="SuccessKilled">

        <!--根据seckillId查询SuccessKilled对象，并携带Seckill对象-->
        <!--如何告诉mybatis把结果映射到SuccessKill属性同时映射到Seckill属性-->
        <!--可以自由控制SQL语句-->
        SELECT
            sk.seckill_id,
            sk.user_phone,
            sk.create_time,
            sk.state,
            s.seckill_id "seckill.seckill_id",
            s.name "seckill.name",
            s.number "seckill",
            s.start_time "seckill.start_time",
            s.end_time "seckill.end_time",
            s.create_time "seckill.create_time"
        FROM success_killed sk
        INNER JOIN seckill s ON sk.seckill_id=s.seckill_id
        WHERE sk.seckill_id=#{seckillId}
        AND sk.user_phone=#{userPhone}
    </select>

</mapper>
```

接下来我们开始MyBatis和Spring的整合，整合目标:1.更少的编码:只写接口，不写实现类。2.更少的配置:别名、配置扫描映射xml文件、dao实现。3.足够的灵活性:自由定制SQL语句、自由传结果集自动赋值。  

在resources包下创建一个spring包，里面放置spring对Dao、Service、transaction的配置文件。在浏览器中输入`http://docs.spring.io/spring/docs/`进入到Spring的官网中下载其pdf官方文档，在其官方文档中找到它的xml的定义内容头部:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
```

在spring包下创建一个spring配置dao层对象的配置文件spring-dao.xml，加入上述dtd约束，然后添加二者整合的配置，内容如下:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--配置整合mybatis过程
    1.配置数据库相关参数-->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--2.数据库连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!--配置连接池属性-->
        <property name="driverClass" value="${driver}" />

        <!-- 基本属性 url、user、password -->
        <property name="jdbcUrl" value="${url}" />
        <property name="user" value="${username}" />
        <property name="password" value="${password}" />

        <!--c3p0私有属性-->
        <property name="maxPoolSize" value="30"/>
        <property name="minPoolSize" value="10"/>
        <!--关闭连接后不自动commit-->
        <property name="autoCommitOnClose" value="false"/>

        <!--获取连接超时时间-->
        <property name="checkoutTimeout" value="1000"/>
        <!--当获取连接失败重试次数-->
        <property name="acquireRetryAttempts" value="2"/>
    </bean>

    <!--约定大于配置-->
    <!--３.配置SqlSessionFactory对象-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--往下才是mybatis和spring真正整合的配置-->
        <!--注入数据库连接池-->
        <property name="dataSource" ref="dataSource"/>
        <!--配置mybatis全局配置文件:mybatis-config.xml-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <!--扫描entity包,使用别名,多个用;隔开-->
        <property name="typeAliasesPackage" value="cn.codingxiaxw.entity"/>
        <!--扫描sql配置文件:mapper需要的xml文件-->
        <property name="mapperLocations" value="classpath:mapper/*.xml"/>
    </bean>

    <!--４:配置扫描Dao接口包,动态实现DAO接口,注入到spring容器-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--注入SqlSessionFactory-->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <!-- 给出需要扫描的Dao接口-->
        <property name="basePackage" value="cn.codingxiaxw.dao"/>
    </bean>
</beans>
```

需要我们在resources包下创建jdbc.properties用于配置数据库的连接信息，内容如下:
```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/seckill?useUnicode=true&characterEncoding=utf-8
username=root
password=xiaxunwu1996.
```

这样我们便完成了Dao层编码的开发，接下来就可以利用junit进行我们Dao层编码的测试了。首先测试SeckillDao.java，利用IDEA快捷键`shift+command+T`对SeckillDao.java进行测试，然后IDEA会自动在test包的java包下为我们生成对SeckillDao.java中所有方法的测试类SeckillDaoTest.java,内容如下:
```java
public class SeckillDaoTest {
    @Test
    public void reduceNumber() throws Exception {

    }

    @Test
    public void queryById() throws Exception {

    }

    @Test
    public void queryAll() throws Exception {

    }
}
```

然后便可以在这个测试类中对SeckillDao接口的所有方法进行测试了,先测试queryById()方法，在该方法中添加内容:
```java
/**
 * Created by codingBoy on 16/11/27.
 * 配置spring和junit整合，这样junit在启动时就会加载spring容器
 */
@RunWith(SpringJUnit4ClassRunner.class)
//告诉junit spring的配置文件
@ContextConfiguration({"classpath:spring/spring-dao.xml"})
public class SeckillDaoTest {

    //注入Dao实现类依赖
    @Resource
    private SeckillDao seckillDao;


    @Test
    public void queryById() throws Exception {
        long seckillId=1000;
        Seckill seckill=seckillDao.queryById(seckillId);
        System.out.println(seckill.getName());
        System.out.println(seckill);
    }
}
```

右键选择"debug queryById()"，测试台成功输入该id为1000的商品信息，证明Dao的该方法正确，然后测试queryAll()方法，在该方法中添加如下内容:
```java
 @Test
    public void queryAll() throws Exception {

        List<Seckill> seckills=seckillDao.queryAll(0,100);
        for (Seckill seckill : seckills)
        {
            System.out.println(seckill);
        }
    }
```

然后运行该方法，程序报错，报错信息如下:
```
Caused by: org.apache.ibatis.binding.BindingException: Parameter 'offset' not found. Available parameters are [1, 0, param1, param2]
```

意思就是无法完成offset参数的绑定，这也是我们java编程语言的一个问题，也就是java没有保存行参的记录，java在运行的时候会把`List<Seckill> queryAll(int offset,int limit);`中的参数变成这样:`queryAll(int arg0,int arg1)`,这样我们就没有办法去传递多个参数。需要在SeckillDao接口中修改方法:
```java
    List<Seckill> queryAll(@Param("offset") int offset,@Param("limit") int limit);
```

这样才能使我们的MyBatis识别offset和limit两个参数，将Dao层方法中的这两个参数与xml映射文件中sql语句的传入参数完成映射。然后重新测试，发现测试通过。然后测试reduceNumber()方法，在该方法中加入如下内容:
```java
  @Test
    public void reduceNumber() throws Exception {

        long seckillId=1000;
        Date date=new Date();
        int updateCount=seckillDao.reduceNumber(seckillId,date);
        System.out.println(updateCount);

    }
```

运行该方法，报错:
```
Caused by: org.apache.ibatis.binding.BindingException: Parameter 'seckillId' not found. Available parameters are [1, 0, param1, param2]
```

发现依然是我们之前那个错误，更改SeckillDao接口的reduceNumber()方法:
```java
    int reduceNumber(@Param("seckillId") long seckillId, @Param("killTime") Date killTime);
```

然后重新运行，测试通过，可是我们查询数据库发现该库存表的商品数量没有减少，是因为我们当前时间没有达到秒杀商品要求的时间，所以不会成功秒杀。接下来进行SuccessKilledDao接口相关方法的测试，依旧使用IDEA快捷键`shift+command+T`快速生成其方法的相应测试类:
```java
public class SuccessKilledDaoTest {
    @Test
    public void insertSuccessKilled() throws Exception 	{

    }

    @Test
    public void queryByIdWithSeckill() throws Exception 	{

    }
}
```

依然要在SuccessKilledDao的方法中用@Param注解完成参数的绑定，首先完成insertSuccessKilled()的测试:
```java
@RunWith(SpringJUnit4ClassRunner.class)
//告诉junit spring的配置文件
@ContextConfiguration({"classpath:spring/spring-dao.xml"})
public class SuccessKilledDaoTest {

    @Resource
    private SuccessKilledDao successKilledDao;

    @Test
    public void insertSuccessKilled() throws Exception 	{

        long seckillId=1000;
        long userPhone=13476191877L;
        int insertCount=successKilledDao.insertSuccessKilled(seckillId,userPhone);
        System.out.println("insertCount="+insertCount);
    }
｝
```

运行成功，测试台打印出insertCount=1的信息，即我们修改了表中的一条记录，这时查看秒杀成功明细表，发现该用户的信息已经被插入。然后再次运行该测试方法，程序没有报主键异常的错，是因为我们在编写我们的明细表的时候添加了一个联合主键的字段，它保证我们明细表中的seckillId和userPhone不能重复插入，另外在SuccessDao.xml中写的插入语句的ignore关键字也保证了这点。控制台输出0，表示没有对明细表做插入操作。然后进行queryByIdWithSeckill()方法的测试,需要在Dao层的方法中添加@Param注解:
```java
    SuccessKilled queryByIdWithSeckill(@Param("seckillId") long seckillId,@Param("userPhone") long userPhone);
```

然后进行该方法的测试:
```java
@Test
    public void queryByIdWithSeckill() throws Exception 	{
        long seckillId=1000L;
        long userPhone=13476191877L;
        SuccessKilled successKilled=successKilledDao.queryByIdWithSeckill(seckillId,userPhone);
        System.out.println(successKilled);
        System.out.println(successKilled.getSeckill());


    }
```

运行，成功查询出我们明细表中id为1000且手机号码为13476191877的用户信息，并将表中对应的信息映射到SuccessKilled对象和Seckill对象的属性中。  

到此，我们成功完成了Dao层开发及测试，接下来我们将进行Service层的开发工作，请查看我的下篇文章[Java高并发秒杀API之Service层开发](http://codingxiaxw.cn/2016/11/28/54-seckill-service/)  


## 3.联系

  If you have some questions after you see this article,you can tell your doubts in the comments area or you can find some info by  clicking these links.


- [Blog@codingXiaxw's blog](http://codingxiaxw.cn)

- [Weibo@codingXiaxw](http://weibo.com/u/5023661572?from=hissimilar_home&refer_flag=1005050003_)

- [Zhihu@codingXiaxw](http://www.zhihu.com/people/e9f78fa34b8002652811ac348da3f671)  
- [Github@codingXiaxw](https://github.com/codingXiaxw)
