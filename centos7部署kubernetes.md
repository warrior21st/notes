## kubernetes v1.9.11 External Dependencies

*   The supported etcd server version is **3.1.10**, as compared to 3.0.17 in v1.8 ([#49393](https://github.com/kubernetes/kubernetes/pull/49393),[ @hongchaodeng](https://github.com/hongchaodeng))
*   The validated docker versions are the same as for v1.8: **1.11.2 to 1.13.1 and 17.03.x**
*   The Go version was upgraded from go1.8.3 to **go1.9.2** ([#51375](https://github.com/kubernetes/kubernetes/pull/51375),[ @cblecker](https://github.com/cblecker))
    *   The minimum supported go version bumps to 1.9.1. ([#55301](https://github.com/kubernetes/kubernetes/pull/55301),[ @xiangpengzhao](https://github.com/xiangpengzhao))
    *   Kubernetes has been upgraded to go1.9.2 ([#55420](https://github.com/kubernetes/kubernetes/pull/55420),[ @cblecker](https://github.com/cblecker))
*   CNI was upgraded to **v0.6.0** ([#51250](https://github.com/kubernetes/kubernetes/pull/51250),[ @dixudx](https://github.com/dixudx))
*   The dashboard add-on has been updated to [v1.8.0](https://github.com/kubernetes/dashboard/releases/tag/v1.8.0). ([#53046](https://github.com/kubernetes/kubernetes/pull/53046), [@maciaszczykm](https://github.com/maciaszczykm))
*   Heapster has been updated to [v1.5.0](https://github.com/kubernetes/heapster/releases/tag/v1.5.0). ([#57046](https://github.com/kubernetes/kubernetes/pull/57046), [@piosz](https://github.com/piosz))
*   Cluster Autoscaler has been updated to [v1.1.0](https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.1.0). ([#56969](https://github.com/kubernetes/kubernetes/pull/56969), [@mwielgus](https://github.com/mwielgus))
*   Update kube-dns 1.14.7 ([#54443](https://github.com/kubernetes/kubernetes/pull/54443),[ @bowei](https://github.com/bowei))
*   Update influxdb to v1.3.3 and grafana to v4.4.3 ([#53319](https://github.com/kubernetes/kubernetes/pull/53319),[ @kairen](https://github.com/kairen))

## docker yum install 
    sudo yum install yum-utils device-mapper-persistent-data lvm2
    sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    sudo yum install --setopt=obsoletes=0 \
    docker-ce-17.03.2.ce-1.el7.centos.x86_64 \
    docker-ce-selinux-17.03.2.ce-1.el7.centos.noarch

## kubernetes 核心服务(几种部署方式均需要提供这几个服务):
- kube-apiserver
- kube-controller-manager
- kube-scheduler
- etcd

## kubernetes 运行时组件
- kubelet
- kube-proxy
- container runtime

### 部署方式1：使用systemd运行核心服务
- 对于master，需要运行etcd、kube-apiserver、kube-controller-manager、kube-scheduler，注意配置服务依赖。
- 对于node，需要运行kubelet、docker
- [v1.9.11版kube-apiserver、kube-controller-manager、kube-scheduler、kubelet、kubectl、kub-proxy下载地址： https://storage.googleapis.com/kubernetes-release/release/v1.9.11/kubernetes-server-linux-amd64.tar.gz
- v1.9.11版验证过的etcd(release地址：https://github.com/etcd-io/etcd/releases/tag/v3.1.1)，tar下载地址：https://github-production-release-asset-2e65be.s3.amazonaws.com/11225014/a7f9d1d0-6888-11e7-8b3f-98f6f4c2ec78?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20191119%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20191119T072518Z&X-Amz-Expires=300&X-Amz-Signature=2d6b71f0cbd5c7482fbc07d51ecd7ed05ee20b84eca31b6843b6c59c9c898f06&X-Amz-SignedHeaders=host&actor_id=13977767&response-content-disposition=attachment%3B%20filename%3Detcd-v3.1.10-linux-amd64.tar.gz&response-content-type=application%2Foctet-stream

### master services
##### etcd.service
    todo
##### kube-apiserver.service
    todo
##### kube-controller-manager.service
    todo
##### kube-schedulers.service
    todo
### node services
##### kubelet.service
    todo
### 部署方式2: 使用docker容器运行服务

参照
- https://medium.com/containerum/4-ways-to-bootstrap-a-kubernetes-cluster-de0d5150a1e4