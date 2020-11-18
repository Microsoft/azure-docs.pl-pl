---
title: Wyświetlanie dzienników aktywności dla zmian RBAC platformy Azure
description: Wyświetlanie dzienników aktywności na potrzeby kontroli dostępu opartej na rolach (Azure RBAC) na potrzeby zasobów platformy Azure w ciągu ostatnich 90 dni.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: 4ccd668fb6afa6787fadeda6ed92ebd954e2b892
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657809"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Wyświetlanie dzienników aktywności dla zmian RBAC platformy Azure

Czasami potrzebujesz informacji na temat zmian kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, takich jak Inspekcja i rozwiązywanie problemów. W każdej chwili ktoś wprowadza zmiany w przypisaniach ról lub definicjach ról w ramach subskrypcji. zmiany zostaną zarejestrowane w [dzienniku aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md). Możesz wyświetlić dzienniki aktywności, aby zobaczyć wszystkie zmiany RBAC platformy Azure w ciągu ostatnich 90 dni.

## <a name="operations-that-are-logged"></a>Zarejestrowane operacje

Oto operacje związane z usługą Azure RBAC, które są rejestrowane w dzienniku aktywności:

- Utwórz przypisanie roli
- Usuń przypisanie roli
- Utwórz lub zaktualizuj definicję roli niestandardowej
- Usuń definicję roli niestandardowej

## <a name="azure-portal"></a>Azure Portal

Najprostszym sposobem rozpoczęcia pracy jest wyświetlenie dzienników aktywności za pomocą witryny Azure Portal. Poniższy zrzut ekranu przedstawia przykład operacji przypisywania ról w dzienniku aktywności. Zawiera również opcję pobierania dzienników jako plik CSV.

![Dzienniki aktywności przy użyciu portalu — zrzut ekranu](./media/change-history-report/activity-log-portal.png)

Dziennik aktywności w portalu ma kilka filtrów. Oto filtry powiązane z usługą Azure RBAC:

| Filtr | Wartość |
| --------- | --------- |
| Kategoria zdarzenia | <ul><li>Administracyjny</li></ul> |
| Operacja | <ul><li>Utwórz przypisanie roli</li><li>Usuń przypisanie roli</li><li>Utwórz lub zaktualizuj definicję roli niestandardowej</li><li>Usuń definicję roli niestandardowej</li></ul> |

Więcej informacji o dziennikach aktywności znajduje się w temacie [Wyświetlanie dzienników aktywności w celu monitorowania akcji na zasobach](../azure-resource-manager/management/view-activity-logs.md?toc=%252fazure%252fmonitoring-and-diagnostics%252ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Aby wyświetlić dzienniki aktywności z Azure PowerShell, użyj polecenia [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) .

To polecenie wyświetla listę wszystkich zmian przypisań ról w ramach subskrypcji przez ostatnie siedem dni:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

To polecenie wyświetla listę wszystkich zmian definicji ról w grupie zasobów przez ostatnie siedem dni:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

To polecenie wyświetla listę wszystkich zmian przypisań ról i definicji ról w ramach subskrypcji przez ostatnie siedem dni, a następnie wyświetla wyniki na liście:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111
                          eventCategory  : Administrative

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

Jeśli używasz nazwy głównej usługi do tworzenia przypisań ról, właściwość Caller będzie IDENTYFIKATORem obiektu. Aby uzyskać informacje o jednostce usługi, można użyć [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) .

```Example
Caller                  : 44444444-4444-4444-4444-444444444444
EventTimestamp          : 6/4/2020 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: 55555555-5555-5555-5555-555555555555
                          category       : Administrative
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wyświetlić dzienniki aktywności przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ monitor Activity-Log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) .

To polecenie wyświetla listę dzienników aktywności w grupie zasobów od 27 lutego, szukając do przodu siedem dni:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

To polecenie wyświetla listę dzienników aktywności dostawcy zasobów autoryzacji od 27 lutego, szukając do przodu siedem dni:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki Azure monitor](../azure-monitor/log-query/log-query-overview.md) to inne narzędzie, za pomocą którego można zbierać i analizować zmiany z usługi Azure RBAC dla wszystkich zasobów platformy Azure. Azure Monitor dzienników ma następujące zalety:

- Napisz złożone zapytania i logikę
- Integracja z alertami, Power BI i innymi narzędziami
- Zapisywanie danych w celu uzyskania dłuższych okresów przechowywania
- Odsyłacze z innymi dziennikami, takimi jak zabezpieczenia, maszyna wirtualna i niestandardowe

Oto podstawowe kroki, które należy wykonać, aby rozpocząć pracę:

1. [Utwórz obszar roboczy log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Skonfiguruj rozwiązanie Activity Log Analytics](../azure-monitor/platform/activity-log.md#activity-log-analytics-monitoring-solution) dla obszaru roboczego.

1. [Wyświetlanie dzienników aktywności](../azure-monitor/platform/activity-log.md#activity-log-analytics-monitoring-solution). Aby przejść do strony przeglądu rozwiązań Activity Log Analytics, kliknij opcję **dzienniki** .

   ![Opcja dzienników Azure Monitor w portalu](./media/change-history-report/azure-log-analytics-option.png)

1. Opcjonalnie można użyć [Log Analytics Azure monitor](../azure-monitor/log-query/log-analytics-tutorial.md) , aby zbadać i wyświetlić dzienniki. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z dziennikami Azure monitor](../azure-monitor/log-query/get-started-queries.md).

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
* [Wyświetlanie zdarzeń w dzienniku aktywności](../azure-resource-manager/management/view-activity-logs.md?toc=%252fazure%252fmonitoring-and-diagnostics%252ftoc.json)
* [Monitorowanie aktywności subskrypcji za pomocą dziennika aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md)