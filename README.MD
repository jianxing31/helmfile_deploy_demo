# HELMFILE MULTI-ENV DEPLOY DEMO

## Pre-requisites
- Local Kubernetes environment
- Helm installed
- [Helmfile installed](https://helmfile.readthedocs.io/en/latest/#installation)

On Macos:
```shell
brew install helmfile
```
On windows:
```shell
scoop install helmfile
```
- [Helm diff plugin installed](https://github.com/databus23/helm-diff )

Officially recommended command:
```shell
helm plugin install https://github.com/databus23/helm-diff
```
Note: the command above didn't work in my env with [installation error](https://github.com/databus23/helm-diff/issues/322), I had to install a specific version:
```shell
helm plugin install https://github.com/databus23/helm-diff --version 3.1.3
```

## Get started

### File Structure
```shell
 .
├── .github
│   └── workflows
│       ├── lint.yaml
│       └── release.yaml
├── .pre-commit-config.yaml
├── README.MD
├── charts
│   └── demo
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── _helpers.tpl
│       │   ├── deployment.yaml
│       │   ├── hpa.yaml
│       │   ├── service.yaml
│       │   ├── serviceaccount.yaml
│       │   └── tests
│       │       └── test-connection.yaml
│       └── values.yaml
├── helmfile.yaml
└── demo_values
    ├── demo_default.yaml
    ├── demo_dev.yaml
    └── demo_prod.yaml
```

### 1. Run helmfile apply in dev env
Replace dev with prod will apply in prod env
```shell
helmfile -e dev apply
```

### 2. Check services deployed
We can use port-forward and access the api with link `http://localhost:8080`:
```shell
export POD_NAME=$(kubectl get pods --namespace dev -l "app.kubernetes.io/name=demo,app.kubernetes.io/instance=demo-dev" -o jsonpath="{.items[0].metadata.name}")
export CONTAINER_PORT=$(kubectl get pod --namespace dev $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
kubectl --namespace dev port-forward $POD_NAME 8080:$CONTAINER_PORT
```

### 3. Delete the deployed chart
```shell
helmfile -e dev delete
```
