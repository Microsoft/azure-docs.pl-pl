---
title: Szablon do tworzenia alertów Resource Health
description: Programowe tworzenie alertów powiadamiających o stanie niedostępności zasobów platformy Azure.
ms.topic: conceptual
ms.date: 9/4/2018
ms.openlocfilehash: 3859bb0ce2497b1c1f547c3750e53745ef6d6f28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537433"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Konfigurowanie alertów dotyczących kondycji zasobów przy użyciu szablonów usługi Resource Manager

W tym artykule opisano sposób tworzenia alertów dziennika aktywności Resource Health programowo przy użyciu szablonów Azure Resource Manager i Azure PowerShell.

Azure Resource Health informuje o aktualnym i historycznym stanie kondycji zasobów platformy Azure. Alerty Azure Resource Health mogą powiadamiać niemal w czasie rzeczywistym o zmianie stanu kondycji tych zasobów. Tworzenie alertów Resource Health programowo pozwala użytkownikom na tworzenie i dostosowywanie alertów zbiorczo.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby postępować zgodnie z instrukcjami na tej stronie, musisz skonfigurować kilka rzeczy z wyprzedzeniem:

1. Należy zainstalować [moduł Azure PowerShell](/powershell/azure/install-az-ps)
2. Należy [utworzyć lub ponownie użyć grupy akcji](../azure-monitor/platform/action-groups.md) skonfigurowanej do powiadamiania użytkownika

## <a name="instructions"></a>Instrukcje
1. Korzystając z programu PowerShell, zaloguj się do platformy Azure przy użyciu swojego konta i wybierz subskrypcję, z którą chcesz korzystać

    ```azurepowershell
    Login-AzAccount
    Select-AzSubscription -Subscription <subscriptionId>
    ```

    > Możesz użyć, `Get-AzSubscription` Aby wyświetlić listę subskrypcji, do których masz dostęp.

2. Znajdź i Zapisz pełny identyfikator Azure Resource Manager dla grupy akcji

    ```azurepowershell
    (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id
    ```

