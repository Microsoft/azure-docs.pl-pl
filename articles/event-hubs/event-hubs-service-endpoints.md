---
title: Punkty końcowe usługi sieci wirtualnej — usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące sposobu dodawania punktu końcowego usługi Microsoft.EventHub do sieci wirtualnej.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: 91b08d6130da640adc28a3b7d85bd33f0e876caf
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390281"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Korzystanie z punktów końcowych usługi sieci wirtualnej w centrach zdarzeń platformy Azure

Integracja centrów zdarzeń z [punktami końcowymi usługi sieci wirtualnej (VNet)][vnet-sep] umożliwia bezpieczny dostęp do funkcji obsługi wiadomości z obciążeń, takich jak maszyny wirtualne, które są powiązane z sieciami wirtualnymi, przy czym ścieżka ruchu sieciowego jest zabezpieczona na obu końcach.

Po skonfigurowaniu do powiązanych z co najmniej jednym punktem końcowym usługi podsieci sieci wirtualnej odpowiedni obszar nazw Centrum zdarzeń nie akceptuje już ruchu z dowolnego miejsca, ale autoryzowanych podsieci w sieciach wirtualnych. Z punktu widzenia sieci wirtualnej powiązanie obszaru nazw Centrum zdarzeń z punktem końcowym usługi konfiguruje tunel sieci izolowanej z podsieci sieci wirtualnej do usługi obsługi wiadomości. 

Wynikiem jest prywatna i izolowana relacja między obciążeniami powiązanymi z podsiecią i odpowiednim obszarem nazw Centrum zdarzeń, pomimo obserwowalnego adresu sieciowego punktu końcowego usługi obsługi wiadomości w zakresie publicznego adresu IP. Istnieje wyjątek od tego zachowania. Włączenie punktu końcowego usługi domyślnie `denyall` włącza regułę w [zaporze IP](event-hubs-ip-filtering.md) skojarzonej z siecią wirtualną. Można dodać określone adresy IP w zaporze IP, aby włączyć dostęp do publicznego punktu końcowego Centrum zdarzeń. 

>[!WARNING]
> Implementowanie integracji sieci wirtualnych może uniemożliwić innym usługom platformy Azure interakcję z usługą Event Hubs.
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


> [!IMPORTANT]
> Sieci wirtualne są obsługiwane w warstwach **Standardowa** i **Dedykowana** usługi Event Hubs. Nie jest obsługiwany w warstwie **podstawowej.**

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Zaawansowane scenariusze zabezpieczeń włączone przez integrację sieci wirtualnej 

Rozwiązania, które wymagają ścisłego i podzielonego zabezpieczeń i w których podsieci sieci wirtualnej zapewniają segmentację między usługami podzielonymi na przedziały, nadal potrzebują ścieżek komunikacji między usługami zamieszkałymi w tych przedziałach.

Każda bezpośrednia trasa IP między przedziałami, w tym przewożącym HTTPS przez protokół TCP/IP, niesie ze sobą ryzyko wykorzystania luk w zabezpieczeniach z warstwy sieciowej. Usługi obsługi wiadomości zapewniają izolowane ścieżki komunikacji, gdzie wiadomości są nawet zapisywane na dysku podczas przechodzenia między stronami. Obciążenia w dwóch różnych sieciach wirtualnych, które są powiązane z tym samym wystąpieniem centrum zdarzeń, mogą komunikować się wydajnie i niezawodnie za pośrednictwem wiadomości, podczas gdy integralność granicy izolacji sieci jest zachowywana.
 
Oznacza to, że rozwiązania chmury wrażliwe na zabezpieczenia nie tylko uzyskują dostęp do wiodących w branży niezawodnych i skalowalnych asynchronicznych funkcji obsługi wiadomości asynchronicznych platformy Azure, ale mogą teraz używać wiadomości do tworzenia ścieżek komunikacji między bezpiecznymi przedziałami rozwiązań, które są z natury bezpieczniejsze niż to, co jest osiągalne w dowolnym trybie komunikacji typu peer-to-peer, w tym https i innych protokołów gniazd zabezpieczonych protokołami TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Powiąż centra zdarzeń z sieciami wirtualnymi

**Reguły sieci wirtualnej** to funkcja zabezpieczeń zapory, która określa, czy obszar nazw usługi Azure Event Hubs akceptuje połączenia z określonej podsieci sieci wirtualnej.

