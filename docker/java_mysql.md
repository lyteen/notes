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
# TODO: Different with docker-compose.yml?

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
4. 配置docker-compose.yml文件

```Yaml
services:
  mysql:
    image: mysql:8.0
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: mydb
      MYSQL_USER: myuser
      MYSQL_PASSWORD: mypass
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - app-network

  java-app:
    build: .
    image: my-java-app:1.0
    container_name: java-app
    ports:
      - "8080:8080"
    depends_on:
      - mysql
    networks:
      - app-network

volumes:
  mysql_data:

networks:
  app-network:
```
<details>
    <summary>docker-compose</summary>
        <ul>
	      <li>Docker编排工具, 用于多容器应用</li>
          <li>一键启动多个容器，并自动处理依赖关系(如数据库先启动)</li>
          <li>通过 YAML 文件定义服务，然后执行 docker-compose up 启动整个应用栈</li> 
        </ul>
</details>


- 运行 docker-compose
```bash
    sudo docker-compose up -d
    # sudo docker-compose up --build #-d
```
<details>
    <summary>docker-compose up -d 与 docker-compose up --build的区别</summary>
        <ul>
	        docker-compose up -d: 仅启动已经构建的容器, 并在后台运行(-d)
            docker-compose up --build: 强制重新构建容器, (-d是否后台(前台)运行)
        </ul>
</details>

- 检查docker容器状态
```bash
    sudo docker ps
```

5. 容器操作

- 进入容器

```bash
    docker exec -it java-app sh
    # docker exec -it mysql bash
```

- 停止容器

```bash
    sudo docker-compose stop
```

- 删除容器

```bash
    sudo docker-compose down
    # 并删除数据卷
    sudo docker-compose down -v
```


