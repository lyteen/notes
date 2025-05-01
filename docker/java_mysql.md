## Docker 构建Java+Mysql环境 

### 关键词: 
- Docker
- Java
- Mysql

### 摘要: 
关于 Docker 建立java与mysql工作环境

### 情形:

1. Java 启动

- Linux 安装java

```bash
    sudo pacman -Ss jdk
    sudo pacman -S jdk17-openjdk
    # check java work
    archlinux-java status
```
output:
```bash
❯ archlinux-java status
Available Java environments:
  java-17-openjdk (default)
```
配置路经
```bash
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk
    export PATH=$JAVA_HOME/bin:$PATH
```

2. 配置java项目

- 使用Spring Initializr 快速生成
[start.spring](https://start.spring.io/)
点击Generate后解压,进入项目

- 构建java项目
```bash
    # 如果是mvnw项目
    ./mvnw clean package
    # 如果是gradlew项目
    ./gradlew build
```
output:
```bash
❯ ./gradlew build
Starting a Gradle Daemon, 1 incompatible Daemon could not be reused, use --status for details
OpenJDK 64-Bit Server VM warning: Sharing is only supported for boot loader classes because bootstrap classpath has been appended

BUILD SUCCESSFUL in 33s
7 actionable tasks: 7 executed
```
3. 构建Dockerfile

在项目根目录构建Dockerfile
```
# Use OpenJDK 17 as Based image
FORM openjdk:17-jdk-alplne

# Workspace
WORKDIR /app

# Copy Gradle builded JAR file
COPY build/libs/demo-0.0.1-SNAPSHOT-plain.jar /app/app.jar

# Run
ENTRYPOINT ["java", "-jar", "app.jar"]
```
output:
```bash
❯ ./gradlew build

BUILD SUCCESSFUL in 598ms
7 actionable tasks: 7 up-to-date
```
4. 

<details>
    <summary>补充</summary>
        <ul>
	      <li><strong>.</strong>： .</li>
        </ul>
</details>
