# Istio 

## Installation

```shell
curl -L https://istio.io/downloadIstio | sh -
export PATH="$PATH:/Users/dilas/Downloads/istio-1.23.2/bin"
istioctl version
istioctl x precheck
istioctl install --set profile=demo --set meshConfig.defaultConfig.tracing.zipkin.address=jaeger-collector.istio-system:9411 -y
istioctl verify-install

kubectl apply -f ./istio-1.23.2/samples/addons
kubectl label namespace default istio-injection=enabled
kubectl get crd gateways.gateway.networking.k8s.io &> /dev/null || \
{ kubectl kustomize "github.com/kubernetes-sigs/gateway-api/config/crd?ref=v1.1.0" | kubectl apply -f -; }
kubectl rollout status deployment/kiali -n istio-system
```

## Open Istio Addons

```shell
istioctl dashboard kiali
istioctl dashboard prometheus
istioctl dashboard grafana
istioctl dashboard jaeger
```
# ArgoCD

## Installation

```shell
kubectl create namespace argocd

kubectl -n argocd apply -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl -n argocd get secret argocd-initial-admin-secret --template={{.data.password}} | base64 --decode
# mUN9zcIFBZzRpWzM
# A7q3C00xp6GOfeV2

kubectl -n argocd port-forward svc/argocd-server 8080:443

kubectl apply -f root-argocd-app.yaml
```

# Argo Rollouts

## Installation

```shell
kubectl create namespace argo-rollouts
kubectl -n argo-rollouts apply -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml
```

## Kubectl Plugin (optional)

```shell
brew install argoproj/tap/kubectl-argo-rollouts
kubectl argo rollouts version
```

## Generate Traffic

```shell
minikube tunnel

while true; do 
curl -I http://sample-app-gateway.127.0.0.1.nip.io
sleep .1
done

while true; do
curl -I http://sample-app-gateway.127.0.0.1.nip.io/err
sleep 5
done
```

## Watch Rollout

```shell
kubectl argo rollouts -n sample-app-lab-us-2 get rollout sample-app-rollout --watch
```

## Manually Promote Rollout

```shell
kubectl argo rollouts -n sample-app-lab-us-2 promote sample-app-rollout
```

## Manually Abort Rollout

```shell
kubectl argo rollouts -n sample-app-lab-us-2 abort sample-app-rollout
```

## Undo Rollout

```shell
kubectl argo rollouts -n sample-app-lab-us-2 undo sample-app-rollout
```