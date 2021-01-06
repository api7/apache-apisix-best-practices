# Ubuntu 安装 APISIX

如何在 Ubuntu 系统上通过 Docker 快速安装并运行 APISIX 服务，本教程将依据 [APISIX 官方文档](https://github.com/apache/apisix/blob/master/README.md)，将带您从零搭建 APISIX 服务。

## APISIX 安装

本教程通过 Docker 方式快速在 Ubuntu 系统上部署 APISIX 服务，这里已经默认你安装了 Docker 。

1、拉取 APISIX 发布包

```shell
$ docker pull apache/apisix
```

注：默认会拉取最新的版本，并且 Docker 镜像中并不包含 etcd 。

2、查看 APISIX 镜像

```shell
$ docker images
REPOSITORY                 TAG                 IMAGE ID       CREATED         SIZE
apache/apisix             latest              730b88f3d8e4   4 weeks ago     240MB
```

3、启动及进入容器

```shell
$ docker run -it apache/apisix:latest /bin/bash
```

4、容器中安装 etcd

```bash
# 下载 etcd 安装包
$ wget https://github.com/etcd-io/etcd/releases/download/v3.4.13/etcd-v3.4.13-linux-amd64.tar.gz

# 解压安装
$ tar -xvf etcd-v3.4.13-linux-amd64.tar.gz && \
    cd etcd-v3.4.13-linux-amd64 && \
    sudo cp -a etcd etcdctl /usr/bin/

# 开启 etcd 服务
$ nohup etcd &
```

>注意：Apache APISIX 从 v2.0 开始不再支持 etcd v2 协议，并且 etcd 最低支持版本为 v3.4.0

5、检查 APISIX 的版本号

```shell
$ apisix version

2.1
```

6、启动 APISIX 服务

```shell
$ apisix start
```

7、查看 APISIX 启动情况

```shell
$ ps -ef | grep nginx
root         492       0  0 07:55 ?        00:00:00 nginx: master process openresty -p /usr/local/apisix -c /usr/local/apisix/conf/nginx.conf
nobody       493     492  0 07:55 ?        00:00:00 nginx: worker process
nobody       494     492  0 07:55 ?        00:00:00 nginx: worker process
nobody       495     492  0 07:55 ?        00:00:00 nginx: cache manager process
nobody       496     492  0 07:55 ?        00:00:00 nginx: cache loader process
root         497     492  0 07:55 ?        00:00:00 nginx: privileged agent process
root         499     300  0 07:55 pts/3    00:00:00 grep --color=auto nginx
```

可以看到 APISIX 服务进程信息，说明启动成功。

## 验证 APISIX 服务

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

## 总结

本教程是基于 Ubuntu 系统通过 Docker 快速部署 APISIX 服务，并做了一个简单的代理请求验证。更多安装方式请查看 [APISIX 官方安装文档](https://github.com/apache/apisix/blob/master/README.md) 。
