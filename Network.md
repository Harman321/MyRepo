ubuntu16.04





## 一、 网络配置相关文件

### 1. /etc/host.conf 配置文件

```
/etc/host.conf文件的默认内容如下：

# The"order" line is only used by old versions of the C library.
order hosts,bind  # 指定主机名的解析顺序，即本地解析，DNS域名解析

multi on  # 允许主机拥有多个IP地址
```



### 2. /etc/hosts 配置文件

    /etc/hosts文件的默认内容如下(不同主机，IP映射的主机名不同)：
    
    127.0.0.1 butbueatiful  localhost.localdomain  localhost
    ::1 localhost6.localdomain6  localhost6
    

可见，默认的情况是本机ip和本机一些主机名的对应关系，

第一行是ipv4信息，

第二行是ipv6信息，如果用不上ipv6本机解析，一般把该行注释掉。

第一行的解析效果是，butbueatiful localhost.localdomain localhost三者都会被解析成127.0.0.1,我们可以用ping试试。

如果我们要追加新的本地解析，比如我们希望在我们的机器里把yyyy.com和www.yyyy.com都解析成192.168.0.100，那么就追加如下一句即可：

```
192.168.0.100  yyyy.com  www.yyyy.com
```

### 3. /etc/resolv.conf 配置文件

该文件用来指定DNS域名解析服务器的IP信息，其配置参数一般有以下四个：

nameserver #指定DNS服务器的IP地址
domain #定义本地域名信息
search #定义域名的搜索列表

sortlist #对gethostbyname返回的地址进行排序

但是最常用的配置参数是nameserver，其他的可以不设置，这个参数指定了DNS服务器的IP地址，如果设置不正确，就无法进行正常的域名解析。

一般来说，推荐设置2个DNS服务器，比如我们用google的免费DNS服务器，那么该文件的设置内容如下：

```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

同样，这个文件也是危险的，如果被人恶意改成了他自己的DNS服务器，他就可以为所欲为的控制你通过域名访问的每个目的地了，这就是常说的DNS劫持



### 4. /etc/network/interfaces 配置文件

interfaces网络接口配置文件，是Ubuntu网络配置中最为重要的一个文件。本文件用来设定主机的IP类型，IP地址，子网掩码，网关IP，广播地址，MAC物理地址等信息，详细的配置方法参见本文第二章节，以下以静态IP配置给出该文件的内容示例。

```
auto lo #auto 表示系统启动（boot up）时 自动加载lo接口
iface lo inet loopback #接口 lo 用作 loopback 本地回环测试

auto eth0 # 指明当前配置的是主机上的eth0网卡
iface eth0 inet static  # 将eth0网卡配置为静态IP类型
address 10.8.210.145 # 手动设定eth0网卡的IP地址为10.8.210.145
netmask 255.255.255.0 # 手动设定子网掩码
gateway 10.8.210.7 # 手动设定网关IP
#pre-up ifconfigeth0 hw ether 11:22:33:44:55:66 # 手动设定MAC地址

#auto eth0:1 # 创建基于eth0网卡的虚拟网卡，即让一个网卡拥有过个IP #iface eth0:1inet static # 将虚拟网卡eth0:1设置为静态IP #address192.168.1.10 # 配置虚拟网卡eth0:1网卡的IP地址 #netmask255.255.255.0 # 配置虚拟网卡eth0:1网卡的子网掩码 #gateway192.168.1.1 # 配置虚拟网卡eth0:1网卡的网关地址


