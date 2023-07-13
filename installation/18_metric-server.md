## Kubernetes Metrics Server

Metrics Server is a scalable, efficient source of container resource metrics for Kubernetes built-in autoscaling pipelines.    

### Use Metrics Server for
```
CPU/Memory based horizontal autoscaling (learn more about Horizontal Autoscaling)
Automatically adjusting/suggesting resources needed by containers (learn more about Vertical Autoscaling)
```

### Don't use Metrics Server for
```
Non-Kubernetes clusters
An accurate source of resource usage metrics
Horizontal autoscaling based on other resources than CPU/Memory
```

### Install with helm / HA 

```
helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/

helm install --namespace kube-system metrics-server metrics-server/metrics-server \
   --set replicas=2 
```
