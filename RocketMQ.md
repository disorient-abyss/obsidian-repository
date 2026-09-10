### 系统架构
[RocketMQ系统架构图.excalidraw](Excalidraw/RocketMQ系统架构图.excalidraw.md)
### 核心作用
-  实现跨系统调用的异步化和延迟调用
-  [分布式之间的同步调用和异步调用.excalidraw](Excalidraw/分布式之间的同步调用和异步调用.excalidraw.md)

### 应用场景
- 快速应用变更
	- 通过增加消费者的监听器，对于服务本身并不需要更改代码，只需要将生产的信息抛出到MQ，通过对接其它的消费者应用实现业务的快速变更。
- [流量削峰](流量削峰.md)
- 异步填谷
### 基础架构与收发模式
- 核心架构组件
	- NameServer
	- Broker
	- [Producer](Producer.md)
	- Consumer
- 收发拓扑关系
	- 单生产者 -> 单/多消费者
	