# ecs-kube
My home k8s cluster

## Configuration

### Nodes
MasterNode  : ubuntu-20.04 / cpu 4, memory 4096 / 192.168.52.10   
WorkerNode1 : ubuntu-20.04 / cpu 2, memory 2048 / 192.168.52.11   
WorkerNode1 : ubuntu-20.04 / cpu 2, memory 2048 / 192.168.52.12   
   
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
