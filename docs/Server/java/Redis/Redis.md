### Redis

#### 一、配置文件

##### 1、INCLUDES

+ includes ：

  > Redis只有一个配置文件，如果多个人进行开发维护，那么就需要多个这样的配置文件，这时候多个配置文件就可以在此通过 include /path/to/local.conf 配置进来，而原本的 redis.conf 配置文件就作为一个总闸。
  >
  > 另外需要注意的时，如果将此配置写在redis.conf 文件的开头，那么后面的配置会覆盖引入文件的配置，如果想以引入文件的配置为主，那么需要将 include 配置写在 redis.conf 文件的末尾
  >
  > 例如：
  >
  > include /path/to/local.conf
  >
  > include /path/to/test.conf

##### 2、NETWORK

+ bind

  > 绑定redis服务器网卡IP，默认为127.0.0.1,即本地回环地址。这样的话，访问redis服务只能通过本机的客户端连接，而无法通过远程连接。如果bind选项为空的话，那会接受所有来自于可用网络接口的连接

+ port

  > 指定redis运行的端口，默认是6379。由于Redis是单线程模型，因此单机开多个Redis进程的时候会修改端口

+ timeout

  > 设置客户端连接时的超时时间，单位为秒。当客户端在这段时间内没有发出任何指令，那么关闭该连接。默认值为0，表示不关闭

+ tcp-keepalive

  > 单位是秒，表示将周期性的使用SO_KEEPALIVE检测客户端是否还处于健康状态，避免服务器一直阻塞，官方给出的建议值是300s，如果设置为0，则不会周期性的检测

##### 3、GENERAL

+ daemonize

  > 设置为yes表示指定Redis以守护进程的方式启动（后台启动）。
  >
  > 默认值为 no

+ pidfile

  > 配置PID文件路径
  >
  > 当redis作为守护进程运行的时候，它会把 pid 默认写到 /var/redis/run/redis_6379.pid 文件里面

+ loglevel

  > 定义日志级别。默认值为notice，有如下4种取值：
  >
  > debug（记录大量日志信息，适用于开发、测试阶段）
  >
  > verbose（较多日志信息）
  >
  > notice（适量日志信息，使用于生产环境）
  >
  > warning（仅有部分重要、关键信息才会被记录）

+ logfile

  > 配置log文件地址,默认打印在命令行终端的窗口上

+ databases

  > 设置数据库的数目。默认的数据库是DB 0 ，可以在每个连接上使用select  <dbid> 命令选择一个不同的数据库，dbid是一个介于0到databases - 1 之间的数值。默认值是 16，也就是说默认Redis有16个数据库

##### 4、SNAPSHOTTING

+ save

  > 这里是用来配置触发 Redis的持久化条件，也就是什么时候将内存中的数据保存到硬盘。默认如下配置：
  >
  > save 900 1：表示900 秒内如果至少有 1 个 key 的值变化，则保存
  >
  > save 300 10：表示300 秒内如果至少有 10 个 key 的值变化，则保存
  >
  > save 60 10000：表示60 秒内如果至少有 10000 个 key 的值变化，则保存

+ stop-writes-on-bgsave-error

  > 默认值为yes。当启用了RDB且最后一次后台保存数据失败，Redis是否停止接收数据。这会让用户意识到数据没有正确持久化到磁盘上，否则没有人会注意到灾难（disaster）发生了。如果Redis重启了，那么又可以重新开始接收数据了

+ rdbcompression

  > 默认值是yes。对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能，但是存储在磁盘上的快照会比较大

+ rdbchecksum

  > 默认值是yes。在存储快照后，我们还可以让redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能

+ dbfilename

  > 设置快照的文件名，默认是 dump.rdb

+ dir

  > 设置快照文件的存放路径，这个配置项一定是个目录，而不能是文件名。使用上面的 dbfilename 作为保存的文件名

##### 5、REPLICATION

+ slave-server-stale-data

  > 默认值为yes。当一个 slave 与 master 失去联系，或者复制正在进行的时候，slave 可能会有两种表现：
  >
  > 1) 如果为 yes ，slave 仍然会应答客户端请求，但返回的数据可能是过时，或者数据可能是空的在第一次同步的时候 
  >
  > 2) 如果为 no ，在你执行除了 info he salveof 之外的其他命令时，slave 都将返回一个 "SYNC with master in progress" 的错误

+ slave-read-only

  > 配置Redis的Slave实例是否接受写操作，即Slave是否为只读Redis。默认值为yes。

+ repl-diskless-sync

  >  主从数据复制是否使用无硬盘复制功能。
  >
  > 默认值为no。

+ repl-diskless-sync-delay

  > 当启用无硬盘备份，服务器等待一段时间后才会通过套接字向从站传送RDB文件，这个等待时间是可配置的。  
  >
  > 这一点很重要，因为一旦传送开始，就不可能再为一个新到达的从站服务。从站则要排队等待下一次RDB传送。因此服务器等待一段  时间以期更多的从站到达。延迟时间以秒为单位，默认为5秒。
  >
  > 要关掉这一功能，只需将它设置为0秒，传送会立即启动。默认值为5

+ repl-disable-tcp-nodelay

  > 同步之后是否禁用从站上的TCP_NODELAY 如果你选择yes，redis会使用较少量的TCP包和带宽向从站发送数据。但这会导致在从站增加一点数据的延时。  
  >
  > Linux内核默认配置情况下最多40毫秒的延时。如果选择no，从站的数据延时不会那么多，但备份需要的带宽相对较多。默认情况下我们将潜在因素优化，但在高负载情况下或者在主从站都跳的情况下，把它切换为yes是个好主意。
  >
  > 默认值为no。

##### 6、SECURITY

