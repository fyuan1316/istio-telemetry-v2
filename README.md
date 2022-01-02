## Deep dive Istio telemetry v2

This is a quick jump into Istio telemetry v2 to accompany [this blog post]().

For more information, see the [Istio documentation](https://istio.io/latest/docs/concepts/observability/). For even more, see Chapter 7 on Observability in my book [Istio in Action](https://www.manning.com/books/istio-in-action).

### Set up cluster

```
$  kind create cluster
$  istioctl install -y
$  ./setup.sh
```

From here, we can make a call to the Istio ingress gateway:

```
$  kubectl -n default exec -it deploy/sleep -- curl -H "Host: istioinaction.io" http://istio-ingressgateway.istio-system/
```

We should see 1/2 of the calls fail between recommendation and purchase-history services.

If we scrape the metrics, we can see

```
kubectl exec -it -n istioinaction deploy/recommendation -c istio-proxy -- curl localhost:15000/stats/prometheus | grep istio_requests_total
```

增加 istio 1.12 envoyfilter 的配置：

```bash
# 增加、修改、删除dimesions，需要在对应的workload 上增加annotation *sidecar.istio.io/extraStatTags: posta,posta_two*
kubectl apply -f custom-stats-filter-1.12.yaml

# 增加自定义指标和维度
kubectl apply -f create-new-metric-dim-1.12.yaml

```
