# Mybatis

​		什么是Mybatis呢？Mybatis是一款优秀的持久层框架，它用于简化了JDBC的开发。官网地址为：https://mybatis.org/mybatis-3/zh/index.html 

### 1.Mybatis入门

```
步骤：
	1.创建一个springboot项目，并导入mybatis依赖，mysql的驱动。
	2.准备数据，运行script脚本，添加用户数据，并创建实体类。
	3.配置Mybatis。
	4.编写SQL语句并运行测试。
```

①

![image-20230618150226280](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230618150226280.png)

②

```sql
-- 用户表
create table user(
    id int unsigned primary key auto_increment comment 'ID',
    name varchar(100) comment '姓名',
    age tinyint unsigned comment '年龄',
    gender tinyint unsigned comment '性别, 1:男, 2:女',
    phone varchar(11) comment '手机号'
) comment '用户表';

-- 测试数据
insert into user(id, name, age, gender, phone) VALUES (null,'白眉鹰王',55,'1','18800000000');
insert into user(id, name, age, gender, phone) VALUES (null,'金毛狮王',45,'1','18800000001');
insert into user(id, name, age, gender, phone) VALUES (null,'青翼蝠王',38,'1','18800000002');
insert into user(id, name, age, gender, phone) VALUES (null,'紫衫龙王',42,'2','18800000003');
insert into user(id, name, age, gender, phone) VALUES (null,'光明左使',37,'1','18800000004');
insert into user(id, name, age, gender, phone) VALUES (null,'光明右使',48,'1','18800000005');
```

③

![image-20230618141735041](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230618141735041.png)

​		在springboot项目中，可以编写application.properties文件，配置数据库连接信息。需要连接数据库，需要配置数据库连接的基本信息，包括：driver-class-name、url 、username，password。

application.properties:

```properties
#驱动类名称
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
#数据库连接的url
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis
#连接数据库的用户名
spring.datasource.username=root
#连接数据库的密码
spring.datasource.password=1234
```

④

在创建出来的springboot工程中，在引导类所在的包下，创建一个mapper，mapper包下定义一个UserMapper接口，这是一个持久层接口。

UserMapper：

~~~java
import com.itheima.pojo.User;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;
import java.util.List;

@Mapper
public interface UserMapper {
    
    //查询所有用户数据
    @Select("select id, name, age, gender, phone from user")
    public List<User> list();
    
}
~~~

> @Mapper注解：表示是mybatis中的Mapper接口
>
> - 程序运行时：框架会自动生成接口的实现类对象(代理对象)，并给交Spring的IOC容器管理
>
>  @Select注解：代表的就是select查询，用于书写select查询语句

测试：

在测试类中添加注解@SpringBootTest，在测试类运行的时候，就会自动加载IOC容器，通过@Autowired注解自动注入进行，就可以进行测试了。

```java
@SpringBootTest
public class MybatisQuickstartApplicationTests {
	
    @Autowired
    private UserMapper userMapper;
	
    @Test
    public void testList(){
        List<User> userList = userMapper.list();
        for (User user : userList) {
            System.out.println(user);
        }
    }
}
```

> 运行结果：
>
> ~~~
> User{id=1, name='白眉鹰王', age=55, gender=1, phone='18800000000'}
> User{id=2, name='金毛狮王', age=45, gender=1, phone='18800000001'}
> User{id=3, name='青翼蝠王', age=38, gender=1, phone='18800000002'}
> User{id=4, name='紫衫龙王', age=42, gender=2, phone='18800000003'}
> User{id=5, name='光明左使', age=37, gender=1, phone='18800000004'}
> User{id=6, name='光明右使', age=48, gender=1, phone='18800000005'}
> ~~~

### 2. JDBC介绍

​		什么是JDBC呢？JDBC： ( Java DataBase Connectivity )，是使用Java语言操作关系型数据库的一套API。

![image-20230618143400503](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230618143400503.png)

​		JDBC本质：

- sun公司官方定义的一套操作所有关系型数据库的规范，即接口。

- 各个数据库厂商去实现这套接口，提供数据库驱动jar包。

