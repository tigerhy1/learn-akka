learn-akka
==========

my note for learning akka

akka doc的结构：

1. remoting VS cluster：有什么不同呢？

   我现在的理解是，remoting的控制单元是actor,而这个actor可以不是在本机
   而cluster的控制单元是node,一个cluster system可以包含多个node.一个node上可以创建很多个actor.
   所以说cluster比起remoting是更高层的东西。
   
2. 一个重要的东西，就是actor的path，需要搞清楚。
    
   
3. seed nodes起的是什么作用？
   新加入的node，通过configuration就能知道现存的node,可以join.
   如果没有seed node配置的话，新的node不知道怎么加入cluster(不知道向谁发Join消息).

4. router用法
   
   我现在的理解是，router只是帮你实现了一些路由规则。
   而router的doc比较复杂，自己写路由规则一个是更灵活，一个是省去看复杂的doc.

   还是继续看吧，觉得还是了解一些比较好。
   
   cluster aware router: 分成两种， group和pool:
   
   group, 就是在cluster所有node上的routees，都可以被所有的routers使用，但是不能创建只能lookup（多对多）
   pool, 一个router能够自己创建routees无论是在local还是在remote，但是只能用自己创建的routees(一对多)

5. actorSelection, actorOf, actorFor的联系和区别

   actorSelection是actorFor的代替
   （actorFor被depreated，因为对于local和remote的actor表现的行为不同，
   具体是对于local actor,actorFor只查找一次，也就是如果对应的actor是不存在的话，即使之后对应path的actor建立了，ActorRef还是空的。而对于remote actor,每次send的时候，都要再次查找一下。）

   actorFor用的非常的多，要说到和actorSelection有什么不同，可能还需要更多的了解。   


工程应用的问题：（针对现在正在做的push）

1. 任务分发，用consistent hash还是其他动态的分发？

   用动态分发，会增加自适应部分的逻辑，比如要记住一个用户的逻辑对应的是哪个handler, 而用consistent hash的话，
   
2. 从消息队列到handler是用pull还是push?

   其实2和1是相应的：静态+push, 动态+pull.原因是：如果动态+push的话，消息队列也要知道，用户id对应哪个handler.如果这样设计的话，需要一个yellowpage，让消息队列知道往哪个handler推。
   
   如果要加yellowpage的话，要在queue和loadbalancer里加入addressbook的缓存，然后采取sub-pub的方式来更新addressbook的cache。

3. 最终还是决定，采用动态分发+push+yellowpage的设计。
    
   最灵活，也是最终极的解决方案，当然编码要考虑的东西也相对多一点。
   

明天任务：看cluster aware router的两个例子。
   
