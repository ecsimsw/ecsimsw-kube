# ecs-kube
My home kubernetes cluster

## Configuration

### Architecture
![image](https://github.com/ecsimsw/ecsimsw-kube/assets/46060746/3d196fe8-4ecb-4a64-bfd3-6260e74b6275)

### K8S cluster nodes   
Master  : ubuntu-20.04 / cpu 4, memory 4096 / 192.168.0.100   
Worker1 : ubuntu-20.04 / cpu 4, memory 4096 / 192.168.0.101   
Worker2 : ubuntu-20.04 / cpu 2, memory 2048 / 192.168.0.102   
Worker3 : ubuntu-18.04 / cpu 2, memory 8192 / 192.168.0.11   
   
### NFS nodes
NFS1    : ubuntu-20.04 / cpu 1, memory 1024 / 50GB / 192.168.0.111        
NFS2    : ubuntu-20.04 / cpu 0.5, memory 512 / 10GB / 192.168.0.112         

## Kubernetes Cluster Info
K8S : v1.27.2      
CRI : CRI-O v1.23      
CNI : CALICO v3.25     
   
Ingress controller : Kong v3.1 / 192.168.0.120        
Network load balancers : Metallb v0.13.7     

node-reserved-ip-pool-1 : 192.168.0.0   - 192.168.0.99    
node-reserved-ip-pool-2 : 192.168.0.100 - 192.168.0.119    
External-ip-pool-1      : 192.168.0.120 - 192.168.0.149        

## Schedule
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
  - [x] Metric-server
- [x] MySql
- [x] Mongodb
- [x] Kafka
- [x] Redis
  - [ ] Sentinel?
  - [ ] Metrics?
- [x] Deploy spring application (mymarket)    
  - [x] Envs - environment, secret mappings
  - [x] Probes - startupProbe, readinessProbe, livenessProbe
  - [x] PodAntiAffinity
  - [x] HPA
  - [x] PodDisruptionBudget
- [x] CI/CD
  - [x] Github actions self hosted runner
  - [x] Actions runner controller
  - [x] Runner deployment
  - [x] Horizontal runner auto scaler
  - [x] Build cache
- [ ] TLS configuration
  - [x] CertManager
- [ ] Load test
