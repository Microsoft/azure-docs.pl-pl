---
title: Skalowanie automatyczne AKS z użyciem metryk usługi Azure Application Gateway
description: Ten artykuł zawiera instrukcje dotyczące skalowania jednostek AKS zaplecza przy użyciu metryk Application Gateway i karty metryki usługi Azure Kubernetes
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5e0533a44db269229b2f26fa8d2f2b4f84f4d0b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85125467"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Automatyczne skalowanie AKSów przy użyciu metryk Application Gateway (beta)

W miarę zwiększania się ruchu przychodzącego jest on decydujący o skalowaniu aplikacji na żądanie.

W poniższym samouczku wyjaśniono, jak można użyć `AvgRequestCountPerHealthyHost` metryki Application Gateway do skalowania aplikacji w górę. `AvgRequestCountPerHealthyHost` Mierzy średnie żądania wysyłane do określonej puli zaplecza i kombinacji ustawień protokołu HTTP zaplecza.

Będziemy używać następujących dwóch składników:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) — Użyjemy karty metryki, aby udostępnić metryki Application Gateway za pomocą serwera metryk. Karta metryki usługi Azure Kubernetes to projekt Open Source na platformie Azure podobny do kontrolera Application Gateway transferu danych przychodzących. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) -Będziemy używać usługi HPA do używania metryk Application Gateway i określania celu skalowania.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Konfigurowanie karty metryki usługi Azure Kubernetes

1. Najpierw utworzymy nazwę główną usługi Azure AAD i przypiszesz im `Monitoring Reader` dostęp do grupy zasobów Application Gateway. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Teraz zostanie wdrożony program [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) przy użyciu nazwy głównej usługi AAD utworzonej powyżej.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principal created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Utworzymy `ExternalMetric` zasób o nazwie `appgw-request-count-metric` . Ten zasób nakazuje karcie metryki udostępnienie `AvgRequestCountPerHealthyHost` metryki dla `myApplicationGateway` zasobu w `myResourceGroup` grupie zasobów. Możesz użyć pola, `filter` Aby wskazać określoną pulę zaplecza i ustawienie protokołu HTTP zaplecza w Application Gateway.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

Teraz można wysłać żądanie do serwera metryk, aby sprawdzić, czy nasza nowa Metryka jest widoczna:
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Używanie nowej metryki do skalowania w górę wdrożenia

Gdy będziemy mogli uwidocznić `appgw-request-count-metric` serwer metryk, jesteśmy gotowi do [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) skalowania wdrożenia docelowego.

W poniższym przykładzie zostanie wykorzystamy przykładowe wdrożenie `aspnet` . Skalowanie w górę skaluje się w górę, gdy `appgw-request-count-metric` > 200 w wysokości do maksymalnej liczby `10` zasobników.

Zastąp docelową nazwę wdrożenia i zastosuj następującą konfigurację automatycznego skalowania:
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Przetestuj konfigurację przy użyciu narzędzia testowego obciążenia, takiego jak Apache kanap:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Następne kroki
- [**Rozwiązywanie problemów z kontrolerem**](ingress-controller-troubleshoot.md)danych przychodzących: Rozwiązywanie problemów z kontrolerem transferu danych przychodzących.