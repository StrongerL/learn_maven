## 定义

Maven 是 Apache 软件基金会组织维护的一款自动化构建工具，专注服务于 Java 平台的项目构建和依赖管理。Maven 这个单词的本意是：专家，内行。读音是['meɪv(ə)n]或['mevn]。

### 构建

构建就是以我们编写的 Java 代码、框架配置文件、国际化等其他资源文件、JSP 页面和图片等静态资源作为“原材料”，去“生产”出一个可以运行的项目的过程。

过程：

1. **清理**：删除以前的编译结果，为重新编译做好准备。
2. **编译**：将 Java 源程序编译为字节码文件。
3. **测试**：针对项目中的关键点进行测试，确保项目在迭代开发过程中关键点的正确性。
4. **报告**：在每一次测试后以标准的格式记录和展示测试结果。
5. **打包**：将一个工程封装为一个压缩文件用于安装或部署。Java 工程对应 jar 包，Web工程对应 war 包。
6. **安装**：在 Maven 环境下特指将打包的结果——jar 包或 war 包安装到本地仓库中。
7. **部署**：将打包的结果部署到远程仓库或将 war 包部署到服务器上运行。

Maven可以自动进行上述过程，也即自动化构建。

## 作用

1. 统一地、便捷地导入jar包
2. 自动处理jar包之间的依赖关系
3. 将项目拆成多个模块

## Maven 项目结构

```
Hello
|---src
|---|---main
|---|---|---java
|---|---|---resources
|---|---test
|---|---|---java
|---|---|---resources
|---pom.xml
```

## POM文件

Project Object Model：项目对象模型。将 Java **项目**的相关信息封装为**对象**作为便于操作和管理的**模型**。Maven 工程的核心配置。

示例

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>

  <name>my-app</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <pluginManagement>
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>
```



## 坐标

### 定义

使用如下三个向量在 Maven 的仓库中唯一的确定一个 Maven 工程，有时简称为gav。

1. **g**roupid：公司或组织的域名倒序+当前项目名称
2. **a**rtifactId：当前项目的模块名称
3. **v**ersion：当前模块的版本

```xml
<groupId>com.atguigu.maven</groupId>
<artifactId>Hello</artifactId>
<version>0.0.1-SNAPSHOT</version>
```

### gav与jar包位置的对应关系

gav

```xml
<groupId>com.atguigu.maven</groupId>
<artifactId>Hello</artifactId>
<version>0.0.1-SNAPSHOT</version>
```

仓库目录下jar包位置

```
com/atguigu/maven/Hello/0.0.1-SNAPSHOT/Hello-0.0.1-SNAPSHOT.jar
```

规则

将 gav 三个向量连起来，以连起来的字符串作为目录结构到仓库中查找，文件名为：\<artifactId>-\<version>.jar



## 依赖

### 作用

导入项目需要的jar包，使用是使用 dependency 标签指定被依赖 jar 包的坐标就可以，示例如下。

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
    <scope>test</scope>
</dependency>
```

### 范围

从作用范围和作用阶段来区分。

- compile：主程序、测试程序、部署运行都需要该依赖；

- test：只有测试时需要该依赖；

- provided：部署运行时不需要该依赖，其他时候需要。

compile的依赖具有传递性，其他没有。例如：A依赖B，B依赖C，如果B依赖C的范围是compile，那么A也依赖C。

以compile范围依赖了B，而B依赖了C，如果只想依赖B而不想要依赖C，可以使用\<exclusion>标签，示例如下。

```xml
<dependency>
    <groupId>com.atguigu.maven</groupId>
    <artifactId>HelloFriend</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <type>jar</type>
    <scope>compile</scope>
    <exclusions> 
        <exclusion> 
            <groupId>commons-logging</groupId> 
            <artifactId>commons-logging</artifactId> 
        </exclusion> 
    </exclusions> 
</dependency>
```

### jar包冲突时的依赖原则

A以compile的范围依赖了B和C，而B和C都依赖了D，但是D的版本不同。A选择D版本的原则为：

1. 路径最短优先
2. 路径长度相同时在A的POM文件中先声明的优先（即dependency的书写顺序）

### 统一管理

对于同一个框架的一组jar包，一般是使用同一版本，这时可以把版本号提取出来，示例如下。

```xml
<properties>
    <atguigu.spring.version>4.1.1.RELEASE</atguigu.spring.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>${atguigu.spring.version}</version>
    </dependency>
</dependencies>
```

