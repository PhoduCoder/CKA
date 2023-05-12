Upgrading a Cluster 

Control Plane Upgrade Steps 
a) Upgrade kubeadm
b) Drain the CP
c) kubeadm upgrade plan
d) kubeadm upgrade apply
e) update kubelet and kubectl on control plane
f) Uncordon the control plane 

Worker Node Upgrade Steps
a) Drain the node
b) Upgrade kubeadm
c) Update the kubelet configuration [kubeadm upgrade node]
d) Update Kubelet and kubectl 
e) Uncordon


So in essence, we have to update the kubeadm initially, 
then drain the nodes, then we use kubeadm to plan and upgrade the nodes[much like terraform model]
Note that on the worker node, the plan and upgrade step is 
replaced by kubeadm upgrade node command.

In both the nodes, we will then update the kubelet and kubectl 