+ rename-command

  > 命令重命名，对于一些危险命令例如：
  >
  > flushdb（清空数据库）
  >
  > flushall（清空所有记录）
  >
  > config（客户端连接后可配置服务器）
  >
  > keys（客户端连接后可查看所有存在的键）    
  >
  > 作为服务端redis-server，常常需要禁用以上命令来使得服务器更加安全，禁用的具体做法是是：
  >
  > rename-command FLUSHALL ""
  >
  > 也可以保留命令但是不能轻易使用，重命名这个命令即可：
  >
  > rename-command FLUSHALL abcdefg
  >
  > 这样，重启服务器后则需要使用新命令来执行操作，否则服务器会报错unknown 

+ requirepass

  > 设置redis连接密码
  >
  > 比如: requirepass 123  表示redis的连接密码为123.

##### 7、CLIENTS

+ maxclients

  > 设置客户端最大并发连接数，
  >
  > 默认无限制，
  >
  > Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件。  
  >
  > 描述符数-32（redis server自身会使用一些）
  >
  > 如果设置 maxclients为0 。表示不作限制。
  >
  > 当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息

##### 8、MEMORYMANAGEMENT

+ maxmemory

  > 设置Redis的最大内存，
  >
  > 如果设置为0 。表示不作限制。
  >
  > 通常是配合下面介绍的maxmemory-policy参数一起使用

+ maxmemory-policy

  > 当内存使用达到maxmemory设置的最大值时，redis使用的内存清除策略。有以下几种可以选择：
  >
  > volatile-lru  利用LRU算法移除设置过过期时间的key (LRU:最近使用 Least Recently Used ) 
  >
  > allkeys-lru   利用LRU算法移除任何key 
  >
  > volatile-random   移除设置过过期时间的随机key 
  >
  > allkeys-random  移除随机ke
  >
  > volatile-ttl   移除即将过期的key(minor TTL) 
  >
  > noeviction  noeviction  不移除任何key，只是返回一个写错误 ，默认选项

+ maxmemory-samples

  > LRU 和 minimal TTL 算法都不是精准的算法，但是相对精确的算法(为了节省内存)。随意你可以选择样本大小进行检，redis默认选择3个样本进行检测，你可以通过maxmemory-samples进行设置样本数

##### 9、APPEND ONLY MODE

+ appendonly

  > 默认redis使用的是rdb方式持久化，这种方式在许多应用中已经足够用了。
  >
  > 但是redis如果中途宕机，会导致可能有几分钟的数据丢失，根据save策略来进行持久化，
  >
  > Append Only File是另一种持久化方式，可以提供更好的持久化特性。Redis会把每次写入的数据在接收后都写入appendonly.aof文件，每次启动时Redis都会先把这个文件的数据读入内存里，先忽略RDB文件。
  >
  > 默认值为no

+ appendfilename

  > aof文件名，
  >
  > 默认是"appendonly.aof"

+ appendfsync

  > aof持久化策略的配置：
  >
  > no表示不执行fsync，由操作系统保证数据同步到磁盘，速度最快；
  >
  > always表示每次写入都执行fsync，以保证数据同步到磁盘；
  >
  > everysec表示每秒执行一次fsync，可能会导致丢失这1s数据

+ no-appendfsync-on-rewrite

  > 在aof重写或者写入rdb文件的时候，会执行大量IO，此时对于everysec和always的aof模式来说，执行fsync会造成阻塞过长时间，
  >
  > no-appendfsync-on-rewrite字段设置为默认设置为no。
  >
  > 如果对延迟要求很高的应用，这个字段可以设置为yes，否则还是设置为no，这样对持久化特性来说这是更安全的选择。  
  >
  > 设置为yes表示rewrite期间对新写操作不fsync,暂时存在内存中,等rewrite完成后再写入，默认为no，建议yes。
  >
  > Linux的默认fsync策略是30秒。可能丢失30秒数据

+ auto-aof-rewrite-percentage

  > 默认值为100。
  >
  > aof自动重写配置，当目前aof文件大小超过上一次重写的aof文件大小的百分之多少进行重写，即当aof文件增长到一定大小的时候，Redis能够调用bgrewriteaof对日志文件进行重写。
  >
  > 当前AOF文件大小是上次日志重写得到AOF文件大小的二倍（设置为100）时，自动启动新的日志重写过程。

+ auto-aof-rewrite-min-size

  > 设置允许重写的最小aof文件大小，避免了达到约定百分比但尺寸仍然很小的情况还要重写

+ aof-load-truncated

  > aof文件可能在尾部是不完整的，当redis启动的时候，aof文件的数据被载入内存。重启可能发生在redis所在的主机操作系统宕机后，尤其在ext4文件系统没有加上data=ordered选项，出现这种现象  redis宕机或者异常终止都会造成尾部不完整现象，可以选择让redis退出，或者导入尽可能多的数据。
  >
  > 如果选择的是yes，当截断的aof文件被导入的时候，会自动发布一个log给客户端然后load。
  >
  > 如果选择的是no，用户必须手动redis-check-aof修复AOF文件才可以。
  >
  > 默认值为 yes。

##### 10、LUA SCR IPTING

+ lua-time-limit

  > 一个lua脚本执行的最大时间，单位为ms。
  >
  > 默认值为5000.

##### 11、REDIS CLUSTER

+ cluster-enabled

  > 集群开关，默认是不开启集群模式。

+ cluster-config-file

  > 集群配置文件的名称，每个节点都有一个集群相关的配置文件，持久化保存集群的信息。 这个文件并不需要手动配置，这个配置文件有Redis生成并更新，
  >
  > 每个Redis集群节点需要一个单独的配置文件。请确保与实例运行的系统中配置文件名称不冲突。
  >
  > 默认配置为nodes-6379.conf。

+ cluster-node-timeout

  > 可以配置值为15000。
  >
  > 节点互连超时的阀值，集群节点超时毫秒数

