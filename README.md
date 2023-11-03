# istio-charts

charts for deploying upstream istio

## istiod

```sh
oc adm policy add-scc-to-group anyuid system:serviceaccounts:istio-system
helm repo add istio https://istio-release.storage.googleapis.com/charts
helm repo update
kubectl create namespace istio-system
helm upgrade -i istio-base istio-base -n istio-system
helm upgrade -i istio-cni istio-istio-cni -n kube-system
helm upgrade -i istio-istiod istio-istiod -n istio-system 
```

## gateways

```sh
kubectl create namespace istio-ingress
oc adm policy add-scc-to-group anyuid system:serviceaccounts:istio-ingress
kubectl label namespace istio-ingress istio-injection=enabled
helm upgrade -i ingressgateway istio-ingress -n istio-ingress -f istio-ingress/values-openshift.yaml
```

## bookinfo

```sh
kubectl create namespace bookinfo
kubectl label namespace bookinfo istio-injection=enabled
oc adm policy add-scc-to-group anyuid system:serviceaccounts:bookinfo
helm upgrade -i bookinfo bookinfo -n bookinfo
```

## delete

```sh
helm delete istiod -n istio system
helm delete istio-cni -n kube-system
```

## notes

```sh
istioctl profile dump default
istioctl profile dump openshift
```
