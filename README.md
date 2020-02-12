# Android 面经收集大全

[TOC]

## 简述

但不仅限于Android方面,希望通过此方式把自己的知识台阶一步一步搭起,最后通向offer的大门,知识汇聚,知识分享,开源的力量是无穷尽的,也祝大家早些时日提取自己心满意足的offer.

**PS: 本收集属于对其他各大技术论坛大佬精美好文的简短总结**



## 数据库

### 1.数据库的索引和实现原理

1. 索引就是一个查找问题,索引是一个排序的数据结构,实现通常是B树及其变种
2.  优点
   1. 唯一索引,保证每一行数据的唯一性
   2. 加快检索速度
   3. 加速表之间的连接
   4. 减少分组和排序时间
   5. 使用优化隐藏器提高性能
3.  缺点
   1. 创建和维护耗时
   2. 占据更多的物理空间
   3. 增删改需要动态维护索引
4.  在什么列上需要创建索引
   1. 经常搜索的列
   2. 主键列 [唯一性]
   3. 经常连接的列  [外键]
   4. 经常需要排序的列
   5. 经常使用在where子句中的列  [条件判断]
5. 什么列不需要索引
   1. 查询少的列
   2. 只有很少数据值的列
   3. 定义数据类型很大的列  [比如text image bit]
   4. 修改性能大于检索性能的列 
6. 存储结构 [B树,B+树,红黑树]





## 操作系统



### 1.操作系统线程和进程的同步机制和通信机制

  1.   同步机制

         1.   临界区 

              ```java
              多线程的串行化访问公共资源,一个线程进入临界区之后其余线程必须在临界区外部等待,等到临界区被释放后,其他线程就可以抢占
              ```

         2.   互斥量

              ```java
              互斥对象机制,只有拥有互斥对象的线程才有访问共享资源的权限,互斥对象只有一个,还能实现不同应用程序的线程之间的资源访问
              ```

         3.   信号量

              ```java
              允许多个线程同一时刻访问同一资源,但需要限制最大线程数量,类似于操作系统的PV操作
              ```

              **PV操作**

              ```java
              信号量S>0,表示可供使用的资源实体数量
              
              信号量S<0,表示等待使用资源的进程数
              
              相关操作:
              	申请资源
              					S-- [资源实体少一个]
              						if(--S >= 0){线程继续执行}
              						if(--S < 0){该进程阻塞,进入进程调度}
              	释放资源
              					s++ [资源实体多一个]
              						if(++S > 0){进程继续执行}
              						if(++S <= 0){唤醒一个等待进程或者转入进程调度}
              ```

              **关键词解释: 进程调度**

              ```java
              多个进程抢用CPU使用权,操作系统究竟给哪一个进程CPU资源,由操作系统决定,这个决定的方法就是调度策略,理所当然的<进程调度>就是<操作系统使用调度策略给某个具体的进程给予CPU资源的使用权>
              
              调度算法:
              			抢占式
              							进程拥有最大的运行时间,并且这个时间是确定的,进程运行时间大于最大运行时间,该进程就会被挂起,选择调度算法挑选下一个进程运行
              			非抢占式
              							一个进程一直运行到阻塞或者自愿退出
              ```

              

         4.   事件

              ```java
              使用通知操作,生产者和消费者的关系<观察者模式>
              ```

              

  2.    通讯机制

         1.   无名管道

              ```java
              数据单向流动,是在具有亲缘关系之间的进程之间通讯,存在于内存
              ```

       	 2.  命名管道

             ```java
             数据双向流动,无关进程之间可以数据交换
             ```

         3.   消息队列

              ```java
              消息的链表,存放在内核中,独立于发送和接收线程
              ```

         4.   信号量

              ```Java
              计数器,实现进程间的互斥和同步,不是存储数据
              ```

         5.   共享内存

              ```java
              共享一个给定的存储区,最快的方式,进程直接对内存数据进行读取
              ```







## 设计模式



### 1.手写Java双重检验的单列模式

1.  侧重考点就是在如何实现双重验证

   **双重验证的逐渐演化过程**

   

   1.单线程适用 

   缺点:只能适用与单线程

   ```java
   class	A{
     
     private A instance;
     
     private A(){}
     
     public static A getInstance(){
       if(instance == null){
         instance = new A();
       }
       return instance;
     }
   }
   ```

   2.多线程适用  [适用**synchronized方法**]

   缺点:每次调用方法都需要同步的代价,实际上是只有if语句需要同步

   ```Java
   class	A{
     
     private A instance;
     
     private A(){}
     
     public static synchronized A getInstance(){
       if(instance == null){
         instance = new A();
       }
       return instance;
     }
   }
   ```

   3.降低同步调用代价 [适用synchronized代码块]

   缺点: 当两个线程进入if判断之后,有一个线程进入了同步代码块,还有另外一个线程在同步块外,if语句内等待,所以当同步块线程创建完对象之后退出同步块,另一个线程没有再次判断instance是否为null

   ```java
   class	A{
     
     private A instance;
     
     private A(){}
     
     public static A getInstance(){
       if(instance == null){
         synchronized(A.class){
         	instance = new A();
         }
       }
       return instance;
     }
   }
   ```

   4.双重检查 [在创建对象之前再加一个if判断null]

   缺点:理想很美好,现实很骨感,不能保证在单处理器和多处理器上顺序执行,因为**内存模型写入是无序的**

   ```java
   class	A{
     
     private A instance;
     
     private A(){}
     
     public static A getInstance(){
       if(instance == null){
         synchronized(A.class){
           if(instance == null){
         		instance = new A();
           }
         }
       }
       return instance;
     }
   }
   ```

   5.解决无序写入问题  [双重同步代码块]

   ```java
   class	A{
     
     private A instance;
     
     private A(){}
     
     public static A getInstance(){
       if(instance == null){
         synchronized(A.class){
           A tmp = instance;
           if(tmp == null){
         		synchronized(A.class){
               tmp = new A();
             }
             instance = tmp;
           }
         }
       }
       return instance;
     }
   }
   ```

   6.优化无序写入

   ```java
   class	A{
     
     private A instance;
     
     private A(){}
     
     public static A getInstance(){
       if(instance == null){
         synchronized(A.class){
           A tmp = instance;
           if(tmp == null){
         		synchronized(A.class){
               instance = new A();
             }
           }
         }
       }
       return instance;
     }
   }
   ```

   7.考虑到内存模型和写入顺序,最终选择方案

      1.    使用static关键字的单例模式

            ```java
            class A{
              private static A instance = new A();
              
              private A(){}
              
              public static A getinstance(){
                return instance;
              }
            }
            ```

            

      2.    使用同步方法

            ```java
            public static synchronized A getinstance(){
              if(instance == null){
                instance = new A();
              }
              return instance;
            }
            ```

            

   

2.  









