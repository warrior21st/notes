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