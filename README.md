# fluxcd redis example

## 手順メモ
### minimum
`namespaces/dev.yaml` の作成。
`helm-releases/redis-dev.yaml` の作成

### kubernetes
macOS,  
minikube, helm, fluxctl インストール済み  

```bash
helm repo add fluxcd https://charts.fluxcd.io
kubectl create ns fluxcd
```


## 参考資料等
https://helm.sh/docs/
https://docs.fluxcd.io/projects/helm-operator/en/1.0.0-rc9/index.html
https://github.com/fluxcd/helm-operator
https://github.com/fluxcd/helm-operator-get-started