---
title: Wysoka dostępność usługi Azure Kubernetes Service (AKS) z umową SLA
description: Dowiedz się więcej o opcjonalnej ofercie SLA dotyczącej czasu działania wysokiej dostępności dla serwera interfejsu API usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 1c340f85a107cac437e1241025d8c9bc6991b965
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125727"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Umowa SLA dla usługi Azure Kubernetes Service (AKS)

Umowa SLA na czas pracy to opcjonalna funkcja umożliwiająca zapewnienie finansowo zabezpieczonej wyższej umowy SLA dla klastra. Umowa SLA na czas działania gwarantuje dostępność na 99,95% punktu końcowego serwera interfejsu API Kubernetes dla klastrów korzystających ze [strefy dostępności][availability-zones] i 99,9% dostępności dla klastrów, które nie korzystają ze stref dostępności. AKS używa replik węzłów głównych w ramach aktualizacji i domen błędów w celu zapewnienia spełnienia wymagań umowy SLA.

Klienci potrzebujący umowy SLA ze względu na zgodność lub przedłużania umowy SLA do klientów powinni włączyć tę funkcję. Klienci z krytycznymi obciążeniami, którzy potrzebują wyższej dostępności z możliwością korzystania z tej funkcji przez umowę SLA. Włącz funkcję z Strefy dostępności, aby uzyskać większą dostępność serwera interfejsu API Kubernetes.  

Klienci mogą tworzyć nieograniczoną liczbę bezpłatnych klastrów z celem poziomu usługi (SLO) wynoszącym 99,5%.

> [!Important]
> W przypadku klastrów mających blokadę [ruchu wychodzącego, zobacz Ograniczanie ruchem](limit-egress-traffic.md) wyjściowym do otwierania odpowiednich portów dla umowy SLA dotyczącej czasu pracy

## <a name="sla-terms-and-conditions"></a>Warunki i postanowienia umowy SLA

Umowa SLA na czas pracy to płatna funkcja i włączona na klaster. Cennik umowy SLA na czas pracy jest określany przez liczbę klastrów, a nie przez rozmiar klastrów. Aby uzyskać więcej informacji, możesz wyświetlić [szczegóły cennika umowy SLA](https://azure.microsoft.com/pricing/details/kubernetes-service/) .

## <a name="region-availability"></a>Dostępność regionów

Umowa SLA na czas działania jest dostępna w następujących regionach:

* Australia Wschodnia
* Kanada Środkowa
* Wschodnie stany USA
* Wschodnie stany USA 2
* Południowo-środkowe stany USA
* Azja Południowo-Wschodnia
* Zachodnie stany USA 2

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Interfejs wiersza polecenia platformy Azure w wersji 2.7.0 lub nowszej

## <a name="creating-a-cluster-with-uptime-sla"></a>Tworzenie klastra z umową SLA

Aby utworzyć nowy klaster z umową SLA o czas działania, użyj interfejsu wiersza polecenia platformy Azure.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
Utwórz klaster AKS za pomocą polecenia [az aks create][az-aks-create]. W poniższym przykładzie pokazano tworzenie klastra o nazwie *myAKSCluster* z jednym węzłem. Usługę Azure Monitor dla kontenerów można również włączyć przy użyciu parametru *--enable-addons monitoring*.  Wykonanie tej operacji może potrwać kilka minut.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON. Poniższy fragment kodu JSON przedstawia warstwę płatną dla jednostki SKU, wskazując, że klaster jest włączony z umową SLA.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters",
  "windowsProfile": null
```

## <a name="limitations"></a>Ograniczenia

* Nie można obecnie dodać umowy SLA do czasu działania do istniejących klastrów.
* Obecnie nie ma możliwości usunięcia umowy SLA dotyczącej czasu działania z klastra AKS.  

## <a name="next-steps"></a>Następne kroki

Użyj [strefy dostępności][availability-zones] , aby zwiększyć wysoką dostępność przy użyciu obciążeń klastra AKS.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
