# MybatisPlus

​		什么是MybatisPlus？它是苞米豆公司基于Mybatis研发的一款基于 MyBatis 的增强工具（简称为 MyBatis+），旨在简化 MyBatis 在开发过程中的使用，提供了许多方便的功能和特性，它具有以下主要特性。

```
1.简化的 CRUD 操作：MyBatis-Plus 提供了许多内置的通用方法，如插入、更新、删除和查询等，减少了繁琐的编写 SQL 的工作，使开发更加高效。
2.条件构造器：MyBatis-Plus 提供了很多灵活的条件构造器，可以通过链式调用来构建复杂的查询条件，避免了手动拼接 SQL 的麻烦和错误。
3.分页查询支持：MyBatis-Plus 提供了方便的分页查询方法，可以轻松地进行分页查询操作。它还支持自动进行总记录数的统计，并提供了常见的分页参数传递方式。
4.代码生成器：MyBatis-Plus 提供了一个代码生成器工具，可以根据数据库表结构快速生成实体类、Mapper 接口和 XML 配置文件等代码，减少了手动编写这些重复代码的工作量。
5.乐观锁支持：MyBatis-Plus 内置了乐观锁插件，可以方便地实现基于版本号或者时间戳的乐观锁操作，提供了简单而强大的并发控制能力。
6.自动填充字段：MyBatis-Plus 支持自动填充数据库表中的字段值，比如创建时间、更新时间等，减少了手动设置这些字段的工作量。
```

### 1.MybatisPlus入门程序

##### 1.1创建SpringBoot项目，技术栈仅保留JDBC

![image-20230628160232948](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230628160232948.png)

##### 1.2添加MP起步依赖

**注意：由于MP没有被收录进idea系统的内置配置，所以还需要引入Druid数据源，需要导入对应坐标**

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.1</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.16</version>
</dependency>
```

##### 1.3制作实体类与表结构

**注意：类名需要与表名对应，属性名和字段名对应**

##### 1.4在application.yml内设置jdbc参数

```yml
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mybatisplus_db?serverTimezone=UTC
    username: root
    password: 1234
```

​		数据库连接池用的是阿里巴巴的Druid。

##### 1.5定义接口，继承BaseMapper

```java
package com.itheima.dao;
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.itheima.domain.User;
import org.apache.ibatis.annotations.Mapper;

@Mapper //写上@Mapper            //接口继承BaseMapper，类型写需要操作的实体类
public interface UserDao extends BaseMapper<User> {
}
```

##### 1.6在测试类中注入Mapper接口，测试功能

```java
package com.itheima;

import com.itheima.dao.UserDao;
import com.itheima.domain.User;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import java.util.List;

@SpringBootTest
public class Mybatisplus01QuickstartApplicationTests {

    @Autowired
    private UserDao userDao;

    @Test
    void testGetAll() {
        List<User> userList = userDao.selectList(null);
        System.out.println(userList);
    }
}
```

### 2.标准数据层开发

##### 2.1MP的CURD操作

![image-20230628171016362](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230628171016362.png)

```java
package com.itheima;
import com.itheima.dao.UserDao;
import com.itheima.domain.User;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import java.util.List;

@SpringBootTest
class Mybatisplus01QuickstartApplicationTests {
	
	//注入Mapper接口
    @Autowired
    private UserDao userDao;
	//新增
    @Test
    void testSave() {
        User user = new User();
        user.setName("黑马程序员");
        user.setPassword("itheima");
        user.setAge(12);
        user.setTel("4006184000");
        userDao.insert(user);
    }
	//根据ID删除
    @Test
    void testDelete() {
        userDao.deleteById(1401856123725713409L);
    }
	//根据ID修改
    @Test
    void testUpdate() {
        User user = new User();
        user.setId(1L);
        user.setName("Tom888");
        user.setPassword("tom888");
        userDao.updateById(user);
    }
	//根据ID查询
    @Test
    void testGetById() {
        User user = userDao.selectById(2L);
        System.out.println(user);
    }
	//查询所有用户
    @Test
    void testGetAll() {
        List<User> userList = userDao.selectList(null);
        System.out.println(userList);
    }
}
```

**Lombok插件：**什么是Lombok插件呢？它可以简化实体类中的代码，自动生成实体类的 get/set方法，无参/实参构造方法，hashCode方法和equals方法。使用方法：在实体类的类上打上@Data、@NoArgsConstructor、@AllArgsConstructor注解即可。 

##### 2.2MP分页功能

1.设置分页拦截器并交给Spring管理

```java
package com.itheima.config;

