# APISIX 配置文件修改

APISIX 的配置文件在 `conf/` 目录下，本文章将介绍 `config.yaml` 和 `config-default.yaml` 两个配置文件。

## 初识配置文件

APISIX 有一个默认的配置文件 `config-default.yaml` 和一个自定义的配置文件 `config.yaml` ，它们之间是一种增量的关系。如果两个文件中存在相同字段信息的配置，则是 `config.yaml` 文件中的配置生效。

在刚安装的 APISIX 中，默认监听的 HTTP 端口为 9080、HTTPS 端口为 9443，默认允许访问 APISIX 服务的客户端IP地址范围为 `127.0.0.0/24`。了解更多相关配置信息请查看 [config-default.yaml](https://github.com/apache/apisix/blob/master/conf/config-default.yaml) 配置文件。

## 如何修改配置

我们通过修改 `config.yaml` 文件来完成对 APISIX 服务本身配置的更改。

**示例1：**

指定 APISIX 监听的 HTTP 端口为 8080，其他配置保持默认，在 `config.yaml` 文件中只需要这样配置：

```yaml
apisix:
  node_listen: 8080             # APISIX listening port
```

**示例2：**

指定 APISIX 监听的 HTTP 端口为 8080，并且设置 etcd 地址为 `http://foo:2379`， 其他配置保持默认。在 `config.yaml` 文件中只需要这样配置：

```yaml
apisix:
  node_listen: 8080             # APISIX listening port

etcd:
  host: "http://foo:2379"       # etcd address
```

**示例3：**

指定 APISIX 监听的 HTTP 端口为 8080、HTTPS 端口为 8443，允许所有的客户端地址访问，etcd 地址为 `http://foo:2379`，并且只启用 `ip-restriction` 和 `key-auth` 两个插件。在 `config.yaml` 文件中只需要这样配置：

```yaml
apisix:
  node_listen: 8080
  ssl:    
    listen_port: 8443
  allow_admin:
    - 0.0.0.0/0
  
etcd:
  host:
    - "http://foo:2379"

plugins:
  - ip-restriction
  - key-auth
```

**注意：**

1、不要随意修改 `config-default.yaml` 文件的配置，如果需要自定义任何配置，我们都应在 `config.yaml` 文件中完成。

2、不要手动修改 APISIX 自身的 `conf/nginx.conf` 文件，因为当服务每次启动时，APISIX 会根据相应的模板文件自动生成新的 `conf/nginx.conf` 文件。
