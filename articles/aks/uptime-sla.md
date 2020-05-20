---
title: Usługa Azure Kubernetes Service (AKS) z umową SLA
description: Dowiedz się więcej o opcjonalnej ofercie SLA dla usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: e0e1399f69640dddfd618ac99637023390f28a92
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683218"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Umowa SLA dla usługi Azure Kubernetes Service (AKS)

Umowa SLA na czas pracy to opcjonalna funkcja umożliwiająca zapewnienie finansowo zabezpieczonej, wyższej umowy SLA dla klastra. Umowa SLA na czas działania gwarantuje dostępność na 99,95% punktu końcowego serwera interfejsu API Kubernetes dla klastrów, które używają [strefy dostępności][availability-zones] i 99,9% dostępności dla klastrów, które nie używają strefy dostępności. AKS używa replik węzłów głównych w ramach aktualizacji i domen błędów w celu zapewnienia spełnienia wymagań umowy SLA.

Klienci wymagający umowy SLA w celu spełnienia wymagań dotyczących zgodności lub wymagania rozszerzenia umowy SLA na ich użytkowników końcowych powinni włączyć tę funkcję. Klienci z krytycznymi obciążeniami, którzy będą mogli korzystać z umowy SLA o wyższym przestoju. Korzystanie z funkcji umowy SLA w trakcie działania z Strefy dostępności zapewnia wyższą dostępność w razie przestoju serwera interfejsu API Kubernetes.  

Klienci mogą nadal tworzyć nieograniczoną liczbę bezpłatnych klastrów z celem poziomu usługi (SLO) wynoszącym 99,5% i korzystać z preferowanego działania SLO lub umowy SLA w razie potrzeby.

> [!Important]
> W przypadku klastrów mających blokadę [ruchu wychodzącego, zobacz Ograniczanie ruch wyjście](limit-egress-traffic.md) do otwierania odpowiednich portów.

## <a name="sla-terms-and-conditions"></a>Warunki i postanowienia umowy SLA

Umowa SLA na czas pracy to płatna funkcja i włączona na klaster. Cennik umowy SLA na czas pracy jest określany przez liczbę klastrów dyskretnych, a nie przez rozmiar poszczególnych klastrów. Aby uzyskać więcej informacji, możesz wyświetlić [szczegóły cennika umowy SLA](https://azure.microsoft.com/pricing/details/kubernetes-service/) .

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
```

## <a name="limitations"></a>Ograniczenia

* Obecnie nie można konwertować jako istniejący klaster, aby włączyć umowę SLA dotyczącą czasu pracy.
* Obecnie nie ma możliwości usunięcia umowy SLA dotyczącej czasu działania z klastra AKS po jej utworzeniu.  
* Klastry prywatne nie są obecnie obsługiwane.

## <a name="next-steps"></a>Następne kroki

Użyj [strefy dostępności][availability-zones] , aby zwiększyć wysoką dostępność przy użyciu obciążeń klastra AKS.
Skonfiguruj klaster, aby [ograniczyć ruch wychodzący](limit-egress-traffic.md).

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
