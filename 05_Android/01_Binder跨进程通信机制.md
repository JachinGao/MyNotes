## 一、跨进程通信机制

### 1. 传统的跨进程通信

传统的跨进程通信方式有很多，比如**Socket、信号量、管道、内存共享、消息队列**。对于传统的跨进程通信，比如，Socket开销大、效率不高。管道和消息队列拷贝次数多；而且传统的通信机制安全性低，大部分情况下接收方无法得到发送方进程的可信PID/UID，难以对其身份甄别。

Binder在实现上既确保传输性能的同时又提高其安全性，它是基于[OpenBinder](http://www.angryredplanet.com/~hackbod/openbinder/docs/html/BinderIPCMechanism.html)来实现的。



### 2. Binder的组成

Client和Server之间的进程间通信通过Binder驱动程序间接实现。

Android系统的Binder机制中，由一系统组件组成，分别是**Client**、**Server**、**ServiceManager**和**Binder驱动程序**，其中Client、Server和Service Manager运行在用户空间，Binder驱动程序运行内核空间。

核心组件便是Binder驱动程序，提供设备文件/dev/binder，通过与用户空间交互；Client、Server和Service Manager通过open和ioctl文件操作函数与Binder驱动程序进行通信。Service Manager提供了辅助管理的功能，是一个守护进程，用来管理Server，并向Client提供查询Server接口的能力。Client和Server正是在Binder驱动和Service Manager提供的基础设施上，进行Client-Server之间的通信。



### 3. ServiceManager启动

Service Manger、Client和Server三者分别是运行在独立的进程当中，这样它们之间的通信也属于进程间通信，而且也是采用Binder机制进行进程间通信。

Service Manager启动时做了如下事情：

一是打开Binder设备文件，实际持有了binder的引用。

二是建立一块内存映射；

三是进入一个无穷循环，充当Server的角色，等待Client的请求；



### 4. Server和Client如何获取Service Manager接口

**对于普通的Server来说**，Client如果想要获得Server的远程接口，那么必须通过Service Manager远程接口提供的getService接口来获得远程接口，因为不在同一个进程，这本身就是一个使用Binder机制来进行进程间通信的过程。

**对于Service Manager这个Server来说**，Client如果想要获得Service Manager远程接口，却不必通过进程间通信机制来获得，因为Service Manager远程接口是一个特殊的Binder引用，它的引用句柄一定是0。



### 5. Server和Client拿到Service Manager远程接口之后如何使用？

**Service端添加到Service Manager中**

因为Service Manager远程接口是一个特殊的Binder引用，它的引用句柄一定是0，所以Server可以直接获得Service Manager远程接口；通过调用IServiceManager::addService接口把自己的Service添加到Service Manager中去，然后把自己启动起来，等待Client的请求。

addService的时候把自己添加到Service Manager的时候，在内核空间，会创建相应的Binder实体节点和一个对该实体节点的引用，并会将该引用传给ServiceManager，ServiceManager收到后就会取出其Binder的名字和引用插入到一张数据表中。



**Client获取Service的Java远程接口**

**对Client来说，**就是调用IServiceManager::getService这个接口来和Binder驱动程序交互，以获得Service的远程接口； 获取service需要知道服务的名字，层层调用，最后获取Service这个Binder实体在Client进程中的句柄，经过包装，在java层返回一个BinderProxy对象作为代理来与service交互。