定义接口 --> 基于接口实现调用
	实现类 --> Feign的降级（指A调用B调用失败时，执行自己的业务逻辑）
两个配置：连接超时时间 业务超时时间
[openFeign.excalidraw](Excalidraw/openFeign.excalidraw.md)