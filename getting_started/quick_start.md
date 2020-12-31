# APISIX 快速入门

## Apache APISIX 是什么？

Apache APISIX 是一个动态、实时、高性能的 API 网关，基于 Nginx 网络库和 etcd 实现， 提供负载均衡、动态上游、灰度发布、服务熔断、身份认证、可观测性等丰富的流量管理功能。

你可以使用 Apache APISIX 来处理传统的南北向流量，以及服务间的东西向流量， 也可以当做 Kubernetes ingress controller 来使用。

## 简介

对于未接触过 APISIX 的初学者，可以根据本教程了解到 APISIX 基本的目录结构和使用，适用的人群：APISIX 的初学者。

## APISIX 目录结构

主要对以下目录做简单的介绍。

1、conf 目录

2、apisix 目录

3、deps 目录

4、logs 目录

## 如何修改配置

1、config.yaml

2、config-default.yaml

## 如何查看日志

添加不同路由输出不同日志。

1、访问日志

2、错误日志

## 如何代理请求

通过路由演示下面几种情况。

1、完全匹配

httpbin.org/get

2、前缀匹配

httpbin.org/*

3、条件匹配

httpbin.org/get + ip

4、相同路由优先级

httpbin.org/get

www.httpbin.org/get

## 总结