+ cluster-slave-validity-factor

  > 可以配置值为10。
  >
  > 在进行故障转移的时候，全部slave都会请求申请为master，但是有些slave可能与master断开连接一段时间了，导致数据过于陈旧，这样的slave不应该被提升为master。
  >
  > 该参数就是用来判断slave节点与master断线的时间是否过长。
  >
  > 判断方法是：比较slave断开连接的时间和(node-timeout * slave-validity-factor) + repl-ping-slave-period   
  >
  > 如果节点超时时间为三十秒, 并且slave-validity-factor为10,假设默认的repl-ping-slave-period是10秒，即如果超过310秒slave将不会尝试进行故障转移

+ cluster-migration-barrier

  > 可以配置值为1。
  >
  > master的slave数量大于该值，slave才能迁移到其他孤立master上，
  >
  > 如这个参数若被设为2，那么只有当一个主节点拥有2 个可工作的从节点时，它的一个从节点会尝试迁移。

+ cluster-require-full-coverage

  > 默认情况下，集群全部的slot有节点负责，集群状态才为ok，才能提供服务。  
  >
  > 设置为no，可以在slot没有全部分配的时候提供服务。
  >
  > 不建议打开该配置，这样会造成分区的时候，小分区的master一直在接受写请求，而造成很长时间数据不一致。

#### 二、RESP

##### 简介

> RESP是redis客户端与服务端之间使用的一种通讯协议，具有的优点是：实现简单、快速解析、可读性好
>
> RESP传输的最小单元类型
>
> 单行字符串： 以 + 符号开头
>
> 多行字符串 以 $ 符号开头，后跟字符串长度
>
> 整数值 以 : 符号开头，后跟整数的字符串形式
>
> 错误消息 以 - 符号开头
>
> 数组 以 * 号开头，后跟数组的长度
>
> 除了错误消息之外，每个单元结束时统一加上回车换行符号\r\n

##### 原理：

> 客户端执行set name zhangsan 这个命令，会被序列化成为下列字符串
>
> *3\r\n$3\r\nset\r\n$4\r\nname\r\n$8\r\nzhangsan\r\n
>
>  
>
> 换一种视图比较好理解
>
> *3    代表这次请求发送了三个数据
>
> $3    代表第一个数据字符串长度为3，set长度为3
>
> set    代表第一个数据的内容
>
> $4    代表第二个数据的长度为4 name长度为4
>
> name   代表第二个数据的内容
>
> $8    代表第三个数据的长度
>
> zhangsan  代表第三个长度的内容
>
>  
>
> 服务端向客户端回复的相应要支持多种数据结构，但是响应结构都是一样的
>
> \#单行字符串响应，如：set author codehole
>
> +OK
>
> \#错误响应，如：incr author
>
> -ERR value is not an integer or out of range
>
> \#整数响应，如：incr books
>
> :1
>
> \#多行字符串响应，如：get author
>
> $8
>
> codehole
>
> \#数组响应，如：hgetall info
>
> *2
>
> $4
>
> name
>
> $6
>
> laoqian

##### 手写一个redis客户端

~~~java
public class RedisClient {

	Socket socket;
	InputStream reader;
	OutputStream writer;
	
	public RedisClient() throws Exception {
		socket = new Socket("127.0.0.1",6379);
		reader = socket.getInputStream();
		writer = socket.getOutputStream();
	}
	
	public String set(String key,String value) throws Exception {
		StringBuffer command = new StringBuffer();
		command.append("*3").append("\r\n");
		command.append("$3").append("\r\n");
		command.append("SET").append("\r\n");
		command.append("$").append(key.getBytes().length).append("\r\n");
		command.append(key).append("\r\n");
		command.append("$").append(value.getBytes().length).append("\r\n");
		command.append(value).append("\r\n");
		System.out.println("----------------自定义set发送的命令 开始-----------------");
		System.out.println(command);
		System.out.println("-----------------结束 ---------------------------------------");
		
		writer.write(command.toString().getBytes());
		
		byte[] response = new byte[1024];
		reader.read(response);
		System.out.println(new String(response));
		return new String(response);
	}
	
	public String get(String key) throws Exception {
		StringBuffer command = new StringBuffer();
		command.append("*2").append("\r\n");
		command.append("$3").append("\r\n");
		command.append("GET").append("\r\n");
		command.append("$").append(key.getBytes().length).append("\r\n");
		command.append(key).append("\r\n");
		System.out.println("----------------自定义get发送的命令 开始-----------------");
		System.out.println(command);
		System.out.println("-----------------结束 ---------------------------------------");
		
		writer.write(command.toString().getBytes());
		
		byte[] response = new byte[1024];
		reader.read(response);
		System.out.println("----------------redis响应结果 ---------------------------------------");
		String respStr = new String(response);
		System.out.println(respStr);
		String[] strs = respStr.split("\r\n");
		return strs[1];
	}
}

~~~

#### 三、基本数据类型

##### 1、string

+ append

  > 对一个key追加数据
  >
  > Append a value to a key

+ bitcount

  > 命令含义：统计字符串被设置为1的bit数
  >
  > 
  >
  > 首先要知道的概念，bit 是什么？
  >
  > bit是英文 binary digit的缩写。比特是表示信息的最小单位，是二进制数的一位包含的信息或2个选项中特别指定1个的需要信息量。一般来说，n比特的信息量可以表现出2的n次方种选择。
  >
  > 1 byte = 8 bit
  >
  > 
  >
  > 接下来来解析bitcount的命令：
  >
  > 如果key的值为1
  >
  > bitcount key 返回的值为 三，
  >
  > 因为 1 在ascii表中的二进制表示为 0011 0001        其中有三位被设置为1，所以返回三
  >
  > 若 key的值为12
  >
  > bitcount key 返回的值为6
  >
  > 12 在ascii中展示为 00110001  00110010   其中有6位被设置为1，所以返回6
  >
  > 
  >
  > bitcount key 0 1
  >
  > 就表示统计第一个字节 和 第二个字节中所有的 1 的个数
  >
  > 例如 现在key存了两个字节 为00110001  00110010 
  >
  > bitcount key 0 1  返回值为6 
  >
  > bitcount key 0 0 返回值为3
  >
  > 若想统计所有字节的含有1的个数，首先知道开始和结束的索引是有正负之分的，正值就是从左到右，负值就是从右向左，
  >
  > bitcount key 0 -1 就是统计所有字节中含有1的个数

