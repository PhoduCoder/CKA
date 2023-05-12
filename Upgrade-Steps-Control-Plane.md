

**Drain the Control Plane Node**

cloud_user@k8s-control:~$ kubectl drain k8s-control --ignore-daemonsets
node/k8s-control already cordoned
WARNING: ignoring DaemonSet-managed Pods: kube-system/calico-node-89hpp, kube-system/kube-proxy-zghmd
evicting pod kube-system/coredns-6d4b75cb6d-nnc5f
evicting pod kube-system/calico-kube-controllers-55fc758c88-c6j5g
evicting pod kube-system/coredns-6d4b75cb6d-hmgff
pod/calico-kube-controllers-55fc758c88-c6j5g evicted
pod/coredns-6d4b75cb6d-nnc5f evicted
pod/coredns-6d4b75cb6d-hmgff evicted
node/k8s-control drained


**Upgrade the kubeadm binary

cloud_user@k8s-control:~$ kubeadm version 
kubeadm version: &version.Info{Major:"1", Minor:"24", GitVersion:"v1.24.0", GitCommit:"4ce5a8954017644c5420bae81d72b09b735c21f0", GitTreeState:"clean", BuildDate:"2022-05-03T13:44:24Z", GoVersion:"go1.18.1", Compiler:"gc", Platform:"linux/amd64"}
cloud_user@k8s-control:~$ 

**Find the current version**

cloud_user@k8s-control:~$ apt-cache madison kubeadm | head
   kubeadm |  1.27.1-00 | https://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.27.0-00 | https://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.26.4-00 | https://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.26.3-00 | https://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.26.2-00 | https://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.26.1-00 | https://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.26.0-00 | https://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.25.9-00 | https://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.25.8-00 | https://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   kubeadm |  1.25.7-00 | https://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
cloud_user@k8s-control:~$ 
cloud_user@k8s-control:~$ 

**Now unhold the kubeadm, update the kubeadm and then again hold it** 

cloud_user@k8s-control:~$ sudo apt-mark unhold kubeadm &&  sudo apt-get update && sudo apt-get install -y kubeadm=1.25.9-00 &&  sudo apt-mark hold kubeadm

**Output**
kubeadm was already not hold.
Hit:1 http://us-west-1.ec2.archive.ubuntu.com/ubuntu focal InRelease
Hit:2 http://us-west-1.ec2.archive.ubuntu.com/ubuntu focal-updates InRelease                                                                         
Hit:3 http://us-west-1.ec2.archive.ubuntu.com/ubuntu focal-backports InRelease                                                                       
Hit:4 https://download.docker.com/linux/ubuntu focal InRelease                                                                                       
Hit:6 http://security.ubuntu.com/ubuntu focal-security InRelease                                                                                     
Hit:5 https://packages.cloud.google.com/apt kubernetes-xenial InRelease
Reading package lists... Done
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages will be upgraded:
  kubeadm
1 upgraded, 0 newly installed, 0 to remove and 25 not upgraded.
Need to get 9234 kB of archives.
After this operation, 545 kB disk space will be freed.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubeadm amd64 1.25.9-00 [9234 kB]
Fetched 9234 kB in 1s (9543 kB/s)  
(Reading database ... 195733 files and directories currently installed.)
Preparing to unpack .../kubeadm_1.25.9-00_amd64.deb ...
Unpacking kubeadm (1.25.9-00) over (1.24.0-00) ...
Setting up kubeadm (1.25.9-00) ...
kubeadm set on hold.
cloud_user@k8s-control:~$ 


**Confirm this by checking the kubeadm version**
cloud_user@k8s-control:~$ kubeadm version
kubeadm version: &version.Info{Major:"1", Minor:"25", GitVersion:"v1.25.9", GitCommit:"a1a87a0a2bcd605820920c6b0e618a8ab7d117d4", GitTreeState:"clean", BuildDate:"2023-04-12T12:15:07Z", GoVersion:"go1.19.8", Compiler:"gc", Platform:"linux/amd64"}
cloud_user@k8s-control:~$ 

**Now follow the kubeadm update plan and kubeadm update apply - much like terraform model**

