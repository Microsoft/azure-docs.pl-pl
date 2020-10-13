---
title: Konfigurowanie zapory IP dla Azure Relay przestrzeni nazw
description: W tym artykule opisano sposób używania reguł zapory do zezwalania na połączenia z określonych adresów IP do Azure Relay przestrzenie nazw.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ad8feed5df49dcc4503226a5fae50195bb9d48aa
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999511"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>Konfigurowanie zapory IP dla Azure Relay przestrzeni nazw
Domyślnie przestrzenie nazw przekaźnika są dostępne z Internetu, o ile żądanie zawiera prawidłowe uwierzytelnianie i autoryzację. Za pomocą zapory IP można ograniczyć ją tylko do zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (bez klas Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Ta funkcja jest przydatna w scenariuszach, w których Azure Relay powinny być dostępne tylko z niektórych dobrze znanych witryn. Reguły zapory umożliwiają konfigurowanie reguł w celu akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład, jeśli używasz przekaźnika z [usługą Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), możesz utworzyć **regułę zapory** , aby zezwolić na ruch tylko z adresów IP infrastruktury lokalnej. 


> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. 


## <a name="enable-ip-firewall-rules"></a>Włącz reguły zapory adresów IP
Reguły zapory adresów IP są stosowane na poziomie przestrzeni nazw. W związku z tym reguły są stosowane do wszystkich połączeń z klientów przy użyciu dowolnego obsługiwanego protokołu. Wszystkie próby połączenia z adresu IP, które nie pasują do dozwolonej reguły adresów IP w przestrzeni nazw, są odrzucane jako nieautoryzowane. Odpowiedź nie zawiera wzmianki o regule adresów IP. Reguły filtrowania adresów IP są stosowane w podanej kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

### <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji pokazano, jak za pomocą Azure Portal utworzyć reguły zapory IP dla przestrzeni nazw. 

1. Przejdź do **przestrzeni nazw przekaźnika** w [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz opcję **Sieć** . W przypadku wybrania opcji **wszystkie sieci** w sekcji  **Zezwalanie na dostęp z** obszaru przestrzeń nazw usługi Relay akceptuje połączenia z dowolnego adresu IP. To ustawienie jest równoważne z regułą akceptującą zakres adresów IP 0.0.0.0/0. 

    ![Zrzut ekranu przedstawia stronę sieci z wybraną opcją wszystkie sieci.](./media/ip-firewall/all-networks-selected.png)
1. Aby ograniczyć dostęp do określonych sieci i adresów IP, wybierz opcję **wybrane sieci** . W sekcji **Zapora** wykonaj następujące kroki:
    1. Wybierz opcję **Dodaj adres IP klienta** , aby zapewnić bieżącemu adresowi IP klienta dostęp do przestrzeni nazw. 
    2. W polu **zakres adresów**wprowadź określony adres IPv4 lub zakres adresów IPv4 w notacji CIDR. 

        ![Zapora — wybrana opcja Wszystkie sieci](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienia. Poczekaj kilka minut, aż potwierdzenie będzie widoczne na powiadomieniach portalu.


### <a name="use-resource-manager-template"></a>Używanie szablonu usługi Resource Manager
Poniższy szablon Menedżer zasobów umożliwia dodanie reguły filtru IP do istniejącej przestrzeni nazw przekaźnika.

Szablon przyjmuje jeden parametr: **ipMask**, który jest pojedynczym adresem IPv4 lub blokiem adresów IP w notacji CIDR. Na przykład w notacji CIDR 70.37.104.0/24 reprezentuje adresy IPv4 256 z 70.37.104.0 do 70.37.104.255, z 24 wskazujące liczbę znaczących bitów prefiksu dla zakresu.

> [!NOTE]
> Chociaż nie ma możliwych reguł Odmów, szablon Azure Resource Manager ma ustawioną akcję domyślną **"Zezwalaj"** , która nie ogranicza połączeń.
> Podczas tworzenia reguł Virtual Network lub zapór należy zmienić wartość ***"DefaultAction"***
> 
> wniosek
> ```json
> "defaultAction": "Allow"
> ```
> na wartość
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
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
        "type": "Microsoft.Relay/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
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
          "virtualNetworkRules": [],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Aby wdrożyć szablon, postępuj zgodnie z instrukcjami dotyczącymi [Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).



## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o innych funkcjach związanych z zabezpieczeniami sieci, zobacz [zabezpieczenia sieci](network-security.md).


<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
