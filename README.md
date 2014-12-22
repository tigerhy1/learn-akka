learn-akka
==========

my note for learning akka

akka doc的结构：

1. remoting VS cluster：有什么不同呢？

   我现在的理解是，remoting的控制单元是actor,而这个actor可以不是在本机
   而cluster的控制单元是node,同时一个node也是一个cluster system.一个node上可以创建很多个actor.
   所以说cluster是更高层的东西。
   

