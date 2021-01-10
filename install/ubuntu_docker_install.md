# Ubuntu 系统通过 Docker 快速安装 APISIX

如何在 Ubuntu 系统上通过 Docker 快速安装并运行 APISIX 服务，本教程将依据 [APISIX 官方文档](https://github.com/apache/apisix/blob/master/README.md)，将带您从零搭建 APISIX 服务。

## APISIX 安装

本教程通过 Docker 方式快速在 Ubuntu 系统上部署 APISIX 服务，这里已经默认你安装了 Docker 。

1、下载 apisix-docker

```shell
git clone https://github.com/apache/apisix-docker.git
```

2、通过 docker-compose 快速部署

```shell
# 进入到 apisix-docker 的 example 目录下
$ cd apisix-docker/example/

# 执行下面命令，部署 APISIX 并启动应用
$ docker-compose -p docker-apisix up -d
```

3、查看 APISIX 服务

在执行完 `docker-compose -p docker-apisix up -d` 命令后，已经自动完成 APISIX 相关服务的部署。

```shell
# 查看镜像，可以看到有三个镜像，其中 nginx 的镜像是用于模拟提供上游服务
$ docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
docker.io/nginx           1.18.0-alpine       f2343e2e2507        3 weeks ago         21.9 MB
docker.io/apache/apisix   2.1-alpine          f11935317510        5 weeks ago         113 MB
docker.io/bitnami/etcd    3.4.9               ea2f063671fe        5 months ago        132 MB

#查看服务运行状态，可以看到除了 APISIX 和 etcd 应用外，还启动了两个用于模拟上游服务的 nginx 容器
$ docker ps
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS              PORTS                                            NAMES
5987459f5408        apache/apisix:2.1-alpine   "sh -c '/usr/bin/a..."   31 minutes ago      Up 30 minutes       0.0.0.0:9080->9080/tcp, 0.0.0.0:9443->9443/tcp   dockerapisix_apisix_1
8cf3e81b8c22        bitnami/etcd:3.4.9         "/entrypoint.sh etcd"    40 minutes ago      Up 31 minutes       0.0.0.0:2379->2379/tcp, 2380/tcp                 dockerapisix_etcd_1
18c9051da2e7        nginx:1.18.0-alpine        "/docker-entrypoin..."   56 minutes ago      Up 56 minutes       0.0.0.0:9081->80/tcp                             dockerapisix_web1_1
17cd15f0a6b7        nginx:1.18.0-alpine        "/docker-entrypoin..."   56 minutes ago      Up 56 minutes       0.0.0.0:9082->80/tcp                             dockerapisix_web2_1
```

可以看到 APISIX 服务已经成功部署。

## 验证 APISIX 服务

下面将基于 APISIX 的路由来做一个简单的验证。

1、创建路由

这里使用 `httpbin` 模拟一个 API 服务，该服务可以回显发送到这个 API 请求数据。这里创建一个 id 为 1 ，`uri` 为 `/get` ，上游地址服务地址为 `httpbin.org` 的路由：

```shell
curl http://127.0.0.1:9080/apisix/admin/routes/1 -H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT -d '
{
    "upstream": {
       "nodes": {
           "httpbin.org": 1
       },
       "type": "roundrobin"
    },
    "uri": "/get"
}'
```

2、测试

通过执行下面命令，返回 200 状态码及响应 body 数据，说明 APISIX 服务能正常：

```shell
$ curl http://127.0.0.1:9080/get -i
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 299
Connection: keep-alive
Date: Thu, 31 Dec 2020 05:19:46 GMT
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
Server: APISIX/2.1

{
  "args": {}, 
  "headers": {
    "Accept": "*/*", 
    "Host": "127.0.0.1", 
    "User-Agent": "curl/7.29.0", 
    "X-Amzn-Trace-Id": "Root=1-5ff14c2a-2d5f625a691b375f42afae70", 
    "X-Forwarded-Host": "127.0.0.1"
  }, 
  "origin": "127.0.0.1, 121.41.116.83", 
  "url": "http://127.0.0.1/get"
}
```

更多示例请查看 apisix-docker 的 [README.md](https://github.com/apache/apisix-docker/blob/master/example/README.md) 文档。

## 总结

本教程是基于 Ubuntu 系统通过 Docker 快速部署 APISIX 服务，并做了一个简单的代理请求验证。更多安装方式请查看 [APISIX 官方安装文档](https://github.com/apache/apisix/blob/master/README.md) 。