import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MybatisPlusConfig {
    
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor(){
        //1 创建MybatisPlusInterceptor拦截器对象
        MybatisPlusInterceptor mpInterceptor=new MybatisPlusInterceptor();
        //2 添加分页拦截器
        mpInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
        return mpInterceptor;
    }
}
```

2.执行分页查询

```java
//分页查询
@Test
void testSelectPage(){
    //1 创建IPage分页对象,设置分页参数
    IPage<User> page=new Page<>(1,3);
    //2 执行分页查询
    userDao.selectPage(page,null);
    //3 获取分页结果
    System.out.println("当前页码值："+page.getCurrent());
    System.out.println("每页显示数："+page.getSize());
    System.out.println("总页数："+page.getPages());
    System.out.println("总条数："+page.getTotal());
    System.out.println("当前页数据："+page.getRecords());
}
```

### 3.DQL编程控制

##### **3.1条件查询方式**

​		MyBatisPlus将书写复杂的SQL查询条件进行了封装，使用编程的形式完成查询条件的组合

+ **条件查询**

```java
//方式一：按条件查询
QueryWarpper<User> qw = new QueryWrapper<>();
qw.lt("age",18);
List<User> userlist = userDao.selectList(qw);
System.out.println(userlist);
```

+ **lambda格式按条件查询**

```java
//方式二：lambda格式按条件查询
QueryWrapper<User> qw = new QueryWrapper<>();
qw.lambda().lt(User::getAge,10);
List<User> userlist = userDao.selectList(qw);
System.out.println(userlist);
```

+ **lambda格式按条件查询（推荐）**

```java
//方式三：lambda格式按条件查询
LambdaQureyWrapper<User> lqw = new LambdaQureyWrapper<>();
lqw.lt(User::getAge,10);
List<User> userlist = userDao.selectList(lqw);
System.out.println(userlist);
```

##### 3.2组合条件

+ **并且关系（and）**

```java
//并且关系
LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
//并且关系：10到30岁之间
lqw.lt(User::getAge, 30).gt(User::getAge, 10);
List<User> userList = userDao.selectList(lqw);
System.out.println(userList);
```

+ **或者关系（or）**

```java
//或者关系
LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
//或者关系：小于10岁或者大于30岁
lqw.lt(User::getAge, 10).or().gt(User::getAge, 30);
List<User> userList = userDao.selectList(lqw);
System.out.println(userList);
```

+ **NULL处理**

​		思考：在实际场景中，查询时并非每个条件都有值，那么遇到某些条件为NULL时，应该怎么查询呢？

**if语句控制条件追加**

​		只有if中的条件符合，才会限制查询范围。

```java
Integer minAge=10;  //将来有用户传递进来,此处简化成直接定义变量了
Integer maxAge=null;  //将来有用户传递进来,此处简化成直接定义变量了
LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
if(minAge!=null){
    lqw.gt(User::getAge, minAge);
}
if(maxAge!=null){
    lqw.lt(User::getAge, maxAge);
}
List<User> userList = userDao.selectList(lqw);
userList.forEach(System.out::println);
```

**条件参数控制**

​		将查询条件作为参数传递进来，当条件不为空时，就限制查询范围。

```java
Integer minAge=10;  //将来有用户传递进来,此处简化成直接定义变量了
Integer maxAge=null;  //将来有用户传递进来,此处简化成直接定义变量了
LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
//参数1：如果表达式为true，那么查询才使用该条件
lqw.gt(minAge!=null,User::getAge, minAge);
lqw.lt(maxAge!=null,User::getAge, maxAge);
List<User> userList = userDao.selectList(lqw);
userList.forEach(System.out::println);
```

**条件参数控制（链式编程）**

​		通过链式编程将条件写在一条语句中。

```java
Integer minAge=10;  //将来有用户传递进来,此处简化成直接定义变量了
Integer maxAge=null;  //将来有用户传递进来,此处简化成直接定义变量了
LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
//参数1：如果表达式为true，那么查询才使用该条件
lqw.gt(minAge!=null,User::getAge, minAge)
   .lt(maxAge!=null,User::getAge, maxAge);
