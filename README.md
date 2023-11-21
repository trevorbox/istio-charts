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

## prometheus

```sh
kubectl create namespace istio-monitoring
kubectl label namespace istio-monitoring istio-injection=enabled
oc adm policy add-scc-to-group anyuid system:serviceaccounts:istio-monitoring
helm upgrade -i prometheus prometheus -n istio-monitoring
```

## kiali

```sh
helm upgrade -i kiali kiali -n istio-system --set kiali.kiali_route_url=https://kiali-istio-system.apps-crc.testing
```

## rate limit

```sh
helm upgrade -i rate-limit rate-limit -n istio-system
```

## adaptive concurrency

```sh
helm upgrade -i adaptive-concurrency adaptive-concurrency/ -n istio-system
```

## delete

```sh
helm delete istiod -n istio system
helm delete istio-cni -n kube-system
helm delete rate-limit -n istio-system
```

## notes

```sh
istioctl profile dump default
istioctl profile dump openshift

kubectl -n istio-ingress exec -it deploy/istio-ingressgateway -- curl localhost:15000/config_dump > istio-ingressgateway-config.json
curl localhost:15000/stats/prometheus | grep rate
```

## test

```sh
siege -c 10 -r 100 https://api-istio-ingress.apps-crc.testing/productpage
```

## monitoring

```promql
envoy_http_inbound_0_0_0_0_9080_adaptive_concurrency_gradient_controller_concurrency_limit
sum(irate(envoy_http_inbound_0_0_0_0_9080_adaptive_concurrency_gradient_controller_rq_blocked[5m])) by (pod, namespace)

envoy_http_inbound_0_0_0_0_9080_adaptive_concurrency_gradient_controller_min_rtt_msecs
envoy_http_inbound_0_0_0_0_9080_adaptive_concurrency_gradient_controller_burst_queue_size
```
