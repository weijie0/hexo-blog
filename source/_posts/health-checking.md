---
layout:     post
title:      "health-checking"
subtitle:   "GRPC 健康检查协议"
date:       2021-04-07
author:     "weijie"
catalog:    true
header-img: "grpc.jpg"
tags:
    - grpc
---

健康检查用于调查服务器是否能够处理 rpcs。客户端对服务器的健康检查可以从点到点或通过某些控制系统进行。服务器可能会选择回复"不健康"，因为它尚未准备好接收请求、正在关闭或其他原因。如果在一段时间内未收到响应，或者回复中显示不健康，客户端可以采取相应行动。

GRPC 服务用作简单客户端对服务器场景和其他控制系统（如负载平衡）的健康检查机制。作为一个高水平的服务提供了一些好处。首先，因为它是GRPC服务本身，做健康检查是在同一格式的正常rpc。其次，它具有丰富的语义，如每服务健康状况。第三，作为GRPC服务，它可以重复使用所有现有的计费、配额基础设施等，因此服务器可以完全控制健康检查服务的访问。

## 服务定义

服务器应导出以下原件中定义的服务：

```
syntax = "proto3";

package grpc.health.v1;

message HealthCheckRequest {
  string service = 1;
}

message HealthCheckResponse {
  enum ServingStatus {
    UNKNOWN = 0;
    SERVING = 1;
    NOT_SERVING = 2;
    SERVICE_UNKNOWN = 3;  // Used only by the Watch method.
  }
  ServingStatus status = 1;
}

service Health {
  rpc Check(HealthCheckRequest) returns (HealthCheckResponse);

  rpc Watch(HealthCheckRequest) returns (stream HealthCheckResponse);
}
```

客户端可以通过调用该方法查询服务器的健康状况，并且应在 rpc 上设置截止日期。客户端可以可选地设置它想要查询的健康状况的服务名称。建议的服务名称格式是`package_names.ServiceName`,例如`grpc.health.v1.Health`

服务器应手动注册所有服务并设置单个状态，包括空服务名称及其状态。对于收到的每一个请求，如果服务名称可以在注册表中找到，则必须用状态`OK`发送回复，并且状态字段应设置为或相应地设置`SERVING` or `NOT_SERVING`。如果未注册服务名称，服务器将返回 GRPC 状态`NOT_FOUND`

服务器应使用空字符串作为服务器整体健康状况的关键，以便对特定服务不感兴趣的客户端可以使用空请求查询服务器的状态。服务器只需对服务名称进行精确匹配，而无需支持任何类型的通配符匹配。但是，服务所有者可以自由实施客户端和服务器都同意的更复杂的匹配语义。

如果 rpc 在一段时间后未完成，则客户端可以声明服务器不健康。客户端应该能够处理服务器没有健康服务的情况。

客户端可以调用该方法执行流式健康检查。服务器将立即发回指示当前服务状态的消息。然后，当服务状态发生变化时，它将发送新消息。