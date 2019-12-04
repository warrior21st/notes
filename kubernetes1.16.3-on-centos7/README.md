# Deploy kubernetes v1.16.3 on CentOS7

### kubernetes 核心服务(几种部署方式均是使机器提供这几个服务):
- kube-apiserver
- kube-controller-manager
- kube-scheduler
### kubernetes 依赖服务
- etcd(一个key-value式数据库)

### kubernetes 运行时组件
- kubelet
- kube-proxy
- container runtime

#### 安装docker-ce-17.03.2
    sudo yum install yum-utils device-mapper-persistent-data lvm2
    sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    sudo yum install --setopt=obsoletes=0 \
    docker-ce-17.03.2.ce-1.el7.centos.x86_64 \
    docker-ce-selinux-17.03.2.ce-1.el7.centos.noarch
#### 关闭swap
	swapoff -a
	vi /etc/fstab 注释掉swap那一行
#### 创建bin目录
	mkdir /k8s
	mkdir /k8s/bin
	chmod -R 755 /k8s/bin
#### 下载etcd并复制到bin
	cd /k8s
	wget https://github.com/etcd-io/etcd/releases/download/v3.3.17/etcd-v3.3.17-linux-amd64.tar.gz
	tar -zxf etcd-v3.3.17-linux-amd64.tar.gz
	cp ./etcd-v3.3.17-linux-amd64/etcd /k8s/bin/etcd
#### 为etcd增加执行权限
	chmod 755 /k8s/bin/etcd
#### 创建etcd运行目录
	mkdir /var/lib/etcd
#### 创建etcd服务并启用(etcd.service内容在后面)
	vi /etc/systemd/system/etcd.service
	systemctl enable etcd
	systemctl start etcd
		
#### 下载k8s server文件并复制到bin
	cd /k8s
	wget https://dl.k8s.io/v1.16.3/kubernetes-server-linux-amd64.tar.gz
	tar -zxf kubernetes-server-linux-amd64.tar.gz
	cp ./kubernetes-server-linux-amd64/kubernetes/server/bin/kube-apiserver /k8s/bin/kube-apiserver
	cp ./kubernetes-server-linux-amd64/kubernetes/server/bin/kube-controller-manager /k8s/bin/kube-controller-manager
	cp ./kubernetes-server-linux-amd64/kubernetes/server/bin/kube-scheduler /k8s/bin/kube-scheduler
	cp ./kubernetes-server-linux-amd64/kubernetes/server/bin/kubelet /k8s/bin/kubelet
	cp ./kubernetes-server-linux-amd64/kubernetes/server/bin/kubectl /k8s/bin/kubectl	
### 部署服务
- 对于master，需要运行etcd、kube-apiserver、kube-controller-manager、kube-scheduler，注意配置服务依赖。
- 对于node，需要运行kubelet、docker
### master
##### etcd.service
    [Unit]	
	Description=etcd	
	
	[Service]
	Type=notify	
	WorkingDirectory=/var/lib/etcd	
	ExecStart=/usr/bin/etcd --listen-client-urls=http://127.0.0.1:2379 --advertise-client-urls=http://127.0.0.1:2379
	SyslogIdentifier=etcd-k8s	
	User=root		
	Restart=always
	LimitNOFILE=65536	
	RestartSec=10
	
	[Install]	
	WantedBy=multi-user.target
##### kube-apiserver.service
    [Unit]
	Description=Kubernetes API Server
	Documentation=https://kubernetes.io/docs/concepts/overview/components/#kube-apiserver https://kubernetes.io/docs/reference/generated/kube-apiserver/
	After=network.target
	After=etcd.service
	
	[Service]
	Environment=KUBE_LOGTOSTDERR="--logtostderr=true"
	Environment=KUBE_LOG_LEVEL="--v=0"
	Environment=KUBE_ALLOW_PRIV="--allow-privileged=false"
	Environment=KUBE_MASTER="--master=http://192.168.1.100:8080"
	Environment=KUBE_API_ADDRESS="--insecure-bind-address=0.0.0.0"
	Environment=KUBE_API_PORT="--port=8080"
	Environment=KUBELET_PORT="--kubelet-port=10250"
	Environment=KUBE_ETCD_SERVERS="--etcd-servers=http://127.0.0.1:2379"
	Environment=KUBE_SERVICE_ADDRESSES="--service-cluster-ip-range=10.254.0.0/16"
	Environment=KUBE_ADMISSION_CONTROL="--admission-control=NamespaceLifecycle,LimitRanger,SecurityContextDeny,ServiceAccount,ResourceQuota"
	
	
	# Add your own!
	Environment=OTHER_ARGS=""
	
	User=root
	ExecStart=/usr/bin/kube-apiserver \
		    $KUBE_LOGTOSTDERR \
		    $KUBE_LOG_LEVEL \
		    $KUBE_ETCD_SERVERS \
		    $KUBE_API_ADDRESS \
		    $KUBE_API_PORT \
		    $KUBELET_PORT \
		    $KUBE_ALLOW_PRIV \
		    $KUBE_SERVICE_ADDRESSES \
		    $KUBE_ADMISSION_CONTROL \
		    $OTHER_ARGS
	Restart=on-failure
	Type=notify
	LimitNOFILE=65536
	
	[Install]
	WantedBy=multi-user.target
