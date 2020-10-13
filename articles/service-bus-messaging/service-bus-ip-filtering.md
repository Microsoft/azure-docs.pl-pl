---
title: Konfigurowanie reguł zapory adresów IP dla Azure Service Bus
description: Jak używać reguł zapory do zezwalania na połączenia z określonych adresów IP do Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 2ba6b1902213af8d968b220a387e419e56e8aa67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91300959"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-ip-addresses-or-ranges"></a>Zezwalaj na dostęp do przestrzeni nazw Azure Service Bus z określonych adresów IP lub zakresów
Domyślnie obszary nazw Service Bus są dostępne z Internetu, o ile żądanie zawiera prawidłowe uwierzytelnianie i autoryzację. Za pomocą zapory IP można ograniczyć ją tylko do zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (bez klas Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Ta funkcja jest przydatna w scenariuszach, w których Azure Service Bus powinny być dostępne tylko z niektórych dobrze znanych witryn. Reguły zapory umożliwiają konfigurowanie reguł w celu akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład, jeśli używasz Service Bus z [usługą Azure Express Route][express-route], możesz utworzyć **regułę zapory** , aby zezwolić na ruch tylko z adresów IP lub adresów lokalnych infrastruktury translatora adresów sieciowych. 

> [!IMPORTANT]
> Zapory i sieci wirtualne są obsługiwane tylko w warstwie **premium** Service Bus. Jeśli uaktualnienie **do warstwy Premium** nie jest opcją, zalecamy utrzymywanie bezpiecznego tokenu sygnatury dostępu współdzielonego (SAS) i udostępnianie go tylko autoryzowanym użytkownikom. Aby uzyskać informacje o uwierzytelnianiu SAS, zobacz [uwierzytelnianie i autoryzacja](service-bus-authentication-and-authorization.md#shared-access-signature).

## <a name="ip-firewall-rules"></a>Reguły zapory adresów IP
Reguły zapory adresów IP są stosowane na poziomie przestrzeni nazw Service Bus. W związku z tym reguły są stosowane do wszystkich połączeń z klientów przy użyciu dowolnego obsługiwanego protokołu. Wszystkie próby połączenia z adresu IP, które nie pasują do dozwolonej reguły adresów IP w przestrzeni nazw Service Bus, są odrzucane jako nieautoryzowane. Odpowiedź nie zawiera wzmianki o regule adresów IP. Reguły filtrowania adresów IP są stosowane w podanej kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

>[!WARNING]
> Implementowanie reguł zapory może uniemożliwić innym usługom platformy Azure współdziałanie z Service Bus.
>
> Zaufane usługi firmy Microsoft nie są obsługiwane, gdy są implementowane filtrowanie adresów IP (reguły zapory) i wkrótce zostaną udostępnione.
>
> Typowe scenariusze platformy Azure, które nie współpracują z filtrowaniem adresów IP (należy zauważyć, że lista **nie** jest wyczerpująca) —
> - Integracja z usługą Azure Event Grid
> - Trasy usługi Azure IoT Hub
> - Device Explorer usługi Azure IoT
>
> Następujące usługi firmy Microsoft muszą znajdować się w sieci wirtualnej
> - Azure App Service
> - Azure Functions
> - Azure Monitor (ustawienie diagnostyczne)

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji pokazano, jak za pomocą Azure Portal utworzyć reguły zapory IP dla przestrzeni nazw Service Bus. 

1. Przejdź do **przestrzeni nazw Service Bus** w [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz opcję **Sieć** w obszarze **Ustawienia**.  

    > [!NOTE]
    > Karta **Sieć** zawiera tylko obszary nazw w **warstwie Premium** .  
    
    Domyślnie wybrana jest opcja **wybrane sieci** . Jeśli nie dodasz co najmniej jednej reguły zapory IP lub sieci wirtualnej na tej stronie, można uzyskać dostęp do przestrzeni nazw za pośrednictwem publicznej sieci Internet (przy użyciu klucza dostępu).

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Strona sieci — domyślna" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    W przypadku wybrania opcji **wszystkie sieci** Service Bus przestrzeń nazw akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest równoważne z regułą akceptującą zakres adresów IP 0.0.0.0/0. 

    ![Zrzut ekranu przedstawiający stronę sieci Azure Portal. Opcja zezwalania na dostęp ze wszystkich sieci jest zaznaczona na karcie zapory i sieci wirtualne.](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Aby zezwolić na dostęp tylko z określonego adresu IP, wybierz opcję **wybrane sieci** , jeśli nie została jeszcze wybrana. W sekcji **Zapora** wykonaj następujące kroki:
    1. Wybierz opcję **Dodaj adres IP klienta** , aby zapewnić bieżącemu adresowi IP klienta dostęp do przestrzeni nazw. 
    2. W polu **zakres adresów**wprowadź określony adres IPv4 lub zakres adresów IPv4 w notacji CIDR. 
    3. Określ, czy chcesz **zezwolić zaufanym usługom firmy Microsoft na ominięcie tej zapory**. 

        > [!WARNING]
        > Jeśli wybierzesz opcję **wybrane sieci** i nie określisz adresu IP lub zakresu adresów, usługa zezwoli na ruch ze wszystkich sieci. 

        ![Zrzut ekranu przedstawiający stronę sieci Azure Portal. Zaznaczona jest opcja zezwalania na dostęp z wybranych sieci, a sekcja Zapora jest wyróżniona.](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienia. Poczekaj kilka minut, aż potwierdzenie będzie widoczne na powiadomieniach portalu.

    > [!NOTE]
    > Aby ograniczyć dostęp do określonych sieci wirtualnych, zobacz [Zezwalanie na dostęp z określonych sieci](service-bus-service-endpoints.md).

## <a name="use-resource-manager-template"></a>Używanie szablonu usługi Resource Manager
Ta sekcja zawiera przykładowy szablon Azure Resource Manager, który tworzy sieć wirtualną i regułę zapory.


Poniższy szablon Menedżer zasobów umożliwia dodanie reguły sieci wirtualnej do istniejącej Service Bus przestrzeni nazw.

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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

Aby ograniczyć dostęp do Service Bus do sieci wirtualnych platformy Azure, Skorzystaj z następującego linku:

- [Virtual Network punkty końcowe usługi dla Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  ../expressroute/expressroute-faqs.md#supported-services