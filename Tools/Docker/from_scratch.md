## Docker Based Knowledge 

### 关键词: 
- Docker
- From Scratch

### 摘要: 
Docker Beginning

---
### Docker:

1. 管理 Docker 进程

```bash
    sudo systemctl start docker.service # 启动 Docker 守护进程
    sudo systemctl stop docker # 停止 Docker 服务
    sudo systemctl restart docker # 重启 Docker 服务
    sudo systemctl status docker # 查看状态
    sudo journalctl -u docker # 查看日志
```

<details>
    <summary>补充</summary>
        <ul>
	      <li>systemctl：Linux中管理系统服务的工具, 使docker在系统后台运行</li>  
        </ul>
</details>

- stop docker 问题:
```bash
    > sudo systemctl stop docker
    Stopping 'docker.service', but its triggering units are still active: docker.socket
```
stop socket 后即可stop docker.service
```bash
    > sudo systemctl stop docker.socket
    > sudo systemctl stop docker
```
<details>
    <summary>socket</summary>
        <ul>
            <li>docker.sock 是 Docker 的核心通信接口, 使客户端(docker CLI) 与Docker守护进程(dockerd)交互. 1st. 通过文件系统路经与dockerd通信, 而非网络接口(TCP/IP). 2st. 通过文件权限(0660)限制访问(root 和 docker 组用户)</li>
            <li>运行 sudo .. stop docker 后docker是否激活?: 查看status -> Active: inactive (dead) since Thu 2025-05-01 10:21:10 可知docker.service是非激活的, 但socket是激活的</li>
        </ul>
</details>

- 客户端(通过socket)激活dockerd
```bash
    # 通过 curl 激活dockerd
    curl --unix-socket /var/run/docker.sock http://localhost/info
    # 若权限不足使用sudo提权
```

2. Docker 配置镜像

Docker 拉取镜像通过 Docker Daemon 网络连接, 而非本地的shell
配置阿里云镜像, 位于路经```/etc/docker/deamon.json```
```json
{
    "registry-mirrors": [
        "https://docker.m.daocloud.io",
        "https://hub-mirror.c.163.com"
    ]
}
```

重启服务
```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

3. Docker 配置代理
位于路经```/etc/systemd/system/docker.service.d/http-proxy.conf```
```conf
[Service]
Environment="HTTP_PROXY=http://127.0.0.1:7890"
Environment="HTTPS_PROXY=http://127.0.0.1:7890"
Environment="ALL_PROXY=socks5://127.0.0.1:7891"
```

重启docker服务
```bash
sudo systemctl daemon-reload && sudo systemctl restart docker
```

---
---
### Docker Project

项目:
```
my-springboot-app/
├── Dockerfile
├── pom.xml
├── mvnw
└── src/
    └── main/
        └── java/
            └── com/example/demo/
                └── DemoApplication.java
```

1. Dockerfile

```Dockerfile
FROM openjdk:17-jdk-slim
COPY . /app
WORKDIR /app
RUN ./mvnw package
CMD ["java", "-jar", "target/app.jar"]
```

1️⃣ FROM openjdk:17-jdk-slim

- 指定基础镜像(根据已有的镜像)

2️⃣ COPY . /app

- 把当前目录下所有文件复制到容器中 /app 下

        ⚠️ 注意：如果你有不需要打进镜像的文件（如 .git, logs, node_modules 等），创建 .dockerignore 文件排除它们.

3️⃣ WORKDIR /app

- 工作目录。后续所有的命令（如 RUN, CMD, COPY）都会在这个目录下执行

4️⃣ RUN ./mvnw package

- ./mvnw package: 使用 Maven Wrapper 构建项目, 生成打包后的 JAR 文件

- package 是 Maven 的生命周期阶段之一, 为编译, 测试并打包为JAR

5️⃣ CMD ["java", "-jar", "target/app.jar"]

- java -jar target/app.jar：运行打包好的 Java 应用程序

        💡 CMD 可以被 docker run 命令覆盖，适合用于设置默认行为.
