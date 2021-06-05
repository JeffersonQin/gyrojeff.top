---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2020-11-18 10:45:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/84.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:57:33'
parent: 0
password: ''
slug: '41'
status: publish
tags: [Docker, Linux, MySQL, Shell]
template: ''
title: Docker常用命令与部署MySQL
type: post
---
# 使用Docker直接部署MySQL

拉取官方最新的MySQL镜像：

```bash
docker pull mysql:latest
```

运行：

```bash
docker run --name <name> -e MY_ROOT_PASSWORD=<root_password> -p <port>:3306 -d mysql:<tag>
```

其中：

- `<name>`填入容器的命名
- `<root_password>`填入数据库`root`用户的密码
- `<port>`填入想要对外映射的端口
- `<tag>`是镜像的版本号，建议填`latest`

传送门：https://hub.docker.com/r/library/mysql/

# Docker的一些常用命令

查看所有Docker:

```bash
sudo docker ps -a
```

删除一个容器：

```bash
sudo docker rm <name>
```

停止：

```bash
sudo docker stop <name>
```

暂停：

```bash
sudo docker pause <name>
```

重启：

```bash
sudo docker restart <name>
```

让容器执行命令：

```bash
sudo docker exec -it <name> <exec>
```

其中：

- `<name>`是容器名称
- `<exec>`是命令
- `-it`的官方解释：
  
  ```
  -d, --detach               Detached mode: run command in the background
      --detach-keys string   Override the key sequence for detaching a container
  -e, --env list             Set environment variables
  -i, --interactive          Keep STDIN open even if not attached
      --privileged           Give extended privileges to the command
  -t, --tty                  Allocate a pseudo-TTY
  -u, --user string          Username or UID (format: <name|uid>[:<group|gid>])
  -w, --workdir string       Working directory inside the container
  ```
  
  所以进入容器其实可以直接 (如果对方有`/bin/bash`)：
  
  ```bash
  docker exec -it <name> /bin/bash
  ```

其余的请直接查看help:

```bash
sudo docker --help
```

```bash
sudo docker <exec> --help
```

