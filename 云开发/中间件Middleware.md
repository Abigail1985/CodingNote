
## 通俗解释
将具体业务和底层逻辑解耦的组件。

大致的效果是：  
需要利用服务的人（前端写业务的），不需要知道底层逻辑（提供服务的）的具体实现，只要拿着中间件结果来用就好了。

举个例子：  
我开了一家炸鸡店（业务端），然而周边有太多屠鸡场（底层），为了成本我肯定想一个个比价，再综合质量挑选一家屠鸡场合作（适配不同底层逻辑）。由于市场变化，合作一段时间后，或许性价比最高的屠鸡场就不是我最开始选的了，我又要重新和另一家屠鸡场合作，进货方式、交易方式等等全都要重来一套（重新适配）。

然而我只想好好做炸鸡，有性价比高的肉送来就行。于是我找到了一个专门整合屠鸡场资源的第三方代理（中间件），跟他谈好价格和质量后（统一接口），从今天开始，我就只需要给代理钱，然后拿肉就行。代理负责保证肉的质量，至于如何根据实际性价比，选择不同的屠鸡场，那就是代理做的事了。


