---
title: Punkty końcowe usługi Virtual Network — Event Hubs platformy Azure | Microsoft Docs
description: Ten artykuł zawiera informacje na temat dodawania punktu końcowego usługi Microsoft. EventHub do sieci wirtualnej.
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 029338e3835d03b1a66ff6629e872c84113b0ff2
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427210"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-virtual-networks"></a>Zezwalaj na dostęp do przestrzeni nazw platformy Azure Event Hubs z określonych sieci wirtualnych 

Integracja Event Hubs z [punktami końcowymi usługi Virtual Network (VNET)][vnet-sep] umożliwia bezpieczny dostęp do możliwości obsługi komunikatów z obciążeń, takich jak maszyny wirtualne powiązane z sieciami wirtualnymi, z zabezpieczoną ścieżką ruchu sieciowego na obu końcach.

Po skonfigurowaniu powiązania z co najmniej jednym punktem końcowym usługi podsieci sieci wirtualnej odpowiednia przestrzeń nazw Event Hubs nie akceptuje już ruchu z dowolnego miejsca, ale autoryzowanych podsieci w sieciach wirtualnych. Z punktu widzenia sieci wirtualnej powiązanie przestrzeni nazw Event Hubs z punktem końcowym usługi konfiguruje odizolowany tunel sieciowy z podsieci sieci wirtualnej do usługi obsługi komunikatów. 

Wynikiem jest relacja między obciążeniami powiązanymi z podsiecią i odpowiadającą jej przestrzenią nazw Event Hubs, w odróżnieniu od pozostałego adresu sieciowego punktu końcowego usługi obsługi komunikatów znajdującego się w zakresie publicznego adresu IP. Wystąpił wyjątek dotyczący tego zachowania. Domyślnie włączenie punktu końcowego usługi powoduje włączenie `denyall` reguły w [zaporze IP](event-hubs-ip-filtering.md) skojarzonej z siecią wirtualną. Można dodać określone adresy IP w zaporze IP, aby umożliwić dostęp do publicznego punktu końcowego centrum zdarzeń. 

