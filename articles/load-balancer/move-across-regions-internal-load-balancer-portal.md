---
title: Przenoszenie wewnętrznej Load Balancer platformy Azure do innego regionu platformy Azure przy użyciu Azure Portal
description: Użyj szablonu Azure Resource Manager, aby przenieść wewnętrzne Load Balancer platformy Azure z jednego regionu platformy Azure do innego przy użyciu Azure Portal
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 68a2cb6926cb41956711a9e3c15d21c250d27f0b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94698495"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Przenoszenie wewnętrznej Load Balancer platformy Azure do innego regionu przy użyciu Azure Portal

Istnieją różne scenariusze, w których należy przenieść istniejący wewnętrzny moduł równoważenia obciążenia z jednego regionu do innego. Na przykład możesz chcieć utworzyć wewnętrzny moduł równoważenia obciążenia z tą samą konfiguracją do testowania. Możesz również przenieść wewnętrzny moduł równoważenia obciążenia do innego regionu w ramach planowania odzyskiwania po awarii.

Wewnętrznych modułów równoważenia obciążenia platformy Azure nie można przenosić z jednego regionu do innego. Można jednak użyć szablonu Azure Resource Manager do eksportowania istniejącej konfiguracji i sieci wirtualnej wewnętrznego modułu równoważenia obciążenia.  Następnie można przemieścić zasób w innym regionie, eksportując moduł równoważenia obciążenia i sieć wirtualną do szablonu, modyfikując parametry w celu dopasowania do regionu docelowego, a następnie wdrażając szablony w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżer zasobów i szablonów, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że wewnętrzny moduł równoważenia obciążenia platformy Azure znajduje się w regionie świadczenia usługi Azure, z którego chcesz przenieść.

- Wewnętrznych modułów równoważenia obciążenia platformy Azure nie można przenosić między regionami.  Należy skojarzyć nowy moduł równoważenia obciążenia z zasobami w regionie docelowym.

- Aby wyeksportować konfigurację wewnętrznego modułu równoważenia obciążenia i wdrożyć szablon w celu utworzenia wewnętrznego modułu równoważenia obciążenia w innym regionie, musisz mieć rolę współautor sieci lub wyższą.

- Zidentyfikuj układ sieci źródłowej i wszystkie aktualnie używane zasoby. Ten układ obejmuje, ale nie jest ograniczony do modułów równoważenia obciążenia, sieciowych grup zabezpieczeń, maszyn wirtualnych i sieci wirtualnych.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie wewnętrznych modułów równoważenia obciążenia w używanym regionie docelowym. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że Twoja subskrypcja ma wystarczającą ilość zasobów, aby obsłużyć Dodawanie modułów równoważenia obciążenia dla tego procesu.  Zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Przygotowywanie i przenoszenie
Poniższe kroki pokazują, jak przygotować wewnętrzny moduł równoważenia obciążenia do przenoszenia przy użyciu szablonu Menedżer zasobów i przenieść konfigurację wewnętrznego modułu równoważenia obciążenia do regionu docelowego przy użyciu Azure Portal.  W ramach tego procesu należy uwzględnić konfigurację sieci wirtualnej wewnętrznego modułu równoważenia obciążenia i należy ją najpierw wykonać przed przeniesieniem wewnętrznego modułu równoważenia obciążenia.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Wyeksportuj szablon sieci wirtualnej i Wdróż go z Azure Portal