- 我们可以使用这套接口(JDBC)编程，真正执行的代码是驱动jar包中的实现类。

下面是原始的JDBC操作数据库的流程，十分复杂繁琐:

1. 注册驱动
2. 获取连接对象
3. 执行SQL语句，返回执行结果
4. 处理执行结果
5. 释放资源

> 在pom.xml文件中已引入MySQL驱动依赖，我们直接编写JDBC代码即可

JDBC具体代码实现：

```java
import com.itheima.pojo.User;
import org.junit.jupiter.api.Test;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;

public class JdbcTest {
    @Test
    public void testJdbc() throws Exception {
        //1. 注册驱动
        Class.forName("com.mysql.cj.jdbc.Driver");

        //2. 获取数据库连接
        String url="jdbc:mysql://127.0.0.1:3306/mybatis";
        String username = "root";
        String password = "1234";
        Connection connection = DriverManager.getConnection(url, username, password);

        //3. 执行SQL
        Statement statement = connection.createStatement(); //操作SQL的对象
        String sql="select id,name,age,gender,phone from user";
        ResultSet rs = statement.executeQuery(sql);//SQL查询结果会封装在ResultSet对象中

        List<User> userList = new ArrayList<>();//集合对象（用于存储User对象）
        //4. 处理SQL执行结果
        while (rs.next()){
            //取出一行记录中id、name、age、gender、phone下的数据
            int id = rs.getInt("id");
            String name = rs.getString("name");
            short age = rs.getShort("age");
            short gender = rs.getShort("gender");
            String phone = rs.getString("phone");
            //把一行记录中的数据，封装到User对象中
            User user = new User(id,name,age,gender,phone);
            userList.add(user);//User对象添加到集合
        }
        //5. 释放资源
        statement.close();
        connection.close();
        rs.close();

        //遍历集合
        for (User user : userList) {
            System.out.println(user);
        }
    }
}
```

> DriverManager(类)：数据库驱动管理类。
>
> - 作用：
>
>   1. 注册驱动
>
>   2. 创建java代码和数据库之间的连接，即获取Connection对象
>
> Connection(接口)：建立数据库连接的对象
>
> - 作用：用于建立java程序和数据库之间的连接
>
> Statement(接口)： 数据库操作对象(执行SQL语句的对象)。
>
> - 作用：用于向数据库发送sql语句
>
> ResultSet(接口)：结果集对象（一张虚拟表）
>
> - 作用：sql查询语句的执行结果会封装在ResultSet中

分析问题：

1.数据库连接的驱动、链接地址、用户名和密码全部编写在java代码中。

2.查询结果的解析和封装十分复杂。

3.每一次查询数据库都需要获取连接，操作完成后还需要断开连接，性能太低，浪费资源。

### 3.Mybatis与 JDBC技术对比

**优点：**

1. 数据库连接四要素(驱动、链接、用户名、密码)，都配置在springboot默认的配置文件 application.properties中

2. 查询结果的解析及封装，由mybatis自动完成映射封装，我们无需关注

3. 在mybatis中使用了数据库连接池技术，从而避免了频繁的创建连接、销毁连接而带来的资源浪费。

##### 3.1数据库连接池

​		mybatis中为了避免频繁的创建连接和关闭连接所带来的资源浪费，使用了数据库连接池技术。**那么什么是数据库连接池呢？数据库连接池是个容器，负责分配、管理数据库连接(Connection)**

- 程序在启动时，会在数据库连接池(容器)中，创建一定数量的Connection对象

允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个

- 客户端在执行SQL时，先从连接池中获取一个Connection对象，然后在执行SQL语句，SQL语句执行完之后，释放Connection时就会把Connection对象归还给连接池（Connection对象可以复用）

释放空闲时间超过最大空闲时间的连接，来避免因为没有释放连接而引起的数据库连接遗漏

- 客户端获取到Connection对象了，但是Connection对象并没有去访问数据库(处于空闲)，数据库连接池发现Connection对象的空闲时间 > 连接池中预设的最大空闲时间，此时数据库连接池就会自动释放掉这个连接对象

