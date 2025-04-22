
# 1.为什么要存储

默认情况下，在运行中的容器里创建的文件，被保存在一个可写的容器层：

- 如果容器被删除了，则数据也没有了
- 这个可写的容器层是和特定的容器绑定的，也就是这些数据无法方便的和其它容器共享

Docker主要提供了两种方式做数据的持久化

- Data Volume, 由Docker管理，(/var/lib/docker/volumes/ Linux), 持久化数据的最好方式
- Bind Mount，由用户指定存储的数据具体mount在系统什么位置

# 2. Data Volume

## 2.1环境准备

准备一个Dockerfile 和一个 my-cron的文件

```d
$ ls
Dockerfile  my-cron
$ more Dockerfile
FROM alpine:latest
RUN apk update
RUN apk --no-cache add curl
ENV SUPERCRONIC_URL=https://github.com/aptible/supercronic/releases/download/v0.1.12/supercronic-linux-amd64 \
    SUPERCRONIC=supercronic-linux-amd64 \
    SUPERCRONIC_SHA1SUM=048b95b48b708983effb2e5c935a1ef8483d9e3e
RUN curl -fsSLO "$SUPERCRONIC_URL" \
    && echo "${SUPERCRONIC_SHA1SUM}  ${SUPERCRONIC}" | sha1sum -c - \
    && chmod +x "$SUPERCRONIC" \
    && mv "$SUPERCRONIC" "/usr/local/bin/${SUPERCRONIC}" \
    && ln -s "/usr/local/bin/${SUPERCRONIC}" /usr/local/bin/supercronic
COPY my-cron /app/my-cron
WORKDIR /app

VOLUME ["/app"]

# RUN cron job
CMD ["/usr/local/bin/supercronic", "/app/my-cron"]
$
$ more my-cron
*/1 * * * * date >> /app/test.txt
```

## 2.2构建镜像
```
$ docker image build -t my-cron .
$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
my-cron      latest    e9fbd9a562c9   4 seconds ago   24.7MB
```

## 2.3创建容器(不指定-v参数)
此时Docker会自动创建一个随机名字的volume，去存储我们在Dockerfile定义的volume `VOLUME ["/app"]`
```c
$ docker run -d my-cron
9a8fa93f03c42427a498b21ac520660752122e20bcdbf939661646f71d277f8f
$ docker volume ls
DRIVER    VOLUME NAME
local     043a196c21202c484c69f2098b6b9ec22b9a9e4e4bb8d4f55a4c3dce13c15264
$ docker volume inspect 043a196c21202c484c69f2098b6b9ec22b9a9e4e4bb8d4f55a4c3dce13c15264
[
    {
        "CreatedAt": "2021-06-22T23:06:13+02:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/043a196c21202c484c69f2098b6b9ec22b9a9e4e4bb8d4f55a4c3dce13c15264/_data",
        "Name": "043a196c21202c484c69f2098b6b9ec22b9a9e4e4bb8d4f55a4c3dce13c15264",
        "Options": null,
        "Scope": "local"
    }
]
```
## 2.4创建容器(指定-v参数)
在创建容器的时候通过 `-v` 参数我们可以手动的指定需要创建Volume的名字，以及对应于容器内的路径，这个路径是可以任意的，不必需要在Dockerfile里通过VOLUME定义

比如我们把上面的Dockerfile里的VOLUME删除
```d
FROM alpine:latest
RUN apk update
RUN apk --no-cache add curl
ENV SUPERCRONIC_URL=https://github.com/aptible/supercronic/releases/download/v0.1.12/supercronic-linux-amd64 \
    SUPERCRONIC=supercronic-linux-amd64 \
    SUPERCRONIC_SHA1SUM=048b95b48b708983effb2e5c935a1ef8483d9e3e
RUN curl -fsSLO "$SUPERCRONIC_URL" \
    && echo "${SUPERCRONIC_SHA1SUM}  ${SUPERCRONIC}" | sha1sum -c - \
    && chmod +x "$SUPERCRONIC" \
    && mv "$SUPERCRONIC" "/usr/local/bin/${SUPERCRONIC}" \
    && ln -s "/usr/local/bin/${SUPERCRONIC}" /usr/local/bin/supercronic
COPY my-cron /app/my-cron
WORKDIR /app

# RUN cron job
CMD ["/usr/local/bin/supercronic", "/app/my-cron"]
```
重新build镜像，然后创建容器，加-v参数
```
$ docker image build -t my-cron .
$ docker container run -d -v cron-data:/app my-cron
43c6d0357b0893861092a752c61ab01bdfa62ea766d01d2fcb8b3ecb6c88b3de
$ docker volume ls
DRIVER    VOLUME NAME
local     cron-data
$ docker volume inspect cron-data
[
    {
        "CreatedAt": "2021-06-22T23:25:02+02:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/cron-data/_data",
        "Name": "cron-data",
        "Options": null,
        "Scope": "local"
    }
]
$ ls /var/lib/docker/volumes/cron-data/_data
my-cron
$ ls /var/lib/docker/volumes/cron-data/_data
my-cron  test.txt
```

Volume也创建了。

## 2.5环境清理[](https://dockertips.readthedocs.io/en/latest/docker-volume/data-volume.html#id4 "Permalink to this headline")

强制删除所有容器，系统清理和volume清理
```
$ docker rm -f $(docker container ps -aq)
$ docker system prune -f
$ docker volume prune -f
```
