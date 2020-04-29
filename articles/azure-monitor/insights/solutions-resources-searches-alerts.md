---
title: Zapisane wyszukiwania w rozwiązaniach do zarządzania | Microsoft Docs
description: Rozwiązania do zarządzania zwykle obejmują zapisane zapytania dziennika do analizowania danych zbieranych przez rozwiązanie. W tym artykule opisano sposób definiowania Log Analytics zapisane wyszukiwania w szablonie Menedżer zasobów.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61fc64e140af091b5ff3f631398daf901557791b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77663032"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Dodawanie Log Analytics zapisanych wyszukiwań i alertów do rozwiązania do zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Jak [ogłoszono wcześniej](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)obszary robocze usługi log Analytics utworzone po *1 czerwca 2019* — będą mogły zarządzać regułami alertów przy użyciu **tylko** scheduledQueryRules [interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)platformy Azure, [szablonu Azure Resource Manager](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) i [polecenia cmdlet programu PowerShell](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Klienci mogą łatwo [przełączać swoje preferowane środki zarządzania regułami alertów](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) dla starszych obszarów roboczych, aby korzystać z Azure monitor scheduledQueryRules jako domyślne i uzyskiwać wiele [nowych korzyści](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) , takich jak możliwość używania natywnych poleceń cmdlet programu PowerShell, zwiększonych lookback okresów czasu w regułach, tworzenie reguł w osobnej grupie zasobów lub subskrypcji i wiele innych.

> [!NOTE]
> Jest to wstępna dokumentacja dotycząca tworzenia rozwiązań do zarządzania, które są obecnie dostępne w wersji zapoznawczej. Każdy schemat opisany poniżej może ulec zmianie.

[Rozwiązania do zarządzania](solutions.md) zwykle obejmują [zapisane wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) w log Analytics do analizowania danych zebranych przez rozwiązanie. Mogą także definiować [alerty](../../azure-monitor/platform/alerts-overview.md) w celu powiadomienia użytkownika lub automatycznego podjęcia działania w odpowiedzi na problem krytyczny. W tym artykule opisano sposób definiowania Log Analytics zapisanych wyszukiwań i alertów w [szablonie zarządzania zasobami](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) , aby można je było uwzględnić w [rozwiązaniach do zarządzania](solutions-creating.md).

> [!NOTE]
> W przykładach w tym artykule są używane parametry i zmienne, które są wymagane lub wspólne dla rozwiązań do zarządzania, a także opisane w artykule [projektowanie i tworzenie rozwiązania do zarządzania na platformie Azure](solutions-creating.md)

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że wiesz już, jak [utworzyć rozwiązanie do zarządzania](solutions-creating.md) i strukturę [Menedżer zasobów pliku szablonu](../../azure-resource-manager/templates/template-syntax.md) i rozwiązania.


