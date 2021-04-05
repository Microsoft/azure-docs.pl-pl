---
title: Usuwanie klastra Service Fabric na platformie Azure
description: W ramach tego samouczka dowiesz się, jak usunąć klaster usługi Service Fabric hostowany na platformie Azure razem ze wszystkimi zasobami. Istnieje możliwość usunięcia grupy zasobów zawierającej klaster lub selektywnego usuwania zasobów.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 60c43adddd4799a639c2d58e0293086eb1776588
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791091"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Samouczek: usuwanie klastra usługi Service Fabric uruchomionego na platformie Azure

Ten samouczek jest częścią piątą serii i pokazuje, jak usunąć klaster Service Fabric uruchomiony na platformie Azure. Aby całkowicie usunąć klaster usługi Service Fabric, musisz również usunąć wszystkie zasoby używane przez klaster. Dostępne są dwie opcje: usunięcie grupy zasobów, do której należy klaster (co powoduje usunięcie zasobu klastra i wszystkich innych zasobów w grupie zasobów) lub usunięcie konkretnie zasobu klastra i jego powiązanych zasobów (lecz nie innych zasobów w grupie zasobów).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Usuwanie grupy zasobów i jej wszystkich zasobów
> * Selektywne usuwanie zasobów z grupy zasobów

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) na platformie Azure przy użyciu szablonu
> * [Monitorowanie klastra](service-fabric-tutorial-monitor-cluster.md)
> * [Skalowanie klastra na zewnątrz lub do wewnątrz](service-fabric-tutorial-scale-cluster.md)
> * [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)
> * Usuwanie klastra


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Zainstaluj program [Azure PowerShell](/powershell/azure//install-az-ps) lub [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) na platformie Azure

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Usuwanie grupy zasobów zawierającej klaster usługi Service Fabric
Najprostszym sposobem na usunięcie klastra i wszystkich wykorzystywanych przez niego zasobów jest usunięcie grupy zasobów.

Zaloguj się do platformy Azure i wybierz identyfikator subskrypcji, z którym chcesz usunąć klaster.  Identyfikator subskrypcji można uzyskać po zalogowaniu się do [witryny Azure Portal](https://portal.azure.com). Usuń grupę zasobów i wszystkie zasoby klastra za pomocą polecenia cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) lub [AZ Group Delete](/cli/azure/group) .

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>Selektywne usuwanie zasobu klastra i skojarzonych zasobów
Jeśli grupa zasobów zawiera tylko zasoby powiązane z klastrem usługi Service Fabric do usunięcia, to łatwiej usunąć całą grupę zasobów. Jeśli chcesz selektywnie usunąć zasoby z grupy zasobów i zachować zasoby nieskojarzone z klastrem, wykonaj następujące kroki.

Wyświetl zasoby w grupie zasobów:

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

Dla każdego z zasobów do usunięcia uruchom następujący skrypt:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

Aby usunąć zasób klastra, uruchom następujący skrypt:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Usuwanie grupy zasobów i jej wszystkich zasobów
> * Selektywne usuwanie zasobów z grupy zasobów

Po ukończeniu tego samouczka spróbuj wykonać następujące czynności:
* Dowiedz się, jak przeprowadzić inspekcję klastra usługi Service Fabric i zarządzać nim przy użyciu programu [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
* Dowiedz się, jak [zastosować poprawki systemu operacyjnego Windows](service-fabric-patch-orchestration-application.md) w węzłach klastra.
* Dowiedz się, jak agregować i zbierać zdarzenia z [klastrów systemu Windows](service-fabric-diagnostics-event-aggregation-wad.md) oraz jak [skonfigurować usługę Log Analytics](service-fabric-diagnostics-oms-setup.md) pod kątem monitorowania zdarzeń klastra.
