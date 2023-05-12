
## From the control plane
## Drain the worker node

cloud_user@k8s-control:~$ kubectl drain k8s-worker-1 --ignore-daemonsets

Error from server (NotFound): nodes "k8s-worker-1" not found
cloud_user@k8s-control:~$ kubectl drain k8s-worker1 --ignore-daemonsets
node/k8s-worker1 cordoned
Warning: ignoring DaemonSet-managed Pods: kube-system/calico-node-sg62f, kube-system/kube-proxy-dft6p
evicting pod kube-system/coredns-565d847f94-k6qgt
evicting pod default/test-764c85dd84-l7skq
evicting pod default/test-764c85dd84-x5qrq

pod/test-764c85dd84-l7skq evicted
pod/test-764c85dd84-x5qrq evicted
pod/coredns-565d847f94-k6qgt evicted
node/k8s-worker1 drained
cloud_user@k8s-control:~$ 

## Now update the kubeadm package

cloud_user@k8s-worker1:~$ sudo apt-get update && \
> sudo apt-get install -y --allow-change-held-packages kubeadm=1.25.9-00 
[sudo] password for cloud_user: 

Hit:1 http://us-west-1.ec2.archive.ubuntu.com/ubuntu focal InRelease
Get:2 http://us-west-1.ec2.archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]                                                
Get:3 http://us-west-1.ec2.archive.ubuntu.com/ubuntu focal-backports InRelease [108 kB]                                              
Get:4 https://download.docker.com/linux/ubuntu focal InRelease [57.7 kB]                                                                             
Get:5 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]                                                            
Get:6 https://packages.cloud.google.com/apt kubernetes-xenial InRelease [8993 B]
Get:7 http://us-west-1.ec2.archive.ubuntu.com/ubuntu focal-updates/main amd64 DEP-11 Metadata [274 kB]
Get:8 http://us-west-1.ec2.archive.ubuntu.com/ubuntu focal-updates/universe amd64 DEP-11 Metadata [409 kB]
Get:9 http://us-west-1.ec2.archive.ubuntu.com/ubuntu focal-updates/multiverse amd64 DEP-11 Metadata [944 B]
Get:10 http://us-west-1.ec2.archive.ubuntu.com/ubuntu focal-backports/main amd64 DEP-11 Metadata [7996 B]
Get:11 http://us-west-1.ec2.archive.ubuntu.com/ubuntu focal-backports/universe amd64 DEP-11 Metadata [30.5 kB]
Get:12 http://security.ubuntu.com/ubuntu focal-security/main amd64 DEP-11 Metadata [59.8 kB]
Get:13 http://security.ubuntu.com/ubuntu focal-security/universe amd64 DEP-11 Metadata [95.6 kB]
Get:14 http://security.ubuntu.com/ubuntu focal-security/multiverse amd64 DEP-11 Metadata [940 B]
Fetched 1282 kB in 2s (629 kB/s)                                       
Reading package lists... Done
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following held packages will be changed:
  kubeadm
The following packages will be upgraded:
  kubeadm
1 upgraded, 0 newly installed, 0 to remove and 25 not upgraded.
Need to get 9234 kB of archives.
After this operation, 545 kB disk space will be freed.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubeadm amd64 1.25.9-00 [9234 kB]
Fetched 9234 kB in 1s (14.9 MB/s)
(Reading database ... 195733 files and directories currently installed.)
Preparing to unpack .../kubeadm_1.25.9-00_amd64.deb ...
Unpacking kubeadm (1.25.9-00) over (1.24.0-00) ...
Setting up kubeadm (1.25.9-00) ...
cloud_user@k8s-worker1:~$ 
cloud_user@k8s-worker1:~$ 

## Once the package is updated, use the kubeadm upgrade node to update the worker node

cloud_user@k8s-worker1:~$ kubeadm version
kubeadm version: &version.Info{Major:"1", Minor:"25", GitVersion:"v1.25.9", GitCommit:"a1a87a0a2bcd605820920c6b0e618a8ab7d117d4", GitTreeState:"clean", BuildDate:"2023-04-12T12:15:07Z", GoVersion:"go1.19.8", Compiler:"gc", Platform:"linux/amd64"}
cloud_user@k8s-worker1:~$ 
cloud_user@k8s-worker1:~$ 
cloud_user@k8s-worker1:~$ 
cloud_user@k8s-worker1:~$ kubeadm upgrade node
couldn't create a Kubernetes client from file "/etc/kubernetes/kubelet.conf": failed to load admin kubeconfig: open /etc/kubernetes/kubelet.conf: permission denied
To see the stack trace of this error execute with --v=5 or higher
cloud_user@k8s-worker1:~$ 
cloud_user@k8s-worker1:~$ sudo kubeadm upgrade node
[upgrade] Reading configuration from the cluster...
[upgrade] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[preflight] Running pre-flight checks
[preflight] Skipping prepull. Not a control plane node.
[upgrade] Skipping phase. Not a control plane node.
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[upgrade] The configuration for this node was successfully updated!
[upgrade] Now you should go ahead and upgrade the kubelet package using your package manager.
cloud_user@k8s-worker1:~$ 
cloud_user@k8s-worker1:~$ 
cloud_user@k8s-worker1:~$ 

## Then update the kubelet and kubectl 

sudo apt-get update && \
sudo apt-get install -y --allow-change-held-packages kubelet=1.25.9-00 kubectl=1.25.9-00

cloud_user@k8s-worker1:~$ sudo apt-get update && \
> sudo apt-get install -y --allow-change-held-packages kubelet=1.25.9-00 kubectl=1.25.9-00
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
The following held packages will be changed:
  kubectl kubelet
The following packages will be upgraded:
  kubectl kubelet
2 upgraded, 0 newly installed, 0 to remove and 24 not upgraded.
Need to get 29.0 MB of archives.
After this operation, 2750 kB disk space will be freed.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubectl amd64 1.25.9-00 [9512 kB]
Get:2 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubelet amd64 1.25.9-00 [19.5 MB]
Fetched 29.0 MB in 2s (14.2 MB/s)   
(Reading database ... 195733 files and directories currently installed.)
Preparing to unpack .../kubectl_1.25.9-00_amd64.deb ...
Unpacking kubectl (1.25.9-00) over (1.24.0-00) ...
Preparing to unpack .../kubelet_1.25.9-00_amd64.deb ...
Unpacking kubelet (1.25.9-00) over (1.24.0-00) ...
Setting up kubectl (1.25.9-00) ...
Setting up kubelet (1.25.9-00) ...
cloud_user@k8s-worker1:~$ 

## Reload the system-daemon and restart the kubelet 
cloud_user@k8s-worker1:~$ 
cloud_user@k8s-worker1:~$ sudo systemctl daemon-reload && sudo systemctl restart kubelet
cloud_user@k8s-worker1:~$ 

## Finally uncordon the worker node. Execute this from the control plane, since kubectl is not configured on the worker node yet
cloud_user@k8s-control:~$ kubectl uncordon k8s-worker1
node/k8s-worker1 uncordoned
cloud_user@k8s-control:~$ 
