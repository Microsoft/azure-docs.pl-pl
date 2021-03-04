---
title: Tworzenie alertów metryk dla dzienników w Azure Monitor
description: Samouczek dotyczący tworzenia alertów metryk niemal w czasie rzeczywistym na popularnych danych usługi log Analytics.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: bb9bad1668340182083101ad879ee13e0ca3ea77
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038275"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Tworzenie alertów metryk dla dzienników w Azure Monitor

## <a name="overview"></a>Omówienie

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Można używać alertów metryk dla popularnych dzienników Log Analytics wyodrębnionych jako metryki w ramach metryk z dzienników, w tym zasobów na platformie Azure lub lokalnie. Poniżej wymieniono obsługiwane rozwiązania Log Analytics:

- [Liczniki wydajności](./../agents/data-sources-performance-counters.md) dla maszyn z systemem Windows & Linux
- [Rekordy pulsu dla Agent Health](../insights/solution-agenthealth.md)
- [Aktualizowanie rekordów zarządzania](../../automation/update-management/overview.md)
- Dzienniki [danych zdarzeń](./../agents/data-sources-windows-events.md)

Istnieje wiele korzyści związanych z korzystaniem z **alertów metryk w przypadku dzienników** w ramach [alertów dzienników](./alerts-log.md) opartych na kwerendach na platformie Azure. Poniżej wymieniono niektóre z nich:

- Alerty metryk oferują możliwość monitorowania w czasie niemal rzeczywistym i alerty metryk dla dzienników rozwidlenia danych ze źródła dziennika w celu zapewnienia tego samego.
- Alerty metryk są powiadamiane tylko raz, gdy alert jest uruchamiany i po jego rozwiązaniu. w przeciwieństwie do alertów dziennika, które są bezstanowe i w każdym interwale są uruchamiane w przypadku spełnienia warunku alertu.
- Alerty metryk dla dziennika zapewniają wiele wymiarów, umożliwiając filtrowanie do określonych wartości, takich jak komputery, typ systemu operacyjnego itp. prostsze; bez konieczności wykonywania zapytań dotyczących rozgroszania w programie Analytics.

