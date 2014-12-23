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


工程应用的问题：（针对现在正在做的push）

1. 任务分发，用consistent hash还是其他动态的分发？

   用动态分发，会增加自适应部分的逻辑，比如要记住一个用户的逻辑对应的是哪个handler, 而用consistent hash的话，
   
2. 从消息队列到handler是用pull还是push?

   其实2和1是相应的：静态+push, 动态+pull.原因是：如果动态+push的话，消息队列也要知道，用户id对应哪个handler.如果这样设计的话，需要一个yellowpage，让消息队列知道往哪个handler推。
   
   如果要加yellowpage的话，要在queue和loadbalancer里加入addressbook的缓存，然后采取sub-pub的方式来更新addressbook的cache。
   
   
   
