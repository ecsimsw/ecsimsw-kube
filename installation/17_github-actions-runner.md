## install github actions runner

ref,
https://github.com/actions/actions-runner-controller/blob/master/docs/quickstart.md

### 1. install cert-manager
```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.8.2/cert-manager.yaml
```

### 2. install arc with helm
```
helm repo add actions-runner-controller https://actions-runner-controller.github.io/actions-runner-controller
```

```
helm upgrade --install --namespace actions-runner-system --create-namespace\
  --set=authSecret.create=true\
  --set=authSecret.github_token="GITHUB_PAT_TOKEN"\
  --wait actions-runner-controller actions-runner-controller/actions-runner-controller
```

### 3. define runnerDeployment for specific repo

```
apiVersion: actions.summerwind.dev/v1alpha1
kind: RunnerDeployment
metadata:
  name: example-runnerdeploy
spec:
  replicas: 1
  template:
    spec:
      repository: ${USER_NAME}/${REPO_NAME}
```