​		首先没有使用数据库连接池的时候，客户端执行SQL语句：要先创建一个新的连接对象，然后执行SQL语句，SQL语句执行后又需要关闭连接对象从而释放资源，每次执行SQL时都需要创建连接、销毁链接，这种频繁的重复创建销毁的过程是比较耗费计算机的性能。

##### 3.2怎样实现数据库连接池？

​		sun提供的默认数据库连接池是Hikari（追光者）。但是阿里巴巴开源的Druid连接池是功能强大，性能优秀，是Java语言最好的数据库连接池之一。

​		如果想把默认的数据库连接池切换为Druid数据库连接池，只需要完成以下两步操作即可：

> 参考官方地址：https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter

1. 在pom.xml文件中引入依赖

```xml
<dependency>
    <!-- Druid连接池依赖 -->
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.2.8</version>
</dependency>
```

2. 在application.properties中引入数据库连接配置

~~~properties
spring.datasource.druid.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.druid.url=jdbc:mysql://localhost:3306/mybatis
spring.datasource.druid.username=root
spring.datasource.druid.password=1234
~~~

### 4.lombok

​		什么是lombok？Lombok是一个实用的Java类库，可以通过简单的注解来简化和消除一些必须有但显得很臃肿的Java代码。

​		它是通过注解来生成一些重复编写的代码，常用注解有：

| **注解**            | **作用**                                                     |
| ------------------- | ------------------------------------------------------------ |
| @Getter/@Setter     | 为所有的属性提供get/set方法                                  |
| @ToString           | 会给类自动生成易阅读的  toString 方法                        |
| @EqualsAndHashCode  | 根据类所拥有的非静态字段自动重写 equals 方法和  hashCode 方法 |
| @Data               | 提供了更综合的生成代码功能（@Getter  + @Setter + @ToString + @EqualsAndHashCode） |
| @NoArgsConstructor  | 为实体类生成无参的构造器方法                                 |
| @AllArgsConstructor | 为实体类生成除了static修饰的字段之外带有各参数的构造器方法。 |

​		**只需要在实体类上添加@Date、@NoArgsConstrutor和@AllArgsContrustor注解就好了。**

##### 4.1如何使用Lombok？

第1步：在pom.xml文件中引入依赖

```xml
<!-- 在springboot的父工程中，已经集成了lombok并指定了版本号，故当前引入依赖时不需要指定version -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
```

第2步：在实体类上添加注解

```java
import lombok.Data;

@Data
public class User {
    private Integer id;
    private String name;
    private Short age;
    private Short gender;
    private String phone;
}
```

> 在实体类上添加了@Data注解，那么这个类在编译时期，就会生成getter/setter、equals、hashcode、toString等方法。

说明：@Data注解中不包含全参构造方法，通常在实体类上，还会添加上：全参构造、无参构造

~~~java
import lombok.Data;

@Data //getter方法、setter方法、toString方法、hashCode方法、equals方法
@NoArgsConstructor //无参构造
@AllArgsConstructor//全参构造
public class User {
    private Integer id;
    private String name;
    private Short age;
    private Short gender;
    private String phone;
}
~~~

### 5.Mybatis基础操作

##### 5.1 日志输出

​		如果想查看mybatis操作数据库时所执行的语句，可以借助日志来看执行语句时传递的参数和执行结果。**打开application.properties配置文件，开启mybatis的日志，并指定输出到控制台。**

