#Renew certificate

When k8s executes kubectl get nodes on the master node, it prompts:
```
root@k8s-master01 .kube]# kubectl --kubeconfig=/root/.kube/config get nodes
Unable to connect to the server: x509: certificate has expired or is not yet valid: current time 2022-11-24T16:57:15+08:00 is after 2022-09-16T09:13:17Z
```
Certificate update command:

```
kubeadm certs renew all
```
```
[root@k8s-master01 ~]# kubeadm certs renew all
[renew] Reading configuration from the cluster...
[renew] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'

certificate embedded in the kubeconfig file for the admin to use and for kubeadm itself renewed
certificate for serving the Kubernetes API renewed
certificate the apiserver uses to access etcd renewed
certificate for the API server to connect to kubelet renewed
certificate embedded in the kubeconfig file for the controller manager to use renewed
certificate for liveness probes to healthcheck etcd renewed
certificate for etcd nodes to communicate with each other renewed
certificate for serving etcd renewed
certificate for the front proxy client renewed
certificate embedded in the kubeconfig file for the scheduler manager to use renewed
```
Done renewing certificates. You must restart the kube-apiserver, kube-controller-manager, kube-scheduler and etcd, so that they can use the new certificates.

Verify command:
```
kubeadm certs check-expiration
root@node1:~# kubeadm certs check-expiration
[check-expiration] Reading configuration from the cluster...
[check-expiration] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
W0420 06:40:03.993665   40477 utils.go:69] The recommended value for "clusterDNS" in "KubeletConfiguration" is: [10.233.0.10]; the provided value is: [169.254.25.10]
W0420 06:40:04.003594   40477 utils.go:69] The recommended value for "resolvConf" in "KubeletConfiguration" is: /run/systemd/resolve/resolv.conf; the provided value is: /run/systemd/resolve/resolv.conf

CERTIFICATE                EXPIRES                  RESIDUAL TIME   CERTIFICATE AUTHORITY   EXTERNALLY MANAGED
admin.conf                 Apr 01, 2024 16:06 UTC   347d            ca                      no      
apiserver                  Apr 01, 2024 16:06 UTC   347d            ca                      no      
apiserver-kubelet-client   Apr 01, 2024 16:06 UTC   347d            ca                      no      
controller-manager.conf    Apr 01, 2024 16:06 UTC   347d            ca                      no      
front-proxy-client         Apr 01, 2024 16:06 UTC   347d            front-proxy-ca          no      
scheduler.conf             Apr 01, 2024 16:06 UTC   347d            ca                      no      

CERTIFICATE AUTHORITY   EXPIRES                  RESIDUAL TIME   EXTERNALLY MANAGED
ca                      Mar 28, 2032 10:56 UTC   8y              no      
front-proxy-ca          Mar 28, 2032 10:56 UTC   8y              no 
```
Find the admin.conf location
```
[root@k8s-master01 .kube]# find / -name admin.conf
/etc/kubernetes/admin.conf
[root@k8s-master01 .kube]# kubectl --kubeconfig=/etc/kubernetes/admin.conf get nodes\
NAME STATUS ROLES AGE VERSION
k8s-master01 Ready master 433d v1.19.4
k8s-node01 Ready <none> 433d v1.19.4
k8s-node02 Ready <none> 433d v1.19.4
k8s-node03 Ready <none> 433d v1.19.4
```
Copy admin.conf to /root/.kube/config and execute kubectl get nodes command to verify
```
[root@k8s-master01 .kube]# cp /etc/kubernetes/admin.conf /root/.kube/config
cp: overwrite '/root/.kube/config'? yes
[root@k8s-master01 .kube]#
[root@k8s-master01 .kube]#
[root@k8s-master01 .kube]# kubectl get nodes
NAME STATUS ROLES AGE VERSION
k8s-master01 Ready master 433d v1.19.4
k8s-node01 Ready <none> 433d v1.19.4
k8s-node02 Ready <none> 433d v1.19.4
k8s-node03 Ready <none> 433d v1.19.4
```
If still cannot connect to k8s cluster, Reboot the node and check the logs for etcd, kubeapi and kubelet.

Note: 

    - Remember to update your CI/CD job kubeconfig file. If you’re using helm command test that also.

    - You have renew on all controller/master nodes.

    - After renew cert, reboot all controller/master nodes.
