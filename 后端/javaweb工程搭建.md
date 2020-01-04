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

## spring boot工程创建(maven创建)

1. 依赖安装,与打包方式制定

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.xiong</groupId>
    <artifactId>springboot_learn</artifactId>
    <version>1.0-SNAPSHOT</version>
    <repositories>
        <repository>
            <name>aliyun</name>
            <id>aliyun</id>
            <url>https://maven.aliyun.com/repository/public</url>
        </repository>
    </repositories>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.1.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>2.2.1.RELEASE</version>
        </dependency>
    </dependencies>


    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

2. 创建入口类

```java
package com.xiong.springboot_learn;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

// 说明这里是spring boot的入口类
@SpringBootApplication
public class MySpringBootApplication {

    public static void main(String[] args) {
        SpringApplication.run(MySpringBootApplication.class);
    }
}

```


## spring boot工程创建(spring initializr创建 与框架整合)

### 项目创建基本步骤

1. File -> new -> project
2. 左侧选择Spring initializr,右侧选择jdk 1.8以上版本, next
3. 填写group和artificat next
4. 左边web 右边spring web next
5. 填写项目名和路径， finish


### 整合logback

1. 依赖安装

```
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

2. 配置

```xml
<!-- logback.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="60 seconds" debug="false">
    <!-- 定义参数常量 -->
    <!-- TRACE < DEBUG < INFO < WARN < ERROR -->
    <!-- logger.trace("msg") logger.debug... -->
    <property name="log.level" value="debug" />
    <property name="log.maxHistory" value="30" />
    <property name="log.filePath" value="${catalina.base}/logs/webapps" />
    <property name="log.pattern"
              value="%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n" />
    <!-- 控制台设置 -->
    <appender name="consoleAppender" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
    </appender>
    <!-- DEBUG -->
    <appender name="debugAppender" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 文件路径 -->
        <file>${log.filePath}/debug.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 文件名称 -->
            <fileNamePattern>${log.filePath}/debug/debug.%d{yyyy-MM-dd}.log.gz
            </fileNamePattern>
            <!-- 文件最大保存历史数量 -->
            <maxHistory>${log.maxHistory}</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>DEBUG</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>
    <!-- INFO -->
    <appender name="infoAppender" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 文件路径 -->
        <file>${log.filePath}/info.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 文件名称 -->
            <fileNamePattern>${log.filePath}/info/info.%d{yyyy-MM-dd}.log.gz
            </fileNamePattern>
            <!-- 文件最大保存历史数量 -->
            <maxHistory>${log.maxHistory}</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>
    <!-- ERROR -->
    <appender name="errorAppender" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 文件路径 -->
        <file>${log.filePath}/erorr.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 文件名称 -->
            <fileNamePattern>${log.filePath}/error/error.%d{yyyy-MM-dd}.log.gz
            </fileNamePattern>
            <!-- 文件最大保存历史数量 -->
            <maxHistory>${log.maxHistory}</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>
    <logger name="com.xiong" level="${log.level}" additivity="true">
        <appender-ref ref="debugAppender"/>
        <appender-ref ref="infoAppender"/>
        <appender-ref ref="errorAppender"/>
    </logger>
    <root level="info">
        <appender-ref ref="consoleAppender"/>
    </root>
</configuration>
```
### 整合mybatis

1. 依赖安装

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.1</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.3</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.18</version>
</dependency>
```

2. 配置datasource

```java
package com.xiong.springbootseed.config.dao;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

import java.beans.PropertyVetoException;

/**
 * 配置datasource到ioc容器里面
 */
@Configuration
// 配置mybatis mapper的扫描路径
@MapperScan("com.xiong.springbootseed.dao")
public class DataSourceConfiguration {
    @Value("${jdbc.driver}")
    private String jdbcDriver;
    @Value("${jdbc.url}")
    private String jdbcUrl;
    @Value("${jdbc.username}")
    private String jdbcUsername;
    @Value("${jdbc.password}")
    private String jdbcPassword;

    /**
     * 生成与spring-dao.xml对应的bean dataSource
     *
     * @return
     * @throws PropertyVetoException
     */
    @Bean(name = "dataSource")
    public DriverManagerDataSource createDataSource() throws PropertyVetoException {
        // 生成datasource实例
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        // 跟配置文件一样设置以下信息
        // 驱动
        dataSource.setDriverClassName(jdbcDriver);
        // 数据库连接URL
        dataSource.setUrl(jdbcUrl);
        // 设置用户名
        dataSource.setUsername(jdbcUsername);
        // 设置用户密码
        dataSource.setPassword(jdbcPassword);
        return dataSource;
    }

}

```

