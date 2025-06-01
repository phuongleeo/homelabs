```sh

helm repo add argo https://argoproj.github.io/argo-helm
# install Argocd 2.10.9 https://artifacthub.io/packages/helm/argo/argo-cd/6.7.17
helm install argocd argo/argo-cd --version 8.0.14 -f values.yaml -n argocd
kubectl get pod -l 'app.kubernetes.io/component=redis-secret-init' -o jsonpath='{.spec.containers[*].name}'
kubectl -n argocd patch -f patch.yaml # (deprecated)
kubectl exec pod/$(kubectl -n argocd get pod -l 'app.kubernetes.io/component=redis-secret-init' -o jsonpath='{.items..metadata.name}') -c istio-proxy -n argocd -- curl -sS -XPOST 127.0.0.1:15000/quitquitquit
# istio ingress
openssl genrsa -out argocd-server.key 2048
openssl req -new -key argocd-server.key -out argocd-server.csr
openssl x509 -req -days 365 -in argocd-server.csr -signkey argocd-server.key -out argocd-server.crt
kubectl -n istio-system create secret tls argocd-server-tls --cert=argocd-server.crt --key=argocd-server.key
kubectl -n istio-system apply -f istio-gateway.yaml
```

```sh
# Setup Helm registry for demo
helm repo add bitnami https://charts.bitnami.com/bitnami
kubectl create namespace kubeapps
helm install kubeapps --namespace kubeapps bitnami/kubeapps
# Create SA
kubectl create --namespace kubeapps serviceaccount kubeapps-internal-kubeappsapis
kubectl create clusterrolebinding kubeapps-internal-kubeappsapis --clusterrole=cluster-admin --serviceaccount=kubeapps:kubeapps-internal-kubeappsapis
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kubeapps-internal-kubeappsapis-token
  namespace: kubeapps
  annotations:
    kubernetes.io/service-account.name: kubeapps-internal-kubeappsapis
type: kubernetes.io/service-account-token
EOF

kubectl get --namespace kubeapps secret kubeapps-internal-kubeappsapis-token -o go-template='{{.data.token | base64decode}}'
```