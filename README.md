## 个人网站搭建系列: Spring Boot Hello World
### 小插曲
本系列文章，持续更新，关注浩的个人网站<http://imlinhao.cfapps.io/>，以及文章导引页 <http://blog.csdn.net/jksl007/article/details/44183559>

### 基础准备
#### 开发环境及工具
* Jdk 1.7及以上
* Maven 3.2及以上 [下载地址](http://maven.apache.org/download.cgi)

#### 参考站点
* 本文源码 <https://github.com/imlinhao/website-hello-world>
* Spring Boot Reference Guide <http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started>

#### 目标与方法描述
通过Spring Boot快速地建立一个应用。

通过编写pom.xml来引入Spring Boot提供的基础组件，然后编写Example.java来包含基本的Spring Boot应用逻辑。

### Spring Boot Hello World
#### pom.xml
首先新建Maven的`pom.xml`文件，如下：

    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>com.example</groupId>
        <artifactId>myproject</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>1.2.2.RELEASE</version>
        </parent>
    
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
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
`parent`节中`spring-boot-starter-parent`提供了许多有用的Maven设置。`dependency`中的`spring-boot-starter-web`，提供了web开发所需要的诸多依赖。`plugin`下的`spring-boot-maven-plugin`主要是用于编译可执行jar包，下文中我们还会进一步解释。

#### Example.java
由于Maven默认会编译`src/main/java`目录下的代码，所以需要先建立这个目录结构，然后添加一个文件`src/main/java/Example.java`

    import org.springframework.boot.*;
    import org.springframework.boot.autoconfigure.*;
    import org.springframework.stereotype.*;
    import org.springframework.web.bind.annotation.*;
    
    @RestController
    @EnableAutoConfiguration
    public class Example {
    
        @RequestMapping("/")
        String home() {
            return "Hello World!";
        }
    
        public static void main(String[] args) throws Exception {
            SpringApplication.run(Example.class, args);
        }
    
    }

代码中的Annotation解释如下：

* `@RestController` : `@RestController`继承自`@Controller`，该注解表示Example类为一个web `@Controller`，Spring在处理web请求时，可以使用它。
* `@RequestMapping` : `@RequestMapping`提供了“路由”信息，在此它告诉Spring，任何路径为`"/"`的HTTP请求都将映射到`home`方法。之前的`@RestController`表明Spring将会直接回复“原始字符串”给调用者。
* `@EnableAutoConfiguration` : 这个注解告诉Spring，根据已经配置的jar的依赖关系，去猜测如何配置Spring。比如之前的`spring-boot-starter-web`包含了Tomcat和Spring MVC，那么auto-configuration就会假设目前正在进行web应用开发，从而对Spring进行相应的配置。

`main`方法：代码中，最后一块需要解释的部分是`main`方法，之所以取名为main，主要是遵从了Java应用入口名字为main的惯例。在此，main方法通过调用`run`，代理了Spring Boot的`SpringApplication`类。`SpringApplication`将会启动应用，启动中的Spring将会启动被auto-configure的Tomcat web server。我们需要将`Example.class`作为`run`方法的一个参数，告诉`SpringApplication`，基础的Spring组件是谁。`args`参数，则是为了接收命令行传递过来的参数。

### 启动应用
拥有了`pom.xml`和`Example.java`之后，我们的应用就可以启动了。由于我们使用了`spring-boot-starter-parent` POM，从而拥有了一个非常有用的goal `run`，可以通过它来启动应用。通过命令`mvn spring-boot:run`，我们便可以启动应用了。

    $ mvn spring-boot:run
    
      .   ____          _            __ _ _
     /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
    ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
     \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
      '  |____| .__|_| |_|_| |_\__, | / / / /
     =========|_|==============|___/=/_/_/_/
     :: Spring Boot ::  (v1.2.2.RELEASE)

启动之后，打开浏览器输入<http://localhost:8080>，就可以看到如下输出：

    Hello World!

退出应用，在命令行按下`ctrl-c`

### 建立可执行jar包
可执行jar包，包含了工程编译的class文件，以及class文件所依赖的所有jar包。

为了建立可执行jar包，我们需要使用`spring-boot-maven-plugin`，查看之前给出的`pom.xml`，我们已经在`plugin`下给出了这个插件。

命令行执行`mvn package`

    $ mvn package
    
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------------------------------------------------------------
    [INFO] Building myproject 0.0.1-SNAPSHOT
    [INFO] ------------------------------------------------------------------------
    [INFO] .... ..
    [INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ myproject ---
    [INFO] Building jar: /Users/developer/example/spring-boot-example/target/myproject-0.0.1-SNAPSHOT.jar
    [INFO]
    [INFO] --- spring-boot-maven-plugin:1.2.2.RELEASE:repackage (default) @ myproject ---
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------

这个时候，查看`target`目录，里面将会有个`myproject-0.0.1-SNAPSHOT.jar`。然后执行`java -jar`命令:

    $ java -jar target/myproject-0.0.1-SNAPSHOT.jar
    
      .   ____          _            __ _ _
     /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
    ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
     \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
      '  |____| .__|_| |_|_| |_\__, | / / / /
     =========|_|==============|___/=/_/_/_/
     :: Spring Boot ::  (v1.2.2.RELEASE)

项目又以可执行jar包的方式运行了，退出，只要命令行敲入`ctrl-c`。