3. 配置SqlSessionFactoryBean

```java
package com.xiong.springbootseed.config.dao;

import org.mybatis.spring.SqlSessionFactoryBean;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import org.springframework.core.io.support.ResourcePatternResolver;

import javax.annotation.Resource;
import javax.sql.DataSource;
import java.io.IOException;

@Configuration
public class SessionFactoryConfiguration {
    // mybatis-config.xml配置文件的路径
    private static String mybatisConfigFile;

    @Value("${mybaits.config_file}")
    public void setMybatisConfigFile(String mybatisConfigFile) {
        SessionFactoryConfiguration.mybatisConfigFile = mybatisConfigFile;
    }

    // mybatis mapper文件所在路径
    private static String mapperPath;

    @Value("${mybaits.mapper_path}")
    public void setMapperPath(String mapperPath) {
        SessionFactoryConfiguration.mapperPath = mapperPath;
    }

    // 实体类所在的package
    @Value("${mybaits.type_alias_package}")
    private String typeAliasPackage;

    @Resource
    private DataSource dataSource;

    /**
     * 创建sqlSessionFactoryBean 实例 并且设置configtion 设置mapper 映射路径 设置datasource数据源
     *
     * @return
     * @throws IOException
     */
    @Bean(name = "sqlSessionFactory")
    public SqlSessionFactoryBean createSqlSessionFactoryBean() throws IOException {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        // 设置mybatis 配置文件地址
        sqlSessionFactoryBean.setConfigLocation(new ClassPathResource(mybatisConfigFile));
        // mapper的路径可以不配， 不配置的话，在resourses建立和dao包相同的目录即可
        // 添加mapper 扫描路径
//        PathMatchingResourcePatternResolver pathMatchingResourcePatternResolver = new PathMatchingResourcePatternResolver();
//        String packageSearchPath = ResourcePatternResolver.CLASSPATH_ALL_URL_PREFIX + mapperPath;
//        sqlSessionFactoryBean.setMapperLocations(pathMatchingResourcePatternResolver.getResources(packageSearchPath));
        // 设置dataSource
        sqlSessionFactoryBean.setDataSource(dataSource);
        // 设置typeAlias 包扫描路径
        sqlSessionFactoryBean.setTypeAliasesPackage(typeAliasPackage);
        return sqlSessionFactoryBean;
    }

}
```

4. 注意上边@Value注入的属性来自配置文件application.yml

```yml

# DataSource
jdbc:
 driver: com.mysql.cj.jdbc.Driver
 url: jdbc:mysql://127.0.0.1:3306/springbootseed?useUnicode=true&characterEncoding=utf8&useSSL=false
 username: root
 password: 123456ybx

# Mybatis
mybaits:
  config_file: mybatis-config.xml
  type_alias_package: com.xiong.springbootseed.entity
server:
  port: 8082
```

### 整合c3p0

上线的数据源我们用的spring提供的， 也可以改成c3p0, c3p0会更灵活一些

1. 依赖

```xml
<dependency>
    <groupId>com.mchange</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.5.4</version>
</dependency>
```

2. 配置dataSource

```
package com.xiong.springbootseed.config.dao;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.beans.PropertyVetoException;

/**
 * 配置datasource到ioc容器里面
 */
@Configuration
// 配置mybatis mapper的扫描路径
@MapperScan("com.xiong.springbootseed.dao")
public class DataSourceConfiguration {
    @Value("${jdbc.driver}")
    private String jdbcDriver;
    @Value("${jdbc.url}")
    private String jdbcUrl;
    @Value("${jdbc.username}")
    private String jdbcUsername;
    @Value("${jdbc.password}")
    private String jdbcPassword;

    /**
     * 生成与spring-dao.xml对应的bean dataSource
     *
     * @return
     * @throws PropertyVetoException
     */
    @Bean(name = "dataSource")
    public ComboPooledDataSource createDataSource() throws PropertyVetoException {
        ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource();
        comboPooledDataSource.setDriverClass(jdbcDriver);
        comboPooledDataSource.setJdbcUrl(jdbcUrl);
        comboPooledDataSource.setUser(jdbcUsername);
        comboPooledDataSource.setPassword(jdbcPassword);
        // 连接池最大线程数
        comboPooledDataSource.setMaxPoolSize(30);
        // 连接池最小线程数
        comboPooledDataSource.setMinPoolSize(10);
        // 关闭连接后不自动commit
        comboPooledDataSource.setAutoCommitOnClose(false);
        // 连接超时时间
        comboPooledDataSource.setCheckoutTimeout(10000);
        // 连接失败重试次数
        comboPooledDataSource.setAcquireRetryAttempts(2);
        return comboPooledDataSource;
    }

}
```

