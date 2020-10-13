---
title: Przenoszenie konfiguracji publicznego adresu IP platformy Azure do innego regionu platformy Azure przy użyciu Azure PowerShell
description: Użyj szablonu Azure Resource Manager, aby przenieść konfigurację publicznego adresu IP platformy Azure z jednego regionu platformy Azure do innego przy użyciu Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 4f72c22ee26375e025af7b3a391fdd45187e7041
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84703744"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-azure-powershell"></a>Przenoszenie konfiguracji publicznego adresu IP platformy Azure do innego regionu przy użyciu Azure PowerShell

Istnieją różne scenariusze, w których należy przenieść istniejące konfiguracje publicznego adresu IP platformy Azure z jednego regionu do innego. Na przykład możesz chcieć utworzyć publiczny adres IP z taką samą konfiguracją i jednostką SKU na potrzeby testowania. Możesz również przenieść publiczną konfigurację adresu IP do innego regionu w ramach planowania odzyskiwania po awarii.

**Publiczne adresy IP platformy Azure są specyficzne dla regionu i nie można ich przenosić z jednego regionu do innego.** Można jednak użyć szablonu Azure Resource Manager do eksportowania istniejącej konfiguracji publicznego adresu IP.  Następnie można przemieścić zasób w innym regionie, eksportując publiczny adres IP do szablonu, modyfikując parametry w celu dopasowania do regionu docelowego, a następnie wdrożyć szablon w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżer zasobów i szablonów, zobacz [Eksportowanie grup zasobów do szablonów](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że publiczny adres IP platformy Azure znajduje się w regionie świadczenia usługi Azure, z którego chcesz przenieść.

- Publicznych adresów IP platformy Azure nie można przenosić między regionami.  Musisz skojarzyć nowy publiczny adres IP z zasobami w regionie docelowym.

- Aby wyeksportować konfigurację publicznego adresu IP i wdrożyć szablon w celu utworzenia publicznego adresu IP w innym regionie, musisz mieć rolę współautor sieci lub wyższą.
   
- Zidentyfikuj układ sieci źródłowej i wszystkie aktualnie używane zasoby. Ten układ obejmuje, ale nie jest ograniczony do modułów równoważenia obciążenia, sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) i sieci wirtualnych.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie publicznych adresów IP w używanym regionie docelowym. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że Twoja subskrypcja ma wystarczającą ilość zasobów, aby obsłużyć Dodawanie publicznych adresów IP dla tego procesu.  Zobacz [Azure subscription and service limits, quotas, and constraints](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits) (Limity, przydziały i ograniczenia usługi i subskrypcji platformy Azure).


## <a name="prepare-and-move"></a>Przygotowywanie i przenoszenie
Poniższe kroki pokazują, jak przygotować publiczny adres IP do przenoszenia konfiguracji przy użyciu szablonu Menedżer zasobów i przenieść konfigurację publicznego adresu IP do regionu docelowego przy użyciu Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Eksportowanie szablonu i wdrażanie go ze skryptu

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Uzyskaj identyfikator zasobu publicznego adresu IP, który chcesz przenieść do regionu docelowego i umieść go w zmiennej przy użyciu polecenia [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Wyeksportuj źródłową sieć wirtualną do pliku JSON do katalogu, w którym wykonujesz polecenie [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Pobrany plik zostanie nazwany po grupie zasobów, z której został wyeksportowany zasób.  Znajdź plik, który został wyeksportowany z polecenia o nazwie ** \<resource-group-name> . JSON** i otwórz go w wybranym edytorze:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Aby edytować parametr publicznej nazwy IP, należy zmienić właściwość **DefaultValue** źródłowej nazwy publicznego adresu IP na nazwę docelowego publicznego adresu IP, upewnij się, że nazwa jest cudzysłowem:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Aby edytować region docelowy, w którym zostanie przeniesiony publiczny adres IP, Zmień właściwość **lokalizacji** w obszarze zasoby:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Aby uzyskać kody lokalizacji regionu, możesz użyć polecenia cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) programu Azure PowerShell, uruchamiając następujące polecenie:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. W przypadku wybrania opcji i opcjonalnych w zależności od wymagań można także zmienić inne parametry szablonu:

    * **Jednostka SKU** — można zmienić jednostkę SKU publicznego adresu IP w konfiguracji z warstwy Standardowa na podstawowa lub podstawowa na Standard, zmieniając właściwość Nazwa **jednostki SKU**  >  **name** w pliku ** \<resource-group-name> JSON** :

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Aby uzyskać więcej informacji o różnicach między podstawowymi i standardowymi adresami IP jednostki SKU, zobacz [Tworzenie, zmienianie lub usuwanie publicznego adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Metoda alokacji publicznego adresu IP** i **limit czasu bezczynności** — można zmienić obie te opcje w szablonie, zmieniając właściwość **publicIPAllocationMethod** z **dynamicznego** na **statyczny** lub **statyczny** na **dynamiczny**. Limit czasu bezczynności można zmienić, zmieniając właściwość **idleTimeoutInMinutes** na żądaną wartość.  Wartość domyślna to **4**:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Aby uzyskać więcej informacji na temat metod alokacji i wartości limitu czasu bezczynności, zobacz [Tworzenie, zmienianie lub usuwanie publicznego adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


9. Zapisz plik ** \<resource-group-name> JSON** .

10. Utwórz grupę zasobów w regionie docelowym dla docelowego publicznego adresu IP do wdrożenia przy użyciu polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Wdróż edytowany plik ** \<resource-group-name> JSON** w grupie zasobów utworzonej w poprzednim kroku przy użyciu polecenia [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Aby sprawdzić, czy zasoby zostały utworzone w regionie docelowym, użyj polecenie [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) i [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>Odrzuć 

Jeśli po wdrożeniu chcesz zacząć od początku lub odrzucić publiczny adres IP w miejscu docelowym, Usuń grupę zasobów, która została utworzona w miejscu docelowym, a przeniesiony publiczny adres IP zostanie usunięty.  Aby usunąć grupę zasobów, użyj polecenie [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieci wirtualnej, Usuń źródłową sieć wirtualną lub grupę zasobów, użyj polecenie [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) lub [Remove-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przyniesiono publiczny adres IP platformy Azure z jednego regionu do innego i wyczyszczono zasoby źródłowe.  Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
