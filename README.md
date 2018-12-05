# MQTransactionTree
RocketMq的事务消息


MQ事务消息

![](https://i.imgur.com/PcVxQtd.png)

<pre>
发送事务消息为什么必须要实现回查 Check 机制？

    当步骤（1）中 Half 消息发送完成，但本地事务返回状态为 TransactionStatus.Unknow，
    或者应用退出导致本地事务未提交任何状态时，从 MQ Broker 的角度看，这条 Half 状态的消
    息的状态是未知的。因此 MQ Broker 会定期要求发送方能 Check 该 Half 状态消息，并上报
    其最终状态。

    Check 被回调时，业务逻辑都需要做些什么？
        MQ 事务消息的 check 方法里面，应该写一些检查事务一致性的逻辑。MQ 发送事务消息时
        需要实现 LocalTransactionChecker 接口，用来处理 MQ Broker 主动发起的本地事务
        状态回查请求；因此在事务消息的 Check 方法中，需要完成两件事情：
            (1) 检查该 Half 消息对应的本地事务的状态（commited or rollback）；
            (2) 向 MQ Broker 提交该 Half 消息本地事务的状态
</pre>