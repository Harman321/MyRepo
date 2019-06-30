Docker



### 安装Docker

```
sudo apt-get update
sudo apt-get install  docker.io

sudo docker info
sudo docker pull hello-world
sudo docker run -it --rm hello-world
```



![](/home/kabu-nuo/typro/hello-world.png)





### 安装kubernetes

1. 关闭防火墙

```

systemctl disable firewalld && systemctl stop firewalld 
```



2. 关闭swap

   自 1.8 开始，k8s 要求关闭系统 Swap，如果不关闭，kubelet 无法启动。

```
sudo swapoff -a

```



![](/home/kabu-nuo/typro/swap.png)

3. 关闭SELinux

```

setenforce 0
sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
```





安装工具

```
apt-get update && apt-get install -y apt-transport-https
```



添加秘钥

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
```

经测试这里可能报错： gpg:no valid OpenPGP data found

   注意：需要通过下面两条命令来解决：curl -O  https://packages.cloud.google.com/apt/doc/apt-key.gpg  先保存一个apt-key.gpg的文件，再通过apt-key add apt-key.gpg来加载。





添加Kubernetes软件源

```
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
```

Kubernets国内镜像

阿里云提供了Kubernetes国内镜像来安装`kubelet`、`kubectl` 和 `kubeadm`。





查询k8s的可以安装的版本： apt-cache madison kubeadm  apt-cache madison kubelet  apt-cache madison kubectl  apt-cache kubernetes-cni

登陆阿里云镜像网站：https://opsx.alibaba.com/mirror

查找关键字“kubernetes”，点击【帮助】按钮。
Debian / Ubuntu

apt-get update && apt-get install -y apt-transport-https
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF  







安装具体版本， 例如
apt-get install kubeadm=1.9.0-00
apt-get install kubelet=1.9.0-00
apt-get install kubectl=1.9.0-00

apt-get install kubernetes-cni=0.6.0-00



安装kubelet kubeadm kubectl 

```
apt-get update && apt-get install -y kubelet kubeadm kubectl 这里采用对应版本安装，或者从已安装的机器上copy
```









https://blog.csdn.net/russle/article/details/78841937







https://blog.csdn.net/liukuan73/article/details/83116271



https://my.oschina.net/beyondken/blog/1647138



https://my.oschina.net/Kanonpy/blog/3006129





https://blog.csdn.net/nklinsirui/article/details/80581286