# ecs-kube
My home kubernetes cluster

## Configuration

### kubernetes nodes    
MasterNode  : ubuntu-20.04 / cpu 4, memory 4096 / 192.168.52.10   
WorkerNode1 : ubuntu-20.04 / cpu 2, memory 2048 / 192.168.52.11   
WorkerNode1 : ubuntu-20.04 / cpu 2, memory 2048 / 192.168.52.12   
   
### NFS nodes
NFS1(50GB) : ubuntu-20.04 / cpu 1, memory 1024 / 192.168.52.13   
NFS2(10GB) : ubuntu-20.04 / cpu 1, memory 1024 / 192.168.52.14   

### Kubernetes Cluster Info
CRI : CRI-O v1.23   
CNI : CALICO v3.25   
   
Ingress controller : Kong v3.1   
Network load balancers : Metallb v0.13.7   

### Network Info
Pod network CIDR : 172.16.0.0/16   
    
Node-reserved-ip-pool-1 : 192.168.52.10-192.168.52.19       
External-ip-pool-2 : 192.168.52.20-192.168.52.29     
External-ip-pool-2 : 192.168.52.30-192.168.52.39      

### IP / Services   
LbTestEcho : 192.168.52.20   
Kong-proxy : 192.168.52.21   
   
KongTestEcho : kube.ecsimsw.com/kong-sample/**    
Prometeus(v2.41.0) : kube.ecsimsw.com/prometheus/**    
Grafana(v9.4.2) : grafana.ecsimsw.com/**    

## Schedule
- [x] Kubernetes cluster 
  - [x] Kubeadm, Kubectl, Kubelet
  - [x] CRI
  - [x] CNI
- [x] MetalLB
- [x] Kong Ingress controller
- [x] Volumes
  - [x] NFS Server
  - [x] NFS CSI driver / StorageClass
- [ ] Monitoring system
  - [ ] Prometheus
  - [ ] Grafana
  - [ ] Metric server
- [ ] Publishing servers
  - [x] MySql
  - [x] Mongodb
  - [ ] Kafka
  - [ ] Redis
  - [ ] Sample applications
- [ ] CI/CD
  - [ ] Jenkins
  - [ ] Github actions hosted runner
  - [ ] GitOps
- [ ] TLS configuration
  - [ ] CertManager
- [ ] Load test
