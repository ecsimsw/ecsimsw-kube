# ecs-kube
My home k8s cluster

# How to configure cluster

## Run virtual machines with vagrant
1. Operating system
2. Private network ip
3. Hostnames 
4. Resources (Cpu, Memory)

```
# Vagrant file

Vagrant.configure("2") do |config|
  config.vm.define "master" do |master|
    master.vm.box = "bento/ubuntu-20.04"
    master.vm.network "private_network", ip: "192.168.52.10"
    master.vm.hostname = "master"
    master.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
    end
  end

  config.vm.define "worker1" do |worker1|
    worker1.vm.box = "bento/ubuntu-20.04"
    worker1.vm.network "private_network", ip: "192.168.52.11"
    worker1.vm.hostname = "worker1"
    worker1.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
    end
  end

  config.vm.define "worker2" do |worker2|
    worker2.vm.box = "bento/ubuntu-20.04"
    worker2.vm.network "private_network", ip: "192.168.52.12"
    worker2.vm.hostname = "worker2"
    worker2.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
    end
  end
end
```

```
cd machines
vagrant up
```

## Disable swap
```
sudo -i
swapoff -a
echo 0 > /proc/sys/vm/swappiness
sed -e '/swap/ s/^#*/#/' -i /etc/fstab
```

check swap file list is empty

```
cat /proc/swaps
```

## Install crio v1.23
```
sudo apt update -y && 
sudo apt upgrade -y &&

OS=xUbuntu_20.04 &&
CRIO_VERSION=1.23 &&

echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/ /"|sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list &&
echo "deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/$CRIO_VERSION/$OS/ /"|sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:$CRIO_VERSION.list &&

curl -L https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:$CRIO_VERSION/$OS/Release.key | sudo apt-key add - &&
curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/Release.key | sudo apt-key add - && 

sudo apt update -y &&
sudo apt install cri-o cri-o-runc -y &&

sudo systemctl enable crio.service &&
sudo systemctl start crio.service
```
```
systemctl status crio
```

## Install Kubeadm, Kubelet, Kubectl
```
sudo apt-get update -y &&
sudo apt-get install -y apt-transport-https ca-certificates curl &&

sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg &&
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list &&

sudo apt-get update &&
sudo apt-get install -y kubelet kubeadm kubectl &&
sudo apt-mark hold kubelet kubeadm kubectl
```

## Network config
```
modprobe br_netfilter &&
echo '1' > /proc/sys/net/ipv4/ip_forward
```

Declare environment variables (NODE_IP) according to your VM settings. Without this task, the node ip registered to the cluster is set to the default NAT ip address.

```
NODE_IP=192.168.52.11 &&
echo "KUBELET_EXTRA_ARGS=\"--node-ip=$NODE_IP\"" >> /etc/default/kubelet
```

## Initialize cluster (Master node only)

Declare environment variables (NODE_IP, POD_NETWORK_CIDR) according to your VM settings and Network structure.

### Init cluster
```
NODE_IP=192.168.52.11 &&
POD_NETWORK_CIDR=172.16.0.0/16 &&

sudo kubeadm init \
--pod-network-cidr=$POD_NETWORK_CIDR \
--apiserver-advertise-address=$NODE_IP \
--control-plane-endpoint=$NODE_IP
```

### Set user env value

```
# To start using your cluster, you need to run the following as a regular user:
mkdir -p $HOME/.kube &&
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config &&
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
```
# Alternatively, if you are the root user, you can run:
export KUBECONFIG=/etc/kubernetes/admin.conf
```

### Check pod network cidr

```
kubectl cluster-info dump | grep -m 1 cluster-cidr
```

### Check master node ip

```
kubectl get nodes -o wide
```

### Get join command
```
kubeadm token create --print-join-command
```

## Install calico v3.25 (Master only)

### Get calico manifest file
```
CALICO_VERSION=3.25 &&
curl https://docs.projectcalico.org/archive/v$CALICO_VERSION/manifests/calico.yaml -O
```

### Change 'CALICO_IPV4POOL_CIDR'

Enable `CALICO_IPV4POOL_CIDR` and change the value as your `POD_NETWORK_CIDR`
```
- name: CALICO_IPV4POOL_CIDR
  value:  "172.16.0.0/16
```

### Apply
```
kubectl apply -f calico.yaml
```

## Join cluster (Worker only)

### Get join command from master node
```
kubeadm token create --print-join-command
```

### Join
```
kubeadm join ${IP} --token ${TOKEN}
```

# Test with dns resolution
[k8s/dns-debugging-resolution](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)

### Create dns utils pod
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

## Error list

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


