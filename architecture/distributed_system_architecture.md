# 分布式系统架构
[如何建设高可用系统](https://mp.weixin.qq.com/s/kQl2hKETOSNA9x5yCSrxKQ)

设计建议

1. 减少单点 - 去单点首先要识别整个系统所有主链路的单点，如机房（同城异地双机房），应用服务器，DNS服务器，SFTP服务器，LBS，缓存服务器，数据库，消息服务器，代理服务器和专线等，如系统通过专线调用对方服务，需要考虑同时拉联通和电信的专线，联通或电信的专线还是有一定概率会出现问题的，但是同时出问题的概率会小非常多。优先使用软负载，使用硬负载兜底。

2. 减少依赖 - 减少DNS依赖，减少远程服务依赖，DNS依赖可以尝试设置本地host，用工具给所有服务器推送最新的域名映射关系，通过本地缓存或近端服务减少RPC调用。

3. 限制循环 - 避免无限死循环，导致CPU利用率百分百，可以设置for循环的最大循环次数，如最大循环1000次。

4. 控制流量 - 避免异常流量对应用服务器产生影响，可以对指定服务设置流量限制，如QPS，TPS，QPH（每小时总请求量）和QPD（每天总请求量）。

5. 精准监控 - 对CPU利用率，load，内存，带宽，系统调用量，应用错误量，PV，UV和业务量进行监控，避免内存泄露和异常代码对系统产生影响，配置监控一定要精准，如平时内存利用率是50%，监控可以配置成60%进行报警，这样可以提前感知内存泄露问题，避免应用无响应。

6. 无状态 - 服务器不能保存用户状态数据，如在集群环境下不能用static变量保存用户数据，不能长时间把用户文件存放在服务器本地。服务器有状态会难以扩容，且出现单点问题。

7. 容量规划 - 定期对容量进行评估。如大促前进行压测和容量预估，根据需要进行扩容。

8. 功能开关 - 打开和关闭某些功能，比如消息量过大，系统处理不了，把开关打开后直接丢弃消息不处理。上线新功能增加开关，如果有问题关闭新功能。

9. 设置超时 - 设置连接超时和读超时设置，不应该太大，如果是内部调用连接超时可以设置成1秒，读超时3秒，外部系统调用连接超时可以设置成3秒，读超时设置成20秒。

10. 重试策略 - 当调用外部服务异常时可以设置重试策略，每次重试时间递增，但是需要设置最大重试次数和重试开关，避免对下游系统产生影响。

12. 隔离 - 应用隔离，模块隔离，机房隔离和线程池隔离。可以按照优先级，不变和变几个维度来隔离应用和模块，如抽象和不变的代码放在一个模块，这个模块的代码几乎不会修改，可用性高，经常变的业务逻辑放在一个模块里，这样就算有问题，也只会影响到某一个业务。不同的业务使用不同的线程池，避免低优先级任务阻塞高优先级，或高优先级任务过多时影响低优先级任务永远不会执行。 

13. 异步调用 - 同步调用改成异步调用，解决远程调用故障或调用超时对系统的影响。

14. 热点缓存 - 对热点数据进行缓存，降低RPC调用。如B系统提供名单服务，B系统可以提供一个client SDK提供近端缓存服务，定期去服务器端取数据，减少RPC调用。

15. 缓存容灾 - 当数据库不可用时可以使用缓存的数据。并设置分级缓存，如优先读本地缓存，其次读分布式缓存。

16. 分级缓存 - 优先读本地缓存，其次读分布式缓存。通过推模式更新本地缓存。

17. 系统分级 - 对系统进行分级，如ABC三个等级，高级别系统不依赖于低级别系统，并且高级别系统比底级别系统高可用率要高。

18. 服务降级 - 如果系统出现响应缓慢等状况，可以关闭部分功能，从而释放系统资源，保证核心服务的正常运行。需要识别哪些服务可以降级，比如突然有大量消息流入，导致服务不可用，我们会把消息直接丢弃掉。或通过设置流控，拒绝为低级别系统提供服务。

19. 流量蓄洪 - 当流量陡增时，可以将请求进行蓄洪，如把请求保存在数据库中，再按照指定的QPS进行泄洪，有效的保护下游系统，也保证了服务的可用性。当调用对方系统，对方系统响应缓慢或无响应时，可采取自动蓄洪。

20. 服务权重 - 在集群环境中，可自动识别高性能服务，拒绝调用性能低的服务。如在集群环境中，对调用超时的服务器进行权重降低，优先调用权重高的服务器。

21. 依赖简化- 减少系统之间的依赖，比如使用消息驱动，A和B系统通过消息服务器传递数据，A和B系统使用数据库进行读写分离，A系统负责往数据库中写数据，B系统负责读数据，因为数据存放在数据库中，当A不可用时，短时间内不影响B系统提供服务。

22. 弹性扩容 - 根据资源的使用率自动或手动进行扩容。如带宽不够用时，快速增加带宽。

23. 灰度和回滚 - 发布新功能只让部分服务器生效，且观察几天逐渐切流，如果出现问题只影响部分客户。出现问题快速回滚，或者直接下线灰度的机器。

24. 减少远程调用 - 优先调用本地JVM内服务，其次是同机房服务，然后是同城服务，最后是跨城服务。如A调用B，B调用互联网的C系统获取数据，B系统可以把数据缓存起来，并设置数据的保鲜度，减少B对C的依赖。配置中心把注册服务的地址推送到调用服务的系统本地。参数中心把参数配置信息推送到系统的本地内存，而不是让系统去远程服务器获取参数信息。

25. 熔断机制 - 增加熔断机制，当监控出线上数据出现大幅跌涨时，及时中断，避免对业务产生更大影响。如我们做指标计算时，指标可以计算慢，但是不能算错，如果发现某个用户的指标环比或同比增长一倍或跌零，会考虑保存所有消息，并中止该用户的指标计算。

26. 运行时加载模块 - 我们会把经常变的业务代码变成一个个业务模块，使用Java的ClassLoader在运行时动态加载和卸载模块，当某个模块有问题时候，可以快速修复。

27. 代码扫描 - 使用IDEA代码分析等工具进行代码扫描，识别出程序中的BUG，如空指针异常，循环依赖等。

28. 自动备份 - 程序，系统配置和数据定期进行备份。可使用linux命令和shell脚本定时执行备份策略，自动进行本地或异地。出现问题时能快速重新部署。

29. 线上压测 - 系统的对外服务需要进行压测，知道该服务能承受的QPS和TPS，从而做出相对准确的限流。

[分布式系统稳定性模式](http://blog.csdn.net/kobejayandy/article/details/15028503)

1. 隔离。 发生问题，能控制在一个小的范围。 物理隔离/虚拟(容器，实例，VM)隔离/流控
2. 内存溢出。 代码细节，数据量，进程数控制。
3. 预估数量，阀值控制。 远程获取的变量需谨慎处理
4. 超时 QPS = 1000ms / rt(timeOutAvg) * threadCount *QPS越高，线程越少，越完美。 超时时间设定需合理
5. 流控 比超时更友好，有自我恢复能力
6. 异步调用 减少响应时间，在客户端做异步调用
7. 限流 限制请求数，Web服务器拦截，以QPS为准
8. 降级 对弱依赖有效/最好能做到自动降级，自动恢复
9. 开关 手动降级，关闭服务，临时可能会忽略。需有专人负责
10. 热点缓存 容易被攻击，策略：将被攻击的或者不存在的数据放入Cache，不查询DB。
11. 缓存容灾 多份缓存，并且有对应DB的整套缓存，能独立成为数据源
12. 依赖Jar包冲突 二方包间接依赖冲突检测，mvn:dependency:tree
13. 内部调用优先 路由，同机房优先调用DB，Cache。
14. 依赖诊断和调试 接口调试（在线），一些小脚本，jsp能迅速模拟调用
15. 日志跟踪 traceId ? 所有调用应用打日志，以traceId为Key能查询出完整请求调用日志记录。 缺点是但是日志量会很大
16. 依赖识别 强依赖/弱依赖/依赖系统，调用接口的统计管理。能查出系统的瓶颈。
17. 依赖简化 合并依赖，异步化依赖。
18. 发布规范 daily/prepub/beta
19. 服务治理 Admin系统 XXAdmin 白名单，黑名单之类的管理等
20. 容量规划 物理服务器备机准备
21. 预演 模拟正式环境处理问题，能暴露很多问题，帮助很大
22. 告警 短信等，及时处理线上问题
23. 资源调度 优先级资源划分，想法比较好，但比较难做到