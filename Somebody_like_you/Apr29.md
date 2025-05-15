##  Docker

### 关键词: 
- System
- Working Environment

### 摘要: 
Docker 因其容器化技术, 便捷化开发环境的移植.

### 内容:

1. 基本容器的生命周期(Basic Container Lifecycle)

```bash
    # Pull an image from Docker Hub
    docker pull nginx

    # Run a container from the image
    docker run -d -p 8080:80 --name my-nginx nginx

    # List running containers
    docker ps

    # Access the container (curl or browser)
    curl localhost:8080

    # Stop the container
    docker stop my-nginx

    # Remove the container
    docker rm my-nginx
```

2. 使用 Dockerfile 构建自定义镜像

```dockerfile
    # Start from a base image
    # a lightweight version of Debian with Python 3.9 pre-installed
    FROM python:3.9-slim 
    
    # Set working directory
    WORKDIR /app
    
    # Copy requirements first to leverage laryer caching
    COPY requirements.txt .
    RUN pip install -r requirements.txt

    # Copy the rest of the application e.g. app.py  models.py  To  /app
    COPY . .
    
    # Expose port and define entrypoint  run app.py
    EXPOSE 5000
    CMD ["python", "app.py"] 
```

<details>
    <summary>补充</summary>
        <ul>
	      <li><strong>RUN pip install -r requirements.txt</strong>：先将requirements.txt 文件从本地机器复制到容器中(/app)中, 然后安装.txt中的所有 Python包.</li>
          <li><strong>Why</strong>: 为什么先复制 -> 缓存对比, 如果requiements.txt没有变化, Docker 不会重新安装依赖项</li>
        </ul>
</details>

运行与构建

```bash
    # Build the image
    docker build -t my-python-app .

    # Run the container
    docker run -d -p 5000:5000 --name python-app my-python-app
```

3. 持久化数据和卷(volume)

```bash
    # Create volume
    docker volume create my-data

    # Run a container with the volume mounted
    docker run -d \
        --name mysql-db \
        -v my-data:/var/lib/mysql \
        -e MYSQL_ROOT_PASSWORD=secret \
        mysql:5.7

    # Verify the volume
    docker volume inspect my-data
```


<details>
    <summary>补充</summary>
        <ul>
	      <li><strong>.</strong>： .</li>
        </ul>
</details>