+ bitfield

  > bitfield命令可以将一个redis字符串看做是一个由二进制位组成的数组，并对这个数组中储存的长度
  >
  > 不同的整数进行访问(被储存的整数无需对齐).换句话说,通过这个命令,用户可以执行诸如"对偏移量1234上的5位长有符号整数进行设置"，
  >
  > "获取偏移量4567上的31位长无符号整数".此外,bitfield命令还可以对指定的整数执行加法和减法操作，
  >
  > 并且将这些操作可以通过设置妥善地处理计算时出现的溢出情况。
  >
  > bitfield命令可以在一次调用中同时对多个位范围进行操作,它接受一系列待执行的操作作为参数,并返回一个数组作为回复，
  >
  > 数组中的每个元素就是对应操作执行的结果。
  >
  > 
  >
  > 支持子命令以及数字类型：
  >
  > bitfield命令支持的子命令：
  >
  > get <type> <offset> 返回指定的二进制范围
  >
  > set <type> <offset> <value> 对指定的二进制范围进行设置 并返回它的旧值
  >
  > incrby <type> <offset> <increment> 对指定的二进制范围执行加法操作,并返回它的旧值.用户可以通过increment参数传入负值来进行减法操作。
  >
  > 还有一个子命令,可以改变之后执行的incrby子命令在发生溢出情况时的行为。
  >
  > overflow [wrap|sat|fail]
  >
  > 当被设置的二进制范围值为整数时,用户可以在类型参数的前面添加i来表示有符号整数,或者在前面添加u来表示无符号整数。
  >
  > 例如：u8 8位长的无符号整数
  >
  > ​    i16 16位长的有符号整数
  >
  > bitfield命令最大支持64位长有符号整数以及63位长的无符号整数,其中无符号整数的63位长度限制是由于Redis协议目前还无法返回64位长的无符号整数导致。    
  >
  > SET:
  >
  > set test a  //01100001
  >
  > biefield test i8 0 -1  (把无符号设置为有符号整数)
  >
  > getbit test 0 -7  //11111111
  >
  > GET:
  >
  > 获取偏移量100的4位长有符号整数(有符号)：
  >
  > bitfield m  GET i4 100
  >
  > 解析：
  >
  > 整体原码：00000……0001
  >
  > 从100开始获取4位即原码位1000，有符号转十进制,要进行补码(反码加1)，进行反码0111，然后加1，0111+1=1000,十进制就是8,由于最高位是1,所以是-8
  >
  > 
  >
  > 获取偏移量100的4位长有符号整数(无符号)：
  >
  > bitfield m  GET i4 99
  >
  > 解析：
  >
  > 整体原码：00000……0001
  >
  > 从99开始获取4位即原码位0100，最高位是0(正数无符号)，无符号转十进制,0+2^2+0+0=4
  >
  > 
  >
  > 获取偏移量99的4位长无符号整数：
  >
  > bitfield m  GET u4 100
  >
  > 解析：
  >
  > 整体原码：00000……0001
  >
  > 从100开始获取4位即原码位1000，无符号转十进制,2^3+0+0+0=8
  >
  > 使用GET子命令对超出字符串当前范围的二进制进行访问(包括键不存在的情况下)，超出部分的二进制值将被当做是0
  >
  > 
  >
  > INCRBY:
  >
  > 0000……0001
  >
  > BITFIELD m INCRBY i8 100 1
  >
  > 返回-127
  >
  > 解析：
  >
  > i8 100  从100开始获取8位10000000 有符号转十进制,要进行补码(反码加1)，进行反码011111111，01111111+1=10000000 所以2^7+0+0+……=128 最高位是1 所以-128
  >
  > incrby 和最后面的1 就是给100的8位长有符号整数加1即：10000001 所以是-127
  >
  > 减法的话：BITFIELD m INCRBY i8 100 -2  
  >
  > 
  >
  > 二进制位和位置偏移量
  >
  > 在二进制范围命令中,用户有两种方法来设置偏移量：
  >
  > 如果用户给定的是一个没有任何前缀的数字,那么这个数字指示的就是字符串以零开始的偏移量
  >
  > 另一方面,如多用户给定的是一个带有#前缀的偏移量,那么命令将使用这个偏移量与被设置的数字类型的位长度相乘，从而计算出真正的偏移量
  >
  > 举例：
  >
  > bitfield m set i8 #0 100 i8 #1 200
  >
  > 解析：
  >
  > 命令会把m键里面,第一个i8长度的二进制位的值设置为100,第二个i8长度的二进制位设置为200
  >
  > 当我们把一个字符串键当成数组来使用,并且数组中储存的都是同等长度的整数时.使用#前缀可以让我们去避免手动计算被设置
  >
  > 二进制位所在位置的麻烦。
  >
  > getbit m 0-8  //01100100(100的二进制)
  >
  > getbit m 9-16  //11001000(200的二进制)
  >
  > 
  >
  > 溢出控制
  >
  > 用户可以通过overflow命令以及以下展示的三个参数,指定bitfield命令在执行自增或者自减操作时，
  >
  > 碰上向上溢出或者乡下溢出情况时的行为：
  >
  > wrap:使用回绕的方法处理有符号整数和无符号整数的溢出情况。
  >
  > 对于无符号的整数来说,回绕就像使用数值本身与能够被储存的最大无符号整数执行取模计算，这也是C语言的标准行为.
  >
  > 对于有符号整数来说,上溢将导致数字重新从最小的负数开始计算,而下溢将导致数字重新从最大的正数开始计算
  >
  > 比如：127 的i8执行加一操作 那么得到的结果是-128
  >
  > sat:使用饱和计算方法处理溢出,也就是说,下溢计算的结果为最小的整数值,而上溢计算的结果为最大的整数值
  >
  > 例子：
  >
  > 如果对一个值为120的i8整数执行加10计算,那么命令的结果将i8所能存储的最大整数值为127
  >
  > 相反,如果针对i8值计算造成了下溢,那么这个i8值将被设置为-127.
  >
  > fail:在这一模式下,命令将拒绝执行那些会导致上溢或者下溢情况出现的计算,并向用户返回空值表示计算未被执行。
  >
  > 需要注意的是，overflow子命令只会对紧随着它之后被执行的incrby命令产生效果，
  >
  > 这一效果将一直持续到与它一同执行的下一个overflow命令为止。
  >
  > 在默认情况下,incrby命令使用warp方式来处理溢出。
  >
  > 一下是overflow子命令控制溢出的例子：
  >
  > BITFIELD mykey incrby u2 100 1 OVERFLOW SAT incrby u2 102 1
  >
  > 1
  >
  > 1
  >
  > BITFIELD mykey incrby u2 100 1 OVERFLOW SAT incrby u2 102 1
  >
  > 2
  >
  > 2
  >
  > BITFIELD mykey incrby u2 100 1 OVERFLOW SAT incrby u2 102 1
  >
  > 3
  >
  > 3
  >
  > BITFIELD mykey incrby u2 100 1 OVERFLOW SAT incrby u2 102 1
  >
  > 0
  >
  > 3
  >
  > 都是2位长无符号,默认使用warp(回绕) 1(0) 2(1) 3(2) 3之后超出2位,回绕返回0
  >
  > 第二个使用的是sat(饱和) 1(0) 2(1) 3(2) 3已经饱和2位 从3开始2 1
  >
  > 使用fail:
  >
  > BITFIELD mykey OVERFLOW FAIL incrby u2 102 1
  >
  > 返回（nli）

