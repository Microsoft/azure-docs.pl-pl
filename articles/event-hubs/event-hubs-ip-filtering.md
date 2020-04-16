---
title: Reguły zapory usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Użyj reguł zapory, aby zezwolić na połączenia z określonych adresów IP do usługi Azure Event Hubs.
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
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393141"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>Konfigurowanie reguł zapory IP dla obszaru nazw usługi Azure Event Hubs
Domyślnie przestrzenie nazw centrum zdarzeń są dostępne z Internetu, o ile żądanie jest zawiera prawidłowe uwierzytelnianie i autoryzację. Zaporą IP można ją ograniczyć do tylko zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Ta funkcja jest przydatna w scenariuszach, w których usługi Azure Event Hubs powinny być dostępne tylko z niektórych dobrze znanych witryn. Reguły zapory umożliwiają konfigurowanie reguł do akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład jeśli używasz Usługi Event Hubs z [marszrutą Azure Express][express-route], można utworzyć **regułę zapory,** aby zezwolić na ruch tylko z adresów IP infrastruktury lokalnej. 

>[!WARNING]
> Włączenie filtrowania adresów IP może uniemożliwić innym usługom platformy Azure interakcję z centrum zdarzeń.
>
> Zaufane usługi firmy Microsoft nie są obsługiwane podczas implementacji sieci wirtualnych.
>
> Typowe scenariusze platformy Azure, które nie działają z sieciami wirtualnymi (należy pamiętać, że lista **nie** jest wyczerpująca) -
> - Azure Monitor (ustawienie diagnostyczne)
> - Usługa Azure Stream Analytics
> - Integracja z usługą Azure Event Grid
> - Trasy usługi Azure IoT Hub
> - Eksplorator urządzeń Usługi Azure IoT
>
> Następujące usługi firmy Microsoft muszą być w sieci wirtualnej
> - Azure Web Apps
> - Azure Functions


## <a name="ip-firewall-rules"></a>Reguły zapory IP
Reguły zapory IP są stosowane na poziomie obszaru nazw Centrum zdarzeń. W związku z tym reguły mają zastosowanie do wszystkich połączeń z klientami przy użyciu dowolnego obsługiwanego protokołu. Każda próba połączenia z adresu IP, która nie jest zgodna z dozwoloną regułą IP w obszarze nazw Centrum zdarzeń, jest odrzucana jako nieautoryzowana. W odpowiedzi nie wspomina się o regule IP. Reguły filtru IP są stosowane w kolejności, a pierwsza reguła zgodna z adresem IP określa akcję akceptowania lub odrzucania.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji pokazano, jak używać witryny Azure Portal do tworzenia reguł zapory IP dla obszaru nazw centrum zdarzeń. 

1. Przejdź do **obszaru nazw Centrum zdarzeń** w [witrynie Azure portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz opcję **Sieć.** Jeśli wybierzesz opcję **Wszystkie sieci,** centrum zdarzeń zaakceptuje połączenia z dowolnego adresu IP. To ustawienie jest równoważne regule, która akceptuje zakres adresów IP 0.0.0.0/0. 

    ![Zapora sieciowa — wybrano opcję Wszystkie sieci](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Aby ograniczyć dostęp do określonych sieci i adresów IP, wybierz opcję **Wybrane sieci.** W sekcji **Zapora postępuj** zgodnie z tymi czynnościami:
    1. Wybierz **opcję Dodaj adres IP klienta,** aby nadać bieżącemu adresowi IP klienta dostęp do obszaru nazw. 
    2. W przypadku **zakresu adresów**wprowadź określony adres IPv4 lub zakres adresu IPv4 w notacji CIDR. 
    3. Określ, czy chcesz **zezwolić zaufanym usługom firmy Microsoft na ominięcie tej zapory**. 

        ![Zapora sieciowa — wybrano opcję Wszystkie sieci](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Wybierz **pozycję Zapisz** na pasku narzędzi, aby zapisać ustawienia. Poczekaj kilka minut na wyświetlenie potwierdzenia w powiadomieniach portalu.


## <a name="use-resource-manager-template"></a>Używanie szablonu usługi Resource Manager

> [!IMPORTANT]
> Reguły zapory są obsługiwane w **standardowych** i **dedykowanych** warstwach Centrów zdarzeń. Nie są obsługiwane w warstwie Podstawowa.

Poniższy szablon Menedżera zasobów umożliwia dodanie reguły filtru IP do istniejącego obszaru nazw Centrum zdarzeń.

Parametry szablonu:

- **ipMask** jest pojedynczym adresem IPv4 lub blokiem adresów IP w notacji CIDR. Na przykład w notacji CIDR 70.37.104.0/24 reprezentuje 256 adresów IPv4 z 70.37.104.0 do 70.37.104.255, z 24 wskazując liczbę znaczących bitów prefiks dla zakresu.

> [!NOTE]
> Chociaż nie ma reguł odmowy możliwe, szablon usługi Azure Resource Manager ma domyślną akcję ustawioną na **"Zezwalaj",** która nie ogranicza połączeń.
> Podczas tworzenia reguł sieci wirtualnej lub zapór sieciowych musimy zmienić ***"defaultAction"***
> 
> Z
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

Aby wdrożyć szablon, postępuj zgodnie z instrukcjami dotyczącymi [usługi Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Następne kroki

Aby ograniczyć dostęp do centrów zdarzeń do sieci wirtualnych platformy Azure, zobacz następujące łącze:

- [Punkty końcowe usługi sieci wirtualnej dla centrów zdarzeń][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