```properties
#指定mybatis输出日志的位置, 输出控制台
mybatis.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

![image-20230618151243578](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230618151243578.png)

​		开启后可以看到id后面没有直接的值，而是通过？进行占位。这种SQL语句被称之为**预编译SQL。**

##### 5.2预编译SQL

​		预编译SQL有什么优点呢？**1.性能更高。2.更安全（防止SQL注入）**

+ 性能更高的原因是：性能更高：预编译SQL，编译一次之后会将编译后的SQL语句缓存起来，后面再次执行这条语句时，不会再次编译。（只是输入的参数不同）
+ 更加安全是因为：将敏感字进行转义，保障SQL的安全性。

**什么是SQL注入呢？观察下面的例子：**

![image-20230618152105076](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230618152105076.png)

竟然可以登录成功？！为什么会这样呢？

+ 由于没有对用户输入内容进行充分检查，而SQL又是字符串拼接方式而成，在用户输入参数时，在参数中添加一些SQL关键字，达到改变SQL运行结果的目的，从而完成恶意攻击。

![image-20230618152202001](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230618152202001.png)

![image-20230618152339030](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230618152339030.png)

这是因为${}不会进行预编译，而是将参数直接拼接在sql语句中，这样就会产生sql注入的风险。

###### **参数占位符**

在Mybatis中提供的参数占位符有两种：${...} 、#{...}

- #{...}
  - 执行SQL时，会将#{…}替换为?，生成预编译SQL，会自动设置参数值
  - 使用时机：参数传递，都使用#{…}

- ${...}
  - 拼接SQL。直接将参数拼接在SQL语句中，存在SQL注入问题
  - 使用时机：如果对表名、列表进行动态设置时使用

> 注意事项：在项目开发中，建议使用#{...}，生成预编译SQL，防止SQL注入安全。

##### 5.3新增主键返回

​		例如在新增套餐的时候，还需要新增套餐菜品关系表，那么套餐的id应该从哪里获取？

+ 默认情况下，执行插入操作时，是不会主键值返回的。如果我们想要拿到主键值，需要在Mapper接口中的方法上添加一个Options注解，并在注解中指定属性useGeneratedKeys=true和keyProperty="实体类属性名"。

```java
 @Options(useGeneratedKeys = true,keyProperty = "id")
```

##### 5.4数据封装

​		当查询数据库时，可以发现有些数据库字段和实体类中的属性名不一样。这时如果不做处理，则数据不能封装。原因是：

- 实体类属性名和数据库表查询返回的字段名一致，mybatis会自动封装。
- 如果实体类属性名和数据库表查询返回的字段名不一致，不能自动封装。

解决方案有三种：

1. 起别名
2. 结果映射
3. 开启驼峰命名

**起别名：**在SQL语句中对名字不一样的列名起别名，别名和实体类属性名一致。

```java
@Select("select id, username, password, name, gender, image, job, entrydate, dept_id AS deptId, create_time AS createTime, update_time AS updateTimefrom empwhere id=#{id}")
public Emp getById(Integer id);
```

**手动映射：**通过 @Results及@Result 进行手动结果映射。

```java
@Results({@Result(column = "dept_id", property = "deptId"),
          @Result(column = "create_time", property = "createTime"),
          @Result(column = "update_time", property = "updateTime")})