+ strlen

  > 获取value的字节长度
  >
  > Get the length of the value stored in a key

+ incr

  > value值加1
  >
  > Increment the integer value of a key by one

+ setbit

  > 对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)
  >
  > 举例：
  >
  > k1存的值为a
  >
  > a在ascii码中的二进制为01100001
  >
  > 
  >
  > 若想把a改为b，怎么操作？
  >
  > 已知b在ascii码中的二进制为01100010
  >
  > 则需要把a的第6位改为1，第7位改为0就可以了
  >
  > 使用命名：
  >
  > setbit k1 6 1
  >
  > setbit k1 7 0

+ bitop

  > 语法：bitop operation destkey key [key...] 
  >
  > 对一个或多个保存二进制位的字符串key进行位元操作,并将结果保存到destksy上 operation可以是AND、OR、NOT、XOR这四种操作中的任意一种
  >
  > 
  >
  > 若 k1的值为 01001100，k2的值为 00101010
  >
  > 对两个值进行and操作（按位与 同1为1）
  >
  > 01001100
  >
  > 00101010
  >
  > \----------
  >
  > 00001000
  >
  > 得到的就是00001000
  >
  > 所以 
  >
  > bittop and resultkey k1 k2
  >
  > get resultkey
  >
  > 得到的结果就是00001000对应的ascii码␈ 退格

+ bit使用场景

  > 场景： 统计用户任意时间窗口内登录的次数
  >
  > 假如公司有很多用户,使用用户名为key，其中有人名为 tom
  >
  > tom在2020年的第1天登录了系统，
  >
  > 执行：setbit tom 1 1   
  >
  > 执行结果就是tom的value为 01000000
  >
  > tom在2020年的第4天登录了系统
  >
  > 执行：setbit tom 4 1 
  >
  > 执行结果就是tom的value为 01001000
  >
  > ...
  >
  > tom在2020年的第150天登录了系统
  >
  > 执行：setbit tom 150 1 
  >
  > 执行结果就是tom的value为 01001000 00000000 ... 00001000
  >
  > 然后统计这一年内tom一共登录系统多少次
  >
  > bitcount tom 0 -1
  >
  > 就可以直接出结果
  >
  > 优点：速度快，省内存（即使一整年全部登录，在系统中也就占了46k的内存）

  > 场景：在双十一、618的之前，统计活跃用户，通过活跃用户来给仓库算出来备货数量
  >
  > 
  >
  > 假设前提：只要在5月份到十月份之间登录过的用户就算是活跃用户，
  >
  > 思路：
  >
  > 1、每个用户都有一个固定id，假设使用1、2.....10000代替
  >
  > 2、以日期天为key，用户id为偏移量，在二进制数组进行1的操作
  >
  > setbit 20200101 1 1
  >
  > setbit 20200101 4 1
  >
  > setbit 20200101 7 1
  >
  > setbit 20200102 1 1
  >
  > setbit 20200102 5 1
  >
  > setbit 20200104 1 1
  >
  > setbit 20200104 2 1
  >
  > 解释一下：
  >
  > 20200101这一天，有id为1、4、7的三个用户登录
  >
  > 20200102这一天，有id为1、5的两个用户登录
  >
  > 20200104这一天，有id为1、2的两个用户登录
  >
  > 求活跃数：
  >
  > 先把每天的数据进行整合
  >
  > bitop or resultbit 20200101 20200102 20200104
  >
  > 然后进行取出结果中所有1的数量就是这些天内活跃用户的个数
  >
  > bitcount result
  >
  > 
  >
  > \-----------------------------------------------------------------------------------------
  >
  > 进阶操作：如果用户量特别多，可以进行分片操作
  >
  > setbit 20200101:1001  1 1
  >
  > setbit 20200101:1001  1000 1
  >
  > setbit 20200101:2001  2000 1

##### 2、list

+ 双向链表

  > key是对象，存储了链表的头和尾，访问数据会很快

+ lpush

  > lpush k1 a b c d
  >
  > 从左边往k1里面推送数据

