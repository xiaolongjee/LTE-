

***

* **RLF(Ridio Link Failure)，即无线链路失败，表示UE和eNodeB之间的通信链路发生故障，接收方无法收到发射方发射过来的信号，RLF事件通常分为上行失步和下行失步两种。**

***

**1. 下行RLF事件由UE来检测。**
> UE会通过检测小区参考信号来评估下行无线链路的质量，通常会设定两个门限：Qin和Qout，Qin表示UE接收PDCCH的BLER可以控制在2%以内，通常被认为处于in-sync态；Qout表示UE接收PDCCH的BLER已经超出10%，通常被认为处于out-of-sync。
> 当UE在连接态连续检测到链路质量低于Qout N310次时，开启定时器T310，如果T310超时，则认为RLF发生；如果在T310计时过程中，链路质量高于Qin发生连续N311 次，则停止T310，认为无RLF事件。若发生RLF事件，UE根据配置要么发生原因为Other的Release或者发起重建立流程（RLC达到最大重传）。

***

**2. 上行RLF事件是在eNodeB侧进行监测的，与上行RLF有所不同。**
> 当上行无线链路集处于同步状态时，若eNodeB收到N_OUTSYNC_IND个连续不同步指示后，启动无线链路失败定时器T_RLFAILURE，此后若收到N_INSYNC_IND个连续同步指示，eNodeB将停止并复位无线链路失败定时器。
> 如果T_RLFAILURE定时器超时，eNodeB触发无线链路失败流程，并且指示哪个无线链路集处于非同步,在IUB口通过NBAP信令发送NBAP_RL_FAIL_IND消息给RNC，原因值为"Synchronization Failure"，RNC收到无线链路失败指示消息后，会启动RL_Restore定时器，如果在该定时器超时之前， eNodeB收到N_INSYNC_IND个连续同步指示，无线链路集恢复同步，将上报NBAP_RL_RESTORE_IND消息给RNC，指示无线链路同步恢复。
>  当RL_Restore定时器超时，RL仍处于失步状态，RNC将通过NBAP_RL_Deletion消息删除失步链路，此时根据RNC的处理原则可能存在几种结果：
> 1. 产生掉话，RNC发起无线资源释放。
> 1. 如果UE处于软切换或更软切换，RNC删除一条链路，将会进行激活集更新（无1B/1C事件），不计一次掉话。
> 1. 如果网络开启呼叫重建功能，按照呼叫重建流程进行。

> 上行链路失败一般不易识别，但还是可以从以下三个方面信息判断：
> 1. 存在标准NBAP_RL_FAIL_IND流程（原因为同步失败）；
> 1. 在无1B/1C事件条件下，触发无线链路删除流程；
> 1. 在无线链路释放前UE发射功率突增。