@Select("select id, username, password, name, gender, image, job, entrydate, dept_id, create_time, update_time from emp where id=#{id}")
public Emp getById(Integer id);
```

**开启驼峰命名：**如果字段名与属性名符合驼峰命名规则，mybatis会自动通过驼峰命名规则映射。

```properties
# 在application.properties中添加：
mybatis.configuration.map-underscore-to-camel-case=true
```

### 6.Mybatis的XML配置文件

​		什么是Mybatis的XML配置文件？Mybatis的开发有两种方式：注解和XML文件。上面说过了注解，那什么是XML配置文件呢？使用Mybatis的注解方式，主要是来完成一些简单的增删改查功能。如果需要实现复杂的SQL功能，建议使用XML来配置映射语句，也就是将SQL语句写在XML配置文件中。

##### 6.1XML配置规范

在Mybatis中使用XML映射文件方式开发，需要符合一定的规范：

1. XML映射文件的名称与Mapper接口名称一致，并且将XML映射文件和Mapper接口放置在相同包下（同包同名）

2. XML映射文件的namespace属性为Mapper接口的全路径地址一致

3. XML映射文件中sql语句的id与Mapper接口中的方法名一致，并保持返回类型一致。

![image-20230618160401211](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230618160401211.png)

1.创建XML映射文件

![image-20230618162653938](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230618162653938.png)

![image-20230618162700014](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230618162700014.png)

![image-20230618162707230](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230618162707230.png)

2.编写XML映射文件

> xml映射文件中的dtd约束，直接从mybatis官网复制即可

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="">
 
</mapper>
~~~

配置：XML映射文件的namespace属性为Mapper接口的全路径地址

![image-20230618170954989](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230618170954989.png)

**XML映射文件中sql语句的id与Mapper接口中的方法名一致，并保持返回类型一致**

### 7.Mybatis动态SQL

​		什么是动态SQL？当查询或者更新时，条件是动态的，可以有多个条件也可以只有一个条件，那这时应该怎么写SQL语句呢？需要用到动态SQL。

###### 7.1动态SQL

`<if>`：用于判断条件是否成立。使用test属性进行条件判断，如果条件为true，则拼接SQL。

~~~xml
<if test="条件表达式">
   要拼接的sql语句
</if>
~~~

- 查询动态SQL语句（where标签会自动删除第一个if标签里的and）：

~~~xml
<select id="list" resultType="com.itheima.pojo.Emp">
        select * from emp
        <where>
    
             <if test="name != null">
                and name like concat('%',#{name},'%')
             </if>
             <if test="gender != null">
                 and gender = #{gender}
             </if>
             <if test="begin != null and end != null">
                 and entrydate between #{begin} and #{end}
             </if>
    	</where>
        order by update_time desc
</select>
~~~

+ 修改动态SQL语句(set标签会自动删除第一个if标签里的逗号)：

~~~xml
<mapper namespace="com.itheima.mapper.EmpMapper">

    <!--更新操作-->
    <update id="update">
        update emp
        <set>
            <if test="username != null">
                username=#{username},
            </if>
            <if test="name != null">
                name=#{name},
            </if>
            <if test="gender != null">
                gender=#{gender},
            </if>
            <if test="image != null">
                image=#{image},
            </if>
            <if test="job != null">
                job=#{job},
            </if>
            <if test="entrydate != null">
                entrydate=#{entrydate},
            </if>
            <if test="deptId != null">
                dept_id=#{deptId},
            </if>
            <if test="updateTime != null">
                update_time=#{updateTime}
            </if>
          </set>
        where id=#{id}
    </update>
</mapper>
~~~

###### 7.2动态SQL-foreach

SQL语句：

~~~mysql
#删除多条数据
delete from emp where id in (1,2,3);
~~~

Mapper接口：

~~~java
@Mapper
public interface EmpMapper {
    //批量删除
    public void deleteByIds(List<Integer> ids);
}
~~~

XML映射文件：

- 使用`<foreach>`遍历deleteByIds方法中传递的参数ids集合

~~~xml
<foreach collection="集合名称" item="集合遍历出来的元素/项" separator="每一次遍历使用的分隔符" 
         open="遍历开始前拼接的片段" close="遍历结束后拼接的片段">
</foreach>
~~~

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.itheima.mapper.EmpMapper">
    <!--删除操作-->
    <delete id="deleteByIds">
        delete from emp where id in
        <foreach collection="ids" item="id" separator="," open="(" close=")">
            #{id}
        </foreach>
    </delete>
</mapper> 
~~~

###### 动态SQL-sql&include

可以对重复的代码片段进行抽取，将其通过`<sql>`标签封装到一个SQL片段，然后再通过`<include>`标签进行引用。

- `<sql>`：定义可重用的SQL片段
- `<include>`：通过属性refid，指定包含的SQL片段

![image-20230618180201161](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230618180201161.png)

SQL片段： 抽取重复的代码

```xml
<sql id="commonSelect">
 	select id, username, password, name, gender, image, job, entrydate, dept_id, create_time, update_time from emp
</sql>
```

然后通过`<include>` 标签在原来抽取的地方进行引用。操作如下：

```xml
<select id="list" resultType="com.itheima.pojo.Emp">
    <include refid="commonSelect"/>
    <where>
        <if test="name != null">
            name like concat('%',#{name},'%')
        </if>
        <if test="gender != null">
            and gender = #{gender}
        </if>
        <if test="begin != null and end != null">
            and entrydate between #{begin} and #{end}
        </if>
    </where>
    order by update_time desc
</select>
```

