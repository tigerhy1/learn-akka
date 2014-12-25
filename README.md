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

6. UntypedActor和TypedActor的不同在哪里？

7. CurrentClusterState多久收到一次？或者说什么情况下会收到？

   

工程应用的问题：（针对现在正在做的push）

1. 任务分发，用consistent hash还是其他动态的分发？

   用动态分发，会增加自适应部分的逻辑，比如要记住一个用户的逻辑对应的是哪个handler, 而用consistent hash的话，
   
2. 从消息队列到handler是用pull还是push?

   其实2和1是相应的：静态+push, 动态+pull.原因是：如果动态+push的话，消息队列也要知道，用户id对应哪个handler.如果这样设计的话，需要一个yellowpage，让消息队列知道往哪个handler推。
   
   如果要加yellowpage的话，要在queue和loadbalancer里加入addressbook的缓存，然后采取sub-pub的方式来更新addressbook的cache。

3. 最终还是决定，采用动态分发+push+yellowpage的设计。
    
   最灵活，也是最终极的解决方案，当然编码要考虑的东西也相对多一点。
   

4. Actor和SocketIOServer的结合方式，还是有待验证的

5. log怎么打，需要调研一下：用akka自带的，还是直接用log4j.

   用akka自带的。

6. 分布式系统要想清楚的问题：
   1. 加机器时候怎么加
   2. 减机器的时候怎么减
   3. 一个机器down掉的情况，是否能够继续正常提供服务。
   4. 一个机器down掉的情况，是否能够比较方便的恢复。

7. 遇到问题：ActorRef不能通过message来传递，那么能做的，只有把handler和loadbalancer分别编号，存在yellowpage里

8. 有意思的一个地方：为什么是从已经起来的actor注册到还刚起来的node中的actor中呢？
   也就是说已经起来的actor监听到memberup,主动的注册到新起来的node。因为trigger其实是memberup消息。

9. 还有一个问题，没有想清楚：client怎么样能很好的连接不同的loadbalancer?

   现在能想到的方式，是做一个静态的配置文件接口，client去读配置文件，然后就知道去连接哪些lb了。
   
   还有方式，当然是直接上F5,这样的硬件的LB.

   LVS的方式，怎么做呢？看起来LVS是很好的解决方案, 用LVS的DR方式。
   
10. 一个设计上的问题，为什么还要handler这一层呢？或者说为什么要loadbalancer这一层呢？
    直接硬件的LB, 分发到handler那里，不就搞定了？

   答案是不行的，因为对于long polling来说，一个uid必须要对应于一个handler(这个handler保存了这个uid的登陆信息等).


明天任务：看cluster aware router的两个例子。
   