## <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics
Wszystkie zasoby w Log Analytics są zawarte w [obszarze roboczym](../../azure-monitor/platform/manage-access.md). Zgodnie z opisem w [log Analytics obszarze roboczym i koncie usługi Automation](solutions.md#log-analytics-workspace-and-automation-account)obszar roboczy nie jest uwzględniony w rozwiązaniu do zarządzania, ale musi istnieć przed zainstalowaniem rozwiązania. Jeśli nie jest on dostępny, instalacja rozwiązania kończy się niepowodzeniem.

Nazwa obszaru roboczego jest nazwami poszczególnych zasobów Log Analytics. Jest to realizowane w rozwiązaniu z parametrem **obszaru roboczego** , jak w poniższym przykładzie zasobu zapisanego wyszukiwania.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Wersja interfejsu API Log Analytics
Wszystkie zasoby Log Analytics zdefiniowane w szablonie Menedżer zasobów mają właściwość **apiVersion** , która definiuje wersję interfejsu API, który powinien być używany przez zasób.

Poniższa tabela zawiera listę wersji interfejsu API dla zasobu użytego w tym przykładzie.

| Typ zasobu | Wersja interfejsu API | Zapytanie |
|:---|:---|:---|
| savedSearches | 2017-03-15 — wersja zapoznawcza | &#124; zdarzeń, gdzie EventLevelName = = "Error"  |


## <a name="saved-searches"></a>Zapisane wyszukiwania
Uwzględnij [zapisane wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) w rozwiązaniu, aby umożliwić użytkownikom wykonywanie zapytań dotyczących danych zbieranych przez rozwiązanie. Zapisane wyszukiwania są wyświetlane w obszarze **zapisane wyszukiwania** w Azure Portal. Dla każdego alertu wymagane jest również zapisane wyszukiwanie.

[Log Analytics zapisane zasoby wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) mają typ `Microsoft.OperationalInsights/workspaces/savedSearches` i mieć następującą strukturę. Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić fragment kodu do pliku rozwiązania i zmienić nazwy parametrów.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

Każda właściwość zapisanego wyszukiwania jest opisana w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| category | Kategoria zapisanego wyszukiwania.  Wszystkie zapisane wyszukiwania w tym samym rozwiązaniu często korzystają z jednej kategorii, tak aby były zgrupowane w konsoli programu. |
| displayname | Nazwa wyświetlana dla zapisanego wyszukiwania w portalu. |
| query | Zapytanie do uruchomienia. |

> [!NOTE]
> Może być konieczne użycie znaków ucieczki w zapytaniu, jeśli zawiera znaki, które mogą być interpretowane jako dane JSON. Na przykład jeśli kwerenda była **usługą Azure | OperationName: "Microsoft. COMPUTE/virtualMachines/Write"**, należy ją zapisać w pliku rozwiązania jako na **platformie Azure | OperationName:/\"Microsoft. COMPUTE/virtualMachines/Write\"**.

## <a name="alerts"></a>Alerty
[Alerty dzienników platformy Azure](../../azure-monitor/platform/alerts-unified-log.md) są tworzone przez reguły alertów platformy Azure, które uruchamiają określone zapytania dziennika w regularnych odstępach czasu. Jeśli wyniki zapytania pasują do określonych kryteriów, tworzony jest rekord alertu, a co najmniej jedna akcja jest uruchamiana przy użyciu [grup akcji](../../azure-monitor/platform/action-groups.md).

W przypadku użytkowników, którzy rozszerą alerty na platformę Azure, akcje są teraz kontrolowane w grupach akcji platformy Azure. Po przedłużoniu obszaru roboczego i jego alertów na platformę Azure można pobrać lub dodać akcje przy użyciu [szablonu Azure Resource Manager grupy akcji](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
Reguły alertów w starszej wersji rozwiązania do zarządzania składają się z następujących trzech różnych zasobów.

- **Zapisane wyszukiwanie.** Definiuje uruchomioną przeszukiwanie dzienników. Wiele reguł alertów może współdzielić jedno zapisane wyszukiwanie.
- **Rozkład.** Definiuje częstotliwość uruchamiania przeszukiwania dzienników. Każda reguła alertu ma jeden i tylko jeden harmonogram.
- **Akcja alertu.** Każda reguła alertu ma jeden zasób grupy akcji lub zasób akcji (starsza wersja) z typem **alertu** , który definiuje szczegóły alertu, taki jak kryteria dla momentu utworzenia rekordu alertu i ważności alertu. Zasób [grupy akcji](../../azure-monitor/platform/action-groups.md) może mieć listę skonfigurowanych akcji, które mają zostać wykonane po uruchomieniu alertu, takiego jak rozmowa głosowa, wiadomości SMS, poczta e-mail, webhook, narzędzie narzędzia ITSM, element Runbook automatyzacji, aplikacja logiki itd.

Zapisane zasoby wyszukiwania zostały opisane powyżej. Inne zasoby są opisane poniżej.

### <a name="schedule-resource"></a>Zaplanuj zasób

Zapisane wyszukiwanie może mieć jeden lub więcej harmonogramów z każdym harmonogramem reprezentującym oddzielną regułę alertu. Harmonogram definiuje częstotliwość uruchamiania wyszukiwania oraz przedział czasu, w którym dane są pobierane. Planowanie zasobów ma typ `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` i ma następującą strukturę. Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić fragment kodu do pliku rozwiązania i zmienić nazwy parametrów.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }
Właściwości zasobów harmonogramu są opisane w poniższej tabeli.

| Nazwa elementu | Wymagany | Opis |
|:--|:--|:--|
| enabled       | Tak | Określa, czy alert jest włączony podczas jego tworzenia. |
| interval      | Tak | Jak często zapytanie jest wykonywane w ciągu kilku minut. |
| queryTimeSpan | Tak | Długość czasu w minutach, przez który należy obliczyć wyniki. |

Zasób harmonogramu powinien zależeć od zapisanego wyszukiwania, aby został utworzony przed harmonogramem.
> [!NOTE]
> Nazwa harmonogramu musi być unikatowa w danym obszarze roboczym; dwa harmonogramy nie mogą mieć tego samego identyfikatora, nawet jeśli są skojarzone z różnymi zapisanymi wyszukiwaniami. Nazwy wszystkich zapisanych wyszukiwań, harmonogramów i akcji utworzonych za pomocą interfejsu API Log Analytics muszą być pisane małymi literami.

### <a name="actions"></a>Akcje
Harmonogram może mieć wiele akcji. Akcja może definiować jeden lub więcej procesów do wykonania, takich jak wysyłanie wiadomości e-mail lub Uruchamianie elementu Runbook, lub Definiowanie progu, który określa, kiedy wyniki wyszukiwania pasują do niektórych kryteriów. Niektóre akcje definiują oba elementy, aby procesy były wykonywane po spełnieniu progu.
Akcje można definiować za pomocą [grupa akcji] zasobu lub akcji.

Istnieją dwa typy zasobów akcji określone przez właściwość **Type** . Harmonogram wymaga jednej akcji **alertu** , która definiuje szczegóły reguły alertu i jakie akcje są wykonywane po utworzeniu alertu. Zasoby akcji mają typ `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.

Akcje alertów mają następującą strukturę. Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić fragment kodu do pliku rozwiązania i zmienić nazwy parametrów.

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

Właściwości zasobów akcji alertu są opisane w poniższych tabelach.

| Nazwa elementu | Wymagany | Opis |
|:--|:--|:--|
| `type` | Tak | Typ akcji.  Jest to **alert** dotyczący akcji alertów. |
| `name` | Tak | Nazwa wyświetlana alertu.  Jest to nazwa wyświetlana w konsoli dla reguły alertu. |
| `description` | Nie | Opcjonalny opis alertu. |
| `severity` | Tak | Ważność rekordu alertu z następujących wartości:<br><br> **najistotniejsz**<br>**warning**<br>**informacyjną**

#### <a name="threshold"></a>Próg
Ta sekcja jest wymagana. Definiuje właściwości dla progu alertu.

| Nazwa elementu | Wymagany | Opis |
|:--|:--|:--|
| `Operator` | Tak | Operator porównania z następujących wartości:<br><br>**gt = większe niż<br>lt = mniejsze niż** |
| `Value` | Tak | Wartość, aby porównać wyniki. |

##### <a name="metricstrigger"></a>MetricsTrigger
Ta sekcja jest opcjonalna. Uwzględnij go dla alertu pomiaru metryki.

| Nazwa elementu | Wymagany | Opis |
|:--|:--|:--|
| `TriggerCondition` | Tak | Określa, czy próg dotyczy całkowitej liczby naruszeń lub kolejnych naruszeń z następujących wartości:<br><br>**Łączna<br>Liczba kolejnych** |
| `Operator` | Tak | Operator porównania z następujących wartości:<br><br>**gt = większe niż<br>lt = mniejsze niż** |
| `Value` | Tak | Liczba przypadków, gdy należy spełnić kryteria w celu wyzwolenia alertu. |


#### <a name="throttling"></a>Ograniczanie przepływności
Ta sekcja jest opcjonalna. Dołącz tę sekcję, jeśli chcesz pominąć alerty z tej samej reguły przez pewien czas po utworzeniu alertu.

| Nazwa elementu | Wymagany | Opis |
|:--|:--|:--|
| DurationInMinutes | Tak, jeśli uwzględniono ograniczenie elementu | Liczba minut, przez które mają zostać pominięte alerty po utworzeniu jednej z tych samych reguł alertów. |

#### <a name="azure-action-group"></a>Grupa akcji platformy Azure
Wszystkie alerty na platformie Azure używają grupy akcji jako domyślnego mechanizmu obsługi akcji. Za pomocą grupy akcji można określić akcje raz, a następnie skojarzyć grupę akcji z wieloma alertami — na platformie Azure. Bez konieczności, aby wielokrotnie zadeklarować te same akcje w trybie failover i ponownie. Grupy akcji obsługują wiele akcji — w tym wiadomości e-mail, wiadomości SMS, rozmowy głosowej, połączenia narzędzia ITSM, elementu Runbook usługi Automation, identyfikatora URI elementu webhook i nie tylko.

W przypadku użytkowników, którzy rozszerzyły swoje alerty na platformę Azure — harmonogram powinien teraz zawierać szczegóły grupy akcji, które są przesyłane wraz z progiem, aby można było utworzyć alert. Szczegóły poczty E-mail, adresy URL elementu webhook, szczegóły automatyzacji elementu Runbook i inne akcje muszą być zdefiniowane w grupie akcji najpierw przed utworzeniem alertu. jeden może utworzyć [grupę akcji z Azure monitor](../../azure-monitor/platform/action-groups.md) w portalu lub użyć [grupy akcji — szablon zasobu](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Nazwa elementu | Wymagany | Opis |
|:--|:--|:--|
| AzNsNotification | Tak | Identyfikator zasobu grupy akcji platformy Azure, która ma zostać skojarzona z alertem w celu podjęcia odpowiednich działań w przypadku spełnienia kryteriów alertów. |
| CustomEmailSubject | Nie | Niestandardowy wiersz tematu wiadomości e-mail wysyłanej do wszystkich adresów określonych w skojarzonej grupie akcji. |
| CustomWebhookPayload | Nie | Dostosowany ładunek do wysłania do wszystkich punktów końcowych elementu webhook zdefiniowanych w skojarzonej grupie akcji. Format zależy od tego, co oczekuje element webhook, i powinien być prawidłowym serializowanym kodem JSON. |

## <a name="sample"></a>Przykład

Poniżej znajduje się przykład rozwiązania, które obejmuje następujące zasoby:

- Zapisane wyszukiwanie
- Harmonogram
- Grupa akcji

W przykładzie użyto [standardowych zmiennych parametrów rozwiązania]( solutions-solution-file.md#parameters) , które zwykle są używane w rozwiązaniu, a nie do wartości zakodowana w definicjach zasobów.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

Następujący plik parametrów zawiera przykłady wartości dla tego rozwiązania.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki
* [Dodaj widoki](solutions-resources-views.md) do rozwiązania do zarządzania.
* [Dodaj elementy Runbook usługi Automation i inne zasoby](solutions-resources-automation.md) do rozwiązania do zarządzania.
