---
title: Uruchamianie i zatrzymywanie usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zatrzymać lub uruchomić klaster usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 87d51f9c1d084faf79c7ec1cf1255a6fb3c8245d
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201004"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Zatrzymywanie i uruchamianie klastra usługi Azure Kubernetes Service (AKS)

Obciążenia AKS mogą nie być wykonywane w sposób ciągły, na przykład w przypadku klastra programistycznego, który jest używany tylko w godzinach pracy. Prowadzi to do czasu, w którym klaster usługi Azure Kubernetes Service (AKS) może być bezczynny, co nie działa dłużej niż składniki systemowe. Można zmniejszyć rozmiary klastra, przeskalowane [wszystkie `User` Pule węzłów na 0](scale-cluster.md#scale-user-node-pools-to-0), ale [ `System` Pula](use-system-pools.md) jest nadal wymagana do uruchamiania składników systemowych, gdy klaster jest uruchomiony. Aby zoptymalizować koszty w ciągu tych okresów, można całkowicie wyłączyć (zatrzymać) klaster. Ta akcja spowoduje całkowite zatrzymanie płaszczyzny kontroli i węzłów agenta, co pozwala zaoszczędzić na wszystkich kosztach obliczeniowych, zachowując jednocześnie wszystkie obiekty i stan klastra przechowywane dla momentu ponownego uruchomienia. Następnie możesz wybrać odpowiednie miejsce po zakończeniu weekendu lub, aby klaster działał tylko podczas wykonywania zadań wsadowych.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

### <a name="limitations"></a>Ograniczenia

W przypadku korzystania z funkcji uruchamiania/zatrzymywania klastra obowiązują następujące ograniczenia:

- Ta funkcja jest obsługiwana tylko w przypadku Virtual Machine Scale Sets klastrów.
- Stan klastra zatrzymanego klastra AKS jest zachowywany przez maksymalnie 12 miesięcy. Jeśli klaster jest zatrzymany przez ponad 12 miesięcy, nie można odzyskać stanu klastra. Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej AKS](support-policies.md).
- Można uruchomić lub usunąć zatrzymany klaster AKS. Aby wykonać dowolną operację, taką jak skalowanie lub uaktualnianie, najpierw należy uruchomić klaster.

## <a name="stop-an-aks-cluster"></a>Zatrzymaj klaster AKS

Możesz użyć polecenia, `az aks stop` Aby zatrzymać węzły i płaszczyznę kontroli uruchomionego klastra AKS. W poniższym przykładzie zostaje zatrzymany klaster o nazwie *myAKSCluster*:

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

Możesz sprawdzić, czy klaster został zatrzymany za pomocą polecenia [AZ AKS show][az-aks-show] i potwierdzając, `powerState` jak pokazano `Stopped` na poniższym wyjściu:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Jeśli `provisioningState` tak, `Stopping` oznacza to, że klaster nie został jeszcze w pełni zatrzymany.

> [!IMPORTANT]
> W przypadku korzystania z [budżetów przerwy](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) operacja zatrzymania może trwać dłużej, ponieważ proces opróżniania zajmie więcej czasu.

## <a name="start-an-aks-cluster"></a>Uruchom klaster AKS

Możesz użyć polecenia, `az aks start` Aby uruchomić zatrzymane węzły klastra AKS i płaszczyznę kontroli. Klaster jest uruchamiany ponownie z poprzednim stanem płaszczyzny kontroli i liczbą węzłów agenta.  
W poniższym przykładzie jest uruchamiany klaster o nazwie *myAKSCluster*:

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

Możesz sprawdzić, czy klaster został uruchomiony przy użyciu polecenia [AZ AKS show][az-aks-show] i potwierdzając, `powerState` `Running` jak pokazano na poniższym wyjściu:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Jeśli `provisioningState` tak, `Starting` oznacza to, że klaster nie został jeszcze w pełni uruchomiony.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak skalować `User` Pule do wartości 0, zobacz [skalowanie `User` pul do wartości 0](scale-cluster.md#scale-user-node-pools-to-0).
- Aby dowiedzieć się, jak zaoszczędzić koszty przy użyciu wystąpień obliczeniowych, zobacz [Dodawanie puli węzłów dodatkowych do AKS](spot-node-pool.md).
- Aby dowiedzieć się więcej na temat zasad pomocy technicznej AKS, zobacz [AKS support policies](support-policies.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-show]: /cli/azure/aks#az_aks_show
