#iOS 应用架构现状分析



##网络优化
* 使用tcp长连接实现网络服务
* 根据网络状况2G/3G/4G/WIFI进行调优参数
* 根据连接/读/写不同阶段使用重试机制
* 使用ip列表避免DNS解析失败或者劫持
* 根据网络延迟选择服务端ip(使用ping)
* 使用ProtocolBuffer+Gzip减少Payload


