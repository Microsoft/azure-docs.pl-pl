---
title: Azure Kubernetes Service (AKS) z umowami SLA czas pracy
description: Dowiedz się więcej o opcjonalnej ofercie umowy SLA dotyczącej czasu Azure Kubernetes Service api Server (AKS).
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 846446b4c19c066afe789bf636d68ad37b20709e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779565"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes Service sla czasu pracy (AKS)

Umowa SLA czasu pracy to opcjonalna funkcja, która umożliwia dostęp do klastra z wyższym poziomem wsparcia finansowego. Umowa SLA gwarantująca dostępność przez 99,95% czasu dostępności punktu końcowego serwera interfejsu API kubernetes dla klastrów, które korzystają z usługi [Strefy dostępności, i][availability-zones] 99,9% dostępności dla klastrów, które nie korzystają z Strefy dostępności. W celu zapewnienia spełniania wymagań umowy SLA usługi AKS są używane repliki węzłów master w domenach aktualizacji i błędów.

Klienci, którzy potrzebują umowy SLA w celu spełnienia wymagań dotyczących zgodności lub rozszerzenia umowy SLA na swoich użytkowników końcowych, powinni włączyć tę funkcję. Klienci z obciążeniami o krytycznym znaczeniu, którzy będą korzystać z wyższej umowy SLA czasu pracy, również mogą skorzystać na tym. Korzystanie z funkcji umowy SLA dotyczącej czasu Strefy dostępności zapewnia wyższą dostępność dla czasu pracy serwera interfejsu API Kubernetes.  

Klienci mogą nadal tworzyć nieograniczone bezpłatne klastry z celem poziomu usług (SLO) na poziomie 99,5% i w razie potrzeby wybrać preferowany cel poziomu usług lub czas pracy umowy SLA.

> [!Important]
> W przypadku klastrów z blokadą ruchu wychodzącego zobacz [Ograniczanie ruchu wychodzącego](limit-egress-traffic.md) w celu otwarcia odpowiednich portów.

## <a name="region-availability"></a>Dostępność w danym regionie

* Umowa SLA czasu pracy jest dostępna w regionach publicznych i Azure Government regionach, w których [jest obsługiwana przez usługę AKS.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)
* Umowa SLA czasu pracy jest dostępna dla prywatnych klastrów [usługi AKS we][private-clusters] wszystkich regionach publicznych, w których usługa AKS jest obsługiwana.

## <a name="sla-terms-and-conditions"></a>Warunki i postanowienia umowy SLA

Umowa SLA czasu pracy to płatna funkcja, która jest włączana dla klastra. Ceny umowy SLA dotyczące czasu pracy są określane na podstawie liczby dyskretnych klastrów, a nie rozmiaru poszczególnych klastrów. Aby uzyskać więcej [informacji, możesz wyświetlić](https://azure.microsoft.com/pricing/details/kubernetes-service/) szczegóły cennika umowy SLA dotyczącej czasu pracy.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

* Instalowanie [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.8.0 lub nowszej

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Tworzenie nowego klastra z umowami SLA czasu pracy

Aby utworzyć nowy klaster z umowami SLA czas pracy, należy użyć interfejsu wiersza polecenia platformy Azure.

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *lokalizacji eastus:*

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```
Użyj polecenia [`az aks create`][az-aks-create] , aby utworzyć klaster usługi AKS. W poniższym przykładzie pokazano tworzenie klastra o nazwie *myAKSCluster* z jednym węzłem. Ta operacja trwa kilka minut:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON. Poniższy fragment kodu JSON przedstawia płatną warstwę dla sku, wskazując, że klaster jest włączony z umowami SLA czasu pracy:

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Modyfikowanie istniejącego klastra w celu korzystania z umowy SLA dotyczącej czasu pracy

Możesz opcjonalnie zaktualizować istniejące klastry, aby korzystać z umowy SLA czasu pracy.

Jeśli klaster usługi AKS został utworzony w poprzednich krokach, usuń grupę zasobów:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Utwórz nową grupę zasobów:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Utwórz nowy klaster i nie używaj umowy SLA czasu pracy:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

Użyj polecenia [`az aks update`][az-aks-update] , aby zaktualizować istniejący klaster:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
 ```

 Poniższy fragment kodu JSON przedstawia płatną warstwę dla sku, wskazując, że klaster jest włączony z umowami SLA czasu pracy:

 ```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  ```

## <a name="opt-out-of-uptime-sla"></a>Rezygnacja z umowy SLA czasu pracy

Możesz zaktualizować klaster, aby zmienić warstwę na warstwę Bezpłatna i zrezygnować z umowy SLA dotyczącej czasu pracy.

```azurecli-interactive
# Update an existing cluster to opt out of Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --no-uptime-sla
 ```

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć nalicznych opłat, wyczyść wszystkie utworzone zasoby. Aby usunąć klaster, użyj [`az group delete`][az-group-delete] polecenia , aby usunąć grupę zasobów usługi AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Następne kroki

Użyj [Strefy dostępności,][availability-zones] aby zwiększyć wysoką dostępność obciążeń klastra usługi AKS.

Skonfiguruj klaster, aby [ograniczyć ruch wychodzący.](limit-egress-traffic.md)

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az_aks_create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-group-delete]: /cli/azure/group#az_group_delete
[private-clusters]: private-clusters.md