cloud_user@k8s-control:~$ sudo kubeadm upgrade plan version v1.25.9
[upgrade/config] Making sure the configuration is correct:
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[preflight] Running pre-flight checks.
[upgrade] Running cluster health checks
[upgrade] Fetching available versions to upgrade to
[upgrade/versions] Cluster version: v1.24.0
[upgrade/versions] kubeadm version: v1.25.9
I0512 21:27:28.077539   68559 version.go:256] remote version is much newer: v1.27.1; falling back to: stable-1.25
[upgrade/versions] Target version: v1.25.9
[upgrade/versions] Latest version in the v1.24 series: v1.24.13
Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
COMPONENT   CURRENT       TARGET
kubelet     3 x v1.24.0   v1.24.13
Upgrade to the latest version in the v1.24 series:
COMPONENT                 CURRENT   TARGET
kube-apiserver            v1.24.0   v1.24.13
kube-controller-manager   v1.24.0   v1.24.13
kube-scheduler            v1.24.0   v1.24.13
kube-proxy                v1.24.0   v1.24.13
CoreDNS                   v1.8.6    v1.9.3
etcd                      3.5.3-0   3.5.6-0
You can now apply the upgrade by executing the following command:
        kubeadm upgrade apply v1.24.13
_____________________________________________________________________
Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
COMPONENT   CURRENT       TARGET
kubelet     3 x v1.24.0   v1.25.9
Upgrade to the latest stable version:

COMPONENT                 CURRENT   TARGET
kube-apiserver            v1.24.0   v1.25.9
kube-controller-manager   v1.24.0   v1.25.9
kube-scheduler            v1.24.0   v1.25.9
kube-proxy                v1.24.0   v1.25.9
CoreDNS                   v1.8.6    v1.9.3
etcd                      3.5.3-0   3.5.6-0

You can now apply the upgrade by executing the following command:
        kubeadm upgrade apply v1.25.9
_____________________________________________________________________

The table below shows the current state of component configs as understood by this version of kubeadm.
Configs that have a "yes" mark in the "MANUAL UPGRADE REQUIRED" column require manual config upgrade or
resetting to kubeadm defaults before a successful upgrade can be performed. The version to manually
upgrade to is denoted in the "PREFERRED VERSION" column.

API GROUP                 CURRENT VERSION   PREFERRED VERSION   MANUAL UPGRADE REQUIRED
kubeproxy.config.k8s.io   v1alpha1          v1alpha1            no
kubelet.config.k8s.io     v1beta1           v1beta1             no
_____________________________________________________________________

**Note that the above commands lists out all the changes that will be performed
** or things that will change once we upgrade the control plane

**Now apply the changes as indicated using the kubeadm upgrade apply v1.25.9 

cloud_user@k8s-control:~$ 
cloud_user@k8s-control:~$ sudo kubeadm upgrade apply v1.25.9

**Output**
[upgrade/config] Making sure the configuration is correct:
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[preflight] Running pre-flight checks.
[upgrade] Running cluster health checks
[upgrade/version] You have chosen to change the cluster version to "v1.25.9"
[upgrade/versions] Cluster version: v1.24.0
[upgrade/versions] kubeadm version: v1.25.9
[upgrade] Are you sure you want to proceed? [y/N]: y
[upgrade/prepull] Pulling images required for setting up a Kubernetes cluster
[upgrade/prepull] This might take a minute or two, depending on the speed of your internet connection
[upgrade/prepull] You can also perform this action in beforehand using 'kubeadm config images pull'
[upgrade/apply] Upgrading your Static Pod-hosted control plane to version "v1.25.9" (timeout: 5m0s)...
[upgrade/etcd] Upgrading to TLS for etcd
[upgrade/staticpods] Preparing for "etcd" upgrade
[upgrade/staticpods] Renewing etcd-server certificate
[upgrade/staticpods] Renewing etcd-peer certificate
[upgrade/staticpods] Renewing etcd-healthcheck-client certificate
[upgrade/staticpods] Moved new manifest to "/etc/kubernetes/manifests/etcd.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2023-05-12-21-30-11/etcd.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This might take a minute or longer depending on the component/version gap (timeout 5m0s)
[apiclient] Found 1 Pods for label selector component=etcd
[upgrade/staticpods] Component "etcd" upgraded successfully!
[upgrade/etcd] Waiting for etcd to become available
[upgrade/staticpods] Writing new Static Pod manifests to "/etc/kubernetes/tmp/kubeadm-upgraded-manifests1292035974"
[upgrade/staticpods] Preparing for "kube-apiserver" upgrade
[upgrade/staticpods] Renewing apiserver certificate
[upgrade/staticpods] Renewing apiserver-kubelet-client certificate
[upgrade/staticpods] Renewing front-proxy-client certificate
[upgrade/staticpods] Renewing apiserver-etcd-client certificate
[upgrade/staticpods] Moved new manifest to "/etc/kubernetes/manifests/kube-apiserver.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2023-05-12-21-30-11/kube-apiserver.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This might take a minute or longer depending on the component/version gap (timeout 5m0s)
[apiclient] Found 1 Pods for label selector component=kube-apiserver
[upgrade/staticpods] Component "kube-apiserver" upgraded successfully!
[upgrade/staticpods] Preparing for "kube-controller-manager" upgrade
[upgrade/staticpods] Renewing controller-manager.conf certificate
[upgrade/staticpods] Moved new manifest to "/etc/kubernetes/manifests/kube-controller-manager.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2023-05-12-21-30-11/kube-controller-manager.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This might take a minute or longer depending on the component/version gap (timeout 5m0s)
[apiclient] Found 1 Pods for label selector component=kube-controller-manager
[upgrade/staticpods] Component "kube-controller-manager" upgraded successfully!
[upgrade/staticpods] Preparing for "kube-scheduler" upgrade
[upgrade/staticpods] Renewing scheduler.conf certificate
[upgrade/staticpods] Moved new manifest to "/etc/kubernetes/manifests/kube-scheduler.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2023-05-12-21-30-11/kube-scheduler.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This might take a minute or longer depending on the component/version gap (timeout 5m0s)
[apiclient] Found 1 Pods for label selector component=kube-scheduler
[upgrade/staticpods] Component "kube-scheduler" upgraded successfully!
[upgrade/postupgrade] Removing the old taint &Taint{Key:node-role.kubernetes.io/master,Value:,Effect:NoSchedule,TimeAdded:<nil>,} from all control plane Nodes. After this step only the &Taint{Key:node-role.kubernetes.io/control-plane,Value:,Effect:NoSchedule,TimeAdded:<nil>,} taint will be present on control plane Nodes.
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config" in namespace kube-system with the configuration for the kubelets in the cluster
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy
[upgrade/successful] SUCCESS! Your cluster was upgraded to "v1.25.9". Enjoy!
[upgrade/kubelet] Now that your control plane is upgraded, please proceed with upgrading your kubelets if you haven't already done so.
cloud_user@k8s-control:~$ 
cloud_user@k8s-control:~$ 


