---
title: Reguły zapory Event Hubs platformy Azure | Microsoft Docs
description: Użyj reguł zapory, aby zezwolić na połączenia z określonych adresów IP z platformą Azure Event Hubs.
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: 18d043ebff7ff317207d0a33eaeba741fea8cc8a
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517199"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-ip-addresses-or-ranges"></a>Zezwalaj na dostęp do przestrzeni nazw platformy Azure Event Hubs z określonych adresów IP lub zakresów
Domyślnie obszary nazw Event Hubs są dostępne z Internetu, o ile żądanie zawiera prawidłowe uwierzytelnianie i autoryzację. Za pomocą zapory IP można ograniczyć ją tylko do zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (bez klas Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Ta funkcja jest przydatna w scenariuszach, w których usługa Azure Event Hubs powinna być dostępna tylko z niektórych dobrze znanych lokacji. Reguły zapory umożliwiają konfigurowanie reguł w celu akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład jeśli używasz Event Hubs z usługą [Azure Express Route][express-route], możesz utworzyć **regułę zapory** , aby zezwolić na ruch tylko z adresów IP infrastruktury lokalnej. 

>[!WARNING]
> Włączenie reguł zapory dla Event Hubs przestrzeni nazw domyślnie blokuje przychodzące żądania, chyba że żądania pochodzą z usługi z dozwolonych publicznych adresów IP. Zablokowane żądania obejmują te z innych usług platformy Azure, z Azure Portal z usług rejestrowania i metryk i tak dalej. Jako wyjątek, można zezwolić na dostęp do Event Hubs zasobów z niektórych zaufanych usług nawet wtedy, gdy filtrowanie adresów IP jest włączone. Aby zapoznać się z listą zaufanych usług, zobacz [Zaufane usługi firmy Microsoft](#trusted-microsoft-services).

## <a name="ip-firewall-rules"></a>Reguły zapory adresów IP
Reguły zapory adresów IP są stosowane na poziomie przestrzeni nazw Event Hubs. Dlatego reguły dotyczą wszystkich połączeń z klientów przy użyciu dowolnego obsługiwanego protokołu. Wszystkie próby połączenia z adresu IP, które nie pasują do dozwolonej reguły adresów IP w przestrzeni nazw Event Hubs, są odrzucane jako nieautoryzowane. Odpowiedź nie zawiera wzmianki o regule adresów IP. Reguły filtrowania adresów IP są stosowane w podanej kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji pokazano, jak za pomocą Azure Portal utworzyć reguły zapory IP dla Event Hubs przestrzeni nazw. 

1. Przejdź do **przestrzeni nazw Event Hubs** w [Azure Portal](https://portal.azure.com).
4. Wybierz pozycję **Sieć** w obszarze **Ustawienia** w menu po lewej stronie. Karta **Sieć** jest wyświetlana tylko dla **standardowych** lub **dedykowanych** przestrzeni nazw. 
    
    > [!WARNING]
    > Jeśli wybierzesz opcję **wybrane sieci** i nie dodasz co najmniej jednej reguły zapory IP lub sieci wirtualnej na tej stronie, można uzyskać dostęp do przestrzeni nazw za pośrednictwem **publicznej sieci Internet** (przy użyciu klucza dostępu).  

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Karta sieci — opcja wybrane sieci" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    W przypadku wybrania opcji **wszystkie sieci** centrum zdarzeń akceptuje połączenia z dowolnego adresu IP (przy użyciu klucza dostępu). To ustawienie jest równoważne z regułą akceptującą zakres adresów IP 0.0.0.0/0. 

    ![Zrzut ekranu przedstawiający stronę "Zapora i sieci wirtualne" z wybraną opcją "wszystkie sieci".](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Aby ograniczyć dostęp do określonych adresów IP, upewnij się, że wybrano opcję **wybrane sieci** . W sekcji **Zapora** wykonaj następujące kroki:
    1. Wybierz opcję **Dodaj adres IP klienta** , aby zapewnić bieżącemu adresowi IP klienta dostęp do przestrzeni nazw. 
    2. W polu **zakres adresów** wprowadź określony adres IPv4 lub zakres adresów IPv4 w notacji CIDR. 
3. Określ, czy chcesz **zezwolić zaufanym usługom firmy Microsoft na ominięcie tej zapory**. Aby uzyskać szczegółowe informacje, zobacz [Zaufane usługi firmy Microsoft](#trusted-microsoft-services) . 

      ![Zapora — wybrana opcja Wszystkie sieci](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienia. Poczekaj kilka minut, aż potwierdzenie będzie widoczne na powiadomieniach portalu.

    > [!NOTE]
    > Aby ograniczyć dostęp do określonych sieci wirtualnych, zobacz [Zezwalanie na dostęp z określonych sieci](event-hubs-service-endpoints.md).

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]


## <a name="use-resource-manager-template"></a>Używanie szablonu usługi Resource Manager

> [!IMPORTANT]
> Reguły zapory są obsługiwane w warstwach **standardowa** i **dedykowana** Event Hubs. Nie są obsługiwane w warstwie Podstawowa.

Poniższy szablon Menedżer zasobów umożliwia dodanie reguły filtru IP do istniejącej przestrzeni nazw Event Hubs.

**ipMask** w szablonie to pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w notacji CIDR 70.37.104.0/24 reprezentuje adresy IPv4 256 z 70.37.104.0 do 70.37.104.255, z 24 wskazujące liczbę znaczących bitów prefiksu dla zakresu.

W przypadku dodawania reguł sieci wirtualnej lub zapór ustaw wartość `defaultAction` na `Deny` .

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
        "type": "Microsoft.EventHub/namespaces/networkrulesets",
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

> [!IMPORTANT]
> Jeśli nie ma reguł adresów IP i sieci wirtualnej, cały ruch jest przesyłany do przestrzeni nazw nawet wtedy, gdy ustawisz `defaultAction` na `deny` .  Do przestrzeni nazw można uzyskać dostęp za pośrednictwem publicznego Internetu (przy użyciu klucza dostępu). Określ co najmniej jedną regułę IP lub regułę sieci wirtualnej dla przestrzeni nazw, aby zezwalać na ruch tylko z określonych adresów IP lub podsieci sieci wirtualnej.  

## <a name="next-steps"></a>Następne kroki

Aby ograniczyć dostęp do Event Hubs do sieci wirtualnych platformy Azure, Skorzystaj z następującego linku:

- [Virtual Network punkty końcowe usługi dla Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
