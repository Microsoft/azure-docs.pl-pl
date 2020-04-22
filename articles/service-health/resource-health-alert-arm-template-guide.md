---
title: Szablon do tworzenia alertów kondycji zasobów
description: Programowo twórz alerty, które powiadamiają Cię, gdy zasoby platformy Azure staną się niedostępne.
ms.topic: conceptual
ms.date: 9/4/2018
ms.openlocfilehash: 60ff5bdf2f4f0dab94c18fd7c751869c1893ad65
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759020"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Konfigurowanie alertów kondycji zasobów przy użyciu szablonów Menedżera zasobów

W tym artykule pokazano, jak programowo tworzyć alerty dziennika aktywności kondycji zasobów przy użyciu szablonów usługi Azure Resource Manager i programu Azure PowerShell.

Usługa Azure Resource Health informuje o bieżącym i historycznym stanie kondycji zasobów platformy Azure. Alerty usługi Azure Resource Health mogą powiadamiać użytkownika w czasie zbliżonym do rzeczywistego, gdy te zasoby zmienią stan kondycji. Tworzenie alertów kondycji zasobów programowo umożliwiają użytkownikom tworzenie i dostosowywanie alertów zbiorczo.

> [!NOTE]
> Alerty kondycji zasobów są obecnie w wersji zapoznawczej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby postępować zgodnie z instrukcjami na tej stronie, musisz skonfigurować kilka rzeczy z wyprzedzeniem:

1. Musisz zainstalować [moduł programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
2. Należy [utworzyć lub ponownie użyć grupy akcji](../azure-monitor/platform/action-groups.md) skonfigurowanej do powiadamiania

## <a name="instructions"></a>Instrukcje
1. Korzystając z programu PowerShell, zaloguj się na platformę Azure przy użyciu konta i wybierz subskrypcję, z którą chcesz wchodzić w interakcje

        Login-AzAccount
        Select-AzSubscription -Subscription <subscriptionId>

    > Można użyć `Get-AzSubscription` do listy subskrypcji, do których masz dostęp.

2. Znajdowanie i zapisywanie pełnego identyfikatora usługi Azure Resource Manager dla grupy akcji

        (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Tworzenie i zapisywanie szablonu Menedżera zasobów `resourcehealthalert.json` dla alertów kondycji zasobów jako[(szczegóły poniżej)](#resource-manager-template-options-for-resource-health-alerts)

4. Tworzenie nowego wdrożenia usługi Azure Resource Manager przy użyciu tego szablonu

        New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. Zostanie wyświetlony monit o wpisanie wcześniej skopiowanego identyfikatora zasobu Nazwa alertu i identyfikator zasobu grupy akcji:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Jeśli wszystko działało pomyślnie, otrzymasz potwierdzenie w programie PowerShell

        DeploymentName          : ExampleDeployment
        ResourceGroupName       : <resourceGroup>
        ProvisioningState       : Succeeded
        Timestamp               : 11/8/2017 2:32:00 AM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                Name                     Type       Value
                                ===============          =========  ==========
                                activityLogAlertName     String     <Alert Name>
                                activityLogAlertEnabled  Bool       True
                                actionGroupResourceId    String     /...
        
        Outputs                 :
        DeploymentDebugLogLevel :

Należy zauważyć, że jeśli planujesz w pełni zautomatyzować ten proces, wystarczy edytować szablon Menedżera zasobów, aby nie monitować o wartości w kroku 5.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Opcje szablonów Menedżera zasobów dla alertów kondycji zasobów

Tego szablonu podstawowego można użyć jako punktu wyjścia do tworzenia alertów kondycji zasobów. Ten szablon będzie działać zgodnie z zapisem i zarejestruje Cię, aby otrzymywać alerty dla wszystkich nowo aktywowanych zdarzeń kondycji zasobów we wszystkich zasobach w ramach subskrypcji.

> W dolnej części tego artykułu mamy również bardziej złożony szablon alertu, który powinien zwiększyć stosunek sygnału do szumu dla alertów kondycji zasobów w porównaniu do tego szablonu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Jednak szeroki alert jak ten nie jest ogólnie zalecane. Dowiedz się, jak możemy skonsuwować ten alert, aby skupić się na wydarzeniach, na których nam zależy poniżej.

### <a name="adjusting-the-alert-scope"></a>Dostosowywanie zakresu alertu

Alerty kondycji zasobów można skonfigurować do monitorowania zdarzeń w trzech różnych zakresach:

 * Poziom subskrypcji
 * Poziom grupy zasobów
 * Poziom zasobu

Szablon alertu jest skonfigurowany na poziomie subskrypcji, ale jeśli chcesz skonfigurować alert, aby powiadamiać tylko o niektórych zasobach `scopes` lub zasobach w określonej grupie zasobów, wystarczy zmodyfikować sekcję w powyższym szablonie.

W przypadku zakresu na poziomie grupy zasobów sekcja zakresów powinna wyglądać następująco:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

W przypadku zakresu poziomu zasobów sekcja zakresu powinna wyglądać następująco:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Na przykład: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Możesz przejść do witryny Azure Portal i sprawdzić adres URL podczas wyświetlania zasobu platformy Azure, aby uzyskać ten ciąg.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Dostosowywanie typów zasobów, które cię ostrzegają

Alerty na poziomie subskrypcji lub grupy zasobów mogą mieć różne rodzaje zasobów. Jeśli chcesz ograniczyć alerty tylko pochodzą z określonego podzbioru typów `condition` zasobów, można zdefiniować, że w sekcji szablonu tak:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.COMPUTE/VIRTUALMACHINES",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.STORAGE/STORAGEACCOUNTS",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

W tym `anyOf` miejscu używamy otoki, aby umożliwić alert kondycji zasobu, aby dopasować dowolny z warunków, które określimy, umożliwiając alerty, które są przeznaczone dla określonych typów zasobów.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Dostosowywanie zdarzeń kondycji zasobu, które Cię ostrzegają
Gdy zasoby przechodzą zdarzenie dotyczące kondycji, mogą przejść przez serię etapów, `Active` `In Progress`które `Updated`reprezentują `Resolved`stan zdarzenia kondycji: , , i .

Możesz chcieć otrzymywać powiadomienia tylko wtedy, gdy zasób stanie się w złej kondycji, w którym to przypadku chcesz skonfigurować alert tylko po powiadomieniu, gdy `status` jest `Active`. Jeśli jednak chcesz otrzymywać powiadomienia na innych etapach, możesz dodać te szczegóły w ten sposób:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "In Progress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

Jeśli chcesz otrzymywać powiadomienia o wszystkich czterech etapach zdarzeń kondycji, możesz usunąć ten warunek razem, `status` a alert powiadomi Cię niezależnie od właściwości.

> [!NOTE]
> Każda sekcja "anyOf" powinna zawierać tylko jedną wartość typu pola.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Dostosowywanie alertów kondycji zasobu w celu uniknięcia zdarzeń "Nieznany"

Usługa Azure Resource Health może raportować najnowsze kondycji zasobów, stale monitorując je przy użyciu kandydatów testów. Odpowiednie zgłoszone stany zdrowia to: "Dostępne", "Niedostępne" i "Zdegradowane". Jednak w sytuacjach, gdy element programu runner i zasób platformy Azure nie mogą się komunikować, stan kondycji "Nieznany" jest zgłaszany dla zasobu i jest uważany za zdarzenie kondycji "Aktywne".

Jednak gdy zasób zgłasza "Nieznany", jest prawdopodobne, że jego stan kondycji nie uległ zmianie od czasu ostatniego raportu dokładne. Jeśli chcesz wyeliminować alerty o zdarzeniach "Nieznany", możesz określić tę logikę w szablonie:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

W tym przykładzie powiadamiamy tylko o zdarzeniach, w których bieżący i poprzedni stan kondycji nie ma "Nieznany". Ta zmiana może być przydatnym dodatkiem, jeśli alerty są wysyłane bezpośrednio na telefon komórkowy lub adres e-mail. 

Należy zauważyć, że jest możliwe dla currentHealthStatus i previousHealthStatus właściwości mają wartość null w niektórych zdarzeniach. Na przykład po wystąpieniu zaktualizowanego zdarzenia jest prawdopodobne, że stan kondycji zasobu nie uległ zmianie od ostatniego raportu, tylko, że dostępne są dodatkowe informacje o zdarzeniu (np. przyczyna). W związku z tym przy użyciu klauzuli powyżej może spowodować niektóre alerty nie są wyzwalane, ponieważ properties.currentHealthStatus i properties.previousHealthStatus wartości zostaną ustawione na null.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Dostosowywanie alertu w celu uniknięcia zdarzeń inicjowanych przez użytkownika

Zdarzenia kondycji zasobów mogą być wyzwalane przez zainicjowane przez platformę i zdarzenia inicjowane przez użytkownika. Może to mieć sens tylko wysłać powiadomienie, gdy zdarzenie kondycji jest spowodowane przez platformę Azure.

Alert można łatwo skonfigurować do filtrowania tylko pod kątem tego rodzaju zdarzeń:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```
Należy zauważyć, że jest możliwe dla pola przyczyna ma wartość null w niektórych zdarzeniach. Oznacza to, że ma miejsce przejście na zdrowie (np. dostępne dla niedostępnych), a zdarzenie jest rejestrowane natychmiast, aby zapobiec opóźnieniom powiadomień. W związku z tym przy użyciu klauzuli powyżej może spowodować alert nie jest wyzwalany, ponieważ wartość właściwości properties.clause zostanie ustawiona na null.

## <a name="complete-resource-health-alert-template"></a>Kompletny szablon alertu kondycji zasobów

Korzystając z różnych dostosowań opisanych w poprzedniej sekcji, oto przykładowy szablon, który jest skonfigurowany w celu zmaksymalizowania stosunku sygnału do szumu. Należy pamiętać, zastrzeżenia wymienione powyżej, gdzie currentHealthStatus, previousHealthStatus i wartości właściwości może być null w niektórych zdarzeniach.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "In Progress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

Jednak najlepiej dowiesz się, jakie konfiguracje są dla Ciebie skuteczne, więc użyj narzędzi nauczanych w tej dokumentacji, aby dokonać własnego dostosowania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o kondycji zasobów:
-  [Omówienie kondycji zasobów platformy Azure](Resource-health-overview.md)
-  [Typy zasobów i kontrole kondycji dostępne za pośrednictwem usługi Azure Resource Health](resource-health-checks-resource-types.md)


Tworzenie alertów kondycji usługi:
-  [Konfigurowanie alertów dla kondycji usługi](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
-  [Schemat zdarzenia dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log-schema.md)
