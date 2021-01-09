---
title: Usługa Azure Kubernetes Service (AKS) z umową SLA
description: Dowiedz się więcej o opcjonalnej ofercie SLA dla usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 95728ca14b96adf7396ca13888107477d953b2a9
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033340"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Umowa SLA dla usługi Azure Kubernetes Service (AKS)

Umowa SLA na czas pracy to opcjonalna funkcja umożliwiająca zapewnienie finansowo zabezpieczonej, wyższej umowy SLA dla klastra. Umowa SLA na czas działania gwarantuje dostępność na 99,95% punktu końcowego serwera interfejsu API Kubernetes dla klastrów, które używają [strefy dostępności][availability-zones] i 99,9% dostępności dla klastrów, które nie używają strefy dostępności. AKS używa replik węzłów głównych w ramach aktualizacji i domen błędów w celu zapewnienia spełnienia wymagań umowy SLA.

Klienci wymagający umowy SLA w celu spełnienia wymagań dotyczących zgodności lub wymagania rozszerzenia umowy SLA na ich użytkowników końcowych powinni włączyć tę funkcję. Klienci z krytycznymi obciążeniami, którzy będą mogli korzystać z umowy SLA o wyższym przestoju. Korzystanie z funkcji umowy SLA w trakcie działania z Strefy dostępności zapewnia wyższą dostępność w razie przestoju serwera interfejsu API Kubernetes.  

Klienci mogą nadal tworzyć nieograniczoną liczbę bezpłatnych klastrów z celem poziomu usługi (SLO) wynoszącym 99,5% i korzystać z preferowanego działania SLO lub umowy SLA w razie potrzeby.

> [!Important]
> W przypadku klastrów mających blokadę [ruchu wychodzącego, zobacz Ograniczanie ruch wyjście](limit-egress-traffic.md) do otwierania odpowiednich portów.

## <a name="region-availability"></a>Dostępność w danym regionie

* Umowa SLA na czas działania jest dostępna w regionach publicznych i Azure Government regionach, w których [AKS jest obsługiwany](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).
* Umowa SLA na czas działania jest dostępna dla [prywatnych klastrów AKS][private-clusters] we wszystkich regionach publicznych, w których AKS jest obsługiwany.

## <a name="sla-terms-and-conditions"></a>Warunki i postanowienia umowy SLA

Umowa SLA na czas pracy to płatna funkcja i włączona na klaster. Cennik umowy SLA na czas pracy jest określany przez liczbę klastrów dyskretnych, a nie przez rozmiar poszczególnych klastrów. Aby uzyskać więcej informacji, możesz wyświetlić [szczegóły cennika umowy SLA](https://azure.microsoft.com/pricing/details/kubernetes-service/) .

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Instalowanie [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.8.0 lub nowszej

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Tworzenie nowego klastra z umową SLA w razie przestoju

> [!NOTE]
> Obecnie w przypadku włączenia umowy SLA dotyczącej czasu działania nie istnieje sposób, aby usunąć go z klastra.

Aby utworzyć nowy klaster z umową SLA o czas działania, użyj interfejsu wiersza polecenia platformy Azure.

Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```
Użyj [`az aks create`][az-aks-create] polecenia, aby utworzyć klaster AKS. W poniższym przykładzie pokazano tworzenie klastra o nazwie *myAKSCluster* z jednym węzłem. Wykonanie tej operacji trwa kilka minut:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON. Poniższy fragment kodu JSON przedstawia warstwę płatną dla jednostki SKU, wskazując, że klaster jest włączony z umową SLA na czas pracy:

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Zmodyfikuj istniejący klaster, aby korzystać z umowy SLA dotyczącej czasu działania

Opcjonalnie możesz zaktualizować istniejące klastry, aby korzystać z umowy SLA dotyczącej czasu działania.

Jeśli utworzono klaster AKS z poprzednimi krokami, Usuń grupę zasobów:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Utwórz nową grupę zasobów:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Utwórz nowy klaster i nie używaj umowy SLA dotyczącej czasu działania:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

Użyj [`az aks update`][az-aks-nodepool-update] polecenia, aby zaktualizować istniejący klaster:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
 ```

 Poniższy fragment kodu JSON przedstawia warstwę płatną dla jednostki SKU, wskazując, że klaster jest włączony z umową SLA na czas pracy:

 ```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  ```

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć naliczania opłat, wyczyść wszystkie utworzone zasoby. Aby usunąć klaster, użyj polecenia, [`az group delete`][az-group-delete] Aby usunąć grupę zasobów AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Następne kroki

Użyj [strefy dostępności][availability-zones] , aby zwiększyć wysoką dostępność przy użyciu obciążeń klastra AKS.

Skonfiguruj klaster, aby [ograniczyć ruch wychodzący](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?#az-aks-nodepool-update
[az-group-delete]: /cli/azure/group#az-group-delete
[private-clusters]: private-clusters.md
