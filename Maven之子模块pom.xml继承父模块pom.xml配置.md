## 介绍

Maven中可以通过继承父模块pom，来实现pom.xml配置的继承和传递，便于各种Maven插件以及程序依赖的统一管理。通过将子类模块的公共配置，抽象聚合生成父类模块，能够避免pom.xml的重复配置。由于父类模块本身并不包含除了POM之外的项目文件，也就不需要src/main/java之类的文件夹了。每当需要对多个子模块进行相同的配置时，只需要在父类模块的pom中进行配置，而子类中声明使用此配置即可，当然子类pom中也可以自定义配置，并覆盖父类中的各项配置，和Java中类的继承类似。



## 可继承的POM元素

1) groupId：项目组ID，项目坐标的核心元素

2) version：项目版本，项目坐标的核心元素

3) description：项目的表述信息

4) organization：项目的组织信息

5) inception Year：项目的创始年份

6) url：项目的URL地址

7) developers：项目的开发者信息

8) contributors：项目的贡献者信息

9) distributionManagement：项目的部署管理

10) issueManagement：项目的缺陷和跟踪系统信息

11) ciManagement：项目的持续集成信息系统

12) scm：项目的版本控制系统信息

13) mailingLists：项目的邮件列表信息

14) properties：自定义的Maven属性

15) dependencies：项目的依赖属性

16) dependencyManagement：项目的依赖管理配置

17) repositories：项目的仓库配置

18) build：包括项目的源码目录配置、输出目录配置、插件配置、插件管理配置等

19) reporting：包括项目的报告输出目录配置、报告插件配置等




## POM继承中的依赖管理和插件管理

Maven提供的dependencyManagement和pluginManagement元素用于帮助POM继承过程中的依赖管理和插件管理。在父类POM下，此两个元素中的声明的依赖或配置并不会引入实际的依赖或是造成实际的插件调用行为，不过它们能够约束子类POM中的依赖和插件配置的声明。只有当子类POM中配置了真正的dependency或plugin，并且其groupId和artifactId与父类POM中dependencyManagement和pluginManagement相对应时，才会进行实际的依赖引入或插件调用，当然子类中也能够进行自定义配置去覆盖父类，或是额外声明自己的配置


## POM继承示例

**1) 在父类POM中使用`dependencyManagement`和`pluginManagement`，声明子类POM中可能用到的依赖和插件**

```xml
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.tomandersen</groupId>
    <artifactId>HadoopCustomModules</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>flume</module>
        <module>log-collector</module>
    </modules>

    <!--事先声明版本属性-->
    <properties>
        <slf4j.version>1.7.20</slf4j.version>
        <logback.version>1.0.7</logback.version>
    </properties>
	<!-- 在这个 POM 文件中，<properties> 标签的功能是定义了一些属性，这些属性可以在整个 POM 文件中重复使用，以及在子项目的 POM 文件中继承和覆盖使用。这种方式可以使 POM 文件更加简洁和易于维护。

具体来说，<properties> 标签中声明了两个属性：

<slf4j.version>: 它定义了 SLF4J（Simple Logging Facade for Java）的版本号。
<logback.version>: 它定义了 Logback 日志框架的版本号。
这些属性的作用在于，你可以在 POM 文件中通过 ${slf4j.version} 和 ${logback.version} 的方式引用它们，而不需要在多个地方重复写版本号。这样做的好处是，如果你需要更新版本号，只需在一个地方修改即可，而不需要遍历整个 POM 文件。-->


    <!--在父类Maven中使用dependencyManagement声明依赖便于子类Module继承使用,也便于进行依赖版本控制-->
    <dependencyManagement>
        <dependencies>
            <!--阿里巴巴开源json解析框架-->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>fastjson</artifactId>
                <version>1.2.51</version>
            </dependency>

            <!--日志生成框架-->
            <dependency>
                <groupId>ch.qos.logback</groupId>
                <artifactId>logback-core</artifactId>
                <version>${logback.version}</version>
            </dependency>

            <dependency>
                <groupId>ch.qos.logback</groupId>
                <artifactId>logback-classic</artifactId>
                <version>${logback.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>


    <build>
        <!--在父类Maven中使用pluginManagement管理插件便于子类Module继承使用,也便于进行依赖版本控制-->
        <pluginManagement>
            <plugins>
                <!--配置Maven项目compiler插件-->
                <!--此工具不会打包依赖-->
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>2.3.2</version>
                    <configuration>
                        <source>1.8</source>
                        <target>1.8</target>
                    </configuration>
                </plugin>

				<!--配置Maven项目assembly插件-->
            	<!--此工具会将全部依赖打包-->
                <plugin>
                    <artifactId>maven-assembly-plugin</artifactId>
                    <configuration>
                        <descriptorRefs>
                            <descriptorRef>jar-with-dependencies</descriptorRef>
                        </descriptorRefs>
                        <archive>
                            <manifest>
                                <!--子类Maven通过mainClass标签设置成主类的全类名FQCN-->
                                <!--<mainClass></mainClass>-->
                            </manifest>
                        </archive>
                    </configuration>
                    <executions>
                        <execution>
                            <id>make-assembly</id>
                            <phase>package</phase>
                            <goals>
                                <goal>single</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>

            </plugins>
        </pluginManagement>

    </build>

```

**2) 在子类POM中声明父类POM，并配置实际使用的`dependency`和`plugin`，只需要通过声明`groupId`和`artifactId`就可以避免配置各种依赖和插件的详细配置，当然也可以自己覆盖父类配置信息**

```xml
    <!--声明父类POM-->
    <parent>
        <artifactId>HadoopCustomModules</artifactId>
        <groupId>com.tomandersen</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <!--子类POM信息-->
    <groupId>com.tomandersen</groupId>
    <artifactId>log-collector</artifactId>

    <dependencies>
        <!--阿里巴巴开源json解析框架-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
        </dependency>

        <!--日志生成框架-->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-core</artifactId>
        </dependency>
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!--自定义Maven项目编译器compiler插件相关配置-->
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
            </plugin>

            <!--自定义Maven项目汇编assembly插件相关配置-->
            <plugin>
                <artifactId>maven-assembly-plugin</artifactId>
                <configuration>
                    <archive>
                        <manifest>
                            <!--此处设置成主类的全名-->
                            <mainClass>com.tomandersen.appclient.AppMain</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>

```

