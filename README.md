<br/> get logs pod <br/>
```bash
kubectl logs pod/[pod_name] -n [namespace] --since=36h > [pod_name].log 
```
<br/> delete not-running pod <br/>
```bash
kubectl get po --all-namespaces --field-selector 'status.phase!=Running' -o json | kubectl delete -f -
```
<br/> delete evicted pod <br/>
```bash
kubectl get pods -n pinggo-dev| grep Evicted | awk '{print $1}' |xargs kubectl delete pod 
```
<br/>get k8s api <br/>
```bash
for kind in `kubectl api-resources | tail +2 | awk '{ print $1 }'`; do kubectl explain $kind; done | grep -e "KIND:" -e "VERSION:"
```

<br/>container creating err<br/>
```bash
vim /run/flannel/subnet.env
```
