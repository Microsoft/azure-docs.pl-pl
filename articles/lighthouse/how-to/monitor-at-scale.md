---
title: Monitorowanie delegowanych zasobów na dużą skalę
description: Dowiedz się, jak efektywnie korzystać z dzienników Azure Monitor w sposób skalowalny dla dzierżaw klientów, którymi zarządzasz.
ms.date: 01/07/2021
ms.topic: how-to
ms.openlocfilehash: 797a6159d310f85c35c7eb550f05ff152526b3e8
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131155"
---
# <a name="monitor-delegated-resources-at-scale"></a>Monitorowanie delegowanych zasobów na dużą skalę

Jako dostawca usług możesz dołączyć wielu dzierżawców klientów do [usługi Azure Lighthouse](../overview.md). Usługa Azure Lighthouse umożliwia dostawcom usług wykonywanie operacji na dużą skalę w wielu dzierżawcach, co sprawia, że zadania zarządzania są bardziej wydajne.

W tym temacie przedstawiono sposób korzystania z [dzienników Azure monitor](../../azure-monitor/platform/data-platform-logs.md) w sposób skalowalny dla dzierżaw klientów, którymi zarządzasz. Chociaż odwołujemy się do dostawców usług i klientów w tym temacie, te wskazówki dotyczą również [przedsiębiorstw korzystających z usługi Azure Lighthouse do zarządzania wieloma dzierżawcami](../concepts/enterprise.md).

> [!NOTE]
> Upewnij się, że użytkownicy w dzierżawach zarządzali mają przypisane [role niezbędne do zarządzania obszarami roboczymi log Analytics](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions) w delegowanych subskrypcjach klientów.

## <a name="create-log-analytics-workspaces"></a>Tworzenie Log Analytics obszarów roboczych

Aby zbierać dane, musisz utworzyć Log Analytics obszary robocze. Te obszary robocze Log Analytics są unikatowymi środowiskami dla danych zbieranych przez Azure Monitor. Każdy obszar roboczy ma własne repozytorium danych i konfigurację, a źródła danych i rozwiązania są skonfigurowane do przechowywania danych w określonym obszarze roboczym.

Zalecamy tworzenie tych obszarów roboczych bezpośrednio w dzierżawach klientów. W ten sposób ich dane pozostają w swoich dzierżawcach, a nie eksportowane do użytkownika. Umożliwia to również Scentralizowane monitorowanie wszelkich zasobów i usług obsługiwanych przez Log Analytics, co zapewnia większą elastyczność w zakresie typów monitorowanych danych.

> [!TIP]
> Wszystkie konta usługi Automation używane do uzyskiwania dostępu do danych z obszaru roboczego Log Analytics muszą zostać utworzone w tej samej dzierżawie, w której znajduje się obszar roboczy.

Obszar roboczy Log Analytics można utworzyć przy użyciu [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md), przy użyciu [interfejsu wiersza polecenia platformy Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)lub przy użyciu [Azure PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md).

> [!IMPORTANT]
> Nawet jeśli wszystkie obszary robocze są tworzone w dzierżawie klienta, dostawca zasobów Microsoft. Insights musi być również zarejestrowany w ramach subskrypcji zarządzania.

## <a name="deploy-policies-that-log-data"></a>Wdrażanie zasad, które rejestrują dane

Po utworzeniu obszarów roboczych Log Analytics można wdrożyć [Azure Policy](../../governance/policy/index.yml) między hierarchiami klientów, aby dane diagnostyczne były wysyłane do odpowiedniego obszaru roboczego w każdej dzierżawie. Dokładne wdrożone zasady mogą się różnić w zależności od typów zasobów, które mają być monitorowane.

Aby dowiedzieć się więcej o tworzeniu zasad, zobacz [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../../governance/policy/tutorials/create-and-manage.md). To [Narzędzie Community](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) zawiera skrypt ułatwiający Tworzenie zasad w celu monitorowania wybranych typów zasobów.

Po ustaleniu zasad, które mają zostać wdrożone, można [je wdrożyć w ramach delegowanych subskrypcji na dużą skalę](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analizowanie zebranych danych

Po wdrożeniu zasad dane będą rejestrowane w obszarach roboczych Log Analytics utworzonych w każdej dzierżawie klienta. Aby uzyskać szczegółowe informacje na temat wszystkich zarządzanych klientów, można użyć narzędzi, takich jak [Azure monitor skoroszyty](../../azure-monitor/platform/workbooks-overview.md) do zbierania i analizowania informacji z wielu źródeł danych.

## <a name="view-alerts-across-customers"></a>Wyświetlanie alertów dla klientów

[Alerty](../../azure-monitor/platform/alerts-overview.md) dla delegowanych subskrypcji można wyświetlać w dzierżawach klientów zarządzanych przez użytkownika.

Aby automatycznie odświeżać alerty w wielu klientach, należy użyć zapytania [grafu zasobów platformy Azure](../../governance/resource-graph/overview.md) do filtrowania alertów. Możesz przypiąć zapytanie do pulpitu nawigacyjnego i wybrać wszystkich odpowiednich klientów i subskrypcje.

Następujące przykładowe zapytanie będzie wyświetlać alerty o ważności 0 i 1, odświeżanie co 60 minut.

```kusto
alertsmanagementresources
| where type == "microsoft.alertsmanagement/alerts"
| where properties.essentials.severity =~ "Sev0" or properties.essentials.severity =~ "Sev1"
| where properties.essentials.monitorCondition == "Fired"
| where properties.essentials.startDateTime > ago(60m)
| project StartTime=properties.essentials.startDateTime,name,Description=properties.essentials.description, Severity=properties.essentials.severity, subscriptionId
| sort by tostring(StartTime)
```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z skompilowanym przykładowym skoroszytem programu MVP](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks), który śledzi raportowanie zgodności poprawek, wykonując [zapytania dotyczące Update Management dzienników](../../automation/update-management/query-logs.md) w wielu log Analytics obszarach roboczych. 
- Dowiedz się więcej na temat [Azure monitor](../../azure-monitor/index.yml).
- Dowiedz się więcej na temat [dzienników Azure monitor](../../azure-monitor/platform/data-platform-logs.md).
- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).