其他用法

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>
```

## 继承

由于非 compile 范围的依赖信息是不能在“依赖链”中传递的，所以有需要的工程只能单独配置，例如，依赖链中的每个工程都需要junit依赖，使用继承可以统一管理junit的版本，使用方法如下：

1. 创建父工程，打包方式设置为pom

2. 在子工程中声明父工程

   ```xml
   <parent>
       <!-- 父工程坐标 -->
       <groupId>...</groupId>
       <artifactId>...</artifactId>
       <version>...</version>
       <relativePath>从当前目录到父项目的 pom.xml 文件的相对路径</relativePath>
   </parent>
   ```

3. 在父工程中管理依赖

   ```xml
   <dependencyManagement> 
       <dependencies> 
           <dependency> 
               <groupId>junit</groupId> 
               <artifactId>junit</artifactId> 
               <version>4.9</version> 
               <scope>test</scope>
           </dependency>
       </dependencies>
   </dependencyManagement>
   ```

4. 在子项目中指定需要的依赖，删除范围和版本号

   ```xml
   <dependencies>
       <dependency> 
           <groupId>junit</groupId>
           <artifactId>junit</artifactId>
       </dependency>
   </dependencies>
   ```

   

## 聚合

将多个工程拆分为模块后，需要手动逐个安装到仓库后依赖才能够生效。修改源码后也需要逐个手动进行 clean 操作。而使用了聚合之后就可以批量进行 Maven 工程的安装、清理工作。

在总的聚合工程中使用 modules/module 标签组合，指定模块工程的相对路径即可。

```xml
<modules> 
    <module>../Hello</module> 
    <module>../HelloFriend</module> 
    <module>../MakeFriends</module>
</modules>
```



## 仓库

#### 分类

1. 本地仓库
2. 远程仓库
   1. 私服，一般使用nexus
   2. 中央仓库
   3. 中央仓库镜像（为中央仓库分担流量）

当项目新增了依赖，首先会到本地仓库查询，如果没有再到私服，然后到中央仓库或者其镜像站。

#### 仓库中的文件

1. Maven插件
2. 第三方jar包
3. 自己开发的项目模块

不管是什么样的 jar 包，在仓库中都是按照坐标生成目录结构，所以可以通过统一的方式查询或依赖。



## 生命周期

### 定义

Maven 生命周期定义了各个构建环节的执行顺序，有了这个清单，Maven 就可以自动化的执行构建命令了。Maven有3套相互独立的生命周期，分别是：

1. Clean Lifecycle 在进行真正的构建之前进行一些清理工作。
2. Default Lifecycle 构建的核心部分，编译，测试，打包，安装，部署等等。
3. Site Lifecycle 生成项目报告，站点，发布站点。

每套生命周期都由一组阶段(Phase)组成，我们平时在命令行输入的命令总会对应于一个特定的阶段。比如，运行 mvn clean，这个 clean 是 Clean 生命周期的一个阶段。有 Clean 生命周期，也有 clean 阶段。

### Clean 生命周期

1. pre-clean 执行一些需要在 clean 之前完成的工作
2. clean 移除所有上一次构建生成的文件
3. post-clean 执行一些需要在 clean 之后立刻完成的工作

### Site 生命周期

1. pre-site 执行一些需要在生成站点文档之前完成的工作
2. site 生成项目的站点文档
3. post-site 执行一些需要在生成站点文档之后完成的工作，并且为部署做准备
4. site-deploy 将生成的站点文档部署到特定的服务器上

### Default 生命周期

Default 生命周期是 Maven 生命周期中最重要的一个，绝大部分工作都发生在这个生命周期中。

1. validate
2. generate-sources
3. process-sources
4. generate-resources
5. process-resources 复制并处理资源文件，至目标目录，准备打包。
6. compile 编译项目的源代码。
7. process-classes
8. generate-test-sources
9. process-test-sources
10. generate-test-resources
11. process-test-resources 复制并处理资源文件，至目标测试目录。
12. test-compile 编译测试源代码。
13. process-test-classes
14. test 使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署。
15. prepare-package
16. package 接受编译好的代码，打包成可发布的格式，如 JAR。
17. pre-integration-test
18. integration-test
19. post-integration-test
20. verify
21. install 将包安装至本地仓库，以让其它项目依赖。
22. deploy 将最终的包复制到远程的仓库，以让其它开发人员与项目共享或部署到服务器上运行。

### 生命周期与自动化构建

运行任何一个阶段的时候，它前面的所有阶段都会被运行。例如我们运行 mvn install 的时候，代码会被编译，测试，打包。



## 插件和目标

Maven 的核心仅仅定义了抽象的生命周期，具体的任务都是交由插件完成的。

每个插件都能实现多个功能，每个功能就是一个插件目标。

Maven 的生命周期与插件目标相互绑定，以完成某个具体的构建任务。

例如：compile 就是插件 maven-compiler-plugin 的一个目标；pre-clean 是插件 maven-clean-plugin 的一个目标。



## 如何获取所需依赖的坐标

直接在[Maven仓库 https://mvnrepository.com/](https://mvnrepository.com/)搜索。

## 参考资料

1. 教程地址

   [https://www.bilibili.com/video/BV1TW411g7hP](https://www.bilibili.com/video/BV1TW411g7hP)

2. Maven 官网

   [https://maven.apache.org/](https://maven.apache.org/)

3. Maven 仓库

   [https://mvnrepository.com/](https://mvnrepository.com/)