1. Zaloguj się do [](https://portal.azure.com)  >  **grup zasobów** Azure Portal.
2. Znajdź grupę zasobów zawierającą źródłową sieć wirtualną i kliknij ją.
3. Wybierz pozycję > **Ustawienia**  >  **Eksportuj szablon**.
4. Wybierz pozycję **Wdróż** w bloku **Eksportuj szablon** .
5. Kliknij pozycję **szablon**  >  **Edytuj parametry** , aby otworzyć **parameters.jsw** pliku w edytorze online.
6. Aby edytować parametr nazwy sieci wirtualnej, Zmień właściwość **Value** w obszarze **Parametry**:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. Zmień wartość Nazwa źródłowej sieci wirtualnej w edytorze na nazwę wybraną dla docelowej platformy wirtualnej. Upewnij się, że nazwa została ujęta w cudzysłów.

8. Kliknij przycisk **Zapisz** w edytorze.

9. Kliknij pozycję **szablon**  >  **Edytuj szablon** , aby otworzyć **template.jsw** pliku w edytorze online.

10. Aby edytować region docelowy, w którym zostanie przeniesiona Sieć wirtualna, Zmień właściwość **Location** w obszarze zasoby:

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

11. Aby uzyskać kody lokalizacji regionu, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kod regionu to nazwa regionu bez spacji, **środkowe stany USA**  =  .

12. Możesz również zmienić inne parametry w **template.jsw** pliku, jeśli wybierzesz, i są opcjonalne w zależności od wymagań:

    * **Przestrzeń adresowa** — przed zapisaniem można zmienić przestrzeń adresową sieci wirtualnej, modyfikując sekcję   >  **addressSpace** zasobów i zmieniając właściwość **addressPrefixes** w **template.js** w pliku:

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

    * **Podsieć** — nazwę podsieci i przestrzeń adresową podsieci można zmienić lub dodać do niej, modyfikując sekcję **podsieci** **template.jsna** pliku. Nazwę podsieci można zmienić, zmieniając właściwość **name** . Przestrzeń adresową podsieci można zmienić, zmieniając właściwość **addressPrefix** w **template.jsw** pliku:

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

         W **template.jsw** pliku, aby zmienić prefiks adresu, należy go edytować w dwóch miejscach, w sekcji wymienionej powyżej i w sekcji **Type** wymienionej poniżej.  Zmień właściwość **addressPrefix** tak, aby pasowała do powyższego poziomu:

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

13. Kliknij przycisk **Zapisz** w edytorze online.

14. Kliknij pozycję  >  **subskrypcja** podstawy, aby wybrać subskrypcję, w której zostanie wdrożona docelowa sieć wirtualna.

15. Kliknij pozycję **podstawowe**  >  **grupy zasobów** , aby wybrać grupę zasobów, w której zostanie wdrożona docelowa sieć wirtualna.  Możesz kliknąć przycisk **Utwórz nowy** , aby utworzyć nową grupę zasobów dla docelowej sieci wirtualnej.  Upewnij się, że nazwa nie jest taka sama jak źródłowa Grupa zasobów istniejącej sieci wirtualnej.

16. Sprawdź   >  , czy **Lokalizacja** podstawy jest ustawiona na lokalizację docelową, w której ma zostać wdrożona Sieć wirtualna.

17. Sprawdź, czy w obszarze **Ustawienia** nazwa jest zgodna z nazwą wprowadzoną w edytorze parametrów powyżej.

18. Zaznacz pole wyboru w obszarze **warunki i postanowienia**.

19. Kliknij przycisk **Kup** , aby wdrożyć docelową sieć wirtualną.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Wyeksportuj szablon wewnętrznego modułu równoważenia obciążenia i Wdróż go z Azure PowerShell

1. Zaloguj się do [](https://portal.azure.com)  >  **grup zasobów** Azure Portal.
2. Znajdź grupę zasobów zawierającą źródłowy wewnętrzny moduł równoważenia obciążenia i kliknij ją.
3. Wybierz pozycję > **Ustawienia**  >  **Eksportuj szablon**.
4. Wybierz pozycję **Wdróż** w bloku **Eksportuj szablon** .
5. Kliknij pozycję **szablon**  >  **Edytuj parametry** , aby otworzyć **parameters.jsw** pliku w edytorze online.

6. Aby edytować parametr nazwy wewnętrznego modułu równoważenia obciążenia, należy zmienić właściwość **DefaultValue** źródłowego wewnętrznego modułu równoważenia obciążenia na nazwę docelowego wewnętrznego modułu równoważenia obciążenia, upewnij się, że nazwa jest cudzysłowem:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. Aby edytować wartość docelowej sieci wirtualnej, która została przeniesiona powyżej, należy najpierw uzyskać identyfikator zasobu, a następnie skopiować i wkleić go do **parameters.js** pliku. Aby uzyskać identyfikator:

    1. Zaloguj się do [](https://portal.azure.com)  >  **grup zasobów** Azure Portal w innej karcie lub oknie przeglądarki.
    2. Znajdź docelową grupę zasobów zawierającą przenoszonej sieci wirtualnej z powyższych kroków i kliknij ją.
    3. Wybierz właściwości **ustawień**>  >  .
    4. W bloku po prawej stronie zaznacz **Identyfikator zasobu** i skopiuj go do Schowka.  Alternatywnie możesz kliknąć przycisk **Kopiuj do schowka** z prawej strony ścieżki **identyfikatora zasobu** .
    5. Wklej identyfikator zasobu do właściwości **DefaultValue** w edytorze **parametrów edycji** otwartym w innym oknie przeglądarki lub karcie:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Kliknij przycisk **Zapisz** w edytorze online.

7. Kliknij pozycję **szablon**  >  **Edytuj szablon** , aby otworzyć **template.jsw** pliku w edytorze online.
8. Aby edytować region docelowy, w którym zostanie przeniesiona konfiguracja wewnętrznego modułu równoważenia obciążenia, Zmień właściwość **Location** w obszarze **zasoby** w **template.js** w pliku:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

9.  Aby uzyskać kody lokalizacji regionu, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kod regionu to nazwa regionu bez spacji, **środkowe stany USA**  =  .

10. W przypadku wybrania opcji i opcjonalnych w zależności od wymagań można także zmienić inne parametry szablonu:

    * **Jednostka SKU** — można zmienić jednostkę SKU wewnętrznego modułu równoważenia obciążenia w konfiguracji z warstwy Standardowa na podstawowa lub podstawowa na standardowa, zmieniając właściwość Nazwa **jednostki SKU**  >   w **template.js** w pliku:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Aby uzyskać więcej informacji na temat różnic między usługą równoważenia obciążenia Basic i standardowymi jednostkami SKU, zobacz [Omówienie usługi Azure usługa Load Balancer w warstwie Standardowa](./load-balancer-overview.md)

    * **Reguły równoważenia obciążenia** — w konfiguracji można dodawać lub usuwać reguły równoważenia obciążenia, dodając lub usuwając wpisy dotemplate.jssekcji **loadBalancingRules** **w** pliku:

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
       Aby uzyskać więcej informacji o regułach równoważenia obciążenia, zobacz [co to jest Azure Load Balancer?](./load-balancer-overview.md)

    * **Sondy** — można dodać lub usunąć sondę modułu równoważenia obciążenia w konfiguracji przez dodanie lub usunięcie wpisów do sekcji **sondy** w **template.jsw** pliku:

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
       Aby uzyskać więcej informacji na Azure Load Balancer sond kondycji, zobacz [sondy kondycji Load Balancer](./load-balancer-custom-probe-overview.md)

    * **Reguły NAT dla ruchu przychodzącego** — można dodać lub usunąć reguły NAT dla ruchu przychodzącego dla modułu równoważenia obciążenia przez dodanie lub usunięcie wpisów dotemplate.jssekcji **inboundNatRules** **w** pliku:

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
        Aby ukończyć Dodawanie lub usuwanie reguły NAT dla ruchu przychodzącego, reguła musi być obecna lub usunięta jako właściwość **typu** na końcu **template.jsw** pliku:

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
        Aby uzyskać więcej informacji na temat reguł NAT dla ruchu przychodzącego, zobacz [co to jest Azure Load Balancer?](./load-balancer-overview.md)

12. Kliknij przycisk **Zapisz** w edytorze online.

13. Kliknij pozycję  >  **subskrypcja** podstawy, aby wybrać subskrypcję, w której zostanie wdrożony docelowy wewnętrzny moduł równoważenia obciążenia.

15. Kliknij pozycję **podstawowe**  >  **grupy zasobów** , aby wybrać grupę zasobów, w której zostanie wdrożony docelowy moduł równoważenia obciążenia.  Możesz kliknąć przycisk **Utwórz nowy** , aby utworzyć nową grupę zasobów dla docelowego wewnętrznego modułu równoważenia obciążenia lub wybrać istniejącą grupę zasobów, która została utworzona powyżej dla sieci wirtualnej.  Upewnij się, że nazwa nie jest taka sama jak źródłowa Grupa zasobów istniejącego źródłowego wewnętrznego modułu równoważenia obciążenia.

16. Sprawdź   >  , czy **Lokalizacja** podstawy jest ustawiona na lokalizację docelową, w której ma zostać wdrożony wewnętrzny moduł równoważenia obciążenia.

17. Sprawdź, czy w obszarze **Ustawienia** nazwa jest zgodna z nazwą wprowadzoną w edytorze parametrów powyżej.  Sprawdź, czy identyfikatory zasobów są wypełnione dla dowolnych sieci wirtualnych w konfiguracji.

18. Zaznacz pole wyboru w obszarze **warunki i postanowienia**.

19. Kliknij przycisk **Kup** , aby wdrożyć docelową sieć wirtualną.

## <a name="discard"></a>Odrzuć

Jeśli chcesz odrzucić docelową sieć wirtualną i wewnętrzny moduł równoważenia obciążenia, Usuń grupę zasobów zawierającą docelową sieć wirtualną i wewnętrzną usługę równoważenia obciążenia.  Aby to zrobić, wybierz grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz pozycję **Usuń** w górnej części strony przegląd.

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieci wirtualnej i wewnętrznego modułu równoważenia obciążenia, Usuń źródłową sieć wirtualną i wewnętrzną usługę równoważenia obciążenia lub grupę zasobów. W tym celu wybierz sieć wirtualną i wewnętrzną usługę równoważenia obciążenia lub grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz pozycję **Usuń** w górnej części każdej strony.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono wewnętrzny moduł równoważenia obciążenia platformy Azure z jednego regionu do innego i wyczyszczono zasoby źródłowe.  Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](../site-recovery/azure-to-azure-tutorial-migrate.md)