---
title: openstack-helm安装
date: 2019-07-29 20:17:41
tags:
---
## 1. Helm安装
#### 在连上外网的情况下：
```
$ curl https://raw.githubusercontent.com/helm/helm/master/scripts/get > get_helm.sh
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```

#### 无法连上外网：
下载helm-v2.13.1-linux-amd64.tar.gz，解压并将helm复制到/usr/local/bin/
```
helm init --upgrade --tiller-image registry.cn-hangzhou.aliyuncs.com/google_containers/tiller:v2.13.1 --stable-repo-url https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
```

#### 给tiller在k8s中授权：
```
kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
```

#### 安装tiller：
```
yum install socat（k8s的所有节点安装）
helm init --upgrade
```

#### 看到helm和tiller两个都在运行：
```
helm version
```

## 2. 建立helm的openstack-charts
#### 手动建立ClusterRoleBindings：
由于使用kubernetes rbac，而目前openstack helm有bug，不正确建立服务帐户的clusterRolebindings，因此要手动建立。
```
kubectl create -f <(cat <<EOF
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: ceph-sa-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - apiGroup: rbac.authorization.k8s.io
    kind: User
    name: system:serviceaccount:ceph:default
EOF
)
```

```
kubectl create -f <(cat <<EOF
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: openstack-sa-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - apiGroup: rbac.authorization.k8s.io
    kind: User
    name: system:serviceaccount:openstack:default
EOF
)
```
将subject绑定到role；如上述代码，roleref是cluster-admin，一个默认admin角色，将用户User(:system:serviceaccount:ceph:default)绑定为此种角色。

#### 添加helm的本地repo：
```
helm serve &
helm repo add local http://localhost:8879/charts
```

#### 从github获取charts的源码
```
git clone https://github.com/openstack/openstack-helm-infra.git
git clone https://github.com/openstack/openstack-helm.git
```

#### charts的make：
cd 两个目录，make即可；
  helm search可以看到很多local/下面的charts。

## 部署coreDNS
#### 需要编辑5个yaml文件（[参考](https://blog.51cto.com/ylw6006/2108426)）：

```
coredns-sa.yaml
coredns-rbac.yaml
coredns-configmap.yaml
coredns-deployment.yaml
coredns-service.yaml
```

#### 上面五个文件都创建
```
kubectl create -f .
```

#### Debug：
  在coredns-deployment.yaml中，有ImagePullPolicy这个标记，有三个参数：Always 、Never 、IfNotPresent
+ Always：不管镜像是否存在都会进行一次拉取。
+ Never：不管镜像是否存在都不会进行拉取
+ IfNotPresent：只有镜像不存在时，才会进行镜像拉取。
  一般默认为IfNotPresent，但:latest标签的镜像默认为Always。因为我们不连外网，所以必须修改为IfNotPresent

## 标记nodes
#### 全部标记
```
kubectl label nodes ceph-osd=enabled --all
kubectl label nodes ceph-mds=enabled --all
kubectl label nodes ceph-mgr=enabled --all
kubectl label nodes openvswitch=enabled --all
kubectl label nodes openstack-compute-node=enabled --all
```

#### 如果需要删除不需要标记的：
```
kubectl label node x.x.x.x openvswitch-
```

#### 控制节点标记一个网络性能好的：
```
kubectl label node 10.141.209.214 openstack-control-plane=enabled
```

#### Mon和rgw分别选择3个点和2个点部署
```
kubectl label node x.x.x.x ceph-mon=enabled
kubectl label node x.x.x.x ceph-rgw=enabled
```

#### 查看 label：
```
单个node：kubectl label node 10.141.209.214 --list
所有node：kubectl get nodes --show-labels=true
```

#### 取消master节点的taint（无法建立pod）
```
kubectl taint nodes --all node-role.kubernetes.io/master-
```

## 安装ceph
#### Ceph的安装需要四个helm的chart(ip地址为物理机地址)
```
ceph-mon
helm install --namespace=ceph local/ceph-mon --name=ceph-mon \
  --set endpoints.identity.namespace=openstack \
  --set endpoints.object_store.namespace=ceph \
  --set endpoints.ceph_mon.namespace=ceph \
  --set ceph.rgw_keystone_auth=true \
  --set network.public=172.16.2.0/24 \
  --set network.cluster=172.16.2.0/24 \
  --set deployment.storage_secrets=true \
  --set deployment.ceph=true \
  --set deployment.rbd_provisioner=true \
  --set deployment.client_secrets=false \
  --set deployment.rgw_keystone_user_and_endpoints=false \
  --set bootstrap.enabled=true
```