Powiązanie obszaru nazw Centrum zdarzeń z siecią wirtualną jest procesem dwuetapowym. Najpierw należy utworzyć **punkt końcowy usługi sieci wirtualnej** w podsieci sieci wirtualnej i włączyć go dla **microsoft.EventHub,** jak wyjaśniono w artykule [omówienie punktu końcowego usługi.][vnet-sep] Po dodaniu punktu końcowego usługi należy powiązać obszar nazw Centrum zdarzeń z regułą **sieci wirtualnej.**

Reguła sieci wirtualnej jest skojarzeniem obszaru nazw Centrum zdarzeń z podsiecią sieci wirtualnej. Gdy reguła istnieje, wszystkie obciążenia powiązane z podsiecią są przyznawane dostęp do obszaru nazw Centrum zdarzeń. Usługa Event Hubs nigdy nie ustanawia połączeń wychodzących, nie musi uzyskiwać dostępu i dlatego nigdy nie uzyskała dostępu do podsieci, włączając tę regułę.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji pokazano, jak za pomocą witryny Azure Portal, aby dodać punkt końcowy usługi sieci wirtualnej. Aby ograniczyć dostęp, należy zintegrować punkt końcowy usługi sieci wirtualnej dla tej przestrzeni nazw centrum zdarzeń.

1. Przejdź do **obszaru nazw Centrum zdarzeń** w [witrynie Azure portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz opcję **Sieć.** Jeśli wybierzesz opcję **Wszystkie sieci,** centrum zdarzeń zaakceptuje połączenia z dowolnego adresu IP. To ustawienie jest równoważne regule, która akceptuje zakres adresów IP 0.0.0.0/0. 

    ![Zapora sieciowa — wybrano opcję Wszystkie sieci](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Aby uzyskać dostęp do określonych sieci, wybierz opcję **Wybrane sieci** u góry strony.
2. W sekcji **Sieć wirtualna** na stronie wybierz **+Dodaj istniejącą sieć wirtualną***. Wybierz **+ Utwórz nową sieć wirtualną,** jeśli chcesz utworzyć nową sieć wirtualną. 

    ![dodawanie istniejącej sieci wirtualnej](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Wybierz sieć wirtualną z listy sieci wirtualnych, a następnie wybierz **podsieć**. Należy włączyć punkt końcowy usługi przed dodaniem sieci wirtualnej do listy. Jeśli punkt końcowy usługi nie jest włączony, portal wyświetli monit o jego włączenie.
   
   ![wybieranie podsieci](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Po włączeniu punktu końcowego usługi dla podsieci dla witryny **Microsoft.EventHub**powinien zostać wyświetlony następujący komunikat pomyślny. Wybierz **pozycję Dodaj** u dołu strony, aby dodać sieć. 

    ![wybieranie podsieci i włączanie punktu końcowego](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Jeśli nie można włączyć punktu końcowego usługi, można zignorować brakujący punkt końcowy usługi sieci wirtualnej przy użyciu szablonu Menedżera zasobów. Ta funkcja nie jest dostępna w portalu.
6. Wybierz **pozycję Zapisz** na pasku narzędzi, aby zapisać ustawienia. Poczekaj kilka minut na wyświetlenie potwierdzenia w powiadomieniach portalu.

    ![Zapisz sieć](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)


## <a name="use-resource-manager-template"></a>Używanie szablonu usługi Resource Manager

Poniższy szablon Menedżera zasobów umożliwia dodanie reguły sieci wirtualnej do istniejącego obszaru nazw Centrum zdarzeń.

Parametry szablonu:

* **namespaceName**: Obszar nazw centrów zdarzeń.
* **vnetRuleName**: Nazwa reguły sieci wirtualnej, która ma zostać utworzona.
* **virtualNetworkingSubnetId:** W pełni kwalifikowana ścieżka Menedżera zasobów dla podsieci sieci wirtualnej; na przykład `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` dla domyślnej podsieci sieci wirtualnej.

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

Aby wdrożyć szablon, postępuj zgodnie z instrukcjami dotyczącymi [usługi Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz następujące łącza:

- [Punkty końcowe usługi dla sieci wirtualnej platformy Azure][vnet-sep]
- [Filtrowanie adresów IP usługi Azure Event Hubs][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