List<User> userList = userDao.selectList(lqw);
userList.forEach(System.out::println);
```

##### 3.3查询投影

​		查询投影是MybatisPlus中的概念，查询投影（Projection）指的是在查询结果中选择特定字段或属性的操作。它可以让你只获取需要的字段，而不是返回整个实体对象。这样可以减少网络传输和内存消耗，提高查询性能和效率。

+ **查询结果包含模型类中部分属性**

​		假设现在的需求查询的是用户的 id,name,age ，有两种查询的写法

```java
//第一种,用Lambda包装类，查询条件用方法引用
LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
lqw.select(User::getId, User::getName, User::getAge);

//第二种，将需要查询的字段作为参数传给'select'方法中，再调用userDao查询
QueryWrapper<User> lqw = new QueryWrapper<User>();
lqw.select("id", "name", "age", "tel");
List<User> userList = userDao.selectList(lqw);
System.out.println(userList);
```

+ **查询结果包含模型类中未定义的属性**

```java
QueryWrapper<User> lqw = new QueryWrapper<User>();
lqw.select("count(*) as count, tel");
lqw.groupBy("tel");
List<Map<String, Object>> userList = userDao.selectMaps(lqw);
System.out.println(userList);
```

##### 3.4查询条件设定

​		查询条件多种多样，例如：范围匹配（> 、 = 、between）、模糊匹配（like）、空判定（null）、包含性匹配（in）、分组（group）、排序（order）等等。

- **用户登录（eq匹配）**

```java
LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
//等同于=
lqw.eq(User::getName, "Jerry").eq(User::getPassword, "jerry");
User loginUser = userDao.selectOne(lqw);
System.out.println(loginUser);
```

- **购物设定价格区间、户籍设定年龄区间（le ge匹配 或 between匹配）**

```java
LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
//范围查询 lt le gt ge eq between
lqw.between(User::getAge, 10, 30);
List<User> userList = userDao.selectList(lqw);
System.out.println(userList);
```

- **查信息，搜索新闻（非全文检索版：like匹配）**

```java
LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
//模糊匹配 like
lqw.likeLeft(User::getName, "J");
List<User> userList = userDao.selectList(lqw);
System.out.println(userList);
```

- **统计报表（分组查询聚合函数）**

```java
QueryWrapper<User> qw = new QueryWrapper<User>();
qw.select("gender","count(*) as nums");
qw.groupBy("gender");
List<Map<String, Object>> maps = userDao.selectMaps(qw);
System.out.println(maps);
```

##### 3.5字段和实体类映射问题（重点）

​		在数据库表设计时，会有字段和实体类中的属性对应不上的问题、或者表名和实体类名不一致的问题等其他名称不对应的问题，现在一 一来分析解决。

+ **（一）数据表字段与实体类属性不对应**

**解决：**在实体类里对应的属性上打上**@TableField**属性注解，通过**value**属性，设置当前属性对应的数据库表中的字段关系。

![image-20230629115312116](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230629115312116.png)

+ **（二）实体类中有属性在数据库字段中不存在**

**解决：**在实体类上打上**@TableField**属性注解，并设置**exist=false**。设置属性在数据库表字段中是否存在，默认为true。此属性无法与value合并使用。

![image-20230629145103429](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230629145103429.png)

+ **（三）不想某些敏感字段（密码）能被查询**

**解决：**为这些敏感字段设置权限，假如不想密码被查询，则使用**@TableField**属性注解，设置**select**属性为false，此时密码字段不参与查询。

![image-20230629145038565](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230629145038565.png)

+ **（四）数据库表名和实体类名不一样**

**解决：**在实体类上方，使用**@TableName**注解，通过 **value** 属性，设置实体类名为对应的数据库表名称。

![image-20230629145945419](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230629145945419.png)

示例代码：

```java
@Data
@TableName("tbl_user")
public class User {
    /*
        id为Long类型，因为数据库中id为bigint类型，
        并且mybatis有自己的一套id生成方案，生成出来的id必须是Long类型
     */
    private Long id;
    private String name;
    @TableField(value = "pwd",select = false)
    private String password;
    private Integer age;
    private String tel;
    @TableField(exist = false) //表示online字段不参与CRUD操作
    private Boolean online;
}
```

### 4.DML编程控制

​		思考：表中的主键id生成策略应该如何设置？MP中提供了几种id生成的策略。

##### 4.1添加时id全局生成策略

+ 名称：@TableId

- 类型：**属性注解**

- 位置：模型类中用于表示主键的属性定义上方

- 作用：设置当前类中主键属性的生成策略

- 相关属性

  ​	**type**：设置主键属性的生成策略，值参照IdType枚举值。

![image-20230629150550552](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230629150550552.png)

**全局策略配置：**

```yaml
mybatis-plus:
  global-config:
    db-config:
      id-type: assign_id
      table-prefix: tbl_
