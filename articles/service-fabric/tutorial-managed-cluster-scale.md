---
title: Skalowanie w poziomie Service Fabric klastra zarządzanego (wersja zapoznawcza)
description: W tym samouczku dowiesz się, jak skalować typ węzła w klastrze zarządzanym Service Fabric.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 01b299744d462496296884211eff08b7a9c64687
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316139"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster-preview"></a>Samouczek: skalowanie w poziomie Service Fabric klastra zarządzanego (wersja zapoznawcza)

W tej serii samouczków omówiono następujące kwestie:

> [!div class="checklist"]
> * [Jak wdrożyć klaster zarządzany Service Fabric.](tutorial-managed-cluster-deploy.md)
> * Jak skalować w poziomie Service Fabric zarządzany klaster
> * [Jak dodać i usunąć typy węzłów w klastrze zarządzanym Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)
> * [Jak wdrożyć aplikację w klastrze zarządzanym Service Fabric](tutorial-managed-cluster-deploy-app.md)

Ta część serii obejmuje następujące elementy:

> [!div class="checklist"]
> * Skalowanie węzła klastra zarządzanego Service Fabric

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster zarządzany Service Fabric (zobacz [*wdrażanie zarządzanego klastra*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps?preserve-view=true&view=azps-4.7.0#azservicefabric) lub nowszy (zobacz [*Install Azure PowerShell*](/powershell/azure/install-az-ps?preserve-view=true&view=azps-4.7.0)).

## <a name="scale-a-service-fabric-managed-cluster"></a>Skalowanie klastra zarządzanego Service Fabric
Zmień liczbę wystąpień, aby zwiększyć lub zmniejszyć liczbę węzłów w typie węzła, który ma być skalowany. Nazwy typów węzłów można znaleźć w szablonie Azure Resource Manager (szablon ARM) we wdrożeniu klastra lub w Service Fabric Explorer.  

> [!NOTE]
> Jeśli typ węzła jest podstawowy, nie będzie można przejść poniżej 3 węzłów dla podstawowego klastra SKU i 5 węzłów dla standardowego klastra SKU.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

Klaster rozpocznie uaktualnianie automatycznie i po kilku minutach zobaczysz dodatkowe węzły.

## <a name="next-steps"></a>Następne kroki

W tym kroku przeskaluje typ węzła w klastrze zarządzanym Service Fabric. Aby dowiedzieć się więcej o dodawaniu i usuwaniu typów węzłów, zobacz:

> [!div class="nextstepaction"]
> [Dodawanie i usuwanie typów węzłów w klastrze zarządzanym Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)