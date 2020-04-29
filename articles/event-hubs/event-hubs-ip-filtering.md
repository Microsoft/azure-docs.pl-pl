---
title: Reguły zapory Event Hubs platformy Azure | Microsoft Docs
description: Użyj reguł zapory, aby zezwolić na połączenia z określonych adresów IP z platformą Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 18212726f0ab921a05a3b640a32754c62958d047
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393141"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>Konfigurowanie reguł zapory adresów IP dla przestrzeni nazw usługi Azure Event Hubs
Domyślnie obszary nazw Event Hubs są dostępne z Internetu, o ile żądanie zawiera prawidłowe uwierzytelnianie i autoryzację. Za pomocą zapory IP można ograniczyć ją do tylko zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (bez klas routingu między domenami)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Ta funkcja jest przydatna w scenariuszach, w których usługa Azure Event Hubs powinna być dostępna tylko z niektórych dobrze znanych lokacji. Reguły zapory umożliwiają konfigurowanie reguł w celu akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład jeśli używasz Event Hubs z usługą [Azure Express Route][express-route], możesz utworzyć **regułę zapory** , aby zezwolić na ruch tylko z adresów IP infrastruktury lokalnej. 

>[!WARNING]
> Włączenie filtrowania adresów IP może uniemożliwić innym usługom platformy Azure współdziałanie z Event Hubs.
>
> Zaufane usługi firmy Microsoft nie są obsługiwane w przypadku implementacji sieci wirtualnych.
>
> Typowe scenariusze platformy Azure, które nie współpracują z sieciami wirtualnymi (należy zauważyć, że lista **nie** jest wyczerpująca) —
> - Azure Monitor (ustawienie diagnostyczne)
> - Usługa Azure Stream Analytics
> - Integracja z usługą Azure Event Grid
> - Trasy usługi Azure IoT Hub
> - Device Explorer usługi Azure IoT
>
> Następujące usługi firmy Microsoft muszą znajdować się w sieci wirtualnej
> - Azure Web Apps
> - Azure Functions


## <a name="ip-firewall-rules"></a>Reguły zapory adresów IP
Reguły zapory adresów IP są stosowane na poziomie przestrzeni nazw Event Hubs. W związku z tym reguły są stosowane do wszystkich połączeń z klientów przy użyciu dowolnego obsługiwanego protokołu. Wszystkie próby połączenia z adresu IP, które nie pasują do dozwolonej reguły adresów IP w przestrzeni nazw Event Hubs, są odrzucane jako nieautoryzowane. Odpowiedź nie zawiera wzmianki o regule adresów IP. Reguły filtrowania adresów IP są stosowane w podanej kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji pokazano, jak za pomocą Azure Portal utworzyć reguły zapory IP dla Event Hubs przestrzeni nazw. 

1. Przejdź do **przestrzeni nazw Event Hubs** w [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz opcję **Sieć** . W przypadku wybrania opcji **wszystkie sieci** centrum zdarzeń akceptuje połączenia z dowolnego adresu IP. To ustawienie jest równoważne z regułą akceptującą zakres adresów IP 0.0.0.0/0. 

    ![Zapora — wybrana opcja Wszystkie sieci](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Aby ograniczyć dostęp do określonych sieci i adresów IP, wybierz opcję **wybrane sieci** . W sekcji **Zapora** wykonaj następujące kroki:
    1. Wybierz opcję **Dodaj adres IP klienta** , aby zapewnić bieżącemu adresowi IP klienta dostęp do przestrzeni nazw. 
    2. W polu **zakres adresów**wprowadź określony adres IPv4 lub zakres adresów IPv4 w notacji CIDR. 
    3. Określ, czy chcesz **zezwolić zaufanym usługom firmy Microsoft na ominięcie tej zapory**. 

        ![Zapora — wybrana opcja Wszystkie sieci](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienia. Poczekaj kilka minut, aż potwierdzenie będzie widoczne na powiadomieniach portalu.


## <a name="use-resource-manager-template"></a>Używanie szablonu usługi Resource Manager

> [!IMPORTANT]
> Reguły zapory są obsługiwane w warstwach **standardowa** i **dedykowana** Event Hubs. Nie są obsługiwane w warstwie Podstawowa.

Poniższy szablon Menedżer zasobów umożliwia dodanie reguły filtru IP do istniejącej przestrzeni nazw Event Hubs.

Parametry szablonu:

- **ipMask** to pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w notacji CIDR 70.37.104.0/24 reprezentuje adresy IPv4 256 z 70.37.104.0 do 70.37.104.255, z 24 wskazujące liczbę znaczących bitów prefiksu dla zakresu.

> [!NOTE]
> Chociaż nie ma możliwych reguł Odmów, szablon Azure Resource Manager ma ustawioną akcję domyślną **"Zezwalaj"** , która nie ogranicza połączeń.
> Podczas tworzenia reguł Virtual Network lub zapór należy zmienić wartość ***"DefaultAction"***
> 
> wniosek
> ```json
> "defaultAction": "Allow"
> ```
> na
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Aby wdrożyć szablon, postępuj zgodnie z instrukcjami dotyczącymi [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Następne kroki

Aby ograniczyć dostęp do Event Hubs do sieci wirtualnych platformy Azure, Skorzystaj z następującego linku:

- [Virtual Network punkty końcowe usługi dla Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