```
Ceph-osd
helm install --namespace=ceph local/ceph-osd --name=ceph-osd \
  --set endpoints.identity.namespace=openstack \
  --set endpoints.object_store.namespace=ceph \
  --set endpoints.ceph_mon.namespace=ceph \
  --set ceph.rgw_keystone_auth=true \
  --set network.public=172.16.2.0/24 \
  --set network.cluster=172.16.2.0/24 \
  --set deployment.storage_secrets=true \
  --set deployment.ceph=true \
  --set deployment.rbd_provisioner=true \
  --set deployment.client_secrets=false \
  --set deployment.rgw_keystone_user_and_endpoints=false \
  --set bootstrap.enabled=true
```

```
cd openstack
./tools/deployment/multinode/020-ingress.sh
```

```
Ceph-client
helm install --namespace=ceph local/ceph-client --name=ceph-client \
  --set endpoints.identity.namespace=openstack \
  --set endpoints.object_store.namespace=ceph \
  --set endpoints.ceph_mon.namespace=ceph \
  --set ceph.rgw_keystone_auth=true \
  --set network.public=172.16.2.0/24 \
  --set network.cluster=172.16.2.0/24 \
  --set deployment.storage_secrets=true \
  --set deployment.ceph=true \
  --set deployment.rbd_provisioner=true \
  --set deployment.client_secrets=false \
  --set deployment.rgw_keystone_user_and_endpoints=false \
  --set bootstrap.enabled=true
```

```
Ceph-provisioners
helm install --namespace=ceph local/ceph-provisioners --name=ceph-provisioners \
  --set endpoints.identity.namespace=openstack \
  --set endpoints.object_store.namespace=ceph \
  --set endpoints.ceph_mon.namespace=ceph \
  --set ceph.rgw_keystone_auth=true \
  --set network.public=172.16.2.0/24 \
  --set network.cluster=172.16.2.0/24 \
  --set deployment.storage_secrets=true \
  --set deployment.ceph=true \
  --set deployment.rbd_provisioner=true \
  --set deployment.client_secrets=false \
  --set deployment.rgw_keystone_user_and_endpoints=false \
  --set bootstrap.enabled=true
```

#### 上述四个chart按照顺序创建：
+ ceph-mon
+ ceph-osd
+ Ceph-client
+ Ceph-provisioners

#### Debug：
+ Ceph的启动需要rbd的内核模块
  modprobe rbd
+ Ceph的残余内容，key等没有删除干净导致报错
  所有node节点的/var/lib/openstack-helm/ceph 删除干净

## 安装openstack（mariadb+rabbitmq基础服务）
```
cd openstack-helm
./tools/deployment/developer/ceph/045-ceph-ns-activate.sh(修改文件中的ip)
./tools/deployment/developer/ceph/050-mariadb.sh
./tools/deployment/developer/ceph/060-rabbitmq.sh
```
#### Debug：
+ 无法挂载
   + 1.控制节点yum update并yum install ceph-common
   + 2.挂载找不到mon地址
       在物理机的/etc/hosts中添加ceph.mon 的地址
     >如：cat /etc/hosts
      10.141.209.201 ceph-mon.ceph.svc.cluster.local

+ Rabbitmq等待时间过短导致启动失败： epmd error for host : (non-existing domain)
   + 在openstack-helm-infra/rabbitmq/templates/service-discover中修改：
     metadata中：
     >annotations:
       service.alpha.kubernetes.io/tolerate-unready-endpoints: "true"
     >spec中：
       publishNotReadyAddresses: true

+ Rabbitmq崩溃：
  报错同上，但是原因其实是dns无法解析，修改dns的configmap，删除proxy一行。
+ Rabbitmq崩溃：
  报错同上，但是原因其实是flannel网络出现问题，因为时间不同步导致renew lease时崩溃。

## 安装openstack（openvswitch等准备服务)
```
cd openstack-helm-infra:
helm install --name=memcached ./memcached --namespace=openstack
helm install --name=etcd-rabbitmq ./etcd --namespace=openstack
helm install --name=ingress ./ingress --namespace=openstack
helm install --name=libvirt ./libvirt --namespace=openstack
Libvirt启动报错ERROR: libvirtd daemon already running on host
解决方法： systemctl stop libvirtd  然后systemctl disable libvirtd关闭物理机的libvirtd服务
helm install --name=openvswitch ./openvswitch --namespace=openstack
```

