---
title: (PRZESTARZAŁE) Samouczek usługi Azure Container Service — skalowanie aplikacji
description: Samouczek usługi Azure Container Service — skalowanie aplikacji
author: dlepow
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 2ea8a5428c1fabdfda4f2298c0559792537df481
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273986"
---
# <a name="deprecated-scale-kubernetes-pods-and-kubernetes-infrastructure"></a>(PRZESTARZAŁE) Skalowanie zasobników i infrastruktury rozwiązania Kubernetes

> [!TIP]
> Aby uzyskać zaktualizowaną wersję tego samouczka korzystającego z usługi Azure Kubernetes, zobacz [Samouczek: skalowanie aplikacji w usłudze Azure Kubernetes Service (AKS)](../../aks/tutorial-kubernetes-scale.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Jeśli wykonujesz kolejno zadania z samouczków, masz już działający klaster Kubernetes w usłudze Azure Container Service z wdrożoną aplikacją do głosowania platformy Azure. 

Ta część samouczka, piąta z siedmiu, obejmuje skalowanie w poziomie zasobników w tej aplikacji oraz skalowanie automatyczne. Dowiesz się również, jak, skalując liczbę węzłów agenta maszyny wirtualnej platformy Azure, zmieniać możliwości hostowania obciążeń w klastrze. Wykonano następujące zadania:

> [!div class="checklist"]
> * Ręczne skalowanie zasobników Kubernetes
> * Konfigurowanie skalowania automatycznego zasobników obsługujących fronton aplikacji
> * Skalowanie węzłów agentów platformy Azure dla rozwiązania Kubernetes

W kolejnych samouczkach aplikacja do głosowania platformy Azure zostanie zaktualizowana, a usługa Log Analytics zostanie skonfigurowana do monitorowania klastra Kubernetes.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja została spakowana w postaci obrazu kontenera, obraz został przekazany do usługi Azure Container Registry i utworzono klaster Kubernetes. Następnie uruchomiono aplikację w klastrze usługi Kubernetes. 

Jeśli te kroki nie zostały wykonane, a chcesz skorzystać z tej części samouczka, wróć do części [Samouczek 1 — tworzenie obrazów kontenera](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="manually-scale-pods"></a>Ręczne skalowanie zasobników

Poprzednio wdrożono fronton aplikacji do głosowania platformy Azure oraz wystąpienie pamięci podręcznej Redis — każdy z tych elementów został wdrożony z jedną repliką. Aby to zweryfikować, uruchom polecenie [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get).

Przejdź do [https://shell.azure.com](https://shell.azure.com) okna, aby otworzyć Cloud Shell w przeglądarce.

```console
kubectl get pods
```

Dane wyjściowe:

```output
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Ręcznie zmień liczbę zasobników we wdrożeniu aplikacji `azure-vote-front`, używając polecenia [kubectl scale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale). W tym przykładzie liczba ta zostanie zwiększona do 5.

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Uruchom polecenie [kubectl get pods](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get), aby zweryfikować utworzenie zasobników przez rozwiązanie Kubernetes. Po upływie około minuty dodatkowe zasobniki zaczną działać:

```console
kubectl get pods
```

Dane wyjściowe:

```output
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Automatyczne skalowanie zasobników

Rozwiązanie Kubernetes obsługuje [automatyczne skalowanie zasobników w poziomie](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/), umożliwiające dostosowywanie liczby zasobników we wdrożeniu do użycia procesora lub innych wybranych metryk. 

Aby móc korzystać ze skalowania automatycznego, należy zdefiniować wymagania i limity użycia procesora CPU dla zasobników. We wdrożeniu aplikacji `azure-vote-front` kontener frontonu wymaga 0,25 CPU, a limit wynosi 0,5 CPU. Ustawienia są następujące:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

W poniższym przykładzie użyto polecenia [kubectl autoscale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale) w celu automatycznego skalowania liczby zasobników we wdrożeniu aplikacji `azure-vote-front`. Jeśli użycie procesora CPU przekroczy 50%, skalowanie automatyczne spowoduje zwiększenie liczby zasobników, maksymalnie do 10.


```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Aby wyświetlić stan skalowania automatycznego, uruchom następujące polecenie:

```console
kubectl get hpa
```

Dane wyjściowe:

```output
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Po upływie kilku minut, przy minimalnym obciążeniu aplikacji do głosowania platformy Azure, liczba replik zasobników zostanie automatycznie zmniejszona do 3.

## <a name="scale-the-agents"></a>Skalowanie agentów

Jeśli utworzono klaster Kubernetes przy użyciu domyślnych poleceń z poprzedniego samouczka, zawiera on trzy węzły agentów. Jeśli planujesz zwiększenie lub zmniejszenie liczby obciążeń kontenerów w klastrze, możesz ręcznie dostosować liczbę agentów. Użyj polecenia [az acs scale](/cli/azure/acs#az-acs-scale), określając liczbę agentów za pomocą parametru `--new-agent-count`.

W poniższym przykładzie liczba węzłów agentów w klastrze Kubernetes o nazwie *myK8scluster* zostanie zwiększona do 4. Wykonanie tego polecenia może zająć kilka minut.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

Wartość parametru `agentPoolProfiles:count` w danych wyjściowych polecenia wskazuje liczbę węzłów agentów:

```output
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto różnych funkcji skalowania w klastrze Kubernetes. Wykonano następujące zadania:

> [!div class="checklist"]
> * Ręczne skalowanie zasobników Kubernetes
> * Konfigurowanie skalowania automatycznego zasobników obsługujących fronton aplikacji
> * Skalowanie węzłów agentów platformy Azure dla rozwiązania Kubernetes

Przejdź do następnego samouczka, aby dowiedzieć się, jak zaktualizować aplikację w usłudze Kubernetes.

> [!div class="nextstepaction"]
> [Aktualizowanie aplikacji w usłudze Kubernetes](./container-service-tutorial-kubernetes-app-update.md)

