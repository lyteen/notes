##  Images-manageer

### 关键词: 
- docker

### 摘要: 


### 情形:
1. 操作汇总
---
| 操作 | 命令 |
|------|------|
| 查看本地镜像列表 | `docker images` |
| 查看镜像详细信息 | `docker inspect <image>` |
| 查看镜像/容器/卷等占用空间 | `docker system df` |
| 查看更详细的磁盘使用情况 | `docker system df -v` |
| 删除指定镜像 | `docker rmi <image>` |
| 强制删除镜像 | `docker rmi -f <image>` |
| 清理无用的镜像, 容器, 网络, 缓存等 | `docker system prune` |
| 清理所有未使用的资源 | `docker system prune -a` |
---

- 通常使用```docker images```后会查看到一下输出

| PEPOSITORY | TAG | IMAGE ID | CREATED | SIZE |
|-----|-----|-----|-----|-----|
| mysql | 8.0 | 00a235cb31f | 4 days ago | 702MB |
| openjdk | 17-jdk-alpine | 15a235cb31f | 3 days ago | 702MB |
---
\<image> 可以 IMAGE ID 或者 PEPOSITORY + TAG

<details>
    <summary>补充</summary>
        <ul>
	      <li><strong>.</strong>： .</li>
        </ul>
</details>