## 安装openstack（keystone等主要服务）
```
cd openstack-helm
./tools/deployment/multinode/080-keystone.sh
./tools/deployment/multinode/090-ceph-radosgateway.sh（修改文件中的ip ）
./tools/deployment/multinode/100-glance.sh
  修改glance使用ceph rbd，在openstack-helm/glance/values.yaml第21行，修改为storage: rbd
  由于100-glance.sh文件中也有对storage的配置，必须删除 tee /tmp/glance.yaml 文件中关于覆盖values文件storage的GLANCE_BACKEND那两行。
./tools/deployment/developer/ceph/090-heat.sh
./tools/deployment/developer/ceph/130-cinder.sh
./tools/deployment/developer/ceph/100-h orizon.sh
```


/tools/deployment/developer/ceph/160-compute-kit.sh
160-compute-kit.sh文件的修改，需要删除一些内容
Nova的配置，修改nova/values.yaml文件：
Nova使用ceph rbd做后端，第1497行改为images_type: rbd


在nova的values文件中，第203行，将30680对应的port从false改为true


为了方便调试，将nova的logs改为dubug模式，在第1531行， level: DEBUG


Vnc本身的网址是k8s内部地址，为了外部可用，在第1457行，我们添加如下字段：
   novncproxy_base_url: http://10.190.2.21:30680/vnc_auto.html   其中，IP地址是k8s集群某个主机的地址。



ceph的用于nova的pool vms可能需要自己手动创建
```
ceph osd pool create vms 0
ceph osd pool ls
ceph osd pool get-quota vms
ceph osd pool application enable vms rbd
ceph osd pool application get vms
```


Neutron的配置
配置物理网卡，在neutron/values.yaml文件中，进行如下修改（有可用网段，用vlan作为外网的时候）：
network. auto_bridge_add  添加 br-physnet1: ens7f1



conf. ml2_conf. plugins 取消掉队vlan的注释，并添加正确的external tag范围


conf. openvswitch_agent. ovs的bridge_mappings，修改external对应的网卡

Neutron服务报错：
It may be the case that bridge and/or br_netfilter kernel modules are not loaded.
解决方法：重新编译centos的内核，需要版本新一点，我使用的是3.10.957的内核。


(非必要）如果没有外网资源需要使用网桥：
在有l3-agent的node节点，建立gateway.sh；文件内容如下，并运行（没有可用网段，只能使用自定义的外网的时候）
```
#!/bin/bash
set -xe

# Assign IP address to br-ex
OSH_BR_EX_ADDR="172.24.4.1/24"
OSH_EXT_SUBNET="172.24.4.0/24"
sudo ip addr add ${OSH_BR_EX_ADDR} dev br-ex
sudo ip link set br-ex up

# NOTE(portdirect): With Docker >= 1.13.1 the default FORWARD chain policy is
# configured to DROP, for the l3 agent to function as expected and for
# VMs to reach the outside world correctly this needs to be set to ACCEPT.
sudo iptables -P FORWARD ACCEPT

# Setup masquerading on default route dev to public subnet
DEFAULT_ROUTE_DEV="$(sudo ip -4 route list 0/0 | awk '{ print $5; exit }')"
#DEFAULT_ROUTE_DEV="em2"
sudo iptables -t nat -A POSTROUTING -o ${DEFAULT_ROUTE_DEV} -s ${OSH_EXT_SUBNET} -j MASQUERADE
sleep 1
```

## 使用openstack
设置externet（在客户端或者dashboard）
	创建外部网络：

使用openstack的虚拟机
首先安全组需要设置，一般加入以下四个规则



创建实例时创建ssh的key，并下载，比如ssh-key.pem
创建实例选择配置驱动
因为ssh-key.pem文件的权限为644，无法登陆，用chmod将权限改为500
使用ssh -i ssh-key.pem ubuntu@10.190.8.107 即可登陆

在浏览器中访问集群中任一节点的ip:31000；

主机管理如下：可以统计所有的计算节点

其他debug：
Nova-compute服务出错，表现为dashboard虚拟机管理器这个节点：
Log报错如图
解决方法：自己mkdir 相应instances，touch一个disk.config文件，即可。
