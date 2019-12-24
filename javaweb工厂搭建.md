## 使用idea创建maven工程及常用配置

### 工程创建

1. File->new->project
2. 左侧选择maven
3. 右侧Project sdk选择正确的jdk版本，没有的话需要new
4. next
5. 填写groupId(域名反写)和ArtifactId（项目名）
6. next
7. 填项目名和路径
8. finish


### 子module创建

1. 在工程根目录右键->new->module

2. 然后和之前一样创建module

### 镜像仓库设置，加快依赖包下载速度

```xml
<repositories>
    <repository>
        <name>aliyun</name>
        <id>aliyun</id>
        <url>https://maven.aliyun.com/repository/public</url>
    </repository>
</repositories>
```

### 打包类型配置

```xml
<packaging>jar</packaging>
```

或者

```
<packaging>war</packaging>
```


### 常用依赖引入

1. mysql数据库驱动

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.18</version>
</dependency>
```

2. mybatis

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.3</version>
</dependency>
```

3. spring相关

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>${spring.version}</version>
</dependency>
```

4. aspectJ aop相关

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```

5. 事务相关

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>${spring.version}</version>
</dependency>
```

6. spring与mybatis整合相关

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>2.0.3</version>
</dependency>
```

7. servlet相关

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
</dependency>
```

8. jstl

```xml
<dependency>
    <groupId>jstl</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
```


### 一些目录创建于配置

1. 源代码目录创建方法

在目录右键->make as -> source root

2. 配置web特性，就是配置web.xml目录在哪里

file->project stuctures -> 左侧选择facet-> 点击+ -> 在右边编辑web module deployment descriptor 选择xml目录 -> 执行web resource directory -> apply

3. 配置Artifacts, Artifacts表示的就是如何部署

file->project structures -> 左边Artifacts -> 加号->web application exploded->from modules->选择module->在右边你要部署的东西右键->put into output root

4. 配置tomcat

点击菜单小三角 -> edit confugrations -> 点加号 -> tomcat server -> local -> 填name -> 端口等配置 -> 最下边选一个artifacts->点deployment -> 修改上下文路径


### 一些常用配置


1. spring整合mybatis, 开启自动扫描， 声明式事务支持相关配置

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd
    http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop.xsd
    http://www.springframework.org/schema/tx
    http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--整合mybatis-->
    <!--数据源-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/hospital?useUnicode=true&amp;characterEncoding=UTF-8&amp;useSSL=false"/>
        <property name="username" value="root"/>
        <property name="password" value="123456ybx"/>
    </bean>
    <!--sesson工厂-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="typeAliasesPackage" value="com.xiong.hospital.entity"/>
    </bean>
    <!--持久化对象,说明要让mybatis扫描哪个包-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.xiong.hospital.dao"/>
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
    </bean>
    <!--声明式事务-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="get*" read-only="true"/>
            <tx:method name="find*" read-only="true"/>
            <tx:method name="search*" read-only="true"/>
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>

    <!--织入-->
    <aop:config>
        <aop:pointcut id="txPointCut" expression="execution(* com.xiong.hospital.service.*.*(..))"/>
    </aop:config>

    <!--注解扫描-->
    <context:component-scan base-package="com.xiong"/>
    <aop:aspectj-autoproxy />
</beans>
```


2. mybatis映射文件

放到和接口包相同的resources目录下边

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.xiong.hospital.dao.DeptDao">
    <resultMap id="deptResultMap" type="Dept">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="createTime" column="create_time"/>
        <result property="updateTime" column="update_time"/>
        <collection property="category" select="com.xiong.hospital.dao.CategoryDao.find" column="category_id"/>
    </resultMap>
    <insert id="insert" parameterType="Dept" useGeneratedKeys="true">
        insert into dept values (null, #{category.id}, #{name},#{createTime},#{updateTime});
    </insert>
    <update id="update" parameterType="Dept">
        update dept set category_id=#{category.id},name =#{name},create_time=#{createTime},update_time=#{updateTime} where id=#{id}
    </update>
    <delete id="delete" parameterType="Long">
        delete from dept where id=#{value}
    </delete>
    <select id="findAll" resultMap="deptResultMap">
        select * from dept order by update_time desc;
    </select>
    <select id="findByCategory" resultMap="deptResultMap" parameterType="Long">
        select * from dept where category_id=#{value} order by update_time desc ;
    </select>
</mapper>
```