> [!NOTE]
> Określona Metryka i/lub wymiar będą wyświetlane tylko wtedy, gdy dane dla niego istnieją w wybranym okresie. Te metryki są dostępne dla klientów korzystających z obszarów roboczych usługi Azure Log Analytics.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Metryki i wymiary obsługiwane w przypadku dzienników

 Alerty metryki obsługują alerty dotyczące metryk, które korzystają z wymiarów. Wymiarów można użyć do filtrowania metryki na odpowiedni poziom. Zostanie wyświetlona pełna lista metryk obsługiwanych w przypadku dzienników z [obszarów roboczych log Analytics](../essentials/metrics-supported.md#microsoftoperationalinsightsworkspaces) . między obsługiwanymi rozwiązaniami.

> [!NOTE]
> Aby wyświetlić obsługiwaną metrykę wyodrębnioną z obszaru roboczego Log Analytics za pośrednictwem [metryk Azure monitor-](../essentials/metrics-charts.md)Metrics, należy utworzyć alert dotyczący metryki dla dziennika dla tej konkretnej metryki. Wymiary wybrane w alercie metryk dla dzienników — będą wyświetlane tylko w przypadku eksploracji za pośrednictwem metryk Azure Monitor-Metrics.

## <a name="creating-metric-alert-for-log-analytics"></a>Tworzenie alertu metryki dla Log Analytics

Dane metryk z popularnych dzienników są potokami przed przetworzeniem ich w Log Analytics w metrykach Azure Monitor. Dzięki temu użytkownicy mogą korzystać z możliwości platformy metryk oraz alertu dotyczącego metryk — w tym za pomocą alertów o częstotliwości równej 1 minucie.
Poniżej przedstawiono sposób nastawiania alertu metryki dla dzienników.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Wymagania wstępne dotyczące alertu metryki dzienników

Przed użyciem metryki dzienników zebranych na Log Analytics danych, należy skonfigurować i udostępnić następujące elementy:

1. **Obszar roboczy active log Analytics**: prawidłowy i aktywny obszar roboczy log Analytics musi być obecny. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego log Analytics w Azure Portal](../logs/quick-create-workspace.md).
2. **Agent jest skonfigurowany dla log Analytics obszaru roboczego**: należy skonfigurować agenta dla maszyn wirtualnych platformy Azure (i/lub) lokalnych maszyn wirtualnych do wysyłania danych do log Analytics obszaru roboczego używanego we wcześniejszym kroku. Aby uzyskać więcej informacji, zobacz [log Analytics-Agent — Omówienie](./../agents/agents-overview.md).
3. **Obsługiwane rozwiązania log Analytics są zainstalowane**: rozwiązanie log Analytics należy skonfigurować i wysyłać dane do log Analytics rozwiązania obsługiwane przez obszary robocze są [licznikami wydajności dla systemu Windows & Linux](./../agents/data-sources-performance-counters.md), [rekordy pulsu dla Agent Health](../insights/solution-agenthealth.md), [zarządzania aktualizacjami](../../automation/update-management/overview.md)i [danych zdarzeń](./../agents/data-sources-windows-events.md).
4. **Log Analytics rozwiązania skonfigurowane do wysyłania dzienników**: rozwiązanie log Analytics powinno mieć wymagane dzienniki/dane odpowiadające [metrykom obsługiwanym dla log Analyticsych obszarów roboczych](../essentials/metrics-supported.md#microsoftoperationalinsightsworkspaces) . Na przykład dla licznika *% dostępnej pamięci* należy najpierw skonfigurować w rozwiązaniu [liczniki wydajności](./../agents/data-sources-performance-counters.md) .

## <a name="configuring-metric-alert-for-logs"></a>Konfigurowanie alertu metryki dla dzienników

 Alerty metryk można tworzyć i zarządzać nimi za pomocą Azure Portal, Menedżer zasobów szablonów, interfejsu API REST, programu PowerShell i interfejsu wiersza polecenia platformy Azure. Ponieważ alerty metryk dla dzienników są wariantem alertów metryk — po zakończeniu wymagań wstępnych można utworzyć alert metryki dla dzienników dla określonego obszaru roboczego Log Analytics. Wszystkie cechy i funkcje [alertów dotyczących metryk](./alerts-metric-near-real-time.md) dotyczą również alertów metryk dzienników. obejmuje to schemat ładunku, odpowiednie limity przydziału i cenę rozliczaną.

Aby uzyskać szczegółowe informacje i przykłady — zobacz [tworzenie alertów metryk i zarządzanie nimi](./alerts-metric.md). W przypadku alertów metryk dotyczących dzienników — postępuj zgodnie z instrukcjami dotyczącymi zarządzania alertami metryk i upewnij się, że:

- Element docelowy dla alertu metryki jest prawidłowym *obszarem roboczym log Analytics*
- Wybrany sygnał dla alertu metryki dla wybranego *obszaru roboczego log Analytics* jest typu **Metric**
- Filtrowanie określonych warunków lub zasobów przy użyciu filtrów wymiaru; metryki dzienników są wielowymiarowe
- Podczas konfigurowania *logiki sygnałów* można utworzyć pojedynczy alert w celu rozdzielenia wielu wartości wymiaru (na przykład komputera).
- Jeśli **nie** używasz Azure Portal do tworzenia alertu metryki dla wybranego *obszaru roboczego log Analytics*; następnie użytkownik musi ręcznie utworzyć jawną regułę do konwertowania danych dziennika na metrykę przy użyciu [reguł zaplanowanych zapytań Azure monitor](/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> Podczas tworzenia alertu dotyczącego metryki dla Log Analytics obszaru roboczego za pomocą reguły Azure Portal, aby przekonwertować dane dziennika do metryki za pośrednictwem [Azure monitor zaplanowane reguły zapytań](/rest/api/monitor/scheduledqueryrules) są tworzone automatycznie w tle, *bez konieczności interwencji użytkownika lub akcji*. Aby uzyskać alert dotyczący metryki tworzenia dzienników przy użyciu opcji inne niż Azure Portal, zobacz temat [szablon zasobów dla alertów metryk dotyczących dzienników](#resource-template-for-metric-alerts-for-logs) w temacie przykład tworzenia dziennika opartego na ScheduledQueryRule w celu uzyskania reguły konwersji metryki przed utworzeniem alertu dotyczącego metryki. w przeciwnym razie nie będzie żadnych danych dla alertu metryki dla tworzonych dzienników.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Szablon zasobu dla alertów metryk dzienników

Jak wspomniano wcześniej, proces tworzenia alertów metryk z dzienników to dwie dwutorowe:

1. Utwórz regułę wyodrębniania metryk z obsługiwanych dzienników przy użyciu interfejsu API scheduledQueryRule
2. Utwórz alert metryki dla metryki wyodrębnionej z dziennika (w krok 1) i Log Analytics obszar roboczy jako zasób docelowy

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Alerty metryk dla dzienników ze statycznym progiem

Aby osiągnąć ten sam, jeden może użyć przykładowego szablonu Azure Resource Manager poniżej — w przypadku tworzenia alertu metryki statycznej progu zależą od pomyślnego utworzenia reguły wyodrębniania metryk z dzienników za pośrednictwem scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example: /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Załóżmy, że powyższy kod JSON jest zapisywany jako metricfromLogsAlertStatic.js, a następnie można go połączyć z plikiem JSON parametru dla tworzenia opartego na szablonie zasobów. Poniżej znajduje się przykładowy plik JSON parametru:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan"
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Przy założeniu, że powyższy plik parametrów jest zapisywany jako metricfromLogsAlertStatic.parameters.js; następnie jeden może utworzyć alert dotyczący metryk dla dzienników przy użyciu [szablonu zasobu do utworzenia w Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

Alternatywnie można również użyć poniższego polecenia Azure PowerShell:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Lub Użyj szablonu wdrażanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az deployment group create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Alerty metryk dla dzienników z progami dynamicznymi

Aby osiągnąć ten sam, jeden może użyć przykładowego szablonu Azure Resource Manager, w którym tworzenie alertu metryki dynamicznych jest zależne od pomyślnego utworzenia reguły do wyodrębniania metryk z dzienników za pośrednictwem scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example: /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Załóżmy, że powyższy kod JSON jest zapisywany jako metricfromLogsAlertDynamic.js, a następnie można go połączyć z plikiem JSON parametru dla tworzenia opartego na szablonie zasobów. Poniżej znajduje się przykładowy plik JSON parametru:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule"
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterOrLessThan"
          },
          "alertSensitivity": {
              "value": "Medium"
          },
          "numberOfEvaluationPeriods": {
              "value": "4"
          },
          "minFailingPeriodsToAlert": {
              "value": "3"
          },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Przy założeniu, że powyższy plik parametrów jest zapisywany jako metricfromLogsAlertDynamic.parameters.js; następnie jeden może utworzyć alert dotyczący metryk dla dzienników przy użyciu [szablonu zasobu do utworzenia w Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

Alternatywnie można również użyć poniższego polecenia Azure PowerShell:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Lub Użyj szablonu wdrażanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az deployment group create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [alertów dotyczących metryk](../alerts/alerts-metric.md).
- Dowiedz się więcej [na temat alertów dziennika na platformie Azure](./alerts-unified-log.md).
- Dowiedz się więcej o [alertach na platformie Azure](./alerts-overview.md).