---
title: Przenoszenie sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) platformy Azure do innego regionu platformy Azure przy użyciu Azure PowerShell
description: Użyj szablonu Azure Resource Manager, aby przenieść grupę zabezpieczeń sieci platformy Azure z jednego regionu świadczenia usługi Azure do innego przy użyciu Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 04abc051cec8a6fb38ce6aa8f5347ae06cb8bd1d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019756"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Przenoszenie sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) platformy Azure do innego regionu przy użyciu Azure PowerShell

Istnieją różne scenariusze, w których należy przenieść istniejące sieciowych grup zabezpieczeń z jednego regionu do innego. Na przykład możesz chcieć utworzyć sieciowej grupy zabezpieczeń z tą samą konfiguracją i regułami zabezpieczeń na potrzeby testowania. Możesz również przenieść sieciowej grupy zabezpieczeń do innego regionu w ramach planowania odzyskiwania po awarii.

Grup zabezpieczeń platformy Azure nie można przenosić z jednego regionu do innego. Można jednak użyć szablonu Azure Resource Manager, aby wyeksportować istniejące reguły konfiguracji i zabezpieczeń sieciowej grupy zabezpieczeń.  Następnie można przemieścić zasób w innym regionie, eksportując sieciowej grupy zabezpieczeń do szablonu, modyfikując parametry w celu dopasowania do regionu docelowego, a następnie wdrożyć szablon w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżer zasobów i szablonów, zobacz [Eksportowanie grup zasobów do szablonów](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że grupa zabezpieczeń sieci platformy Azure znajduje się w regionie świadczenia usługi Azure, z którego chcesz przenieść.

- Nie można przenosić grup zabezpieczeń sieci platformy Azure między regionami.  Należy skojarzyć nowe sieciowej grupy zabezpieczeń z zasobami w regionie docelowym.

- Aby wyeksportować konfigurację sieciowej grupy zabezpieczeń i wdrożyć szablon w celu utworzenia sieciowej grupy zabezpieczeń w innym regionie, musisz mieć rolę współautor sieci lub wyższą.
   
- Zidentyfikuj układ sieci źródłowej i wszystkie aktualnie używane zasoby. Ten układ obejmuje, ale nie jest ograniczony do modułów równoważenia obciążenia, publicznych adresów IP i sieci wirtualnych.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie sieciowych grup zabezpieczeń w regionie docelowym, który jest używany. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że Twoja subskrypcja ma wystarczającą ilość zasobów, aby obsłużyć Dodawanie sieciowych grup zabezpieczeń dla tego procesu.  Zobacz [Azure subscription and service limits, quotas, and constraints](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits) (Limity, przydziały i ograniczenia usługi i subskrypcji platformy Azure).


## <a name="prepare-and-move"></a>Przygotowywanie i przenoszenie
Poniższe kroki pokazują, jak przygotować grupę zabezpieczeń sieci dla reguły konfiguracji i zabezpieczeń przenieść przy użyciu szablonu Menedżer zasobów i przenieść reguły konfiguracji i zabezpieczeń sieciowej grupy zabezpieczeń do regionu docelowego przy użyciu Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Eksportowanie szablonu i wdrażanie go ze skryptu

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Uzyskaj identyfikator zasobu sieciowej grupy zabezpieczeń, który chcesz przenieść do regionu docelowego i umieść go w zmiennej przy użyciu polecenia [Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Wyeksportuj Źródło sieciowej grupy zabezpieczeń do pliku JSON do katalogu, w którym wykonujesz polecenie [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. Pobrany plik zostanie nazwany po grupie zasobów, z której został wyeksportowany zasób.  Znajdź plik, który został wyeksportowany z polecenia o nazwie **\<resource-group-name> . JSON** i otwórz go w wybranym edytorze:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Aby edytować parametr nazwy sieciowej grupy zabezpieczeń, Zmień właściwość **DefaultValue** źródłowej nazwy sieciowej grupy zabezpieczeń na nazwę elementu docelowego sieciowej grupy zabezpieczeń, upewnij się, że nazwa jest cudzysłowem:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Aby edytować region docelowy, w którym zostaną przeniesione reguły konfiguracji i zabezpieczeń sieciowej grupy zabezpieczeń, Zmień właściwość **Location** w obszarze **zasoby**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Aby uzyskać kody lokalizacji regionu, możesz użyć polecenia cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) programu Azure PowerShell, uruchamiając następujące polecenie:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Możesz również zmienić inne parametry w pliku **\<resource-group-name> JSON** w przypadku wybrania opcji i opcjonalne, w zależności od wymagań:

    * **Reguły zabezpieczeń** — można edytować, które reguły są wdrażane w docelowym sieciowej grupy zabezpieczeń, dodając lub usuwając reguły do sekcji **securityRules** w pliku **\<resource-group-name> JSON** :

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Aby ukończyć Dodawanie lub usuwanie reguł w docelowym sieciowej grupy zabezpieczeń, należy również edytować niestandardowe typy reguł na końcu pliku **\<resource-group-name> JSON** w formacie poniższego przykładu:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Zapisz plik **\<resource-group-name> JSON** .

10. Utwórz grupę zasobów w regionie docelowym dla sieciowej grupy zabezpieczeń docelowego do wdrożenia przy użyciu polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Wdróż edytowany plik **\<resource-group-name> JSON** w grupie zasobów utworzonej w poprzednim kroku przy użyciu polecenia [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Aby sprawdzić, czy zasoby zostały utworzone w regionie docelowym, użyj polecenie [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) i [Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Odrzuć 

Jeśli po wdrożeniu chcesz zacząć od początku lub odrzucić sieciowej grupy zabezpieczeń w elemencie docelowym, Usuń grupę zasobów, która została utworzona w miejscu docelowym, a przeniesiona sieciowej grupy zabezpieczeń zostanie usunięta.  Aby usunąć grupę zasobów, użyj polecenie [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieciowej grupy zabezpieczeń, Usuń źródło sieciowej grupy zabezpieczeń lub grupę zasobów, użyj [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) lub [Remove-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono grupę zabezpieczeń sieci platformy Azure z jednego regionu do innego i wyczyszczono zasoby źródłowe.  Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
