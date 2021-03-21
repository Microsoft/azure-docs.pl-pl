---
title: Dodawanie i usuwanie typów węzłów w klastrze zarządzanym Service Fabric (wersja zapoznawcza)
description: W tym samouczku dowiesz się, jak dodawać i usuwać typy węzłów w klastrze zarządzanym Service Fabric.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: bb33512652677fc4e46d8ba3668dca985bbcfe01
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791243"
---
# <a name="tutorial-add-and-remove-node-types-from-a-service-fabric-managed-cluster-preview"></a>Samouczek: Dodawanie i usuwanie typów węzłów z klastra zarządzanego Service Fabric (wersja zapoznawcza)

W tej serii samouczków omówiono następujące kwestie:

> [!div class="checklist"]
> * [Jak wdrożyć klaster zarządzany Service Fabric](tutorial-managed-cluster-deploy.md)
> * [Jak skalować w poziomie Service Fabric zarządzany klaster](tutorial-managed-cluster-scale.md)
> * Jak dodać i usunąć węzły w klastrze zarządzanym Service Fabric
> * [Jak wdrożyć aplikację w klastrze zarządzanym Service Fabric](tutorial-managed-cluster-deploy-app.md)

Ta część serii obejmuje następujące elementy:

> [!div class="checklist"]
> * Dodawanie typu węzła do Service Fabric zarządzanego klastra
> * Usuwanie typu węzła z Service Fabric zarządzanego klastra

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster zarządzany Service Fabric (zobacz [*wdrażanie zarządzanego klastra*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) lub nowszy (zobacz [*Install Azure PowerShell*](/powershell/azure/install-az-ps)).

## <a name="add-a-node-type-to-a-service-fabric-managed-cluster"></a>Dodawanie typu węzła do Service Fabric zarządzanego klastra

Można dodać typ węzła do Service Fabric zarządzanego klastra za pomocą szablonu Azure Resource Manager, programu PowerShell lub interfejsu wiersza polecenia. W tym samouczku dodamy typ węzła przy użyciu Azure PowerShell.

Aby utworzyć nowy typ węzła, należy zdefiniować trzy właściwości:

* **Nazwa typu węzła**: nazwa, która jest unikatowa dla wszystkich istniejących typów węzłów w klastrze.
* **Liczba wystąpień**: początkowa liczba węzłów nowego typu węzła.
* **Rozmiar maszyny wirtualnej**: jednostka SKU maszyny wirtualnej dla węzłów. Jeśli nie zostanie określony, zostanie użyta wartość domyślna *Standard_D2* .

> [!NOTE]
> Jeśli dodawany typ węzła to pierwszy lub jedyny typ węzła w klastrze, należy użyć właściwości podstawowej.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "nt2"
$vmSize = "Standard_D2_v2"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeTypeName -InstanceCount 3 -vmSize $vmSize
```

## <a name="remove-a-node-type-from-a-service-fabric-managed-cluster"></a>Usuwanie typu węzła z Service Fabric zarządzanego klastra

Aby usunąć typ węzła z Service Fabric zarządzanego klastra, należy użyć programu PowerShell lub interfejsu wiersza polecenia. W tym samouczku usuniemy typ węzła przy użyciu Azure PowerShell.

> [!NOTE]
> Nie można usunąć typu węzła podstawowego, jeśli jest to jedyny typ węzła podstawowego w klastrze.  

Aby usunąć typ węzła:

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "myCluster"
$nodeTypeName = "nt2"

Remove-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName  -Name $nodeTypeName
```

## <a name="next-steps"></a>Następne kroki

 W tej sekcji dodaliśmy i usunięto typy węzłów. Aby dowiedzieć się, jak wdrożyć aplikację w klastrze zarządzanym Service Fabric, zobacz:

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji w klastrze zarządzanym Service Fabric](tutorial-managed-cluster-deploy-app.md)
