## Docker Based Knowledge 

### 关键词: 
- Docker
- From Scratch

### 摘要: 
Docker 的开始之旅

### 情形:

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