##### kube-controller-manager.service
	[Unit]
	Description=Kubernetes Controller Manager
	Documentation=https://kubernetes.io/docs/concepts/overview/components/#kube-controller-manager https://kubernetes.io/docs/reference/generated/kube-controller-manager/
	
	[Service]
	Environment=KUBE_LOGTOSTDERR="--logtostderr=true"
	Environment=KUBE_LOG_LEVEL="--v=0"
	Environment=KUBE_MASTER="--master=http://192.168.1.100:8080"
	
	# Add your own!
	Environment=OTHER_ARGS=""
	
	User=root
	ExecStart=/usr/bin/kube-controller-manager \
		    $KUBE_LOGTOSTDERR \
		    $KUBE_LOG_LEVEL \
		    $KUBE_MASTER \
		    $OTHER_ARGS
	Restart=on-failure
	LimitNOFILE=65536
	
	[Install]
	WantedBy=multi-user.target
##### kube-schedulers.service
    [Unit]
	Description=Kubernetes Scheduler Plugin
	Documentation=https://kubernetes.io/docs/concepts/overview/components/#kube-scheduler https://kubernetes.io/docs/reference/generated/kube-scheduler/
	
	[Service]
	Environment=KUBE_LOGTOSTDERR="--logtostderr=true"
	Environment=KUBE_LOG_LEVEL="--v=0"
	Environment=KUBE_MASTER="--master=http://192.168.1.100:8080"
	
	# Add your own!
	Environment=OTHER_ARGS=""
	
	User=root
	ExecStart=/usr/bin/kube-scheduler \
		    $KUBE_LOGTOSTDERR \
		    $KUBE_LOG_LEVEL \
		    $KUBE_MASTER \
		    $OTHER_ARGS
	Restart=on-failure
	LimitNOFILE=65536
	
	[Install]
	WantedBy=multi-user.target
### slave node
#### 创建kubelet运行目录
	mkdir /var/lib/kubelet
##### kubelet.service
	[Unit]
	Description=Kubernetes Kubelet Server
	Documentation=https://kubernetes.io/docs/concepts/overview/components/#kubelet https://kubernetes.io/docs/reference/generated/kubelet/
	After=docker.service
	Requires=docker.service
	
	[Service]
	User=root
	WorkingDirectory=/var/lib/kubelet
	ExecStart=/usr/bin/kubelet --logtostderr=true --v=0 --kubeconfig=/etc/kubernetes/kubelet.kubeconfig --port=10250 --hostname-override=192.168.1.101 --cgroup-driver=cgroupfs --fail-swap-on=true
	Restart=on-failure
	KillMode=process
	
	[Install]
	WantedBy=multi-user.target
##### kubelet.kubeconfig
	apiVersion: v1
	clusters:
	- cluster:
	    server: http://192.168.1.100:8080
	  name: kubernetes-systemd
	contexts:
	- context:
	    cluster: kubernetes-systemd
	    user: system:node:192.168.1.101
	  name: system:node:192.168.1.101@kubernetes-systemd
	current-context: system:node:192.168.1.101@kubernetes-systemd
	kind: Config
	preferences: {}
	users:
	- name: system:node:192.168.1.101

### dashboard部署
	docker pull k8s.gcr.io/kubernetes-dashboard-amd64:v1.10.1
	mkdir /var/lib/kubectl
	/k8s/bin/kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta6/aio/deploy/recommended.yaml
	/k8s/bin/kubectl proxy --address=0.0.0.0 --accept-hosts='^*$' 

### 参考文档
- https://kubernetes.io/docs
- https://github.com/kubernetes/kubernetes
- https://github.com/kubernetes/dashboard
- https://medium.com/containerum/4-ways-to-bootstrap-a-kubernetes-cluster-de0d5150a1e4
