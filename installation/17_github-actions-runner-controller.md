## install github actions runner controller

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

``` yaml
apiVersion: actions.summerwind.dev/v1alpha1
kind: RunnerDeployment
metadata:
  name: example-runnerdeploy
spec:
  replicas: 1
  template:
    spec:
      repository: ${USER_NAME}/${REPO_NAME}
      labels:
        - ${MY_LABEL}
```

### 4. HPA

ref, `https://github.com/actions/actions-runner-controller/blob/master/docs/automatically-scaling-runners.md`

``` yaml
apiVersion: actions.summerwind.dev/v1alpha1
kind: HorizontalRunnerAutoscaler
metadata:
  name: github-ations-runner-hpa
  namespace: actions-runner-system
spec:
  scaleDownDelaySecondsAfterScaleOut: 300
  scaleTargetRef:
    kind: RunnerDeployment
    name: ${RunnerDeployment_NAME}
  minReplicas: 1
  maxReplicas: 5
  metrics:
    - type: PercentageRunnersBusy
      scaleUpThreshold: '0.75'     # The percentage of busy runners at which the number of desired runners are re-evaluated to scale up
      scaleDownThreshold: '0.25'   #  The percentage of busy runners at which the number of desired runners are re-evaluated to scale down
      scaleUpFactor: '2'           # The scale up multiplier factor applied to desired count
      scaleDownFactor: '0.5'       # The scale down multiplier factor applied to desired count
```