+ lrange

  > lrange k1 0 -1
  >
  > 取出链表的所有数据

+ rpush

  > rpush k1 e f 
  >
  > 从右边推送数据到k1

+ ltrim

  > ltrim k1 0 -1
  >
  > 删除区间以外的所有数据
  >
  > 
  >
  > 应用场景：评论列表
  >
  > 评论一般会很多，先进来的数据属于历史评论，价值不高，用户也不看，就可以删除右侧历史数据，保持redis数据的热点性

##### 3、hash

+ hset

  > hset tom name tom
  >
  > hset tom age 19
  >
  > hset tom birthday 2020-01-01

+ hgetall

  > Get all the fields and values in a hash
  >
  > hgetall tom

+ hkeys

+ hvals

+ 使用场景

  > 常用来做数据聚合
  >
  > 就是把多个表的数据聚合成为一个大的对象存储在redis
  >
  > 例如：购物车详情页面
  >
  > 原因：购物车的详情数据一般都来自于很多表，使用传统的数据库模型会进行多表关联查询，速度不佳
  >
  > 实现：可以在后台批处理或者别的操作把热点数据整合到一个hash对象里面，保存在redis中，请求详情页的时候，直接调用redis的这个hash key，当购物车进行增加或者删除操作，再去对应的修改redis数据

##### 4、set

+ sadd

  > sadd k1 1122 2211 1122 1212

+ smembers

  > smembers k1
  >
  > 获取k1数据

+ srandmember

  > sranmember k1 5
  >
  > 如果为正数，则返回去重的数据
  >
  > 
  >
  > sranmember k1 -6
  >
  > 如果为负数，则返回不去重的数据
  >
  > 假如k1 值为 1 2 4 5 6 1
  >
  > 执行 sranmember k1 -2
  >
  > 可能返回
  >
  > 5
  >
  > 5

+ spop

  > spop k1
  >
  > 读取set中一位数据，然后移除此数据

+ sinter

  > sinter k1 k2
  >
  > 对两个集合做交集
  >
  > 返回两个集合中共同的数据

+ sunion

  > sunion k1 k2
  >
  > 对两个集合做并集
  >
  > 返回两个集合的全部数据并去重

+ sdiff

  > sdiff 差集  分为左差集和右差集
  >
  > 例如
  >
  > k1 a b c d
  >
  > k2 a b y z
  >
  > sdiff k1 k2
  >
  > 返回 c d
  >
  > 调换k1 k2 位置
  >
  > sdiff k2 k1
  >
  > 返回  y z

+ 使用场景

  > 特性：无序 满足随机性
  >
  > 场景1
  >
  > 抽奖
  >
  > 使用无序性，读取set中的数据返回的都是随机的
  >
  > 
  >
  > 对于集合的操作：
  >
  > 场景：
  >
  > 共同好友、可能认识的人、 共同爱好等
  >
  > 实现：
  >
  > k1 存放的是用户列表
  >
  > 共同好友就是两个k取交集
  >
  > 可能认识的人就是两个k取差集

##### 5、zset

+ zadd

  > zadd zk1 1.1 apple 2.4 banana 1 orange
  >
  > 添加数据必须带上分值，也就是排序的比重

+ zrange

  > zrange k1 0 -1
  >
  > 按照分值从大到小取出数据
  >
  > zrange k1 0 -1 withscores
  >
  > 按照分值从大到小取出数据 并显示分值

+ zrevrange

  > 反向获取数据
  >
  > k1  1 tom 2 anty 4 kevy
  >
  > zrevrange k1 0 1
  >
  > 返回值为
  >
  > kevy
  >
  > anty

+ zincrby

  > zincrby k1 4 orange
  >
  > 把
  >
  > k1中 orange的分值改为4
  >
  >  然后redis会自动的对k1进行重新排序

+ 使用场景

  > 排行榜
  >
  > 每次点击量都加1，然后zset自动排序，取出来的都是点击量高的
  >
  > 
  >
  > 评论分页
  >
  > 以评论时间为分数，以评论内容为数据

+ 如何排序：skipList算法

+ GEO

  > GEO功能在Redis3.2版本提供，支持存储地理位置信息用来实现诸如附近位置、摇一摇这类依赖于地理位置信息的功能.geo的数据类型为zset

#### 四、持久化

> 4.x之前默认rdb持久化，可以人工开启aof，然后rdb就失效了
>
> 4.x之后：开启混合模式
>
> 混合模式：
>
> 依旧是一个aof文件，在aof有冗余的时候，进行重写命令，在重写的时候会先拍一个快照，把快照的二进制文件保存到aof文件内，然后再把以后的命令按照aof的规则进行保存

##### 1、快照

> 时间窗口
>
> 就是某一个时间，保存redis内部所有数据进入磁盘，数据是二进制的
>
> 优点：
>
> 全量数据、加载快（加载的时候只需要二进制数据序列化就可以）
>
> 缺点：
>
> 会丢失大部分数据
>
> 如：每隔1小时进行一个快照（也就是RDB），8点的时候完成了一次快照，8点50redis宕机了，那会丢失8点到8点50这50分钟的数据

##### 2、日志

> 简称AOF
>
> 优点
>
> 完整、可以记录每一个操作、追加到日志文件
>
> 缺点
>
> 加载慢（需要把操作命令写到日志）
>
> 有冗余（用户多次对key进行操作，日志会追加很多命令）（可以用重写来解决）

#### 五、事务

> Redis 事务可以一次执行多个命令， 并且带有以下三个重要的保证
>
> 批量操作在发送 EXEC 命令前被放入队列缓存。
>
> 收到 EXEC 命令后进入事务执行，事务中任意命令执行失败，其余的命令依然被执行。
>
> 在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中

##### 1、multi

> 开始事务

#####  2、exec

> 执行一个事务内部所有的命令

#####  3、discard

> 取消事务块内所有的命令

#####  4、watch

> 监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断。

#####  5、unwatch

