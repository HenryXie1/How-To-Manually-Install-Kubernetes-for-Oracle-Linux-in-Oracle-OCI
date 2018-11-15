# How To Add Worker Node Into Old Version Kubernetes Manually
### Requirement:
 We have existing 1.10 Kubernetes cluster running  in Oracle OCI with Linux el7 . We installed follow the [oracle official doc](https://docs.oracle.com/cd/E52668_01/E88884/html/kubernetes_install_overview.html).
 
 Now as more work loads are coming, we plan to add a new worker node into the existing cluster. But we would not like to upgrade K8S to 1.11 or 1.12.  

 The "yum install kubeadm"  from  yum.oracle.com , it installes the latest the kubeadm which is not compatible with our existing 1.10 cluster.  So we need to add a worker node manually to the old cluster



### Solution:
* Do the preparation steps. Refer [my previous install K8S manually doc](https://github.com/HenryXie1/How-To-Manually-Install-Kubernetes-for-Oracle-Linux-in-Oracle-OCI#preparationall-kubernetes-nodes)
* We need to download rpm manually from yum.oracle.com website. ( not from yum command)
 * Go to [Oracle YUM Website](https://yum.oracle.com/repo/OracleLinux/OL7/developer/x86_64/index.html).
 * Search for old version of K8S files. ie  kubeadm-1.10.5-2.0.2.el7.x86_64.rpm   kubectl-1.10.5-2.0.2.el7.x86_64.rpm  kubelet-1.10.5-2.0.2.el7.x86_64.rpm
 * Download and upload the 3 rpms into the new worker node
 * yum install kubernetes-cni
 * yum install socat
 * rpm -i kubeadm-1.10.5-2.0.2.el7.x86_64.rpm   kubectl-1.10.5-2.0.2.el7.x86_64.rpm  kubelet-1.10.5-2.0.2.el7.x86_64.rpm
* Use kubeadm-setup.sh to join the new node into the cluster
 * export KUBE_REPO_PREFIX=container-registry.oracle.com/kubernetes_developer && kubeadm-setup.sh join 100.106.146.3:6443 --token ******* --discovery-token-ca-cert-hash sha256:*********
 * To get or generate token and ca cert hash. Please refer [my previous install K8S manually doc](https://github.com/HenryXie1/How-To-Manually-Install-Kubernetes-for-Oracle-Linux-in-Oracle-OCI/blob/master/README.md#setting-worker-node) 
