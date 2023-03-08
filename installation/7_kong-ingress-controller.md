### Install with helm
```
helm repo add kong https://charts.konghq.com
helm repo update

# Helm v3+
helm install kong/kong --generate-name --set ingressController.installCRDs=false -n kong --create-namespace
```

### Request to Kong Gateway

Find kong proxy ip and port

```
HOST=$(kubectl get svc --namespace kong {KONG_PROXY_SVC_NAME} -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
PORT=$(kubectl get svc --namespace kong {KONG_PROXY_SVC_NAME} -o jsonpath='{.spec.ports[0].port}')
export KONG_PROXY=${HOST}:${PORT}
curl $KONG_PROXY
```

{"message":"no Route matched with those values"}

### To proxy requests,

Create routing configuration to proxy /echo requests to the echo serve

```
kubectl apply -f https://bit.ly/echo-service

echo "
apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  name: kong
spec:
  controller: ingress-controllers.konghq.com/kong
" | kubectl apply -f -

echo "
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: echo
  annotations:
    konghq.com/strip-path: 'true'
spec:
  ingressClassName: kong
  rules:
  - host: kong.example
    http:
      paths:
      - path: /echo
        pathType: ImplementationSpecific
        backend:
          service:
            name: echo
            port:
              number: 80
" | kubectl apply -f -
```

Test the routing rule

```
KONG_PROXY_IP=$(kubectl get svc --namespace kong {KONG_PROXY_SVC_NAME} -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
curl -i http://kong.example/echo --resolve kong.example:80:KONG_PROXY_IP
```

### Docs
installation : `https://docs.konghq.com/kubernetes-ingress-controller/2.8.x/deployment/overview/`
how to use   : `https://docs.konghq.com/kubernetes-ingress-controller/2.8.x/guides/getting-started/`