> 取消 WATCH 命令对所有 key 的监视。

#### 六、lua脚本

##### 1、eval

> redis 127.0.0.1:6379> EVAL script numkeys key [key ...] arg [arg ...]
>
> script： 参数是一段 Lua 5.1 脚本程序。脚本不必(也不应该)定义为一个 Lua 函数
>
> numkeys： 用于指定键名参数的个数。
>
> key [key ...]： 从 EVAL 的第三个参数开始算起，表示在脚本中所用到的那些 Redis 键(key)，这些键名参数可以在 Lua 中通过全局变量 KEYS 数组，用 1 为基址的形式访问( KEYS[1] ， KEYS[2] ，以此类推)。
>
> arg [arg ...]： 附加参数，在 Lua 中通过全局变量 ARGV 数组访问，访问的形式和 KEYS 变量类似( ARGV[1] 、 ARGV[2] ，诸如此类)
>
> 例如：
>
> redis 127.0.0.1:6379> eval "return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]}" 2 key1 key2 first second
>
> ​		1) "key1"
>
> ​		2) "key2"
>
> ​		3) "first"
>
> ​		4) "second"

##### 2、evalsha

> 根据命令给定的sha1校验码，执行缓存在服务器中的脚本
>
> 格式：
>
> redis 127.0.0.1:6379> EVALSHA sha1 numkeys key [key ...] arg [arg ...] 
>
> 
>
> 参数解释：
>
> sha1 ： 通过 SCRIPT LOAD 生成的 sha1 校验码。
>
> numkeys： 用于指定键名参数的个数。
>
> key [key ...]： 从 EVAL 的第三个参数开始算起，表示在脚本中所用到的那些 Redis 键(key)，这些键名参数可以在 Lua 中通过全局变量 KEYS 数组，用 1 为基址的形式访问( KEYS[1] ， KEYS[2] ，以此类推)。
>
> arg [arg ...]： 附加参数，在 Lua 中通过全局变量 ARGV 数组访问，访问的形式和 KEYS 变量类似( ARGV[1] 、 ARGV[2] ，诸如此类)。
>
> 
>
> 例子：
>
> 先生成sha1校验码
>
> redis 127.0.0.1:6379> SCRIPT LOAD "return 'hello moto'"
>
> "232fd51614574cf0867b83d384a5e898cfd24e5a"
>
> 执行脚本
>
> redis 127.0.0.1:6379> EVALSHA "232fd51614574cf0867b83d384a5e898cfd24e5a" 0
>
> "hello moto"

##### 3、exists

> 用于校验指定的脚本是否已经被保存在缓存当中
>
> 格式：
>
> SCRIPT EXISTS sha1 [sha1 ...]
>
> 一个列表，包含 0 和 1 ，前者表示脚本不存在于缓存，后者表示脚本已经在缓存里面了。
>
> 
>
> 例子：
>
> redis 127.0.0.1:6379> SCRIPT LOAD "return 'hello moto'"    # 载入一个脚本
>
> "232fd51614574cf0867b83d384a5e898cfd24e5a"
>
> 
>
> redis 127.0.0.1:6379> SCRIPT EXISTS 232fd51614574cf0867b83d384a5e898cfd24e5a
>
> 1) (integer) 1
>
> 
>
> redis 127.0.0.1:6379> SCRIPT FLUSH     # 清空缓存
>
> OK
>
> 
>
> redis 127.0.0.1:6379> SCRIPT EXISTS 232fd51614574cf0867b83d384a5e898cfd24e5a
>
> 1) (integer) 0

##### 4、flush

> 清空所有lua脚本缓存
>
> SCRIPT FLUSH

##### 5、kill

> 用于杀死当前正在运行的 Lua 脚本，当且仅当这个脚本没有执行过任何写操作时，这个命令才生效
>
> 这个命令主要用于终止运行时间过长的脚本，比如一个因为 BUG 而发生无限循环的脚本。
>
> SCRIPT KILL 执行之后，当前正在运行的脚本会被杀死，执行这个脚本的客户端会从 EVAL 命令的阻塞当中退出，并收到一个错误作为返回值

##### 6、load

> 用于将脚本 script 添加到脚本缓存中，但并不立即执行这个脚本

#### 七、二进制安全

> redis在存储数据的时候是没有类型的概念，只有字节的概念
>
> 意思就是所有存入redis的数据都是被客户端序列化为字节数组
>
> 举个例子：
>
> 例如 set k1 a
>
> 结果：在redis存入的就是a，一个字节长度，因为a属于ascii码，（基础编码集）
>
> 例如：set k1 中
>
> 当我的客户端使用utf-8连接redis的时候，中  这个汉字在utf-8编码集中代表的是三个字节
>
> 结果 在redis存入的就是三个字节
>
> 当我的客户端使用gbk连接redis的时候， 中  这个汉字在gbk编码集中代表2个字节，
>
> 结果 在redis存入的就是2个字节

#### 八、淘汰策略

##### 1、LRU

> 挑选最近最少使用的数据淘汰

##### 2、LFU

> 挑选呢使用次数最少的数据淘汰

##### 3、TTL

> 挑选将要过期的数据淘汰

##### 4、random

> 任意选择数据淘汰

#### 九、删除策略

##### 1、定期删除

##### 2、定时删除

##### 3、惰性删除

#### 十、分布式锁

##### 1、分布式锁的特性

> 首先，为了确保分布式锁可用，我们至少要确保锁的实现同时满足以下四个条件：
>
> 互斥性。在任意时刻，只有一个客户端能持有锁。
>
> 不会发生死锁。即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。
>
> 具有容错性。只要大部分的Redis节点正常运行，客户端就可以加锁和解锁。
>
> 解铃还须系铃人。加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了。

##### 2、加锁代码

~~~java
public class RedisTool {
 
    private static final String LOCK_SUCCESS = "OK";
    private static final String SET_IF_NOT_EXIST = "NX";
    private static final String SET_WITH_EXPIRE_TIME = "PX";
 
