1.pods:是一组紧密关联的容器集合，它们共享IPC(进程间通信)、Network(网络)和UTSnamespace(UTS命 
名空间是Linux命名空间的一个子系统，主要作用是完成对容器 Hostname和Domain的隔离，同时保存内 
核名称、版本、以及底居体系结构突型等信息)，是Kubernetes调度的基本单位。
2.labels:键值对(key/value)标签，可以被关联到如Pod这样的对象上，主要作用是给用户一个直观的感受，
比如这个Pod是用来放置数据库的
3.GUI:用户图形界面，可以是Web用户界面，比如使用kubernetes-dashboard组件，用户可以通过 
Dashboard在Kubernetes集群中部署容器化的应用，可以查看集群中应用的运行情况，同时也能够基于 
Dashboard创建或修改部署、任务、服务等Kubernetes的资源。通过部署向导，用户能够对部署进行扩缩 
容，逬行滚动更新重启Pod和部署新应用。当然，通过Dashboard也能够查看Kubernetes资源的状态
4.kubectl:用于管理Kubernetes集群的命令行工具
5.kube-apiserver:提供了资源操作的唯一入口，并提供认证、授权、访问控制、API注册和发现等机制
6.Kubernetes Master: Kubernetes集群主节点，主要由 kube-apiserver、kube-scheduler、kube-controller-manager、etcd 四个模块组成
7.Kubernetes Node: Kubernetes集群子节点，主要由 kubelet、kube-proxy、runtime 三个模块绍成
8.Image Registry:镜像仓库，比如：Ducker HUB 或 Docker 私服