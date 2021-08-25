# Kubernetes安装与服务发布

## 一、安装

### 1.全节点配置

1.IP地址

2.主机名

```
	1.首先修改/etc/cloud/cloud.cfg
		sudo vim /etc/cloud/cloud.cfg 
		#找到preserve_hostname: false修改为preserve_hostname: true
```

​	2.修改主机名(永久)

```
		*#修改主机名(临时)
		#hostname master
		#修改主机名(永久)
		sudo vim /etc/hostname 
		#然后改为需要的主机名后存盘退出
		#映射主机名* 
		sudo vim /etc/hosts 
		#192.168.1.xxx 主机名
```

​	3.重启 reboot

### 2.安装工具

安装k8s可以直接使用apt-get命令安装

当然前提是需要添加镜像源

到阿里云镜像站里面找kubernetes，里面有配置方法，可以一步步做

https://developer.aliyun.com/mirror/kubernetes?spm=a2c6h.13651102.0.0.53322f70ywQtbs

```
apt-get update && apt-get install -y apt-transport-https
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF  
apt-get update
apt-get install -y kubelet kubeadm kubectl
```

这下就直接安装了3个工具，分别是 **kubelet kubeadm kubectl**

### 3.安装k8s

#### (1)安装master

首先，在主节点创建/usr/local/kubernetes/cluster

输入命令 

```
kubeadm config print init-defaults --kubeconfig ClusterConfiguration > kubeadm.yml
```

然后进入kubeadm.yml文件，修改东西

```
apiVersion: kubeadm.k8s.io/v1beta2
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: abcdef.0123456789abcdef
  ttl: 24h0m0s
  usages:
  - signing
  - authentication
kind: InitConfiguration
localAPIEndpoint:
  advertiseAddress: 192.168.1.31
  bindPort: 6443
nodeRegistration:
  criSocket: /var/run/dockershim.sock
  name: k8s-master
  taints:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
---
apiServer:
  timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta2
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controllerManager: {}
dns:
  type: CoreDNS
etcd:
  local:
    dataDir: /var/lib/etcd
imageRepository: registry.aliyuncs.com/google_containers
kind: ClusterConfiguration
kubernetesVersion: v1.17.0
networking:
  dnsDomain: cluster.local
  podSubnet: "10.244.0.0/16"
  serviceSubnet: 10.96.0.0/12
scheduler: {}
```

保存退出

然后开始安装k8s

输入命令	

```
kubeadm init --config=kubeadm.yml --upload-certs | tee kubeadm-init.log
```

安装完成之后会出现以下提示

```
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.1.31:6443 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:d109c913b871c9516914e09df16a760f2c73dcbab6c73302cef6450011c92b10
```

这些东西会保存在那个**log文件**当中

然后运行

```
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```

然后运行	**kubectl get node**

出现节点信息

#### (2)安装node

直接在node节点复制粘贴运行

```
kubeadm join 192.168.1.31:6443 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:d109c913b871c9516914e09df16a760f2c73dcbab6c73302cef6450011c92b10
```

然后回到master运行	**kubectl get nodes**

发现node已经加入

## 二、配置网络

### Calico插件

calico只是个插件，kubernetes里面没有具体应用，只有api

使用 Calico 作为 NetworkPolicy	https://kubernetes.io/zh/docs/tasks/administer-cluster/network-policy-provider/calico-network-policy/

#### 安装Calico

calico的quickstart可以直接从官网的文档上看https://docs.projectcalico.org/v3.11/getting-started/kubernetes/

Install Calico with the following command.

```
kubectl apply -f https://docs.projectcalico.org/v3.11/manifests/calico.yaml
```

Confirm that all of the pods are running with the following command.

```
watch kubectl get pods --all-namespaces
```

安装时间有点长 耐心等待

等到所有的status都是running之后，安装完成

然后kubectl get nodes 发现所有的节点都是**ready**

可以看见其他节点的**docker images**里面出现了calico

## 三、第一个容器

### 1.检查组件运行状态

kubectl get cs

### 2.检查Master运行状态

kubectl cluster-info

### 3.检查node运行状态

kubectl get nodes

### 4.运行第一个容器

kubectl run nginx --image=nginx --replicas=2 --port=80

kubectl get pods 发现有两个nginx容器

kubectl get deployment

这个时候，应用只是部署在k8s的内网，还未暴露出来

### 5.发布服务(暴露端口)

kubectl expose deployment nginx --port=80 --type=LoadBalancer

kubectl get service（查看服务）

左边内网端口右边暴露端口

kubectl describe service nginx(服务详情)

### 6.查看服务

在浏览器输入node的地址+刚才看到的端口，发现两个nginx部署成功



输入docker ps 可以看见有很多容器正在运行，这也说明了k8s是docker的容器编排工具

**master节点在默认情况下不会运行pod**

在node节点docker ps可以看见nginx容器



docker rm -f [nginx容器id]

再查看docker ps 会发现它会自己给你重启容器



### 7.停止服务

kubectl delete deployment nginx

kubectl delete service nginx

删除已部署的服务和已发布的服务

