<br/> get logs pod <br/>
kubectl logs pod/[pod_name] -n [namespace] --since=36h > [pod_name].log 
<br/> delete not-running pod <br/>
kubectl get po --all-namespaces --field-selector 'status.phase!=Running' -o json | kubectl delete -f -
<br/> delete evicted pod <br/>
kubectl get pods -n pinggo-dev| grep Evicted | awk '{print $1}' |xargs kubectl delete pod 
<br/>
```bash
for kind in `kubectl api-resources | tail +2 | awk '{ print $1 }'`; do kubectl explain $kind; done | grep -e "KIND:" -e "VERSION:"
```
