#### Check if you are using IPVS mode
If you’re using kube-proxy in IPVS mode, since Kubernetes v1.14.2 you have to enable strict ARP mode.

```
# kube-system/kube-proxy configmap

kind: KubeProxyConfiguration                                                                                                  
metricsBindAddress: ""                                                                                                        
mode: ""                 
```

#### Installation With Helm
```
helm repo add metallb https://metallb.github.io/metallb
helm install metallb metallb/metallb -f metalLB-0.13.7-helm-values.yaml -n metallb-system --create-namespace
```