```

如图：id生成全局配置和表名前缀全局配置

![image-20230629150847347](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230629150847347.png)

##### 4.2多记录操作

​		思考：MP是否支持批量操作？

+ 根据id批量删除

```java
//删除指定多条数据
List<Long> list = new ArrayList<>();
list.add(1402551342481838081L);
list.add(1402553134049501186L);
list.add(1402553619611430913L);

userDao.deleteBatchIds(list);
```

+ 根据id批量查询

```java
//查询指定多条数据
List<Long> list = new ArrayList<>();
list.add(1L);
list.add(3L);
list.add(4L);
userDao.selectBatchIds(list);
```

##### 4.3逻辑删除

思考：在实际环境中，如果想删除一条数据，应不应该真的将其从数据库删除？由于删除操作是一个极其危险的操作，所以这里要引入一个概念：**逻辑删除**。即使用该数据时设置为可用状态，删除时设置字段为不可用状态。

示例：

1.在数据库表中添加逻辑删除字段

![image-20230629152446614](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230629152446614.png)

2.实体类中添加对应字段，并在该属性上添加逻辑删除注解

```java
package com.itheima.domain;

import com.baomidou.mybatisplus.annotation.*;

import lombok.Data;

@Data
public class User {

    private Long id;
    
    //逻辑删除字段，标记当前记录是否被删除
    @TableLogic
    private Integer deleted;
    
}
```

3.配置逻辑删除字面值

```yml
mybatis-plus:
  global-config:
    db-config:
      table-prefix: tbl_
      # 逻辑删除字段名
      logic-delete-field: deleted
      # 逻辑删除字面值：未删除为0
      logic-not-delete-value: 0
      # 逻辑删除字面值：删除为1
      logic-delete-value: 1
```

**逻辑删除本质：逻辑删除的本质其实是修改操作。如果加了逻辑删除字段，查询数据时也会自动带上逻辑删除字段。**

### 5.乐观锁（Update）

​		思考：假如现在有一个业务是秒杀，多个用户抢最后一张票，要怎样保证多个用户同时操作时票的数量不为负数。**这种情况可以使用乐观锁，为数据添加一个版本号，每次修改该数据先拿到该版本号，修改后版本号会增加，这样其他用户拿着旧版本号去执行修改就会失败。保证了数据的安全性。**

示例：

1.在数据库表中添加锁标记字段

![image-20230629161338867](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230629161338867.png)

2.实体类中添加对应字段，并设定当前字段为逻辑删除标记字段

```java
package com.itheima.domain;

import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableLogic;
import com.baomidou.mybatisplus.annotation.Version;
import lombok.Data;

@Data
public class User {

	private Long id;
	
    @Version
    private Integer version;
}
```

3.配置乐观锁拦截器实现锁机制对应的动态SQL语句拼装

```java
package com.itheima.config;

