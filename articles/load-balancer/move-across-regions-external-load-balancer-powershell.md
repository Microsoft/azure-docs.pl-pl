---
title: Przenoszenie zewnętrznych Load Balancer platformy Azure do innego regionu platformy Azure przy użyciu Azure PowerShell
description: Użyj szablonu Azure Resource Manager, aby przenieść zewnętrzny Load Balancer platformy Azure z jednego regionu świadczenia usługi Azure do innego przy użyciu Azure PowerShell.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: be1971c9184d0b2b406b669ae9d1ea61598b201f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84809427"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Przenoszenie zewnętrznych Load Balancer platformy Azure do innego regionu przy użyciu Azure PowerShell

Istnieją różne scenariusze, w których należy przenieść istniejący zewnętrzny moduł równoważenia obciążenia z jednego regionu do innego. Na przykład możesz chcieć utworzyć zewnętrzny moduł równoważenia obciążenia z tą samą konfiguracją do testowania. Możesz również przenieść zewnętrzny moduł równoważenia obciążenia do innego regionu w ramach planowania odzyskiwania po awarii.

Zewnętrznych usług równoważenia obciążenia platformy Azure nie można przenosić z jednego regionu do innego. Można jednak użyć szablonu Azure Resource Manager do eksportowania istniejącej konfiguracji i publicznego adresu IP zewnętrznego modułu równoważenia obciążenia.  Następnie można przemieścić zasób w innym regionie, eksportując moduł równoważenia obciążenia i publiczny adres IP do szablonu, modyfikując parametry w celu dopasowania do regionu docelowego, a następnie wdrażając szablony w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżer zasobów i szablonów, zobacz [Eksportowanie grup zasobów do szablonów](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że zewnętrzny moduł równoważenia obciążenia platformy Azure znajduje się w regionie świadczenia usługi Azure, z którego chcesz przenieść.

- Nie można przenosić zewnętrznych usług równoważenia obciążenia platformy Azure między regionami.  Należy skojarzyć nowy moduł równoważenia obciążenia z zasobami w regionie docelowym.

- Aby wyeksportować konfigurację zewnętrznego modułu równoważenia obciążenia i wdrożyć szablon w celu utworzenia zewnętrznego modułu równoważenia obciążenia w innym regionie, musisz mieć rolę współautor sieci lub wyższą.
   
- Zidentyfikuj układ sieci źródłowej i wszystkie aktualnie używane zasoby. Ten układ obejmuje, ale nie jest ograniczony do modułów równoważenia obciążenia, sieciowych grup zabezpieczeń, publicznych adresów IP i sieci wirtualnych.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie zewnętrznych modułów równoważenia obciążenia w używanym regionie docelowym. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że Twoja subskrypcja ma wystarczającą ilość zasobów, aby obsłużyć Dodawanie modułów równoważenia obciążenia dla tego procesu.  Zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Przygotowywanie i przenoszenie
Poniższe kroki pokazują, jak przygotować zewnętrzny moduł równoważenia obciążenia na potrzeby przenoszenia przy użyciu szablonu Menedżer zasobów i przenieść konfigurację zewnętrznego modułu równoważenia obciążenia do regionu docelowego przy użyciu Azure PowerShell.  W ramach tego procesu konfiguracja publicznego adresu IP zewnętrznego modułu równoważenia obciążenia musi być uwzględniona, a przed przeniesieniem zewnętrznego modułu równoważenia obciążenia należy najpierw wykonać te czynności.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>Wyeksportuj szablon publicznego adresu IP i Wdróż go z Azure PowerShell

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Uzyskaj identyfikator zasobu publicznego adresu IP, który chcesz przenieść do regionu docelowego i umieść go w zmiennej przy użyciu polecenia [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Wyeksportuj źródłowy publiczny adres IP do pliku JSON do katalogu, w którym wykonujesz polecenie [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Pobrany plik zostanie nazwany po grupie zasobów, z której został wyeksportowany zasób.  Znajdź plik, który został wyeksportowany z polecenia o nazwie ** \<resource-group-name> . JSON** i otwórz go w wybranym edytorze:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
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

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>Wyeksportuj szablon zewnętrznego modułu równoważenia obciążenia i Wdróż go z Azure PowerShell

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Uzyskaj identyfikator zasobu zewnętrznego modułu równoważenia obciążenia, który ma zostać przeniesiony do regionu docelowego i umieść go w zmiennej przy użyciu polecenia [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Wyeksportuj konfigurację źródłową zewnętrznego modułu równoważenia obciążenia do pliku JSON do katalogu, w którym wykonujesz polecenie [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. Pobrany plik zostanie nazwany po grupie zasobów, z której został wyeksportowany zasób.  Znajdź plik, który został wyeksportowany z polecenia o nazwie ** \<resource-group-name> . JSON** i otwórz go w wybranym edytorze:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Aby edytować parametr nazwy zewnętrznego modułu równoważenia obciążenia, Zmień właściwość **DefaultValue** źródłowego zewnętrznego modułu równoważenia obciążenia na nazwę docelowego zewnętrznego modułu równoważenia obciążenia, a następnie upewnij się, że nazwa jest cudzysłowem:

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  Aby edytować wartość docelowego publicznego adresu IP, który został przeniesiony powyżej, należy najpierw uzyskać identyfikator zasobu, a następnie skopiować go i wkleić do pliku ** \<resource-group-name> JSON** .  Aby uzyskać identyfikator, użyj polecenie [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Wpisz zmienną i naciśnij klawisz ENTER, aby wyświetlić identyfikator zasobu.  Zaznacz ścieżkę identyfikatora i skopiuj ją do schowka:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  W pliku ** \<resource-group-name> JSON** wklej **Identyfikator zasobu** ze zmiennej zamiast elementu **DefaultValue** w drugim parametrze dla zewnętrznego identyfikatora IP, upewnij się, że ścieżka została umieszczona w cudzysłowie:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  W przypadku skonfigurowania wychodzącego NAT i reguł ruchu wychodzącego dla modułu równoważenia obciążenia trzeci wpis będzie obecny w tym pliku dla zewnętrznego identyfikatora dla wychodzącego publicznego adresu IP.  Powtórz powyższe kroki w **regionie docelowym** , aby uzyskać identyfikator wychodzącego publicznego adresu IP i wkleić ten wpis do pliku ** \<resource-group-name> JSON** :

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. Aby edytować region docelowy, w którym zostanie przeniesiona konfiguracja zewnętrznego modułu równoważenia obciążenia, Zmień właściwość **Location** w obszarze **zasoby** w pliku ** \<resource-group-name> JSON** :

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Aby uzyskać kody lokalizacji regionu, możesz użyć polecenia cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) programu Azure PowerShell, uruchamiając następujące polecenie:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. W przypadku wybrania opcji i opcjonalnych w zależności od wymagań można także zmienić inne parametry szablonu:
    
    * **Jednostka SKU** — można zmienić jednostkę SKU zewnętrznego modułu równoważenia obciążenia w konfiguracji z warstwy Standardowa na podstawowa lub podstawowa na standardowa, zmieniając właściwość Nazwa **jednostki SKU**  >  **name** w pliku ** \<resource-group-name> JSON** :

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Aby uzyskać więcej informacji na temat różnic między usługą równoważenia obciążenia Basic i standardowymi jednostkami SKU, zobacz [Omówienie usługi Azure usługa Load Balancer w warstwie Standardowa](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Reguły równoważenia obciążenia** — można dodawać lub usuwać reguły równoważenia obciążenia w konfiguracji przez dodanie lub usunięcie wpisów do sekcji **loadBalancingRules** pliku ** \<resource-group-name> JSON** :

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Aby uzyskać więcej informacji o regułach równoważenia obciążenia, zobacz [co to jest Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Sondy** — można dodać lub usunąć sondę modułu równoważenia obciążenia w konfiguracji przez dodanie lub usunięcie wpisów do sekcji **sondy** w pliku ** \<resource-group-name> JSON** :

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Aby uzyskać więcej informacji na Azure Load Balancer sond kondycji, zobacz [sondy kondycji Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Reguły NAT dla ruchu przychodzącego** — można dodać lub usunąć reguły NAT dla ruchu przychodzącego dla modułu równoważenia obciążenia przez dodanie lub usunięcie wpisów do sekcji **inboundNatRules** pliku ** \<resource-group-name> JSON** :

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Aby ukończyć Dodawanie lub usuwanie reguły NAT dla ruchu przychodzącego, reguła musi być obecna lub usunięta jako właściwość **typu** na końcu pliku ** \<resource-group-name> JSON** :

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Aby uzyskać więcej informacji na temat reguł NAT dla ruchu przychodzącego, zobacz [co to jest Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Reguły ruchu wychodzącego** — można dodawać lub usuwać reguły wychodzące w konfiguracji, edytując Właściwość **outboundRules** w pliku ** \<resource-group-name> JSON** :

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Aby uzyskać więcej informacji na temat reguł ruchu wychodzącego, zobacz [Load Balancer reguły wychodzące](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)

13. Zapisz plik ** \<resource-group-name> JSON** .
    
10. Utwórz lub grupę zasobów w regionie docelowym dla docelowego zewnętrznego modułu równoważenia obciążenia, który ma zostać wdrożony przy użyciu polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). W ramach tego procesu można także ponownie użyć istniejącej grupy zasobów z powyższych elementów:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Wdróż edytowany plik ** \<resource-group-name> JSON** w grupie zasobów utworzonej w poprzednim kroku przy użyciu polecenia [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Aby sprawdzić, czy zasoby zostały utworzone w regionie docelowym, użyj polecenie [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) i [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Odrzuć 

Jeśli po wdrożeniu chcesz zacząć od początku lub odrzucić publiczny adres IP i moduł równoważenia obciążenia w miejscu docelowym, Usuń grupę zasobów, która została utworzona w miejscu docelowym, a przeniesiony publiczny adres IP i moduł równoważenia obciążenia zostaną usunięte.  Aby usunąć grupę zasobów, użyj polecenie [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieciowej grupy zabezpieczeń, Usuń źródło sieciowej grupy zabezpieczeń lub grupę zasobów, użyj [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) lub [Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) i [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono grupę zabezpieczeń sieci platformy Azure z jednego regionu do innego i wyczyszczono zasoby źródłowe.  Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