### 集成FreeMarkder

1. 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
```

2. 配置application.yml

```yml
spring:
  freemarker:
    template-loader-path: classpath:/templates/
    suffix: .ftl
    content-type: text/html
    charset: UTF-8
``` 

### 支持声明式事务

```java
package com.xiong.springbootseed.config.service;

import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;
import org.springframework.transaction.annotation.TransactionManagementConfigurer;

import javax.annotation.Resource;
import javax.sql.DataSource;

/**
 * 对标spring-service里面的transactionManager
 * 继承TransactionManagementConfigurer是因为开启annotation-driven
 *
 */
@Configuration
// 首先使用注解 @EnableTransactionManagement 开启事务支持后
// 在Service方法上添加注解 @Transactional 便可
@EnableTransactionManagement
public class TransactionManagementConfiguration implements TransactionManagementConfigurer {

	@Resource
	// 注入DataSourceConfiguration里边的dataSource,通过createDataSource()获取
	private DataSource dataSource;

	@Override
	/**
	 * 关于事务管理，需要返回PlatformTransactionManager的实现
	 */
	public PlatformTransactionManager annotationDrivenTransactionManager() {
		return new DataSourceTransactionManager(dataSource);
	}

}

```

### 验证码组件Kaptcha整合

1. 依赖

```xml
<dependency>
    <groupId>com.github.penggle</groupId>
    <artifactId>kaptcha</artifactId>
    <version>2.3.2</version>
</dependency>
```

2. 使用编程方式创建servlet

```java
package com.xiong.springbootseed.config.web;

import com.google.code.kaptcha.servlet.KaptchaServlet;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class KaptchaConfiguration {
    @Bean
    ServletRegistrationBean createServletRegistrationBean() {
        ServletRegistrationBean servlet =  new  ServletRegistrationBean(new KaptchaServlet(), "/getCode");
//        servlet.addInitParameter("kaptcha.border", border);// 无边框
//        servlet.addInitParameter("kaptcha.textproducer.font.color", fcolor); // 字体颜色
//        servlet.addInitParameter("kaptcha.image.width", width);// 图片宽度
//        servlet.addInitParameter("kaptcha.textproducer.char.string", cString);// 使用哪些字符生成验证码
//        servlet.addInitParameter("kaptcha.image.height", height);// 图片高度
//        servlet.addInitParameter("kaptcha.textproducer.font.size", fsize);// 字体大小
//        servlet.addInitParameter("kaptcha.noise.color", nColor);// 干扰线的颜色
//        servlet.addInitParameter("kaptcha.textproducer.char.length", clength);// 字符个数
//        servlet.addInitParameter("kaptcha.textproducer.font.names", fnames);// 字体
        return servlet;
    }
}

```

### 图片上传核心配置和代码

1. 依赖

```
 <dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.4</version>
</dependency>
```

2. 配置MultipartResolver

```
package com.xiong.springbootseed.config.web;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.multipart.MultipartResolver;
import org.springframework.web.multipart.commons.CommonsMultipartResolver;

@Configuration
public class MultipartResolverConfiguration {
    @Bean("multipartResolver")
    MultipartResolver createMultipartResolver() {
        CommonsMultipartResolver multipartResolver = new CommonsMultipartResolver();
        multipartResolver.setMaxUploadSize(187336*5);
        return multipartResolver;
    }
}

```

3. 处理文件长传

```
    @PostMapping("/upload")
    @ResponseBody
    public String upload(HttpServletRequest request, @RequestParam("file") MultipartFile multipartFile) throws IOException {
        if (multipartFile != null) {
            //生成uuid作为文件名称
            String uuid = UUID.randomUUID().toString().replaceAll("-","");
            //获得文件类型（可以判断如果不是图片，禁止上传）
            String contentType=multipartFile.getContentType();
            //获得文件后缀名称
            String imageName=contentType.substring(contentType.indexOf("/")+1);
            String pathRoot = "/Users/bear/temp";
            String path="";
            path="/static/images/"+uuid+"."+imageName;
            multipartFile.transferTo(new File(pathRoot+path));
        }
        return  "le";
    }
```