    /**
     * 尝试获取分布式锁
     * @param jedis Redis客户端
     * @param lockKey 锁
     * @param requestId 请求标识
     * @param expireTime 超期时间
     * @return 是否获取成功
     */
    public static boolean tryGetDistributedLock(Jedis jedis, String lockKey, String requestId, 
                                                int expireTime) {
        String result = jedis.set(lockKey, requestId, SET_IF_NOT_EXIST, 
                                  SET_WITH_EXPIRE_TIME, expireTime);
        if (LOCK_SUCCESS.equals(result)) {
            return true;
        }
        return false;
    }
}

/**
可以看到，我们加锁就一行代码：jedis.set(String key, String value, String nxxx, String expx, int time)，这个set()方法一共有五个形参：

第一个为key，我们使用key来当锁，因为key是唯一的。

第二个为value，我们传的是requestId，很多童鞋可能不明白，有key作为锁不就够了吗，为什么还要用到value？原因就是我们在上面讲到可靠性时，分布式锁要满足第四个条件解铃还须系铃人，通过给value赋值为requestId，我们就知道这把锁是哪个请求加的了，在解锁的时候就可以有依据。requestId可以使用UUID.randomUUID().toString()方法生成。

第三个为nxxx，这个参数我们填的是NX，意思是SET IF NOT EXIST，即当key不存在时，我们进行set操作；若key已经存在，则不做任何操作；

第四个为expx，这个参数我们传的是PX，意思是我们要给这个key加一个过期的设置，具体时间由第五个参数决定。

第五个为time，与第四个参数相呼应，代表key的过期时间。

总的来说，执行上面的set()方法就只会导致两种结果：1. 当前没有锁（key不存在），那么就进行加锁操作，并对锁设置个有效期，同时value表示加锁的客户端。2. 已有锁存在，不做任何操作。

心细的童鞋就会发现了，我们的加锁代码满足我们可靠性里描述的三个条件。首先，set()加入了NX参数，可以保证如果已有key存在，则函数不会调用成功，也就是只有一个客户端能持有锁，满足互斥性。其次，由于我们对锁设置了过期时间，即使锁的持有者后续发生崩溃而没有解锁，锁也会因为到了过期时间而自动解锁（即key被删除），不会发生死锁。最后，因为我们将value赋值为requestId，代表加锁的客户端请求标识，那么在客户端在解锁的时候就可以进行校验是否是同一个客户端。由于我们只考虑Redis单机部署的场景，所以容错性我们暂不考虑。
*/
~~~

##### 3、解锁代码

~~~~java
public class RedisTool {
 
    private static final Long RELEASE_SUCCESS = 1L;
 
    /**
     * 释放分布式锁
     * @param jedis Redis客户端
     * @param lockKey 锁
     * @param requestId 请求标识
     * @return 是否释放成功
     */
    public static boolean releaseDistributedLock(Jedis jedis, String lockKey, String requestId) {
 
        String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
        Object result = jedis.eval(script, Collections.singletonList(lockKey), 			                                                             Collections.singletonList(requestId));
        if (RELEASE_SUCCESS.equals(result)) {
            return true;
        }
        return false;
    }
}

/**
可以看到，我们解锁只需要两行代码就搞定了！第一行代码，我们写了一个简单的Lua脚本代码，上一次见到这个编程语言还是在《黑客与画家》里，没想到这次居然用上了。第二行代码，我们将Lua代码传到jedis.eval()方法里，并使参数KEYS[1]赋值为lockKey，ARGV[1]赋值为requestId。eval()方法是将Lua代码交给Redis服务端执行。

那么这段Lua代码的功能是什么呢？其实很简单，首先获取锁对应的value值，检查是否与requestId相等，如果相等则删除锁（解锁）。那么为什么要使用Lua语言来实现呢？因为要确保上述操作是原子性的。关于非原子性会带来什么问题，可以阅读【解锁代码-错误示例2】 。那么为什么执行eval()方法可以确保原子性，源于Redis的特性，下面是官网对eval命令的部分解释：

简单来说，就是在eval命令执行Lua代码的时候，Lua代码将被当成一个命令去执行，并且直到eval命令执行完成，Redis才会执行其他命令。
*/
~~~~

#### 十一、主从复制

> 主从复制的配置十分简单：把下面这行加入到从服务器的配置文件中即可。
>
> slaveof 192.168.1.1 6379
>
>  
>
> 当然你需要把其中的192.168.1.1 6379替换为你自己的主服务器IP（或者主机名hostname）和端口。另外你可以调用SLAVEOF命令，
>
> 主服务器就会开始与从服务器同步。
>
>  
>
> 使用repl-diskless-sync配置参数来启动无磁盘复制。使用repl-diskless-sync-delay 参数来配置传输开始的延迟时间，以便等待更多的从服务器连接上来 
>
> 
>
> 从Redis 2.6开始，从服务器支持只读模式，并且是默认模式。这个行为是由Redis.conf文件中的
>
> slave-read-only 参数控制的，
>
> 可以在运行中通过CONFIG SET来启用或者禁用。
>
>  
>
> 只读的从服务器会拒绝所有写命令，所以对从服务器不会有误写操作。但这不表示可以把从服务器实例暴露在危险的网络环境下，
>
> 因为像DEBUG或者CONFIG这样的管理命令还是可以运行的。不过你可以通过使用rename-command命令来为这些命令改名来增加安全性。
>
>  
>
> 你可能想知道为什么只读限制还可以被还原，使得从服务器还可以进行写操作。虽然当主从服务器进行重新同步或者从服务器重启后，
>
> 这些写操作都会失效，还是有一些使用场景会想从服务器中写入临时数据的，但将来这个特性可能会被去掉。

#### 十二、哨兵

##### 1、监控

##### 2、通知

##### 3、故障转移

#### 十三、集群

#### ......

#### .......