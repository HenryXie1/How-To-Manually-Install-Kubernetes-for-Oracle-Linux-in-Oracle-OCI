# How To Manually Install Kubernetes for Oracle Linux in Oracle OCI

###  Requirement:

To mannually install Kubernetes in Oracle OCI via Oracle Released Docker and Kubernetes version from [Oracle Container Registry][1] Refer [official doc ][2]

###  Preparation(All Kubernetes Nodes):

* Assume Master nodes and worker nodes are in the same VCN. Otherwise we need to add access rules in OCI policy to let nodes communicate with each other. details in [doc][3]
* #yum-config-manager --enable ol7_addons
* #yum install docker-engine
* #systemctl enable docker
* #systemctl start docker
* #docker login container-registry.oracle.com/kubernetes_developer .  We can get free account from [Oracle Container Registry][1]
* #iptables -P FORWARD ACCEPT
* #firewall-cmd --add-masquerade --permanent
* #firewall-cmd --add-port=10250/tcp --permanent
* #firewall-cmd --add-port=8472/udp --permanent
* On Master Node only:  # firewall-cmd --add-port=6443/tcp --permanent
* #systemctl restart firewalld
* #/usr/sbin/setenforce 0
* #vim /etc/selinux/config  and set SELINUX=permissive

###  Setting Master Node

* #yum install kubeadm
* #kubeadm-setup.sh up

> _.......  
Please wait ...  
\- 75% completed  
Waiting for the control plane to become ready ...  
................  
100% completed  
.......  
[===> PLEASE DO THE FOLLOWING STEPS BELOW: <===]  
Your Kubernetes master has initialized successfully!  
To start using your cluster, you need to run the following as a regular user:  
  mkdir -p $HOME/.kube  
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config  
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
>
> You can now join any number of machines by running the following on each node  
as root:  
  export KUBE_REPO_PREFIX=container-registry.oracle.com/kubernetes_developer && kubeadm-setup.sh join 100.106.146.3:6443 --token ********** --discovery-token-ca-cert-hash sha256:****************
>
> _

* groupadd k8sgroup; useradd -G k8ggroup k8suser ;
* visudo --- to add "k8suser ALL=(ALL)       ALL" below "root ALL=(ALL)       ALL"
* su - k8suser
* mkdir -p $HOME/.kub
* sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
* sudo chown $(id -u):$(id -g) $HOME/.kube/config
* echo 'export KUBECONFIG=$HOME/.kube/config' >> $HOME/.bashrc
* use this command to verify: $ kubectl get pods -n kube-system, output would be like

> [_k8suser@instance-cas-mt2 .kube]$ kubectl get pods -n kube-system  
NAME                                       READY     STATUS    RESTARTS   AGE  
etcd-instance-cas-mt2                      1/1       Running   0          2h  
kube-apiserver-instance-cas-mt2            1/1       Running   1          2h  
kube-controller-manager-instance-cas-mt2   1/1       Running   0          2h  
kube-dns-5c57c4787c-xzsgz                  3/3       Running   0          2h  
kube-flannel-ds-87xb9                      1/1       Running   0          2h  
kube-proxy-mwn46                           1/1       Running   0          2h  
kube-scheduler-instance-cas-mt2            1/1       Running   0          2h  
kubernetes-dashboard-7df769d745-m4mgx      1/1       Running   0          2h_



###   Setting Worker Node:

* #yum install kubeadm
* export KUBE_REPO_PREFIX=container-registry.oracle.com/kubernetes_developer && kubeadm-setup.sh join 100.106.146.3:6443 --token ******* --discovery-token-ca-cert-hash sha256:*********

> _Starting to initialize worker node ..._  

>
>> _Checking if env is ready ...  
Checking whether docker can pull busybox image ...  
Checking access to container-registry.oracle.com/kubernetes_developer ...  
Trying to pull repository container-registry.oracle.com/kubernetes_developer/kube-proxy-amd64 ...  
v1.10.5: Pulling from container-registry.oracle.com/kubernetes_developer/kube-proxy-amd64  
Digest: sha256:*****  
Status: Image is up to date for container-registry.oracle.com/ku                                                                                          bernetes_developer/kube-proxy-amd64:v1.10.5  
Checking whether docker can run container ...  
Checking firewalld settings ...  
Checking iptables default rule ...  
Checking br_netfilter module ...  
Checking sysctl variables ...  
Enabling kubelet ...  
Created symlink from /etc/systemd/system/multi-user.target.wants      /kubelet.service to /etc/systemd/system/kubelet.service.  
Check successful, ready to run 'join' command ...  
[preflight] Running pre-flight checks.  
[validation] WARNING: kubeadm doesn't fully support multiple API Servers yet  
[discovery] Trying to connect to API Server "100.106.146.3:6443"  
[discovery] Trying to connect to API Server "100.106.146.3:6443"  
[discovery] Created cluster-info discovery client, requesting info from "https://100.106.146.3:6443"  
[discovery] Created cluster-info discovery client, requesting info from "https://100.106.146.3:6443"  
[discovery] Requesting info from "https://100.106.146.3:6443" again to validate TLS against the pinned public key  
[discovery] Requesting info from "https://100.106.146.3:6443" again to validate TLS against the pinned public key  
[discovery] Cluster info signature and contents are valid and TLS certificate validates against pinned roots, will use API Server "100.106.146.3:6443"  
[discovery] Successfully established connection with API Server     "100.106.146.3:6443"  
[discovery] Cluster info signature and contents are valid and TLS certificate validates against pinned roots, will use API Server "100.106.146.3:6443"  
[discovery] Successfully established connection with API Server     "100.106.146.3:6443"  
This node has joined the cluster:  
* Certificate signing request was sent to master and a response   was received.  
* The Kubelet was informed of the new secure connection details.  
Run 'kubectl get nodes' on the master to see this node join the   cluster._

* kubectl get nodes

_NAME               STATUS    ROLES     AGE       VERSION_  
_instance-test-db2   Ready         5m        v1.10.5+2.0.2.el7_  
_instance-test-mt2   Ready     master    3h        v1.10.5+2.0.2.el7_



[1]: https://container-registry.oracle.com/
[2]: https://docs.oracle.com/cd/E52668_01/E88884/html/pref.html
[3]: https://docs.oracle.com/cd/E52668_01/E88884/html/requirements-bmc.html
