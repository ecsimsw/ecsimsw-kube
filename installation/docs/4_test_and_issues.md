# How to test, Issues I met

## Test with dns resolution
[k8s/dns-debugging-resolution](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)

Create dns utils pod
```
echo "apiVersion: v1
kind: Pod
metadata:
  name: dnsutils
  namespace: default
spec:
  containers:
  - name: dnsutils
    image: registry.k8s.io/e2e-test-images/jessie-dnsutils:1.3
    command:
      - sleep
      - "infinity"
    imagePullPolicy: IfNotPresent
  restartPolicy: Always" | kubectl apply -f -
```

check `nslookup` works

```
kubectl exec -it dnsutils /bin/bash 
nslookup google.com
```

## Issues

if) [ERROR FileContent--proc-sys-net-bridge-bridge-nf-call-iptables]: /proc/sys/net/bridge/bridge-nf-call-iptables does not exist
```
modprobe br_netfilter
```

if) [ERROR FileContent--proc-sys-net-ipv4-ip_forward]: /proc/sys/net/ipv4/ip_forward contents are not set to 1

```
echo '1' > /proc/sys/net/ipv4/ip_forward
```

if) [ERROR FileAvailable--etc-kubernetes-pki-ca.crt]: /etc/kubernetes/pki/ca.crt already exists

```
rm -rf /etc/kubernetes/pki/ca.crt
```

if) [WARNING Swap]: swap is enabled; production deployments should disable swap unless testing the NodeSwap feature gate of the kubelet

```
sudo -i
swapoff -a
echo 0 > /proc/sys/vm/swappiness
sed -e '/swap/ s/^#*/#/' -i /etc/fstab
```

if) The connection to the server localhost:8080 was refused - did you specify the right host or port?

```
# To start using your cluster, you need to run the following as a regular user:
mkdir -p $HOME/.kube &&
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config &&
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# if you are the root user, you can run:
export KUBECONFIG=/etc/kubernetes/admin.conf
```

if) no matches for kind "PodDisruptionBudget" in version "policy/v1beta1" ensure CRDs are installed first
```
I think the yaml of Calico is the old version. 
1. Modify the PodDisruptionBudget version to policy/v1beta1 → policy/v1
2. Or increase the calicoyaml version (I used v3.25 by documentation)
```

if) nslookup is failed, check all the pods are in POD_IP_CIDR range. If not, restart pods to get new ip.

if) Make sure that the IPs of all nodes are not the same. Especially that ips are all same as 10.0.2.15 which is default NAT ip address, you might skip setting up node ip on kubelet.
```
NODE_IP={MACHINE_NODE_IP} &&
echo "KUBELET_EXTRA_ARGS=\"--node-ip=$NODE_IP\"" >> /etc/default/kubelet

kubeadm reset

## kubeadm init if this node is master.
## kubeadm join if this node is worker. 
```
