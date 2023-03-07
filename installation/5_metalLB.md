#### Check if you are using IPVS mode
If you’re using kube-proxy in IPVS mode, since Kubernetes v1.14.2 you have to enable strict ARP mode.

```
kubectl edit configmap -n kube-system kube-proxy           
```

and set ipvs.strictARP as true

```
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
mode: "ipvs"
ipvs:
  strictARP: true
```

#### Installation With Helm
```
helm repo add metallb https://metallb.github.io/metallb
helm install metallb metallb/metallb -f manifests/metalLB-0.13.7-helm-values.yaml -n metallb-system --create-namespace
```

`https://metallb.universe.tf/installation/`

### Sample 

##### IPAddressPool
```
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: external-ip-pool-2
  namespace: metallb-system
spec:
  addresses:
    - 192.168.52.20-192.168.52.29
---
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: external-ip-pool-3
  namespace: metallb-system
spec:
  addresses:
    - 192.168.52.30-192.168.52.39
```

#### L2Advertisement
```
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: l2-advertisement
  namespace: metallb-system
spec:
  ipAddressPools:
  - external-ip-pool-2
  - external-ip-pool-3
```