```

### 5. 其他配置文件

/etc/networks # 实现域名与网络地址的映射

/etc/protocols # 设定主机使用的协议及各个协议的协议号(协议ID)

/etc/services # 设定主机上各个网络服务进程所使用的端口号

上述三个配置文件的作用如注释所说，一般保持默认，不必修改。



## 二、配置网络的2种方式

### 1.概述

Ubuntu在Desktop版本中，提供了两种方法来进行网络参数配置。即图形界面和文本命令行配置。

其实，只要弄清楚interfaces和 nm（NetworkManager）之间的关系，这些问题就不难解释了。

- 首先，当系统内没有第三方网络管理工具（比如nm）时，系统默认使用interfaces文件内的参数进行网络配置。


- 接着，当系统内安装了nm之后，nm默认接管了系统的网络配置，使用nm自己的网络配置参数来进行配置。


- 如果用户在安装nm之后（Desktop版本默认安装了nm），自己手动修改了interfaces 文件，那nm 就自动停止对系统网络的管理，系统改使用interfaces 文件内的参数进行网络配置。此时，再去修改nm 内的参数，不影响系统实际的网络配置。若要让nm 内的配置生效，必须重新启用nm 接管系统的网络配置。




如果用户希望在Desktop版本中，直接使用interfaces 进行网络配置，那么可以通过以下指令来关闭`network-manager`：

```
/etc/init.d/network-managerstop # 手动关闭network-manager
```

之后通过查看`/etc/NetworkManager/nm-system-settings.conf`文件来确定`nm`是否停止网络配置工作。即只需要确保`/etc/NetworkManager/nm-system-settings.conf`内的`managed=false`。



如果希望能继续使用nm 来进行网络配置，则需要进行如下操作：

    sudo service network-manager stop # 停止nm服务
    sudo rm /var/lib/NetworkManager/NetworkManager.state# 移除nm 的状态文件
    sudo gedit /etc/NetworkManager/nm-system-settings.conf# 打开nm 的配置文件
    

修改文件里面这一行：managed=false，将false修改成true，然后重启nm程序，指令如下：

    sudo servicenetwork-manager start # 由nm程序重新接管网络配置工作

【注意】如果手工改过/etc/network/interfaces，`nm会自己把这行改成：managed=false(这里应该默认就是false)



### 2.图形界面配置程序NetworkManager

配置/etc/NetworkManager/system-connections目录下的文件
这是配合图形界面配置程序NetworkManager，可以在桌面通过图形界面配置，配置信息保存在这个目录下的文件中。

```
1. 配置文件
/etc/NetworkManager/system-connections

```

### 3.通过直接修改配置文件配置网络

 Ubuntu 下通过配置文件设置DNS的方式有两种：interfaces 和 resolvconf.

(1)interfaces方式

修改/etc/network/interfaces配置DNS需要在该文件中加入

    dns-nameserver xx.xx.xx.xx
    dns-nameserver xxx.xxx.xx.xx
    dns-nameservers xxx.xxx.xxx.xxx xxx.xxx.xx.xxx
    
    dns-nameserver: 指定一条DNS地址，如果需要指定多个DNS则需要使用添加多行。
    dns-nameservers: 指定多个DNS地址，用空格隔开。

备注： 这种方式修改DNS后需要重启电脑方可生效（我没有找到其它使其生效的方式，重启网络并不能更新cat /etc/resolv.conf。

(2)resolvconf方式
resolvconf方式修改DNS则是通过修改/etc/resolvconf/resolv.conf.d/head文件实现，当需要添加DNS记录时，在文件中加入下面内容：

    nameserver xxx.xxx.xx.xxx
    nameserver xx.xx.xx.xx
    
    nameserver: 指定DNS地址，当有多个DNS记录时每个DNS记录占一行。

修改成功后，运行sudo resolvconf -u更新/etc/resolv.conf文件即可。这种方式不需要重启电脑。

备注： 看到网上说可以修改/etc/resolvconf/resolv.conf.d/base文件同样能配置DNS。





**注：**

两种配置方法只能选用一种，如果eth0同时出现在两个配置文件中，则以 /etc/network/interfaces中的配置为准。



## 三、网络相关工具

### 1.dig



### 2.route



### 3.ifconfig



### 4.netstat









## 四、References:

[ [1] Ubuntu网络配置文件 ](https://blog.csdn.net/dream361/article/details/65936699 )

[ [2] Ubuntu16.04网络配置文件 ](https://blog.csdn.net/RadiantJeral/article/details/86298988)