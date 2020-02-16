# fluxcd redis example

## 手順メモ
### 1.
`namespaces/dev.yaml` の作成。
`helm-releases/redis-dev.yaml` の作成

### 2. kubernetes setup
macOS,  
minikube, helm, fluxctl インストール済み  

```bash
helm repo add fluxcd https://charts.fluxcd.io
kubectl create ns fluxcd
```

flux のインストール（今回はhelmではなくfluxctlを利用）

```bash
helm upgrade -i flux fluxcd/flux --wait \
--namespace fluxcd \
--set git.url=git@github.com:shogotsuneto/fluxcd-redis-example \
--set git.user="FluxCD User" \
--set git.email="shogotsuneto@users.noreply.github.com"
```

github deploy key用公開鍵の取得と登録

```
fluxctl identity --k8s-fwd-ns fluxcd
```

helm-release 用の crd (custom resource definition) の作成

```bash
kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/master/deploy/flux-helm-release-crd.yaml
```

Flux Helm Operator の作成

```bash
helm upgrade -i helm-operator fluxcd/helm-operator \
--namespace fluxcd \
--set helm.versions=v3
```

## トラブルシューティング履歴

#### checkpoint1（83ebc） のマニフェストでは redis-dev が起動しなかった

```bash
kubectl get -n fluxcd pods
kubectl logs -n fluxcd flux-asdfasdfasd #flux podの名前
```

今回得られたログ

```
ts=2020-02-16T09:00:19.332979089Z caller=sync.go:165 component=daemon err="dev:helmrelease/redis-dev: running kubectl: The HelmRelease \"redis-dev\" is invalid: \n* : Invalid value: \"\": \"spec.chart\" must validate one and only one schema (oneOf). Found none valid\n* spec.chart.version: Required value"
```

version の指定が必要


#### checkpoint2 でのエラー


```bash
kubectl logs -n fluxcd helm-operator-dlkjglasjdfljasdlfj # helm operatorの名前
```

```
ts=2020-02-16T09:15:22.147272829Z caller=release.go:140 component=release release=redis-dev targetNamespace=dev resource=dev:helmrelease/redis-dev helmVersion=v3 error="chart unavailable: chart \"redis\" version \"5.0.7\" not found in https://kubernetes-charts.storage.googleapis.com repository"
```


## 参考資料等
https://helm.sh/docs/  
https://docs.fluxcd.io/projects/helm-operator/en/1.0.0-rc9/index.html  
https://github.com/fluxcd/helm-operator  
https://github.com/fluxcd/helm-operator-get-started  