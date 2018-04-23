+++
date = "2017-05-15T20:27:23+08:00"
title = "SSM框架配置"
Categories = ["development"]
Tags = []
+++

* 最近学了SSM框架，在此整理一下配置过程
* 推荐使用maven配置依赖，在pom.xml中添加需要的依赖包


```    
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.zzb</groupId>
    <artifactId>myblog</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>war</packaging>
    <name>myblog</name>
    <url>http://maven.apache.org</url>
    <properties>
    <springside.version>4.2.2.GA</springside.version>
         <!-- spring版本号 -->
        <spring.version>4.0.5.RELEASE</spring.version>
        <!-- mybatis版本号 -->
        <mybatis.version>3.2.5</mybatis.version>
        <mybatis-spring.version>1.2.2</mybatis-spring.version>
        <logback.version>1.1.1</logback.version>
        <tomcat-jdbc.version>7.0.52</tomcat-jdbc.version>
        <jackson.version>2.3.1</jackson.version>
        
        <!-- Plugin的属性定义 -->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
        <jdbc.driver.groupId>mysql</jdbc.driver.groupId>
        <jdbc.driver.artifactId>mysql-connector-java</jdbc.driver.artifactId>
        <jdbc.driver.version>5.1.22</jdbc.driver.version>
    </properties>
    <dependencies>
    <!-- JSON begin -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>${jackson.version}</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.module</groupId>
            <artifactId>jackson-module-jaxb-annotations</artifactId>
            <version>${jackson.version}</version>
        </dependency>
        <!-- JSON end -->
        
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>${logback.version}</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
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
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>${mybatis.version}</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>${mybatis-spring.version}</version>
        </dependency>
        <!-- connection pool -->
        <dependency>
            <groupId>org.apache.tomcat</groupId>
            <artifactId>tomcat-jdbc</artifactId>
            <version>${tomcat-jdbc.version}</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>${jdbc.driver.groupId}</groupId>
            <artifactId>${jdbc.driver.artifactId}</artifactId>
            <version>${jdbc.driver.version}</version>
            <scope>runtime</scope>
        </dependency>
        
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.1.41</version>
        </dependency>
        <dependency>
            <groupId>javax.activation</groupId>
            <artifactId>activation</artifactId>
            <version>1.1.1</version>
        </dependency>
        
        <dependency>
            <groupId>javax.mail</groupId>
            <artifactId>mail</artifactId>
            <version>1.4.7</version>
        </dependency>
        <dependency>
            <groupId>commons-lang</groupId>
            <artifactId>commons-lang</artifactId>
            <version>2.6</version>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.3.2</version>
        </dependency>
        <dependency>
            <groupId>commons-collections</groupId>
            <artifactId>commons-collections</artifactId>
            <version>3.1</version>
        </dependency>
        <dependency>
            <groupId>org.jsoup</groupId>
            <artifactId>jsoup</artifactId>
            <version>1.7.3</version>
        </dependency>
        <dependency>
            <groupId>commons-codec</groupId>
            <artifactId>commons-codec</artifactId>
            <version>1.8</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp.jstl</groupId>
            <artifactId>javax.servlet.jsp.jstl-api</artifactId>
            <version>1.2.1</version>
        </dependency>
        <dependency>
            <groupId>taglibs</groupId>
            <artifactId>standard</artifactId>
            <version>1.1.2</version>
        </dependency>     
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    </dependencies>
    <build>
        <plugins>
            <!-- compiler插件, 设定JDK版本 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                    <showWarnings>true</showWarnings>
                </configuration>
            </plugin>
            <!-- war打包插件, 设定war包名称不带版本号 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>2.4</version>
                <configuration>
                    <warName>${project.artifactId}</warName>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>http://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
    
    </project>
```

* 在src中的resource包新建mybatis文件夹、applicationContext.xml、jdbc.properties、config.properties
* applicationContext.xml