** Now upgrade the kubelet and the kubectl process**

cloud_user@k8s-control:~$ sudo apt-get update && \
> sudo apt-get install -y --allow-change-held-packages kubelet=1.25.9-00 kubectl=1.25.9-00
Hit:1 http://us-west-1.ec2.archive.ubuntu.com/ubuntu focal InRelease
Get:2 http://us-west-1.ec2.archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]                                                
Get:3 http://us-west-1.ec2.archive.ubuntu.com/ubuntu focal-backports InRelease [108 kB]                                              
Get:4 https://download.docker.com/linux/ubuntu focal InRelease [57.7 kB]                                                             
Hit:6 http://security.ubuntu.com/ubuntu focal-security InRelease                                                                     
Hit:5 https://packages.cloud.google.com/apt kubernetes-xenial InRelease
Fetched 280 kB in 1s (302 kB/s)
Reading package lists... Done
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following held packages will be changed:
  kubectl kubelet
The following packages will be upgraded:
  kubectl kubelet
2 upgraded, 0 newly installed, 0 to remove and 24 not upgraded.
Need to get 29.0 MB of archives.
After this operation, 2750 kB disk space will be freed.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubectl amd64 1.25.9-00 [9512 kB]
Get:2 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubelet amd64 1.25.9-00 [19.5 MB]
Fetched 29.0 MB in 2s (11.9 MB/s)  
(Reading database ... 195733 files and directories currently installed.)
Preparing to unpack .../kubectl_1.25.9-00_amd64.deb ...
Unpacking kubectl (1.25.9-00) over (1.24.0-00) ...
Preparing to unpack .../kubelet_1.25.9-00_amd64.deb ...
Unpacking kubelet (1.25.9-00) over (1.24.0-00) ...
Setting up kubectl (1.25.9-00) ...
Setting up kubelet (1.25.9-00) ...
cloud_user@k8s-control:~$ 

**Finally uncordon the control plane node
kubectl uncordon k8s-control

**Check and confirm** 
   cloud_user@k8s-control:~$ kubectl version --short
Flag --short has been deprecated, and will be removed in the future. The --short output will become the default.
Client Version: v1.25.9
Kustomize Version: v4.5.7
Server Version: v1.25.9
cloud_user@k8s-control:~$ 
cloud_user@k8s-control:~$ kubectl get nodes -o=wide
NAME          STATUS   ROLES           AGE    VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
k8s-control   Ready    control-plane   122m   v1.25.9   172.31.43.117   <none>        Ubuntu 20.04.6 LTS   5.15.0-1034-aws   containerd://1.6.21
k8s-worker1   Ready    <none>          118m   v1.24.0   172.31.39.138   <none>        Ubuntu 20.04.6 LTS   5.15.0-1034-aws   containerd://1.6.21
k8s-worker2   Ready    <none>          118m   v1.24.0   172.31.46.229   <none>        Ubuntu 20.04.6 LTS   5.15.0-1034-aws   containerd://1.6.21
cloud_user@k8s-control:~$ 
cloud_user@k8s-control:~$ 