import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.OptimisticLockerInnerInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MpConfig {
    @Bean
    public MybatisPlusInterceptor mpInterceptor() {
        //1.定义Mp拦截器
        MybatisPlusInterceptor mpInterceptor = new MybatisPlusInterceptor();

        //2.添加乐观锁拦截器
        mpInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        
        return mpInterceptor;
    }
}
```

4.使用乐观锁机制在修改前必须先获取到对应数据的verion方可正常进行

```java
@Test
public void testUpdate() {
    /*User user = new User();
    user.setId(3L);
    user.setName("Jock666");
    user.setVersion(1);
    userDao.updateById(user);*/
    
    //1.先通过要修改的数据id将当前数据查询出来
    //User user = userDao.selectById(3L);
    //2.将要修改的属性逐一设置进去
    //user.setName("Jock888");
    //userDao.updateById(user);
    
    //1.先通过要修改的数据id将当前数据查询出来
    User user = userDao.selectById(3L);     //version=3
    User user2 = userDao.selectById(3L);    //version=3
    user2.setName("Jock aaa");
    userDao.updateById(user2);              //version=>4
    user.setName("Jock bbb");
    userDao.updateById(user);               //verion=3?条件还成立吗？
}
```

![image-20230629161506364](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230629161506364.png)

### 6.代码生成器

​		什么是代码生成器？是根据一张表的字段信息，推演出Domain、Dao层的代码？

##### 6.1MP提供模板

+ 实体对象类模板

![image-20230629162702994](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230629162702994.png)

##### 6.2工程搭建和基本代码编写

- 第一步：创建SpringBoot工程，添加代码生成器相关依赖，其他依赖自行添加

```xml
<!--代码生成器-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.4.1</version>
</dependency>

<!--velocity模板引擎-->
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity-engine-core</artifactId>
    <version>2.3</version>
</dependency>
```

- 第二步：编写代码生成器类

```java
package com.itheima;

import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;

public class Generator {
    public static void main(String[] args) {
        //1. 创建代码生成器对象，执行生成代码操作
        AutoGenerator autoGenerator = new AutoGenerator();

        //2. 数据源相关配置：读取数据库中的信息，根据数据库表结构生成代码
        DataSourceConfig dataSource = new DataSourceConfig();
        dataSource.setDriverName("com.mysql.cj.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/mybatisplus_db?serverTimezone=UTC");
        dataSource.setUsername("root");
        dataSource.setPassword("root");
        autoGenerator.setDataSource(dataSource);

         //3. 执行生成操作
        autoGenerator.execute();
    }
}
```

##### 6.3开发者自定义配置

- 设置全局配置

```java
//设置全局配置
GlobalConfig globalConfig = new GlobalConfig();
globalConfig.setOutputDir(System.getProperty("user.dir")+"/mybatisplus_04_generator/src/main/java");    //设置代码生成位置
globalConfig.setOpen(false);    //设置生成完毕后是否打开生成代码所在的目录
globalConfig.setAuthor("黑马程序员");    //设置作者
globalConfig.setFileOverride(true);     //设置是否覆盖原始生成的文件
globalConfig.setMapperName("%sDao");    //设置数据层接口名，%s为占位符，指代模块名称
globalConfig.setIdType(IdType.ASSIGN_ID);   //设置Id生成策略
autoGenerator.setGlobalConfig(globalConfig);
```

- 设置包名相关配置

```java
//设置包名相关配置
PackageConfig packageInfo = new PackageConfig();
packageInfo.setParent("com.aaa");   //设置生成的包名，与代码所在位置不冲突，二者叠加组成完整路径
packageInfo.setEntity("domain");    //设置实体类包名
packageInfo.setMapper("dao");   //设置数据层包名
autoGenerator.setPackageInfo(packageInfo);
```

- 策略设置

```java
//策略设置
StrategyConfig strategyConfig = new StrategyConfig();
strategyConfig.setInclude("tbl_user");  //设置当前参与生成的表名，参数为可变参数
strategyConfig.setTablePrefix("tbl_");  //设置数据库表的前缀名称，模块名 = 数据库表名 - 前缀名  例如： User = tbl_user - tbl_
strategyConfig.setRestControllerStyle(true);    //设置是否启用Rest风格
strategyConfig.setVersionFieldName("version");  //设置乐观锁字段名
strategyConfig.setLogicDeleteFieldName("deleted");  //设置逻辑删除字段名
strategyConfig.setEntityLombokModel(true);  //设置是否启用lombok
autoGenerator.setStrategy(strategyConfig);
```
