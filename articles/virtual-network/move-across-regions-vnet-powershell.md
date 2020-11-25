---
title: Przenoszenie sieci wirtualnej platformy Azure do innego regionu platformy Azure przy użyciu Azure PowerShell
description: Przenoszenie sieci wirtualnej platformy Azure z jednego regionu świadczenia usługi Azure do innego przy użyciu szablonu Menedżer zasobów i Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: e13164c3ec6049a8ae3954528a02d20e313dd883
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008127"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Przenoszenie sieci wirtualnej platformy Azure do innego regionu przy użyciu Azure PowerShell

Istnieją różne scenariusze dotyczące przechodzenia istniejącej sieci wirtualnej platformy Azure z jednego regionu do innego. Na przykład możesz chcieć utworzyć sieć wirtualną o tej samej konfiguracji do testowania i dostępności jako istniejącą sieć wirtualną. Możesz też chcieć przenieść produkcyjną sieć wirtualną do innego regionu w ramach planowania odzyskiwania po awarii.

Możesz użyć szablonu Azure Resource Manager, aby zakończyć przenoszenie sieci wirtualnej do innego regionu. W tym celu należy wyeksportować sieć wirtualną do szablonu, modyfikując parametry w celu dopasowania do regionu docelowego, a następnie wdrożyć szablon w nowym regionie. Aby uzyskać więcej informacji na temat szablonów Menedżer zasobów, zobacz [Eksportowanie grup zasobów do szablonów](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że sieć wirtualna znajduje się w regionie świadczenia usługi Azure, z którego chcesz przejść.

- Aby wyeksportować sieć wirtualną i wdrożyć szablon w celu utworzenia sieci wirtualnej w innym regionie, musisz mieć rolę współautor sieci lub wyższą.

- Komunikacja równorzędna sieci wirtualnych nie zostanie utworzona i nie powiedzie się, jeśli nadal znajdują się one w szablonie. Przed wyeksportowaniem szablonu należy usunąć wszystkie równorzędne sieci wirtualne. Następnie można je ponownie ustanowić po przeniesieniu sieci wirtualnej.
    
- Zidentyfikuj układ sieci źródłowej i wszystkie aktualnie używane zasoby. Ten układ obejmuje, ale nie jest ograniczony do modułów równoważenia obciążenia, sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) i publicznych adresów IP.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie sieci wirtualnych w regionie docelowym. Aby włączyć wymagany limit przydziału, skontaktuj się z pomocą techniczną.

- Upewnij się, że Twoja subskrypcja ma wystarczającą ilość zasobów, aby obsłużyć Dodawanie sieci wirtualnych dla tego procesu. Aby uzyskać więcej informacji, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usług i subskrypcji platformy Azure)](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Przygotuj do przeniesienia
Ta sekcja umożliwia przygotowanie sieci wirtualnej do przenoszenia przy użyciu szablonu Menedżer zasobów. Następnie można przenieść sieć wirtualną do regionu docelowego za pomocą poleceń Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby wyeksportować sieć wirtualną i wdrożyć docelową sieć wirtualną przy użyciu programu PowerShell, wykonaj następujące czynności:

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) , a następnie postępuj zgodnie z instrukcjami wyświetlanymi na ekranie:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Uzyskaj identyfikator zasobu sieci wirtualnej, która ma zostać przeniesiona do regionu docelowego, a następnie umieść go w zmiennej przy użyciu polecenia [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Wyeksportuj źródłową sieć wirtualną do pliku JSON w katalogu, w którym wykonujesz polecenie [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. Pobrany plik ma taką samą nazwę jak grupa zasobów, z której został wyeksportowany zasób. Znajdź plik *\<resource-group-name> JSON* , który został wyeksportowany za pomocą polecenia, a następnie otwórz go w edytorze:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Aby edytować parametr nazwy sieci wirtualnej, Zmień właściwość **DefaultValue** źródłowej nazwy sieci wirtualnej na nazwę docelowej sieci wirtualnej. Upewnij się, że nazwa jest ujęta w cudzysłów.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Aby edytować region docelowy, w którym zostanie przeniesiona Sieć wirtualna, Zmień właściwość **Location** w obszarze zasoby:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
1. Aby uzyskać kody lokalizacji regionu, możesz użyć polecenia cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) programu Azure PowerShell, uruchamiając następujące polecenie:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. Obowiązkowe Możesz również zmienić inne parametry w pliku *\<resource-group-name> JSON* , w zależności od wymagań:

    * **Przestrzeń adresowa**: przed zapisaniem pliku można zmienić przestrzeń adresową sieci wirtualnej, modyfikując **resources**  >  sekcję **addressSpace** zasobów i zmieniając właściwość **addressPrefixes** :

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },
        ```

    * **Podsieć**: można zmienić lub dodać nazwę podsieci i przestrzeń adresową podsieci, zmieniając sekcję **podsieci** pliku. Nazwę podsieci można zmienić, zmieniając właściwość **name** . I można zmienić przestrzeń adresową podsieci, zmieniając właściwość **addressPrefix** :

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        Aby zmienić prefiks adresu, edytuj plik w dwóch miejscach: w kodzie w poprzedniej sekcji i w sekcji **Type** poniższego kodu. Zmień właściwość **addressPrefix** w poniższym kodzie, aby dopasować Właściwość **addressPrefix** w kodzie w poprzedniej sekcji.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. Zapisz plik *\<resource-group-name> JSON* .

1. Utwórz grupę zasobów w regionie docelowym dla docelowej sieci wirtualnej, która ma zostać wdrożona przy użyciu polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Wdróż edytowany plik *\<resource-group-name> JSON* w grupie zasobów utworzonej w poprzednim kroku przy użyciu polecenia [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Aby sprawdzić, czy zasoby zostały utworzone w regionie docelowym, użyj polecenie [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) i [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Usuwanie sieci wirtualnej lub grupy zasobów 

Po wdrożeniu sieci wirtualnej, aby rozpocząć pracę lub odrzucić sieć wirtualną w regionie docelowym, Usuń grupę zasobów utworzoną w regionie docelowym, a przeniesiona Sieć wirtualna zostanie usunięta. 

Aby usunąć grupę zasobów, użyj polecenie [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieci wirtualnej, wykonaj jedną z następujących czynności:

* Usuń grupę zasobów za pomocą polecenia [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Usuń źródłową sieć wirtualną za pomocą polecenia [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono sieć wirtualną z jednego regionu do innego za pomocą programu PowerShell, a następnie oczyszczono niepotrzebne zasoby źródłowe. Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:

- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
