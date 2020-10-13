---
title: Przenoszenie zewnętrznego modułu równoważenia obciążenia platformy Azure do innego regionu platformy Azure przy użyciu Azure Portal
description: Użyj szablonu Azure Resource Manager, aby przenieść zewnętrzny moduł równoważenia obciążenia z jednego regionu świadczenia usługi Azure do innego przy użyciu Azure Portal.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 0598f21cddbaeef6b3cd10cd77250eeae8bd34bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808706"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Przenoszenie zewnętrznego modułu równoważenia obciążenia do innego regionu przy użyciu Azure Portal

Istnieją różne scenariusze, w których należy przenieść zewnętrzny moduł równoważenia obciążenia z jednego regionu do innego. Na przykład możesz chcieć utworzyć inny zewnętrzny moduł równoważenia obciążenia z tą samą konfiguracją na potrzeby testowania. Możesz również przenieść zewnętrzny moduł równoważenia obciążenia do innego regionu w ramach planowania odzyskiwania po awarii.

W sensie literału nie można przenieść zewnętrznego modułu równoważenia obciążenia platformy Azure z jednego regionu do innego. Można jednak użyć szablonu Azure Resource Manager do eksportowania istniejącej konfiguracji i publicznego adresu IP zewnętrznego modułu równoważenia obciążenia. Następnie można przemieścić zasób w innym regionie, eksportując moduł równoważenia obciążenia i publiczny adres IP do szablonu, modyfikując parametry w taki sposób, aby były zgodne z regionem docelowym, a następnie wdrażając szablon w nowym regionie. Aby uzyskać więcej informacji na temat Menedżer zasobów i szablonów, zobacz [Eksportowanie grup zasobów do szablonów](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że zewnętrzny moduł równoważenia obciążenia platformy Azure znajduje się w regionie świadczenia usługi Azure, z którego chcesz przenieść.

- Nie można przenosić zewnętrznych usług równoważenia obciążenia platformy Azure między regionami. Należy skojarzyć nowy moduł równoważenia obciążenia z zasobami w regionie docelowym.

- Aby wyeksportować konfigurację zewnętrznego modułu równoważenia obciążenia i wdrożyć szablon w celu utworzenia zewnętrznego modułu równoważenia obciążenia w innym regionie, musisz mieć przypisaną rolę współautor sieci lub wyższą.

- Zidentyfikuj układ sieci źródłowej i wszystkie aktualnie używane zasoby. Ten układ obejmuje, ale nie jest ograniczony do modułów równoważenia obciążenia, sieciowych grup zabezpieczeń, publicznych adresów IP i sieci wirtualnych.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie zewnętrznych modułów równoważenia obciążenia w regionie docelowym. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że Twoja subskrypcja ma wystarczającą ilość zasobów, aby obsłużyć Dodawanie modułów równoważenia obciążenia. Zobacz [Azure subscription and service limits, quotas, and constraints](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits) (Limity, przydziały i ograniczenia usługi i subskrypcji platformy Azure).

## <a name="prepare-and-move"></a>Przygotowywanie i przenoszenie
W poniższych procedurach pokazano, jak przygotować zewnętrzny moduł równoważenia obciążenia do przenoszenia przy użyciu szablonu Menedżer zasobów i przenieść konfigurację zewnętrznego modułu równoważenia obciążenia do regionu docelowego przy użyciu Azure Portal. Najpierw należy wyeksportować konfigurację publicznego adresu IP zewnętrznego modułu równoważenia obciążenia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Wyeksportuj szablon publicznego adresu IP i Wdróż publiczny adres IP z portalu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.
2. Znajdź grupę zasobów zawierającą źródłowy publiczny adres IP i wybierz ją.
3. Wybierz kolejno pozycje **Ustawienia**  >  **Eksportuj szablon**.
4. Wybierz pozycję **Wdróż** w obszarze **Eksportuj szablon**.
5. Wybierz pozycję **szablon**  >  **Edytuj parametry** , aby otworzyć parameters.jsw pliku w edytorze online.
8. Aby edytować parametr publicznej nazwy IP, należy zmienić właściwość **Value** w obszarze **Parameters** z publicznej publicznego adresu IP na nazwę docelowego publicznego adresu IP. Ujmij nazwę w cudzysłów.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Wybierz pozycję **Zapisz** w edytorze.

9.  Wybierz **szablon**  >  **Edytuj szablon** , aby otworzyć template.jsw pliku w edytorze online.

10. Aby edytować region docelowy, do którego zostanie przeniesiony publiczny adres IP, Zmień właściwość **lokalizacji** w obszarze **zasoby**:

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
  
    Aby uzyskać kody lokalizacji regionu, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kod regionu to nazwa regionu bez spacji. Na przykład kod dla środkowe stany USA to **środkowe**.
    
12. W zależności od wymagań można także zmienić inne parametry szablonu, jeśli chcesz lub zachodzi taka potrzeba:

    * **Jednostka SKU**. Możesz zmienić jednostkę SKU publicznego adresu IP w konfiguracji z warstwy Standardowa na podstawowa lub z warstwy Podstawowa na standardowa, zmieniając właściwość **name** w obszarze **SKU** w template.jspliku:

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

        Aby uzyskać informacje o różnicach między podstawowymi i standardowymi adresami IP jednostki SKU, zobacz [Tworzenie, zmienianie lub usuwanie publicznego adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Metoda alokacji publicznego adresu IP** i **limit czasu bezczynności**. Można zmienić metodę alokacji publicznego adresu IP, zmieniając właściwość **publicIPAllocationMethod** z **dynamicznego** na **statyczny** lub **statycznie** na **dynamiczny**. Limit czasu bezczynności można zmienić, zmieniając właściwość **idleTimeoutInMinutes** na żądaną wartość. Wartość domyślna to **4**.

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

        ```

        Aby uzyskać informacje na temat metod alokacji i wartości limitu czasu bezczynności, zobacz [Tworzenie, zmienianie lub usuwanie publicznego adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

 
13. Wybierz pozycję **Zapisz** w edytorze online.

14. Wybierz **BASICS**pozycję  >  **subskrypcja** podstawy, aby wybrać subskrypcję, w której zostanie wdrożony docelowy publiczny adres IP.

15. Wybierz **pozycję podstawową**  >  **grupę zasobów** , aby wybrać grupę zasobów, w której zostanie wdrożony docelowy publiczny adres IP. Możesz wybrać pozycję **Utwórz nowy** , aby utworzyć nową grupę zasobów dla docelowego publicznego adresu IP. Upewnij się, że nazwa nie jest taka sama jak źródłowa Grupa zasobów istniejącego źródłowego publicznego adresu IP.

16. Sprawdź, **BASICS**czy  >  **Lokalizacja** podstawy jest ustawiona na lokalizację docelową, w której ma zostać wdrożony publiczny adres IP.

17. W obszarze **Ustawienia**Sprawdź, czy nazwa jest zgodna z nazwą wprowadzoną wcześniej w edytorze parametrów.

18. Zaznacz pole wyboru **warunki i postanowienia** .

19. Wybierz pozycję **Kup** , aby wdrożyć docelowy publiczny adres IP.

20. Jeśli masz inny publiczny adres IP, który jest używany dla ruchu wychodzącego NAT dla przenoszonego modułu równoważenia obciążenia, powtórz poprzednie kroki, aby wyeksportować i wdrożyć drugi publiczny adres IP w regionie docelowym.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Wyeksportuj szablon zewnętrznego modułu równoważenia obciążenia i Wdróż moduł równoważenia obciążenia z Azure Portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.
2. Znajdź grupę zasobów zawierającą źródłowy zewnętrzny moduł równoważenia obciążenia i wybierz ją.
3. Wybierz kolejno pozycje **Ustawienia**  >  **Eksportuj szablon**.
4. Wybierz pozycję **Wdróż** w obszarze **Eksportuj szablon**.
5. Wybierz pozycję **szablon**  >  **Edytuj parametry** , aby otworzyć parameters.jsw pliku w edytorze online.

5. Aby edytować parametr nazwy zewnętrznego modułu równoważenia obciążenia, Zmień **wartość** właściwości Nazwa źródłowego zewnętrznego modułu równoważenia obciążenia na nazwę docelowego zewnętrznego modułu równoważenia obciążenia. Ujmij nazwę w cudzysłów.

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Aby edytować wartość docelowego publicznego adresu IP, który został przeniesiony w powyższych krokach, najpierw należy uzyskać identyfikator zasobu, a następnie wkleić go do parameters.jsna pliku. Aby uzyskać identyfikator:

    1. W innej karcie lub oknie przeglądarki Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **grupy zasobów**.
    2. Znajdź docelową grupę zasobów zawierającą publiczny adres IP, który został przeniesiony w powyższych krokach. Wybierz go.
    3. Wybierz pozycję **Ustawienia** > **Właściwości**.
    4. W bloku po prawej stronie zaznacz **Identyfikator zasobu** i skopiuj go do Schowka. Alternatywnie możesz wybrać opcję **Kopiuj do schowka** z prawej strony ścieżki **identyfikatora zasobu** .
    5. Wklej identyfikator zasobu do właściwości **wartość** w edytorze **parametrów edycji** , który jest otwarty w innym oknie przeglądarki lub karcie:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Wybierz pozycję **Zapisz** w edytorze online.


7.  Jeśli skonfigurowano wychodzące reguły NAT i dla modułu równoważenia obciążenia, w tym pliku zobaczysz trzeci wpis dla zewnętrznego identyfikatora publicznego adresu IP. Powtórz poprzednie kroki w **regionie docelowym** , aby uzyskać identyfikator wychodzącego publicznego adresu IP. Wklej ten identyfikator do parameters.jsw pliku:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  Wybierz **szablon**  >  **Edytuj szablon** , aby otworzyć template.jsw pliku w edytorze online.
9.  Aby edytować region docelowy, do którego zostanie przeniesiona konfiguracja zewnętrznego modułu równoważenia obciążenia, Zmień właściwość **Location** w obszarze **zasoby** w template.jsw pliku:

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

10. Aby uzyskać kody lokalizacji regionu, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kod regionu to nazwa regionu bez spacji. Na przykład kod dla środkowe stany USA to **środkowe**.

11. W zależności od wymagań można także zmienić inne parametry szablonu, jeśli chcesz lub zachodzi taka potrzeba:

    * **Jednostka SKU**. Można zmienić jednostkę SKU zewnętrznego modułu równoważenia obciążenia w konfiguracji z warstwy Standardowa na podstawowa lub z warstwy Podstawowa na standardowa, zmieniając właściwość **name** w obszarze **SKU** template.jsw pliku:

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
      Aby uzyskać informacje dotyczące różnic między usługą równoważenia obciążenia Basic i standardowymi jednostkami SKU, zobacz [Omówienie usługi Azure usługa Load Balancer w warstwie Standardowa](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

    * **Reguły równoważenia obciążenia**. Można dodawać lub usuwać reguły równoważenia obciążenia w konfiguracji przez dodanie lub usunięcie wpisów w template.jssekcji **loadBalancingRules** w pliku:

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
       Aby uzyskać informacje na temat reguł równoważenia obciążenia, zobacz [co to jest Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Sondy**. Możesz dodać lub usunąć sondę dla modułu równoważenia obciążenia w konfiguracji przez dodanie lub usunięcie wpisów w sekcji **sondy** template.jsw pliku:

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
       Aby uzyskać więcej informacji, zobacz [Load Balancer sondy kondycji](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **Reguły NAT dla ruchu przychodzącego**. Można dodać lub usunąć reguły NAT dla ruchu przychodzącego dla modułu równoważenia obciążenia przez dodanie lub usunięcie wpisów w template.jssekcji **inboundNatRules** w pliku:

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
        Aby ukończyć Dodawanie lub usuwanie reguły NAT dla ruchu przychodzącego, reguła musi być obecna lub usunięta jako właściwość **typu** na końcu template.jsw pliku:

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
        Aby uzyskać informacje na temat reguł NAT dla ruchu przychodzącego, zobacz [co to jest Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Reguły ruchu wychodzącego**. Możesz dodać lub usunąć reguły wychodzące w konfiguracji, edytując Właściwość **outboundRules** w template.jsw pliku:

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

         Aby uzyskać więcej informacji, zobacz [Load Balancer reguły ruchu wychodzącego](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).

12. Wybierz pozycję **Zapisz** w edytorze online.

13. Wybierz **BASICS**pozycję  >  **subskrypcja** podstawy, aby wybrać subskrypcję, w której zostanie wdrożony docelowy zewnętrzny moduł równoważenia obciążenia.

15. Wybierz **pozycję podstawową**  >  **grupę zasobów** , aby wybrać grupę zasobów, w której zostanie wdrożony docelowy moduł równoważenia obciążenia. Możesz wybrać pozycję **Utwórz nowy** , aby utworzyć nową grupę zasobów dla docelowego zewnętrznego modułu równoważenia obciążenia. Możesz też wybrać istniejącą grupę zasobów utworzoną wcześniej dla publicznego adresu IP. Upewnij się, że nazwa nie jest taka sama jak źródłowa Grupa zasobów istniejącego źródłowego zewnętrznego modułu równoważenia obciążenia.

16. Sprawdź, **BASICS**czy  >  **Lokalizacja** podstawy jest ustawiona na lokalizację docelową, w której ma zostać wdrożony zewnętrzny moduł równoważenia obciążenia.

17. W obszarze **Ustawienia**Sprawdź, czy nazwa jest zgodna z nazwą wprowadzoną wcześniej w edytorze parametrów. Sprawdź, czy identyfikatory zasobów są wypełnione dla wszystkich publicznych adresów IP w konfiguracji.

18. Zaznacz pole wyboru **warunki i postanowienia** .

19. Wybierz pozycję **Kup** , aby wdrożyć docelowy publiczny adres IP.

## <a name="discard"></a>Odrzuć

Jeśli chcesz odrzucić docelowy publiczny adres IP i zewnętrzny moduł równoważenia obciążenia, Usuń grupę zasobów, która je zawiera. Aby to zrobić, wybierz grupę zasobów z pulpitu nawigacyjnego w portalu, a następnie wybierz pozycję **Usuń** w górnej części strony przegląd.

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie publicznego adresu IP i zewnętrznego modułu równoważenia obciążenia, Usuń źródłowy publiczny adres IP i zewnętrzną usługę równoważenia obciążenia lub grupę zasobów. Aby to zrobić, wybierz tę grupę zasobów z pulpitu nawigacyjnego w portalu, a następnie wybierz pozycję **Usuń** w górnej części każdej strony.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono zewnętrzny moduł równoważenia obciążenia platformy Azure z jednego regionu do innego i wyczyszczono zasoby źródłowe. Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