```    
     <description>Spring公共配置</description>
    
    <!-- 开启定时任务 -->
    <task:annotation-driven/>
    <!-- MyBatis配置 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!-- 显式指定Mapper文件位置 -->
        <property name="mapperLocations" value="classpath*:/mybatis/*Mapper.xml" />
        <!-- mybatis配置文件路径 -->
        <property name="configLocation" value="classpath:/mybatis/config.xml"/>    
    </bean>
    
    <bean id="sqlSessionTemplate" class="org.mybatis.spring.SqlSessionTemplate">
       <constructor-arg index="0" ref="sqlSessionFactory" />
       <!-- 这个执行器会批量执行更新语句, 还有SIMPLE 和 REUSE -->
       <constructor-arg index="1" value="BATCH" />
    </bean>
    <!-- 扫描basePackage接口 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 映射器接口文件的包路径， -->
        <property name="basePackage" value="com.zzb.myblog.dao" />
    </bean>
    <!-- 使用annotation定义事务 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <tx:annotation-driven transaction-manager="transactionManager" proxy-target-class="true" />
    <!-- 数据源配置, 使用Tomcat JDBC连接池 -->
    <bean id="dataSource" class="org.apache.tomcat.jdbc.pool.DataSource" destroy-method="close">
        <!-- Connection Info -->
        <property name="driverClassName" value="${jdbc.driver}" />
        <property name="url" value="${jdbc.url}" />
        <property name="username" value="${jdbc.username}" />
        <property name="password" value="${jdbc.password}" />
        <!-- Connection Pooling Info -->
        <property name="maxActive" value="${jdbc.pool.maxActive}" />
        <property name="maxIdle" value="${jdbc.pool.maxIdle}" />
        <property name="minIdle" value="0" />
        <property name="defaultAutoCommit" value="false" />
    </bean>
    <!-- production环境 -->
    <beans profile="production">
        <context:property-placeholder ignore-unresolvable="true" file-encoding="utf-8" 
        location="classpath:config.properties,classpath:jdbc.properties" />
    </beans>
```

* jdbc.xml

```    
    jdbc.driver=com.mysql.jdbc.Driver
    jdbc.url=jdbc:mysql://localhost:3306/blog?useUnicode=true&characterEncoding=utf-8&autoReconnect=true&allowMultiQueries=true
    jdbc.username=root
    jdbc.password=root
    #connection pool settings
    jdbc.pool.maxIdle=20
    jdbc.pool.maxActive=190
```

* ArticleMapper.xml


```
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace = "com.zzb.myblog.dao.ArticleDao">
    <select id="getRowCount" resultType="int">
        select count(*) from article
    </select>
    <select id="selectByParams" resultType = "com.zzb.myblog.entity.Article" parameterType="map">
        select * from article
        order by id asc
        limit ${offset}, ${size}
    </select>
    </mapper>
</code>

* 在webapp文件夹下建立springmvc.xml 、web.xml
* springmvc.xml
```
 
 ```   
    <!-- 
        spring可以自动去扫描base-package下面或者子包下面的java文件，
        如果扫描到有@Component @Controller @Service @Repository等这些注解的类，则把这些类注册为bean 
    -->
    <context:component-scan base-package="com.zzb.*" />
    <!-- 
        模型解析，在请求时为模型视图名称添加前后缀 
        比如在controller类中需要请求/WEB-INF/page/index.jsp文件，直接写index就可以了
    -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" p:prefix="/WEB-INF/page/" p:suffix=".jsp" />
 ```
 * web.xml

 ```
     <!-- spring框架必须定义ContextLoaderListener，在启动Web容器时，自动装配Spring applicationContext.xml的配置信息 -->
    <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <context-param>
    <!-- 指定Spring上下文配置文件 -->
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath*:applicationContext.xml</param-value>
    </context-param>
    <context-param>
    <param-name>spring.profiles.active</param-name>
    <param-value>production</param-value>
    </context-param>
    <servlet>
    <servlet-name>Dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <!-- 指定SpringMVC配置文件 -->
      <param-name>contextConfigLocation</param-name>
      <param-value>/WEB-INF/springmvc.xml</param-value>
    </init-param>
    </servlet>
    <servlet-mapping>
      <!-- 指定请求的后缀，可以随意写，这里用.html作为请求后缀 -->
    <servlet-name>Dispatcher</servlet-name>
    <url-pattern>*.html</url-pattern>
    </servlet-mapping>
    <!-- 编码格式为UTF-8 -->
    <filter>
      <filter-name>encodingFilter</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <init-param>
         <param-name>encoding</param-name>
         <param-value>UTF-8</param-value>
      </init-param>
      <init-param>
         <param-name>forceEncoding</param-name>
         <param-value>true</param-value>
      </init-param>
    </filter>
    <filter-mapping>
     <filter-name>encodingFilter</filter-name>
     <url-pattern>/*</url-pattern>
    </filter-mapping>
    <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    </welcome-file-list> 
    </web-app>
 ```

#### 整个demo在我的GitHub：

* https://github.com/pugongyingbo/myblog-ssm