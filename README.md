
# Austin - 消息推送平台


## 项目介绍

austin项目**核心功能**：统一的接口发送各种类型消息，对消息生命周期全链路追踪

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5436b2e3d6cd471db9aafbd436198ca7~tplv-k3u1fbpfcp-zoom-1.image)

**项目出现意义**：只要公司内有发送消息的需求，都应该要有类似`austin`的项目，对各类消息进行统一发送处理。这有利于对功能的收拢，以及提高业务需求开发的效率

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c267ebb2ff234243b8665312dbb46310~tplv-k3u1fbpfcp-zoom-1.image)

## 系统项目架构

austin项目**核心流程**：`austin-api`接收到发送消息请求，直接将请求进`MQ`。`austin-handler`消费`MQ`消息后由各类消息的Handler进行发送处理


![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b5d4dfde0f164805a6e85a86498b0cd7~tplv-k3u1fbpfcp-watermark.image?)

**Question** ：为什么发个消息需要MQ？

**Answer**：发送消息实际上是调用各个服务提供的API，假设某消息的服务超时，`austin-api`如果是直接调用服务，那存在**超时**风险，拖垮整个接口性能。MQ在这是为了做异步和解耦，并且在一定程度上抗住业务流量。

**Question**：能简单说下接入层做了什么事吗？

**Answer**：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c94059a008784a69bd10b98caa46d683~tplv-k3u1fbpfcp-zoom-1.image)

**Question**：`austin-stream`和`austin-datahouse`的作用？

**Answer**：`austin-handler`在发送消息的过程中会做些**通用业务处理**以及**发送消息**，这个过程会产生大量的日志数据。日志数据会被收集至MQ，由`austin-stream`流式处理模块进行消费并最后将数据写入至`austin-datahouse`

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e4bd420001c549ebad922637f7b2e38a~tplv-k3u1fbpfcp-zoom-1.image)

**Question**：`austin-admin`和`austin-web`和`austin-cron`的作用？

**Answer**：`autsin-admin`是`austin`项目的前端项目，可通过它实现对管理消息以及查看消息下发的情况，而`austin-web`则是提供相关的接口给到`austin-admin`进行调用（austin项目是前后端分离的）

业务方可操作`austin-admin`管理后台调用`austin-web`创建**定时**发送消息，`austin-cron`就承载着定时任务处理的工作
