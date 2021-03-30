# 模块
- namesrv 简化的注册和配置中心
- broker 具体的存储消息的地方

# 部署架构
1. master 单机
2. master-slave 单个主机+单个从
    - 客户端写入master
    - master同步至salve
    - 如果是同步同步，则客户端请求打入master，master打入salve，salve存储之后，master进行存储，最后返回至客户端success
    - 如果是异步同步，则客户端请求打入master，master打入本地缓存队列，返回客户端success，master后台线程，进行轮训缓存队列，进行同步至salve
    - 同步同步的优点：保证数据一致；缺点：性能不如异步
    - 同步同步的场景：例如金融等需要一致性比较强的场景
    - 如果master挂了：消费者可以继续消费slave的，但是生产者无法继续生产消息(报错)  
    

3. 多master-slave多个(master-slave)
    - 客户端进行写入多master(负载均衡的形式，比如master1写入一条，然后master2写入一条)
    - 每个master同步至自己的slave
4. 多master
    - 客户端进行写入多master(负载均衡的形式，比如master1写入一条，然后master2写入一条)
5. Dleader
    - 至少3个节点，基于raft的形式的同步
    - 意味着节点的数据是强一致的
    - 可以自动切换leader，同时客户端可以感知到