3. Utwórz i Zapisz szablon Menedżer zasobów dla alertów Resource Health AS `resourcehealthalert.json` ([Zobacz szczegóły poniżej](#resource-manager-template-options-for-resource-health-alerts))

4. Utwórz nowe wdrożenie Azure Resource Manager przy użyciu tego szablonu

    ```azurepowershell
    New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>
    ```

5. Zostanie wyświetlony monit o wpisanie nazwy alertu i identyfikatora zasobu grupy akcji skopiowanego wcześniej:

    ```azurepowershell
    Supply values for the following parameters:
    (Type !? for Help.)
    activityLogAlertName: <Alert Name>
    actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>
    ```

6. Jeśli wszystko działało prawidłowo, otrzymasz potwierdzenie w programie PowerShell

    ```output
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
    ```

Należy pamiętać, że jeśli planujesz w pełni automatyzowanie tego procesu, wystarczy edytować szablon Menedżer zasobów, aby nie monitował o wartości w kroku 5.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Opcje szablonu Menedżer zasobów dla alertów Resource Health

Tego szablonu podstawowego można użyć jako punktu wyjścia do tworzenia alertów Resource Health. Ten szablon będzie działał jako zapisany i zostanie zasubskrybowany w celu otrzymywania alertów dotyczących wszystkich nowo aktywowanych zdarzeń dotyczących kondycji zasobów dla wszystkich zasobów w ramach subskrypcji.

> W dolnej części tego artykułu Dodaliśmy również bardziej skomplikowany szablon alertu, który powinien zwiększyć współczynnik hałasu dla Resource Health alertów w porównaniu z tym szablonem.

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

Jednak szeroki alert, taki jak ten, zazwyczaj nie jest zalecany. Dowiedz się, w jaki sposób można określić zakres tego alertu, aby skoncentrować się na zdarzeniach, które są poniżej.

### <a name="adjusting-the-alert-scope"></a>Dostosowywanie zakresu alertu

Alerty Resource Health można skonfigurować do monitorowania zdarzeń w trzech różnych zakresach:

 * Poziom subskrypcji
 * Poziom grupy zasobów
 * Poziom zasobu

Szablon alertu jest skonfigurowany na poziomie subskrypcji, ale jeśli chcesz skonfigurować alert w taki sposób, aby powiadamiał tylko o określonych zasobach lub zasobach należących do określonej grupy zasobów, wystarczy zmodyfikować `scopes` sekcję w powyższym szablonie.

W przypadku zakresu poziomu grupy zasobów sekcja zakresy powinna wyglądać następująco:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

W przypadku zakresu poziomu zasobów sekcja Scope powinna wyglądać następująco:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Na przykład: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Możesz przejść do witryny Azure Portal i przyjrzeć się adresowi URL podczas wyświetlania zasobu platformy Azure, aby uzyskać ten ciąg.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Dostosowywanie typów zasobów, które alertu

Alerty na poziomie subskrypcji lub grupy zasobów mogą mieć różne rodzaje zasobów. Jeśli chcesz ograniczyć alerty tylko do określonego podzestawu typów zasobów, możesz zdefiniować te wartości w `condition` sekcji szablonu, takiej jak:

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

W tym miejscu użyto `anyOf` otoki, aby umożliwić Alertowi kondycji zasobu dopasowanie do dowolnego z określonych przez nas warunków, co pozwala na alerty dotyczące określonych typów zasobów.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Dostosowywanie zdarzeń Resource Health, które wysyłają alert
Gdy zasoby podlegają zdarzeniom dotyczącym kondycji, mogą przejść przez serię etapów, która reprezentuje stan zdarzenia kondycji: `Active` , `In Progress` , `Updated` i `Resolved` .

Możesz otrzymywać powiadomienia tylko wtedy, gdy zasób ulegnie złej kondycji, w tym przypadku chcesz skonfigurować alert tak, aby był powiadamiany tylko wtedy, gdy `status` jest to `Active` . Jeśli jednak chcesz również otrzymywać powiadomienia o innych etapach, możesz dodać te szczegóły, takie jak:

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

Jeśli chcesz otrzymywać powiadomienia o wszystkich czterech etapach zdarzeń dotyczących kondycji, możesz usunąć ten warunek ze sobą, a alert będzie powiadamiany niezależnie od `status` właściwości.

> [!NOTE]
> Każda sekcja "anyOf" powinna zawierać tylko jedną wartość typu pola.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Dopasowywanie alertów Resource Health, aby uniknąć "nieznanych" zdarzeń

Azure Resource Health może zgłosić najnowszą kondycję zasobów przez ciągłe monitorowanie ich przy użyciu modułu uruchamiającego testy. Odpowiednie zgłaszane Stany kondycji to: "dostępne", "niedostępne" i "zdegradowane". Jednak w sytuacjach, gdy moduł uruchamiający i zasób platformy Azure nie są w stanie komunikować się, dla zasobu jest raportowany stan kondycji "Nieznane", który jest uznawany za "aktywne" zdarzenie kondycji.

Niemniej jednak, gdy zasób zawiera raport "Nieznane", prawdopodobnie jego stan kondycji nie zmienił się od ostatniego dokładnego raportu. Jeśli chcesz wyeliminować alerty dotyczące zdarzeń "Nieznane", możesz określić tę logikę w szablonie:

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

W tym przykładzie powiadamiamy tylko o zdarzeniach, w których bieżący i poprzedni stan kondycji nie ma "nieznany". Ta zmiana może być przydatna, jeśli alerty są wysyłane bezpośrednio do telefonu komórkowego lub wiadomości e-mail. 

Należy pamiętać, że w niektórych zdarzeniach właściwości currentHealthStatus i previousHealthStatus mają wartość null. Na przykład w przypadku wystąpienia zaktualizowanego zdarzenia istnieje duże ryzyko, że stan kondycji zasobu nie zmienił się od ostatniego raportu. dostępne są tylko dodatkowe informacje o zdarzeniu (np. przyczyna). W związku z tym użycie klauzuli powyżej może spowodować niewyzwalanie niektórych alertów, ponieważ wartości właściwości. currentHealthStatus i Properties. previousHealthStatus będą mieć wartość null.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Dostosowanie alertu w celu uniknięcia zdarzeń inicjowanych przez użytkownika

Zdarzenia Resource Health mogą być wyzwalane przez zainicjowane platformy i zdarzenia zainicjowane przez użytkownika. W przypadku, gdy zdarzenie kondycji jest spowodowane przez platformę Azure, warto wysłać powiadomienie.

Można łatwo skonfigurować alert, aby filtrować tylko te rodzaje zdarzeń:

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
Należy zauważyć, że w niektórych zdarzeniach pole przyczyna będzie miało wartość null. Oznacza to, że przejście do kondycji odbywa się (np. dostępne do niedostępności), a zdarzenie jest rejestrowane natychmiast, aby zapobiec opóźnieniu powiadomień. W związku z tym użycie klauzuli powyżej może spowodować, że alert nie zostanie wyzwolony, ponieważ wartość właściwości Property. klauzula zostanie ustawiona na wartość null.

## <a name="complete-resource-health-alert-template"></a>Ukończ szablon alertu Resource Health

Korzystając z różnych korekt opisanych w poprzedniej sekcji, poniżej przedstawiono przykładowy szablon, który jest skonfigurowany do maksymalizowania sygnałów do współczynnika szumu. Weź pod uwagę zastrzeżenia wymienione powyżej, gdzie wartości właściwości currentHealthStatus, previousHealthStatus i Przyczyna mogą mieć wartość null w niektórych zdarzeniach.

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

Należy jednak wiedzieć, jakie konfiguracje są efektywne dla Ciebie, dlatego należy użyć narzędzia do pracy w tej dokumentacji, aby wprowadzić własne dostosowanie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Resource Health:
-  [Przegląd Azure Resource Health](Resource-health-overview.md)
-  [Typy zasobów i kontrole kondycji dostępne za Azure Resource Health](resource-health-checks-resource-types.md)


Utwórz alerty Service Health:
-  [Konfigurowanie alertów dla Service Health](./alerts-activity-log-service-notifications-portal.md) 
-  [Schemat zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log-schema.md)
