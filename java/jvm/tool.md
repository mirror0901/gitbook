# JVM 性能调优监控工具

## jps

[参考](https://blog.csdn.net/fenglibing/article/details/6411932)

### 1.介绍

    用来查看基于HotSpot的JVM里面中,所有具有访问权限的Java进程的具体状态,包括进程ID,进程启动的路径及启动参数等等,与unix上的ps类似, 即: ps -ef | grep java ,不过jps是用来显示java进程,可以把jps理解为ps的一个子集.
    使用jps时,如果没有指定hostid,它只会显示本地环境中所有的Java进程;如果指定了hostid,它就会显示指定hostid上面的java进程,不过这需要远程服务上开启了jstatd服务。

查看所有的 jvm 进程,包括进程 ID,进程启动的路径等等
也可以使用 PS,即: ps -ef | grep java

### 2.命令格式

    jps [options][hostid]

### 3.常用参数说明

    -q 忽略输出的类名,Jar名以及传递给main方法的参数,只输出pid.
    -m 输出传递给main方法的参数,如果是内嵌的JVM则输出为null.
    -I 输出应用程序主类的完整包名,或者是应用程序JAR文件的完整路径.
    -v 输出传给JVM的参数.
    -V 输出通过标记的文件传递给JVM的参数(.hotspotrc文件,或者是通过参数-XX:Flags=<filename>指定的文件).
    -J 用于传递jvm选项到由javac调用的java加载器中,例如, "-J-Xms48m"将启动内存设置为48M,使用-J选项可以非常方便的向基于Java的开发的底层虚拟机应用程序传递参数.

### 4.服务器标识

    hostid指定了目标的服务器,它的语法如下:
    [protocol:][[//]hostname][:port][/servername]
    protocol -如果protocol及hostname都没有指定,那表示的是与当前环境相关的本地协议,如果指定了hostname却没有指定protocol,那么protocol的默认就是rmi.
    hostname - 服务器的IP或者名称,没有指定则表示本机.
    port - 远程rmi的端口,如果没有指定则默认为1099.
    servername - 注册到RMI注册中心中的jstatd的名称.

## jstack

[参考](https://blog.csdn.net/fenglibing/article/details/6411940)

观察 jvm 中当前所有线程的运行情况和线程当前状态  
系统崩溃了?如果 java 程序崩溃生成 core 文件,jstack 工具可以用来获得 core 文件的 java stack 和 native stack 的信息，从而可以轻松地知道 java 程序是如何崩溃和在程序何处发生问题。  
系统 hung 住了? jstack 工具还可以附属到正在运行的 java 程序中,看到当前运行的 java 程序的 java stack 和 native stack 的信息，如果现在运行的 java 程序呈现 hung 的状态，jstack 是非常有用的。

### 1.介绍

    jstack用于打印出给定的java进程ID或core file或者远程调试服务的Java堆栈信息,如果是在64位机器上,需要指定选项"-J-d64",windows的jstack使用方式只支持以下的这种方式
    jstack [-l] pid
    如果java程序崩溃生成core文件,jstack工具可以用来获得core文件的java stack和native stack的信息,从而可以轻松地知道java程序是如何崩溃和在程序何处发生问题.另外,jstack工具还可以附属到正在运行的java程序中,看到当时运行的java程序的java stack和native stack的信息,如果现在运行的java程序呈现hung的状态,jstack是非常有用的.

### 2.命令格式

    jstack [option] pid
    jstack [option] executable core
    jstack [option] [server-id@]remote-hostname-or-IP

### 3.常用参数说明

    1). options:
    executable Java executable from which the core dump was produced.
    (可能是产生core dump的java可执行程序)
    core 将被打印信息的core dump文件
    remote-hostname-or-IP远程debug服务的主机名或ip
    server-id唯一id,假如一台主机上多个远程debug服务
    2). 基本参数:
    -F 当jstack[-l]pid没有响应的时候强制打印栈信息
    -l 长列表. 打印关于锁的附加信息,例如属于java.util.concurrent的ownable synchronizers列表.
    -m 打印java和native c/c++框架的所有栈信息.
    -h | -help打印帮助信息
    pid 需要被打印配置信息的java进程id,可以用jps查询.

## jstat

[参考](https://blog.csdn.net/fenglibing/article/details/6411951)

jstat 利用 JVM 内建的指令对 JAVA 应用程序的资源和性能进行实时的命令行的监控,包括了对进程的 classloader,compiler,gc 情况;  
特别的,一个极强的监控内存工具，可以用来监控 VM 内存内的各种堆和非堆的大小及其内存使用量，以及加载类的数量。

### 1.介绍

    Jstat用于监控基于HotSpot的JVM,对其堆的使用情况进行实时的命令行的统计,使用jstat我们可以对指定的JVM做如下监控:
    - 类的加载及卸载情况
    - 查看新生代、老生代及持久代的垃圾收集情况,包括垃圾回收的次数及垃圾回收所占用的时间
    - 查看新生代中Eden区及Survior区中容量及分配情况等

    jstat工具特别强大,它有众多的可选项,通过提供多种不同的监控维度,是我们可以从不同的维度来了解当前JVM堆的使用情况.详细查看堆内各个部分的使用量,使用的时候必须加上待统计的Java进程号,可选的不同维度参数以及可选的统计频率参数.

    它主要是用来显示GC及PermGen相关的信息,如果对GC不怎么了解,先看这篇文章:http://blog.csdn.net/fenglibing/archive/2011/04/13/6321453.aspx,否则其中即使你会使用jstat这个命令,你也看不懂它的输出.

### 2.语法

    jstat[generalOption|outputOptions vmid [interval[s|ms][count]] ]
    generalOption - 单个的常用的命令行选项,如-help,-options,或-version.
    outputOptions - 一个或多个输出选项,由单个的statOption选项组成,可以和-t,-h,and-J等选项配合使用.
        statOption:
    根据jstat统计的维度不同,可以使用如下表中的选项进行不同维度的统计,不同的操作系统支持的选项可能会不一样,可以通过-options选项,查看不同操作系统所支持选项,如:
    | Option | Displays…… |
    | ------ | ------: |
    | class | 用于查看类加载情况的统计 |
    | compiler | 用于查看HotSpot中即时编译情况的统计 |
    | gc | 用于查看JVM中堆的垃圾收集情况的统计 |
    | gccapacity | 用于查看新生代、老生代及持久代的存储容量情况 |
    | gccause | 用于查看垃圾收集的统计情况(这个和-gcutil选项一样),如果有发生垃圾收集,它还会显示最后一次及当前正在发生垃圾收集的原因. |
    | gcnew | 用于查看新生代垃圾收集的情况 |
    | gcnewcapacity | 用于查看新生代的存储容量情况 |
    | gcold | 用于查看老生代及持久化代发生GC的情况 |
    | gcoldcapacity | 用于查看老生代的容量 |
    | gcpermcapacity | 用于查看持久代的容量 |
    | gcutil | 用于查看新生代、老生代及永生代垃圾收集的情况 |
    | printcompilation | HotSpot编译方法的统计 |

    -h n
        用于指定每隔几行就输出列头,如果不指定,默认是只在第一行出现列头.
    -JjavaOption
        用于将给定的javaOption传给java应用程序加载器,例如, "-J-Xms48m" 将把启动内存设置为48M.
    -t n
        用于在输出内容的第一列显示时间戳,这个时间戳代表的是JVM开始启动到现在的时间(注:在IBM JDK5中是没有这个选项的)
    vmid -VM 的进程号,即当前运行的java进程号.
    还有两个关于显示频率的选项:
        interval - 间隔时间,单位可以是秒或者毫秒,通过指定s或ms确定,默认单位为毫秒.
        count - 打印次数,如果缺省则打印无数次.

### 3.不同的统计维度(statOption)及输出说明

#### -class

    类加载情况的统计

    | 列名 | 说明 |
    |:---:|:---:|
    | Loaded | 加载了的类的数量 |
    | Bytes | 加载了的类的大小,单位为KB |
    | Unloaded | 卸载了的类的数量 |
    | Bytes | 卸载了的类的大小,单位为KB |
    | Time | 花在类的加载及卸载的时间 |

#### -compiler

    HotSpot中即时编译器编译情况的统计

    | 列名 | 说明 |
    |:---:|:---:|
    | Compiled | 编译任务执行的次数 |
    | Failed | 编译任务执行失败的次数 |
    | Invalid | 编译任务非法执行的次数 |
    | Time | 执行编译花费的时间 |
    | FailedType | 最后一次编译失败的编译类型 |
    | FailedMethod | 最后一次编译失败的类名及方法名 |

#### -gc

    JVM中堆的垃圾收集情况的统计

    | 列名 | 说明 |
    | S0C | 新生代中Survivor space中S0当前容量的大小（KB） |
    | S1C | 新生代中Survivor space中S1当前容量的大小（KB） |
    | S0U | 新生代中Survivor space中S0容量使用的大小（KB） |
    | S1U | 新生代中Survivor space中S1容量使用的大小（KB） |
    | EC | Eden space当前容量的大小（KB） |
    | EU | Eden space容量使用的大小（KB） |
    | OC | Old space当前容量的大小（KB） |
    | OU | Old space使用容量的大小（KB） |
    | PC | Permanent space当前容量的大小（KB） |
    | PU | Permanent space使用容量的大小（KB） |
    | YGC | 从应用程序启动到采样时发生 Young GC 的次数 |
    | YGCT | 从应用程序启动到采样时 Young GC 所用的时间(秒) |
    | FGC | 从应用程序启动到采样时发生 Full GC 的次数 |
    | FGCT | 从应用程序启动到采样时 Full GC 所用的时间(秒) |
    | GCT | T从应用程序启动到采样时用于垃圾回收的总时间(单位秒)，它的值等于YGC+FGC |

#### -gccapacity

    新生代、老生代及持久代的存储容量情况

    | 列名 | 说明 |
    | :---: | :---: |
    | NGCMN | 新生代的最小容量大小（KB） |
    | NGCMX | 新生代的最大容量大小（KB） |
    | NGC | 当前新生代的容量大小（KB） |
    | S0C | 当前新生代中survivor space 0的容量大小（KB） |
    | S1C | 当前新生代中survivor space 1的容量大小（KB） |
    | EC | Eden space当前容量的大小（KB） |
    | OGCMN | 老生代的最小容量大小（KB） |
    | OGCMX | 老生代的最大容量大小（KB） |
    | OGC | 当前老生代的容量大小（KB） |
    | OC | 当前老生代的空间容量大小（KB） |
    | PGCMN | 持久代的最小容量大小（KB） |
    | PGCMX | 持久代的最大容量大小（KB） |
    | PGC | 当前持久代的容量大小（KB） |
    | PC | 当前持久代的空间容量大小（KB） |
    | YGC | 从应用程序启动到采样时发生 Young GC 的次数 |
    | FGC | 从应用程序启动到采样时发生 Full GC 的次数 |

#### -gccause

    这个选项用于查看垃圾收集的统计情况(这个和-gcutil选项一样),如果有发生垃圾收集,他还会显示最后一次及当前正在发生垃圾收集的原因,它比-gcutil会多出最后一次垃圾收集原因以及当前正在发生的垃圾收集的原因.

    用于查看垃圾收集的统计情况,包括最近发生垃圾的原因
    | 列名 | 说明 |
    |:----:|:----:|
    | LGCC | 最后一次垃圾收集的原因,可能为"unknown GCCause"、"System.gc()"等 |
    | GCC | 当前垃圾收集的原因 |

#### -gcnew

    新生代垃圾收集的情况
    | 列名 | 说明 |
    | S0C | 当前新生代中survivor space 0的容量大小 (KB) |
    | S1C | 当前新生代中survivor space 1的容量大小 (KB) |
    | S0U | S0已经使用的大小 (KB) |
    | S1U | S1已经使用的大小 (KB) |
    | TT | Tenuring threshold，要了解这个参数，我们需要了解一点Java内存对象的结构，在Sun JVM中，（除了数组之外的）对象都有两个机器字（words）的头部。第一个字中包含这个对象的标示哈希码以及其他一些类似锁状态和等标识信息，第二个字中包含一个指向对象的类的引用，其中第二个字节就会被垃圾收集算法使用到。
在新生代中做垃圾收集的时候，每次复制一个对象后，将增加这个对象的收集计数，当一个对象在新生代中被复制了一定次数后，该算法即判定该对象是长周期的对象，把他移动到老生代，这个阈值叫着tenuring threshold。这个阈值用于表示某个/些在执行批定次数youngGC后还活着的对象，即使此时新生的的Survior没有满，也同样被认为是长周期对象，将会被移到老生代中。|
    | MTT | Maximum tenuring threshold,用于表示TT的最大值. |
    | DSS | Desired survivor size(KB). 可以参与这里: http://blog.csdn.net/yangjun2/article/details/6542357 |
    | EC | Eden space 当前容量的大小(KB) |
    | EU | Eden space 已经使用的大小(KB) |
    | YGC | 从应用程序启动到采样时发生Young GC的次数 |
    | YGCT | 从应用程序启动到采样时Young GC所用的时间(单位秒) |

#### -gcnewcapacity

    新生代的存储容量情况  
    | 列名 | 说明 |
    |:----:|:----:|
    | NGCMN | 新生代的最小容量大小(KB) |
    | NGCMX | 新生代的最大容量大小(KB) |
    | NGC | 当前新生代的容量大小(KB) |
    | S0CMX | 新生代中S0的最大容量大小(KB) |
    | S0C | 当前新生代中S0的容量大小(KB) |
    | S1CMX | 新生代中S1的最大容量大小(KB) |
    | S1C | 当前新生代中S1的容量大小(KB) |
    | ECMX | 新生代中Eden的最大容量大小(KB) |
    | EC | 当前新生代中Eden的容量大小(KB) |
    | YGC | 从应用程序启动到采样时发生Young GC 的次数 |
    | FGC | 从应用程序启动到采样时发生Full GC 的次数 |

#### -gcold

    老生代及持久代发生GC的情况  
    | 列名 | 说明 |
    |:----:|:----:|
    | PC | 当前持久代容量的大小(KB) |
    | PU | 持久代使用容量的大小(KB) |
    | OC | 当前老年代容量的大小(KB) |
    | OU | 老年代使用容量的大小(KB) |
    | YGC | 从应用程序启动到采样时发生Young GC的次数 |
    | FGC | 从应用程序启动到采样时发生Full GC的次数 |
    | FGCT | 从应用程序启动到采样时Full GC所用的时间(单位秒) |
    | GCT | 从应用程序启动到采样时用于垃圾回收的总时间(单位秒),它的值等于YGC+FGC |

#### -gcoldcapacity

    老生代的存储容量情况  
    | 列名 | 说明 |
    |:----:|:----:|
    | OGCMN | 老生代的最小容量大小(KB) |
    | OGCMX | 老生代的最大容量大小(KB) |
    | OGC | 当前老生代的容量大小(KB) |
    | OC | 当前新生代的空间容量大小(KB) |
    | YGC | 从应用程序启动到采样时发生Young GC的次数 |
    | FGC | 从应用程序启动到采样时发生 Full GC 的次数 |
    | FGCT | 从应用程序启动到采样时 Full GC 所用的时间(单位秒) |
    | GCT |  |

## jmap

监视进程运行中的 jvm 物理内存的占用情况,该进程内存内，所有对象的情况,例如产生了哪些对象，对象数量;  
系统崩溃了? jmap 可以从 core 文件或进程中获得内存的具体匹配情况,包括 Heap size,Perm size 等等

## jinfo

观察进程运行环境参数,包括 Java System 属性和 JVM 命令行参数

## javah

javah 是用于根据 JAVA 本地方法,生成对应的 C 语言文件及相应的 stub 文件的命令,使用比较简单。[JNI 简单示例,包括 C 语言实现和调用](https://blog.csdn.net/fenglibing/article/details/4300381)

## 备注

如果能熟练运用这些命令,尤其是在 linux 下,那么完全可以代替 jprofile 等监控工具。
用命令的好处就是速度快,并且辅助于其他命令,比如 grep gawk sed 等,可以组装多种符合自己要求的工具。
