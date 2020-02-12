# Android 面经收集大全

[TOC]

## 简述

<<<<<<< HEAD
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



### 2.手撕`MySQL`分组查询

1. 按照某个字段进行分组 `group by`

2. `having` 指定查询的条件,对分组的内容尽心过滤

3. 单独使用`group by`

   ```java 
   单独使用查询出来的是分组的第一条记录的值
   select * form student group by gender;
   ```

4.  结合聚合函数使用

   ```Java
   select count(*) ,gender from student group by gender;
   ```

5. 结合having一起使用

   ```c++
   select sum(grade),gender from student group by gender having sum(grade)<300
   ```

   **having和where的区别:**

   都是根据条件进行过滤,

   having后面可以跟随聚合函数



## Linux

### 1.`Linux`中断命令

1. 中断
   1. CPU暂停当前的程序执行,转去处理突发事件,处理完后返回原程序继续执行
   2. 中断请求线: 中断唯一标识的数值
   3. 中断处理函数:内核执行对应的处理函数,中断的优先级最高
2. 分类
   1. 中断<外部中断或异步中断>: 外设向处理器发出中断请求
   2. 异常<内部中断或同步异常>: 处理器执行指令错误 
3.  **终端命令**
   1. `CTRL+C` 强制**中断**程序执行
   2. `CTRL+z`任务中断,进程**挂起**
   3. `CTRL+d`特殊字符 `EOF`
   4. `CTRL+\`**退出**
   5. `kill pid` 



**扩展: 后台进程管理命令:**

1. `jobs`查看当前有多少在后台运行的命令
2. `fg`将后台的命令调至前台执行
3. `bg`将后台暂停的命令继续执行



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



### 2.什么是缓存溢出

1. 缓冲区
   1. 写入的缓冲区的数据超出缓冲区的大小,溢出的数据覆盖合法的数据 [**溢出**]
   2. 一段可读可写的内存区域
2.  危害
   1. 程序崩溃,拒绝服务
   2. 执行恶意代码
   3. 缓冲区攻击 [驱使操作系统执行恶意的代码]
3.  原因
   1. 没有检查用户的合法输入
4.  







## Java基础

### 1.`Java`四种引用

**目的: 决定对象的生命周期利用`JVM`进行垃圾回收**

1. 强引用

   ```java
   直接创建对象赋值,只要有引用变量就永远不被回收,宁可抛出异常;
   
   中断强引用和某个对象之间的关联,直接就是变量置null
   ```

2. 软引用

   ```Java
   内存空间足够,垃圾回收器就不会回收他;
   
   否则对象会被回收,get获取对象就是null
   
   ```

3. 弱引用

   ```java
   只要垃圾回收器需要回收,弱引用必定会被回收
   ```

4. 虚引用

   ```java
   任何时候都有可能会被回收
   ```



### 2.`Java synchronized`的类锁和对象锁

1. 对象锁  

   1. 仅仅有关键字synchronized
   2. **也称实例锁**
   3. 防止其他线程同时访问该实例的synchronized方法块
   4. 每个实例拥有自己的监视块

2.  类锁 

   1. static synchronized
   2. **也称全局锁**,
   3. 控制类的所有实例的并发访问 [限制都线程该该类的所有实例同时访问jvm中对应的代码块]
   4. 所有实例公用一个监视块

3.  demo

   ```
   pulbic class Something(){  
       public synchronized void isSyncA(){}  
       public synchronized void isSyncB(){}  
       public static synchronized void cSyncA(){}  
       public static synchronized void cSyncB(){}  
   }
   ```

   

4. 总结

   ```java
   类锁和对象锁是两个不一样的锁，控制着不同的区域，它们是互不干扰的。同样，线程获得对象锁的同时，也可以获得该类锁，即同时获得两个锁，这是允许的。
   ```

   



## 计算机网络

### 1.`OSI`网络模型 [七层]

1. 物理层

   ```java
   提供物理连接
   
   关心比特流传输
   
   关心机械,电气,功能和规程特性
   
   IEEE 802.2的电器协议
   ```

   

2. 数据链路层

   ```Java
   PPP SLIP ARPANE协议,隧道通讯协议,思科的CDP协议,地址解析协议
   
   物理寻址
   
   将原比特流转换成逻辑传输线路
   
   
   ```

   

3. 网络层

   ```Java
   ICMP  ARP RARP IP,安全协议AH,路由协议OSPF最短路径优先
   
   外部网关EGP 内部网关IGRP IP/IPV6
   
   控制子网运行
   
   分组传输
   
   路由选择
   
   逻辑编址
   ```

   

4. 传输层

   ```JAVA 
   TCP  UDP
   
   分割数据
   
   保证数据有效到达端
   ```

   

5. 会话层

   ```JAVA 
   SMTP  DNS
   
   SSL TLS安全协议
   
   不同机器上用户之间简历管理会话
   
   ```

   

6. 表示层

   ```JAVA 
   SNMP TELNET
   
   信息的语法语义和之间的关联 [加密解密,转换翻译,压缩解压]
   ```

   

7. 应用层

   ```JAVA
   HTTP TFTP FTP SMTP应用程序协议
   ```

   

8.  ![img](http://img.blog.csdn.net/20160731161720376)

### 2.`TCP/IP`模型 [四层]

1. 应用层: 传输协议

2. 传输层:  TCP UDP

3. 网络层: IP ICMP

   ```Java
   IP层传输
   				点到点传输
   				传输IP分组
   
   TCP层传输
   				端到端的传输
   				传输TCP段
   ```

   

4. 物理链路层: 根据需要选择不同的物理链路



### 3.网络设别工作在那一层

1. 网卡 

   物理层 

2. 中继器

    物理层 [复原网络中的信号,从新发送到其他网段]

3. 集线器

   物理层 [连接各个物理设备]

4. 网桥

   数据链路层的MAC子层上<介质访问控制层> [网段中相同协议传输数据包]

5. 交换机

   数据链路层 [和网桥类似的功能] 

6. 路由器

   网络层   [分组转发和路由] 

7. 







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





## Android框架使用

### 1.`Glide`的使用  [加载图片]

1. 特点

   1. 可以加载`gif`动图
   2. 播放本地`MP4`
   3. 加载默认图片 `.fallback(id)`
   4. 重置大小`.resize(int,int)`
   5. 裁剪图片`.fitcenter()`
   6. 缩放图片`thumbnail(0.1f)`
   7. 圆角图片`bitmaptransform(new )`
   8. 缓存  [自定义缓存] `diskCacheStrategy`
   9. 修改缓存大小,位置,图片质量
   10. 请求优先级,加载图片的优先级`.priority(int)`

2.  加载网络图片

   `Glide.with(context).load(url).into(imageview)`

3.  加载文件

   ```Java
   File file = new File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES),"Test.jpg");
   Glide.with(context).load(file).into(imageViewFile);
   ```

   

4.  根据id加载

   ```Java
   int resourceId = R.mipmap.ic_launcher;
   Glide.with(context).load(resourceId).into(imageViewResource);
   ```

   

5.  uri加载

   ```Java
   Glide.with(context).load(uri).into(imageViewUri);
   ```





### 2. `EventBus`的使用

1. 发布订阅<观察者模式>的事件总线
2. 作用
   1. 简化组件之间通讯 [**两个fragment之间的**]
   2. 组件和后台线程间的通讯  [**网络请求**]
3.  使用
   1. `event`事件   [任意类型的对象]
   2. `subscribe`订阅者   [指定线程模型]
   3. `publisher`发布者  [任意线程位置发送事件`post方法`]
4. 线程模型
   1. `posting`事件发布和事件接受在同一个线程  [默认]  <避免执行耗时操作,因会阻塞事件传递>
   2. `main`在ui线程中处理事件,不能耗时操作,ui线程本来就不能耗时操作
   3. `background`在子线程中处理事件  [**一般是网络请求等耗时操作**] <子线程发布事件就在子线程中处理事件,**ui线程发布事件就创建新的子线程处理事件**>
   4. `async`无论在哪个线程发布事件,都将新建子线程处理事件



### 3.`Rxjava`通讯机制

1.  扩展的观察者模式
   1. `observable`被观察者
   2. `observer`观察者
   3. `subscribe`订阅
2.  事件回调方法
   1. `onnext`<类似于点击>
   2. `oncompleted`事件队列完成
   3. `onerror`错误
3.  使用
   1. 创建观察者
      1.  [重写三个回调方法] `observer`  
      2. <这个的一个抽象类`subscriber`>  用法一致  
      3. [onstart方法在所在线程处理事件,不能更新ui,更新ui需要用doonsubscribe方法中]
   2. 创建被观察者`observable`  
      1. 从写call方法,内部调用`subscriber.onnext()  oncompleted() `等方法,一次执行事件
      2. 还有`just() from() `
   3. `subscribe`订阅事件
      1. 把观察者和被观察者关联起来`observable.subscribe(observer);`
4.  `Action0()  Action1()`将对象打包起来内部打包了不同的回调方法
5.  `scheduler`线程控制器  [指定一段代码运行在什么样的线程下]
   1.  默认下: 在哪一个线程调用subscriber()就在该线程产生事件,就在该线程消费事件
   2. `immediate()`默认情况,在当前线程运行
   3. `newThread()`总是创建新的线程执行代码
   4. `io`io操作[网络,数据库,文件读取],内部实现有一个无数量上限的线程池
   5. `computation`CPU计算密集型
   6. `mainthread`Android主线程
6.  **变换**
   1. 加工整个序列,转换成不同的事件序列
   2. `Func1`具有返回值的包装
   3. `map` `flatmap`





## Android基础

### 1.你常用的组件有哪些

| 常用组件                       | 用途                             |
| ------------------------------ | -------------------------------- |
| text view                      | 文本显示                         |
| edit text                      | 注册框,搜索框                    |
| button                         | 登录按钮                         |
| float action button <悬浮按钮> | flutter中印象深刻的一个按钮,登录 |
| recycle view                   | list view少用了,动态列表         |
| switch                         | 白天和黑夜模式的开关             |
| scroll view                    | 上下滑动                         |
| fragment                       | 结合view page使用布局切换        |
| image view                     | 显示各种图片                     |
| check box                      | 订单的选择                       |
| web view                       | 新闻网页的显示                   |
| progress bar                   | 加载进度                         |
| spinner                        | 下拉消息选项                     |
| rating bar                     | 订单评价                         |
| search view                    | 搜索框                           |
| constraint layout              | 最喜欢的一个布局                 |
| linear layout                  | 常常使用等比例均分的属性         |
| grid layout                    | 搜索预选择的格子                 |
| horizontal scroll view         | 横向布局 ,切换页面               |
| tool bar                       | 首页的顶部文字显示               |
| bottom navigation view         | 主界面的底部导航栏               |
| view                           | 一些分割线                       |



### 2.`Android`四大组件

1. activity

   1. 单独的窗口
   2. 通过intent通讯
   3. 必须在`AndroidManifest.xml`配置文件中声明

2. service

   1. 用于后台完成用户操作
   2. 启动,调用`startService()`
      1. 与启动服务的组件无关
      2. 可以无限期运行
      3. 销毁服务需要调用`stopSelf() 或者 stopService()`
   3. 绑定,调用`bindService()`
      1. 与绑定服务的组件相关联
   4. 配之文件需要声明service

3. content provider

   1. 不同程序之间的数据共享
   2. 使用uri标实数据集

4. broadcast receiver

   1.  对感兴趣的外部事件进行接受并作出响应
   2. 可以启用一个activity或者service来响应接收到的信息
   3. 也可以使用notificationmanager通知用户
   4. 动态注册
      1. 生命周期随注册的activity共存亡
   5. 静态注册
      1. 生命周期独立于程序

   









## 算法题

### 1.归并排序

### 2. 汉诺塔

### 3.数组反转

### 4.给一对无序数组，给一个target整数，找出数组中两个数字相加为target，并输出下标(不能用哈希)

### 5.数组反转，给一个target整数，每target长度反转一次