>[!WARNING]
> Włączenie sieci wirtualnych dla Event Hubs przestrzeni nazw domyślnie blokuje przychodzące żądania, chyba że żądania pochodzą z usługi z dozwolonych sieci wirtualnych. Zablokowane żądania obejmują te z innych usług platformy Azure, z Azure Portal z usług rejestrowania i metryk i tak dalej. Jako wyjątek, można zezwolić na dostęp do Event Hubs zasobów z niektórych zaufanych usług nawet wtedy, gdy są włączone sieci wirtualne. Aby zapoznać się z listą zaufanych usług, zobacz temat [usługi zaufane](#trusted-microsoft-services).

> [!NOTE]
> Sieci wirtualne są obsługiwane w warstwach **Standardowa** i **Dedykowana** usługi Event Hubs. Ta wartość nie jest obsługiwana w warstwie **podstawowa** .

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Zaawansowane scenariusze zabezpieczeń obsługujące integrację sieci wirtualnej 

Rozwiązania, które wymagają ścisłych i compartmentalized zabezpieczeń, a wirtualne podsieci sieci zapewniają segmentację między usługami compartmentalized, nadal muszą mieć ścieżki komunikacyjne między usługami znajdującymi się w tych przedziałach.

Wszystkie bezpośrednie trasy IP między przedziałami, w tym przenoszące HTTPS za pośrednictwem protokołu TCP/IP, obejmują ryzyko wykorzystania luk w zabezpieczeniach z warstwy sieciowej. Usługi obsługi komunikatów zapewniają izolowane ścieżki komunikacyjne, w przypadku których komunikaty są nawet zapisywane na dysku podczas przejścia między stronami. Obciążenia w dwóch odrębnych sieciach wirtualnych, które są powiązane z tym samym wystąpieniem Event Hubs mogą komunikować się efektywnie i niezawodnie za pośrednictwem komunikatów, podczas gdy jest zachowywana odpowiednia integralność granic izolacji sieci.
 
Oznacza to, że rozwiązania w chmurze dotyczące zabezpieczeń nie tylko uzyskują dostęp do wiodących i skalowalnych w branży asynchronicznych funkcji obsługi komunikatów, ale mogą teraz używać komunikatów do tworzenia ścieżek komunikacji między bezpiecznymi przedziałami rozwiązania, które są najbardziej bezpieczne, niż to, co jest osiągalne w trybie komunikacji równorzędnej, w tym protokołu HTTPS i innych protokołów gniazd zabezpieczonych za pomocą protokołu TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Powiązywanie centrów zdarzeń z sieciami wirtualnymi

**Reguły sieci wirtualnej** to funkcja zabezpieczeń zapory, która kontroluje, czy przestrzeń nazw usługi Azure Event Hubs akceptuje połączenia z określonej podsieci sieci wirtualnej.

Powiązanie przestrzeni nazw Event Hubs z siecią wirtualną jest procesem dwuetapowym. Najpierw musisz utworzyć **punkt końcowy usługi sieci wirtualnej** w podsieci sieci wirtualnej i włączyć go dla elementu **Microsoft. EventHub** zgodnie z opisem w artykule [Przegląd punktu końcowego usługi][vnet-sep] . Po dodaniu punktu końcowego usługi należy powiązać z nim przestrzeń nazw Event Hubs z **regułą sieci wirtualnej**.

Reguła sieci wirtualnej jest skojarzeniem przestrzeni nazw Event Hubs z podsiecią sieci wirtualnej. Chociaż reguła istnieje, wszystkie obciążenia powiązane z podsiecią mają udzielony dostęp do przestrzeni nazw Event Hubs. Sama Event Hubs nigdy nie ustanawia połączeń wychodzących, nie musi uzyskiwać dostępu i dlatego nigdy nie udzielono dostępu do podsieci przez włączenie tej reguły.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji pokazano, jak dodać punkt końcowy usługi sieci wirtualnej przy użyciu Azure Portal. Aby ograniczyć dostęp, należy zintegrować punkt końcowy usługi sieci wirtualnej dla tej Event Hubs przestrzeni nazw.

1. Przejdź do **przestrzeni nazw Event Hubs** w [Azure Portal](https://portal.azure.com).
4. Wybierz pozycję **Sieć** w obszarze **Ustawienia** w menu po lewej stronie. Karta **Sieć** jest wyświetlana tylko dla **standardowych** lub **dedykowanych** przestrzeni nazw. 

    > [!NOTE]
    > Domyślnie wybrana jest opcja **wybrane sieci** , jak pokazano na poniższej ilustracji. Jeśli nie określisz reguły zapory adresu IP lub nie dodasz sieci wirtualnej na tej stronie, można uzyskać dostęp do przestrzeni nazw za pośrednictwem **publicznej sieci Internet** (przy użyciu klucza dostępu). 

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Karta sieci — opcja wybrane sieci" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    W przypadku wybrania opcji **wszystkie sieci** centrum zdarzeń akceptuje połączenia z dowolnego adresu IP (przy użyciu klucza dostępu). To ustawienie jest równoważne z regułą akceptującą zakres adresów IP 0.0.0.0/0. 

    ![Zapora — wybrana opcja Wszystkie sieci](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Aby ograniczyć dostęp do określonych sieci, wybierz opcję **wybrane sieci** w górnej części strony, jeśli nie została jeszcze wybrana.
2. W sekcji **Virtual Network** strony wybierz pozycję **+ Dodaj istniejącą sieć wirtualną** _. Wybierz _ *+ Utwórz nową sieć wirtualną* *, jeśli chcesz utworzyć nową VNET. 

    ![dodawanie istniejącej sieci wirtualnej](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Wybierz sieć wirtualną z listy sieci wirtualnych, a następnie wybierz **podsieć**. Przed dodaniem sieci wirtualnej do listy musisz włączyć punkt końcowy usługi. Jeśli punkt końcowy usługi nie jest włączony, w portalu zostanie wyświetlony monit o jego włączenie.
   
   ![wybieranie podsieci](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Po włączeniu punktu końcowego usługi dla podsieci dla elementu **Microsoft. EventHub** powinien zostać wyświetlony następujący komunikat o powodzeniu. Wybierz pozycję **Dodaj** w dolnej części strony, aby dodać sieć. 

    ![wybieranie podsieci i włączanie punktu końcowego](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Jeśli nie możesz włączyć punktu końcowego usługi, możesz zignorować brakujący punkt końcowy usługi sieci wirtualnej przy użyciu szablonu Menedżer zasobów. Ta funkcja nie jest dostępna w portalu.
5. Określ, czy chcesz **zezwolić zaufanym usługom firmy Microsoft na ominięcie tej zapory**. Aby uzyskać szczegółowe informacje, zobacz [Zaufane usługi firmy Microsoft](#trusted-microsoft-services) . 
6. Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienia. Poczekaj kilka minut, aż potwierdzenie będzie widoczne na powiadomieniach portalu.

    ![Zapisz sieć](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)

    > [!NOTE]
    > Aby ograniczyć dostęp do określonych adresów IP lub zakresów, zobacz [Zezwalanie na dostęp z określonych adresów IP lub zakresów](event-hubs-ip-filtering.md).

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Używanie szablonu usługi Resource Manager

Poniższy szablon Menedżer zasobów umożliwia dodanie reguły sieci wirtualnej do istniejącej Event Hubs przestrzeni nazw.

Parametry szablonu:

* `namespaceName`: Event Hubs przestrzeń nazw.
* `vnetRuleName`: Nazwa reguły Virtual Network, która ma zostać utworzona.
* `virtualNetworkingSubnetId`: W pełni kwalifikowana ścieżka Menedżer zasobów dla podsieci sieci wirtualnej; na przykład `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` dla domyślnej podsieci sieci wirtualnej.

> [!NOTE]
> Chociaż nie ma możliwych reguł Odmów, szablon Azure Resource Manager ma ustawioną akcję domyślną **"Zezwalaj"** , która nie ogranicza połączeń.
> Podczas tworzenia reguł Virtual Network lub zapór należy zmienić wartość **_"DefaultAction"_**
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
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
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

Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz następujące linki:

- [Punkty końcowe usługi dla sieci wirtualnej platformy Azure][vnet-sep]
- [Filtrowanie adresów IP Event Hubs platformy Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
