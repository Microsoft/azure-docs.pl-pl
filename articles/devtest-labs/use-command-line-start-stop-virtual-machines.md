---
title: Korzystanie z narzędzi wiersza polecenia do uruchamiania i zatrzymywania maszyn wirtualnych Azure DevTest Labs
description: Dowiedz się, jak uruchamiać i zatrzymywać maszyny wirtualne w Azure DevTest Labs przy użyciu narzędzi wiersza polecenia.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e5a42658f2b83f101271f158c9af70833601b56d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476420"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Uruchamianie i zatrzymywanie Azure DevTest Labs maszyn wirtualnych przy użyciu narzędzi wiersza polecenia
W tym artykule pokazano, jak używać Azure PowerShell lub interfejsu wiersza polecenia platformy Azure do uruchamiania lub zatrzymywania maszyn wirtualnych w laboratorium w Azure DevTest Labs. Aby zautomatyzować te operacje, można utworzyć skrypty środowiska PowerShell/interfejsu wiersza polecenia. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
Azure DevTest Labs to sposób tworzenia szybkich, łatwych i oszczędnych środowisk deweloperskich i testowych. Umożliwia zarządzanie kosztami, szybkie Inicjowanie obsługi maszyn wirtualnych i minimalizowanie odpadów.  W Azure Portal są wbudowane funkcje, które umożliwiają skonfigurowanie maszyn wirtualnych w laboratorium do automatycznego uruchamiania i zatrzymywania w określonych godzinach. 

Jednak w niektórych scenariuszach można zautomatyzować uruchamianie i zatrzymywanie maszyn wirtualnych ze skryptów programu PowerShell/interfejsu wiersza polecenia. Zapewnia ona pewną elastyczność w zakresie uruchamiania i zatrzymywania poszczególnych maszyn w dowolnym momencie, a nie w określonych godzinach. Poniżej przedstawiono niektóre sytuacje, w których wykonywanie tych zadań przy użyciu skryptów byłoby przydatne.

- W przypadku korzystania z aplikacji 3-warstwowej jako części środowiska testowego warstwy muszą zostać uruchomione w sekwencji. 
- Wyłącz maszynę wirtualną w przypadku spełnienia kryteriów niestandardowych, aby zaoszczędzić pieniądze. 
- Użyj jej jako zadania w ramach przepływu pracy ciągłej integracji/ciągłego wdrażania, aby zacząć od początku przepływu, użyj maszyn wirtualnych jako maszyn kompilacji, maszyn testowych lub infrastruktury, a następnie Zatrzymaj maszyny wirtualne po zakończeniu procesu. Przykładem może być niestandardowa fabryka obrazów z Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> Poniższy skrypt używa Azure PowerShell AZ module. 

Poniższy skrypt programu PowerShell uruchamia maszynę wirtualną w laboratorium. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) jest podstawowym fokusem dla tego skryptu. Parametr **ResourceID** to w pełni kwalifikowany identyfikator zasobu dla maszyny wirtualnej w laboratorium. W parametrze **akcji** jest ustawiana opcja **uruchamiania** lub **zatrzymywania** w zależności od tego, co jest potrzebne.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
[Interfejs wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) jest innym sposobem automatyzacji uruchamiania i zatrzymywania maszyn wirtualnych z DevTest Labs. Interfejs wiersza polecenia platformy Azure można [zainstalować](/cli/azure/install-azure-cli?view=azure-cli-latest) w różnych systemach operacyjnych. Poniższy skrypt zawiera polecenia służące do uruchamiania i zatrzymywania maszyny wirtualnej w laboratorium. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Następne kroki
Aby wykonać te operacje Azure Portal, zobacz następujący artykuł: [Uruchom ponownie maszynę wirtualną](devtest-lab-restart-vm.md).
