---
title: Uruchamianie i zatrzymywanie Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zatrzymać lub uruchomić Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 2d3c946bc2f98b0c06fe33dcaaa77a5399f6d56b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782733"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Zatrzymywanie i uruchamianie klastra Azure Kubernetes Service (AKS)

Obciążenia usługi AKS mogą nie działać w sposób ciągły, na przykład klaster projektowy, który jest używany tylko w godzinach pracy. Prowadzi to do sytuacji, Azure Kubernetes Service klastra usługi (AKS) może być bezczynny i nie uruchamiać więcej niż składników systemu. Można zmniejszyć zużycie zasobów klastra przez skalowanie wszystkich pul [ `System` ](use-system-pools.md) węzłów do [ `User` 0](scale-cluster.md#scale-user-node-pools-to-0), ale pula jest nadal wymagana do uruchamiania składników systemu, gdy klaster jest uruchomiony. Aby jeszcze bardziej zoptymalizować koszty w tych okresach, można całkowicie wyłączyć (zatrzymać) klaster. Ta akcja całkowicie zatrzyma płaszczyznę sterowania i węzły agentów, co pozwoli zaoszczędzić na wszystkich kosztach obliczeń przy jednoczesnym zachowaniu wszystkich obiektów i stanu klastra przechowywanych dla programu po jego uruchomieniu ponownie. Następnie możesz uruchomić klaster w miejscu, z którego zjedziesz po weekendzie, lub uruchomić klaster tylko podczas uruchamiania zadań wsadowych.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub [interfejsu Azure Portal][aks-quickstart-portal].

### <a name="limitations"></a>Ograniczenia

W przypadku korzystania z funkcji uruchamiania/zatrzymania klastra obowiązują następujące ograniczenia:

- Ta funkcja jest obsługiwana tylko Virtual Machine Scale Sets klastrach kopii zapasowej.
- Stan klastra zatrzymanych klastrów usługi AKS jest zachowywany przez maksymalnie 12 miesięcy. Jeśli klaster jest zatrzymany przez więcej niż 12 miesięcy, nie można odzyskać stanu klastra. Aby uzyskać więcej informacji, zobacz [zasady obsługi AKS.](support-policies.md)
- Możesz uruchomić lub usunąć tylko zatrzymany klaster usługi AKS. Aby wykonać dowolną operację, na przykład skalowanie lub uaktualnianie, najpierw uruchom klaster.

## <a name="stop-an-aks-cluster"></a>Zatrzymywanie klastra usługi AKS

Możesz użyć polecenia `az aks stop` , aby zatrzymać uruchomione węzły klastra AKS i płaszczyznę sterowania. Poniższy przykład zatrzymuje klaster o *nazwie myAKSCluster:*

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

Aby sprawdzić, kiedy klaster został zatrzymany, możesz użyć polecenia [az aks show][az-aks-show] i potwierdzić, że polecenie jest wyświetlane tak, jak w `powerState` poniższych danych `Stopped` wyjściowych:

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

Jeśli na `provisioningState` `Stopping` ilustracji widać, że klaster nie został jeszcze w pełni zatrzymany.

> [!IMPORTANT]
> Jeśli używasz budżetów [przerw w działaniu zasobników,](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) operacja zatrzymania może trwać dłużej, ponieważ proces opróżniania zajmie więcej czasu.

## <a name="start-an-aks-cluster"></a>Uruchamianie klastra usługi AKS

Możesz użyć polecenia , aby uruchomić węzły i płaszczyznę sterowania `az aks start` zatrzymanego klastra usługi AKS. Klaster jest ponownie uruchamiany z poprzednim stanem płaszczyzny sterowania i liczbą węzłów agenta.  
Poniższy przykład uruchamia klaster o *nazwie myAKSCluster:*

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

Aby sprawdzić, kiedy klaster został uruchomiony, możesz użyć [polecenia az aks show][az-aks-show] i potwierdzić, że polecenie jest wyświetlane w poniższych danych `powerState` `Running` wyjściowych:

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

Jeśli na `provisioningState` `Starting` ilustracji widać, że klaster nie został jeszcze w pełni uruchomiony.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak skalować `User` pule do 0, zobacz [Scale pools to 0 (Skalowanie `User` pul do 0).](scale-cluster.md#scale-user-node-pools-to-0)
- Aby dowiedzieć się, jak zaoszczędzić koszty przy użyciu wystąpień typu spot, zobacz [Dodawanie puli węzłów typu spot do usługi AKS.](spot-node-pool.md)
- Aby dowiedzieć się więcej na temat zasad obsługi AKS, zobacz [AKS support policies (Zasady obsługi AKS).](support-policies.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-show]: /cli/azure/aks#az_aks_show
