---
title: Wyświetlanie dzienników aktywności dla zmian RBAC platformy Azure
description: Wyświetlanie dzienników aktywności dla kontroli dostępu opartej na rolach (Azure RBAC) w ciągu ostatnich 90 dni.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: rolyon
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: d9b39bc9a2f00fe83cae0ff78c6346042967e8bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042139"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Wyświetlanie dzienników aktywności dla zmian RBAC platformy Azure

Czasami potrzebujesz informacji na temat zmian kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, takich jak Inspekcja i rozwiązywanie problemów. W każdej chwili ktoś wprowadza zmiany w przypisaniach ról lub definicjach ról w ramach subskrypcji. zmiany zostaną zarejestrowane w [dzienniku aktywności platformy Azure](../azure-monitor/essentials/platform-logs-overview.md). Możesz wyświetlić dzienniki aktywności, aby zobaczyć wszystkie zmiany RBAC platformy Azure w ciągu ostatnich 90 dni.

## <a name="operations-that-are-logged"></a>Zarejestrowane operacje

Oto operacje związane z usługą Azure RBAC, które są rejestrowane w dzienniku aktywności:

- Utwórz przypisanie roli
- Usuń przypisanie roli
- Utwórz lub zaktualizuj definicję roli niestandardowej
- Usuń definicję roli niestandardowej

## <a name="azure-portal"></a>Azure Portal

Najprostszym sposobem rozpoczęcia pracy jest wyświetlenie dzienników aktywności za pomocą witryny Azure Portal. Poniższy zrzut ekranu przedstawia przykład operacji przypisywania ról w dzienniku aktywności. Zawiera również opcję pobierania dzienników jako plik CSV.

![Dzienniki aktywności przy użyciu portalu — zrzut ekranu](./media/change-history-report/activity-log-portal.png)

Aby uzyskać więcej informacji, kliknij wpis, aby otworzyć okienko podsumowania. Kliknij kartę **JSON** , aby uzyskać szczegółowy dziennik.

![Dzienniki aktywności przy użyciu okna podsumowanie Otwórz okienko podsumowania — zrzut ekranu](./media/change-history-report/activity-log-summary-portal.png)

Dziennik aktywności w portalu ma kilka filtrów. Oto filtry powiązane z usługą Azure RBAC:

| Filtr | Wartość |
| --------- | --------- |
| Kategoria zdarzenia | <ul><li>Administracyjny</li></ul> |
| Operacja | <ul><li>Utwórz przypisanie roli</li><li>Usuń przypisanie roli</li><li>Utwórz lub zaktualizuj definicję roli niestandardowej</li><li>Usuń definicję roli niestandardowej</li></ul> |

Więcej informacji o dziennikach aktywności znajduje się w temacie [Wyświetlanie dzienników aktywności w celu monitorowania akcji na zasobach](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).


## <a name="interpret-a-log-entry"></a>Interpretowanie wpisu dziennika

Dane wyjściowe dziennika z karty JSON, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure mogą zawierać wiele informacji. Poniżej przedstawiono niektóre właściwości klucza do wyszukania podczas próby zinterpretowania wpisu dziennika. Aby dowiedzieć się, jak filtrować dane wyjściowe dziennika za pomocą Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, zobacz następujące sekcje.

> [!div class="mx-tableFixed"]
> | Właściwość | Przykładowe wartości | Opis |
> | --- | --- | --- |
> | Autoryzacja: Akcja | Microsoft.Authorization/roleAssignments/write | Utwórz przypisanie roli |
> |  | Microsoft. Authorization/roleAssignments/Delete | Usuń przypisanie roli |
> |  | Microsoft. Authorization/roleDefinitions/Write | Utwórz lub zaktualizuj definicję roli |
> |  | Microsoft. Authorization/roleDefinitions/Delete | Usuń definicję roli |
> | Autoryzacja: zakres | /subscriptions/{subscriptionId}<br/>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId} | Zakres akcji |
> | Obiekt wywołujący | admin@example.com<br/>Obiektu | Kto zainicjował akcję |
> | eventTimestamp | 2021-03-01T22:07:41.126243 Z | Czas wystąpienia akcji |
> | stan: wartość | Rozpoczęto<br/>Powodzenie<br/>Niepowodzenie | Stan akcji |

## <a name="azure-powershell"></a>Azure PowerShell

Aby wyświetlić dzienniki aktywności z Azure PowerShell, użyj polecenia [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) .

To polecenie wyświetla listę wszystkich zmian przypisań ról w ramach subskrypcji przez ostatnie siedem dni:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

To polecenie wyświetla listę wszystkich zmian definicji ról w grupie zasobów przez ostatnie siedem dni:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

