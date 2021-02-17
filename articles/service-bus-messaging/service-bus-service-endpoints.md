---
title: Skonfiguruj punkty końcowe usługi sieci wirtualnej dla Azure Service Bus
description: Ten artykuł zawiera informacje dotyczące sposobu dodawania punktu końcowego usługi Microsoft. ServiceBus do sieci wirtualnej.
ms.topic: article
ms.date: 02/12/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 2e00c9429ab3e39f95bc5ce6df072a99e4f02b86
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559574"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-virtual-networks"></a>Zezwalaj na dostęp do przestrzeni nazw Azure Service Bus z określonych sieci wirtualnych
Integracja Service Bus z [punktami końcowymi usługi Virtual Network (VNET)][vnet-sep] umożliwia bezpieczny dostęp do funkcji obsługi komunikatów z obciążeń takich jak maszyny wirtualne, które są powiązane z sieciami wirtualnymi, z zabezpieczoną ścieżką ruchu sieciowego na obu końcach.

Po skonfigurowaniu do powiązania z co najmniej jednym punktem końcowym usługi podsieci sieci wirtualnej odpowiednie Service Bus przestrzeni nazw nie będą już akceptować ruchu z dowolnego miejsca, ale autoryzowanych sieci wirtualnych i, opcjonalnie, określonych internetowych adresów IP. Z punktu widzenia sieci wirtualnej powiązanie przestrzeni nazw Service Bus z punktem końcowym usługi konfiguruje odizolowany tunel sieciowy z podsieci sieci wirtualnej do usługi obsługi komunikatów.

Wynikiem jest relacja między obciążeniami powiązanymi z podsiecią i odpowiadającą jej przestrzenią nazw Service Bus, w odróżnieniu od pozostałego adresu sieciowego punktu końcowego usługi obsługi komunikatów znajdującego się w zakresie publicznego adresu IP.

Implementowanie integracji sieci wirtualnych może uniemożliwić innym usługom platformy Azure interakcję z usługą Service Bus. Jako wyjątek, można zezwolić na dostęp do Service Bus zasobów z niektórych zaufanych usług nawet wtedy, gdy są włączone punkty końcowe usługi sieciowej. Aby zapoznać się z listą zaufanych usług, zobacz temat [usługi zaufane](#trusted-microsoft-services).

Następujące usługi firmy Microsoft muszą znajdować się w sieci wirtualnej
- Azure App Service
- Azure Functions

Sieci wirtualne są obsługiwane tylko w [warstwie Premium](service-bus-premium-messaging.md) Service Bus przestrzenie nazw. W przypadku korzystania z punktów końcowych usługi sieci wirtualnej z Service Bus nie należy włączać tych punktów końcowych w aplikacjach, które mieszają warstwy Standardowa i Premium Service Bus przestrzenie nazw. Ponieważ warstwa standardowa nie obsługuje sieci wirtualnych. Punkt końcowy jest ograniczony tylko do przestrzeni nazw warstwy Premium.

> [!IMPORTANT]
> Określ co najmniej jedną regułę IP lub regułę sieci wirtualnej dla przestrzeni nazw, aby zezwalać na ruch tylko z określonych adresów IP lub podsieci sieci wirtualnej. W przypadku braku reguł adresów IP i sieci wirtualnych można uzyskać dostęp do przestrzeni nazw za pośrednictwem publicznego Internetu (przy użyciu klucza dostępu).  

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Zaawansowane scenariusze zabezpieczeń obsługujące integrację sieci wirtualnej 

Rozwiązania, które wymagają ścisłych i compartmentalized zabezpieczeń, a wirtualne podsieci sieci zapewniają segmentację między usługami compartmentalized, zazwyczaj nadal potrzebują ścieżek komunikacji między usługami znajdującymi się w tych przedziałach.

Wszystkie bezpośrednie trasy IP między przedziałami, w tym przenoszące HTTPS za pośrednictwem protokołu TCP/IP, obejmują ryzyko wykorzystania luk w zabezpieczeniach z warstwy sieciowej. Usługi obsługi komunikatów zapewniają całkowicie izolowane ścieżki komunikacyjne, w przypadku których komunikaty są nawet zapisywane na dysku podczas przejścia między stronami. Obciążenia w dwóch odrębnych sieciach wirtualnych, które są powiązane z tym samym wystąpieniem Service Bus mogą komunikować się efektywnie i niezawodnie za pośrednictwem komunikatów, podczas gdy jest zachowywana odpowiednia integralność granic izolacji sieci.
 
Oznacza to, że rozwiązania w chmurze dotyczące zabezpieczeń nie tylko uzyskują dostęp do wiodących i skalowalnych w branży asynchronicznych funkcji obsługi komunikatów, ale mogą teraz używać komunikatów do tworzenia ścieżek komunikacji między bezpiecznymi przedziałami rozwiązania, które są najbardziej bezpieczne, niż to, co jest osiągalne w trybie komunikacji równorzędnej, w tym protokołu HTTPS i innych protokołów gniazd zabezpieczonych za pomocą protokołu TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Powiązywanie Service Bus z sieciami wirtualnymi

*Reguły sieci wirtualnej* to funkcja zabezpieczeń zapory, która kontroluje, czy serwer Azure Service Bus akceptuje połączenia z określonej podsieci sieci wirtualnej.

Powiązanie przestrzeni nazw Service Bus z siecią wirtualną jest procesem dwuetapowym. Najpierw należy utworzyć **punkt końcowy usługi Virtual Network** w podsieci Virtual Network i włączyć ją dla elementu **Microsoft. ServiceBus** , zgodnie z opisem w temacie [Omówienie punktu końcowego usługi][vnet-sep]. Po dodaniu punktu końcowego usługi należy powiązać z nim przestrzeń nazw Service Bus z **regułą sieci wirtualnej**.

Reguła sieci wirtualnej jest skojarzeniem przestrzeni nazw Service Bus z podsiecią sieci wirtualnej. Chociaż reguła istnieje, wszystkie obciążenia powiązane z podsiecią mają udzielony dostęp do przestrzeni nazw Service Bus. Sama Service Bus nigdy nie ustanawia połączeń wychodzących, nie musi uzyskiwać dostępu i dlatego nigdy nie udzielono dostępu do podsieci przez włączenie tej reguły.

> [!NOTE]
> Należy pamiętać, że punkt końcowy usługi sieciowej zapewnia aplikacjom działającym w sieci wirtualnej dostęp do przestrzeni nazw Service Bus. Sieć wirtualna kontroluje osiągalność punktu końcowego, ale nie można wykonywać operacji na Service Bus jednostkach (kolejkach, tematach lub subskrypcjach). Użyj Azure Active Directory (Azure AD), aby autoryzować operacje, które aplikacje mogą wykonywać w przestrzeni nazw i jej jednostkach. Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie i Autoryzowanie aplikacji za pomocą usługi Azure AD w celu uzyskania dostępu do jednostek Service Bus](authenticate-application.md).


## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji pokazano, jak dodać punkt końcowy usługi sieci wirtualnej przy użyciu Azure Portal. Aby ograniczyć dostęp, należy zintegrować punkt końcowy usługi sieci wirtualnej dla tej Event Hubs przestrzeni nazw.

1. Przejdź do **przestrzeni nazw Service Bus** w [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz opcję **Sieć** w obszarze **Ustawienia**.  

    > [!NOTE]
    > Karta **Sieć** zawiera tylko obszary nazw w **warstwie Premium** .  
    
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Strona sieci — domyślna" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    W przypadku wybrania opcji **wszystkie sieci** Service Bus przestrzeń nazw akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest równoważne z regułą akceptującą zakres adresów IP 0.0.0.0/0. 

    ![Zapora — wybrana opcja Wszystkie sieci](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
2. Aby ograniczyć dostęp do określonych sieci wirtualnych, wybierz opcję **wybrane sieci** , jeśli nie została jeszcze wybrana.
1. W sekcji **Virtual Network** strony wybierz pozycję **+ Dodaj istniejącą sieć wirtualną**. 

    ![dodawanie istniejącej sieci wirtualnej](./media/service-endpoints/add-vnet-menu.png)

    >[!WARNING]
    > Jeśli wybierzesz opcję **wybrane sieci** i nie dodasz co najmniej jednej reguły zapory IP lub sieci wirtualnej na tej stronie, można uzyskać dostęp do przestrzeni nazw za pośrednictwem publicznej sieci Internet (przy użyciu klucza dostępu).
3. Wybierz sieć wirtualną z listy sieci wirtualnych, a następnie wybierz **podsieć**. Przed dodaniem sieci wirtualnej do listy musisz włączyć punkt końcowy usługi. Jeśli punkt końcowy usługi nie jest włączony, w portalu zostanie wyświetlony monit o jego włączenie.
   
   ![wybieranie podsieci](./media/service-endpoints/select-subnet.png)

4. Po włączeniu punktu końcowego usługi dla podsieci na potrzeby elementu **Microsoft. ServiceBus** powinien zostać wyświetlony następujący komunikat o powodzeniu. Wybierz pozycję **Dodaj** w dolnej części strony, aby dodać sieć. 

    ![wybieranie podsieci i włączanie punktu końcowego](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Jeśli nie możesz włączyć punktu końcowego usługi, możesz zignorować brakujący punkt końcowy usługi sieci wirtualnej przy użyciu szablonu Menedżer zasobów. Ta funkcja nie jest dostępna w portalu.
6. Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienia. Poczekaj kilka minut, aż potwierdzenie będzie widoczne w powiadomieniach portalu. Przycisk **Zapisz** powinien być wyłączony. 

    ![Zapisz sieć](./media/service-endpoints/save-vnet.png)

    > [!NOTE]
    > Aby uzyskać instrukcje dotyczące zezwalania na dostęp z określonych adresów IP lub zakresów, zobacz [Zezwalanie na dostęp z określonych adresów IP lub zakresów](service-bus-ip-filtering.md).

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Używanie szablonu usługi Resource Manager
Poniższy przykładowy szablon Menedżer zasobów dodaje regułę sieci wirtualnej do istniejącej Service Bus przestrzeni nazw. Dla reguły sieci określa identyfikator podsieci w sieci wirtualnej. 

Identyfikator jest w pełni kwalifikowaną ścieżką Menedżer zasobów dla podsieci sieci wirtualnej. Na przykład `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` dla domyślnej podsieci sieci wirtualnej.

W przypadku dodawania reguł sieci wirtualnej lub zapór ustaw wartość `defaultAction` na `Deny` .

Szablon:

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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
                    "service": "Microsoft.ServiceBus"
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
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

> [!IMPORTANT]
> Jeśli nie ma reguł adresów IP i sieci wirtualnej, cały ruch jest przesyłany do przestrzeni nazw nawet wtedy, gdy ustawisz `defaultAction` na `deny` .  Do przestrzeni nazw można uzyskać dostęp za pośrednictwem publicznego Internetu (przy użyciu klucza dostępu). Określ co najmniej jedną regułę IP lub regułę sieci wirtualnej dla przestrzeni nazw, aby zezwalać na ruch tylko z określonych adresów IP lub podsieci sieci wirtualnej.  

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz następujące linki:

- [Punkty końcowe usługi dla sieci wirtualnej platformy Azure][vnet-sep]
- [Azure Service Bus filtrowanie adresów IP][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