### <a name="filter-log-output"></a>Filtruj dane wyjściowe dziennika

Dane wyjściowe dziennika mogą zawierać wiele informacji. To polecenie wyświetla listę wszystkich zmian przypisań ról i definicji ról w ramach subskrypcji przez ostatnie siedem dni i filtruje dane wyjściowe:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

Poniżej przedstawiono przykład przefiltrowanych danych wyjściowych dziennika podczas tworzenia przypisania roli:

```azurepowershell
Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:42 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:41 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"{roleAssignmentId}","Properties":{"PrincipalId":"{principalId}","PrincipalType":"User","RoleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64","Scope":"/subscriptions/
                          {subscriptionId}/resourceGroups/example-group"}}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

```

Jeśli używasz jednostki usługi do tworzenia przypisań ról, właściwość Caller będzie IDENTYFIKATORem obiektu głównego usługi. Aby uzyskać informacje o jednostce usługi, można użyć [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) .

```Example
Caller                  : {objectId}
EventTimestamp          : 3/1/2021 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wyświetlić dzienniki aktywności przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ monitor Activity-Log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) .

To polecenie wyświetla listę dzienników aktywności w grupie zasobów od 1 marca, patrząc na siedem dni:

```azurecli
az monitor activity-log list --resource-group example-group --start-time 2021-03-01 --offset 7d
```

To polecenie wyświetla listę dzienników aktywności dla dostawcy zasobów autoryzacji od 1 marca, patrząc na siedem dni:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d
```

### <a name="filter-log-output"></a>Filtruj dane wyjściowe dziennika

Dane wyjściowe dziennika mogą zawierać wiele informacji. To polecenie wyświetla listę wszystkich zmian ról i definicji ról w subskrypcji, które przeszukują siedem dni i filtruje dane wyjściowe:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d --query '[].{authorization:authorization, caller:caller, eventTimestamp:eventTimestamp, properties:properties}'
```

Poniżej przedstawiono przykład przefiltrowanych danych wyjściowych dziennika podczas tworzenia przypisania roli:

```azurecli
[
 {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:42.456241+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "serviceRequestId": "{serviceRequestId}",
      "statusCode": "Created"
    }
  },
  {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:41.126243+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "requestbody": "{\"Id\":\"{roleAssignmentId}\",\"Properties\":{\"PrincipalId\":\"{principalId}\",\"PrincipalType\":\"User\",\"RoleDefinitionId\":\"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64\",\"Scope\":\"/subscriptions/{subscriptionId}/resourceGroups/example-group\"}}"
    }
  }
]
```

## <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki Azure monitor](../azure-monitor/logs/log-query-overview.md) to inne narzędzie, za pomocą którego można zbierać i analizować zmiany z usługi Azure RBAC dla wszystkich zasobów platformy Azure. Azure Monitor dzienników ma następujące zalety:

- Napisz złożone zapytania i logikę
- Integracja z alertami, Power BI i innymi narzędziami
- Zapisywanie danych w celu uzyskania dłuższych okresów przechowywania
- Odsyłacze z innymi dziennikami, takimi jak zabezpieczenia, maszyna wirtualna i niestandardowe

Oto podstawowe kroki, które należy wykonać, aby rozpocząć pracę:

1. [Utwórz obszar roboczy log Analytics](../azure-monitor/logs/quick-create-workspace.md).

1. [Skonfiguruj rozwiązanie Activity Log Analytics](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution) dla obszaru roboczego.

1. [Wyświetlanie dzienników aktywności](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution). Aby przejść do strony przeglądu rozwiązań Activity Log Analytics, kliknij opcję **dzienniki** .

   ![Opcja dzienników Azure Monitor w portalu](./media/change-history-report/azure-log-analytics-option.png)

1. Opcjonalnie można użyć [Log Analytics Azure monitor](../azure-monitor/logs/log-analytics-tutorial.md) , aby zbadać i wyświetlić dzienniki. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z zapytaniami dzienników w Azure monitor](../azure-monitor/logs/get-started-queries.md).

Oto zapytanie zwracające nowe przypisania ról uporządkowane według docelowego dostawcy zasobów:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Oto zapytanie, które zwraca zmiany przypisania roli wyświetlane na wykresie:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Dzienniki aktywności przy użyciu portalu analizy zaawansowanej — zrzut ekranu](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Następne kroki
* [Wyświetlanie dzienników aktywności w celu monitorowania akcji dotyczących zasobów](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorowanie działania subskrypcji przy użyciu dziennika aktywności platformy Azure](../azure-monitor/essentials/platform-logs-overview.md)