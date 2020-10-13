---
title: Dowiedz się, w jaki sposób integracja aprowizacji jest zintegrowana z dziennikami Azure Monitor w Azure Active Directory.
description: Dowiedz się, w jaki sposób integracja aprowizacji jest zintegrowana z dziennikami Azure Monitor w Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 4e14bd6365ce53d98d6e0b0d1f2601ff3b3e59b4
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997310"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>Informacje o sposobie integracji aprowizacji z dziennikami Azure Monitor

Inicjowanie obsługi integruje się z dziennikami Azure Monitor i Log Analytics. Usługa Azure Monitoring umożliwia wykonywanie takich czynności jak tworzenie skoroszytów, znanych również jako pulpitów nawigacyjnych, przechowywanie dzienników aprowizacji przez 30 dni i tworzenie niestandardowych zapytań i alertów. W tym artykule omówiono sposób integrowania dzienników aprowizacji z dziennikami Azure Monitor. Aby dowiedzieć się więcej o tym, jak dzienniki aprowizacji są zwykle wykonywane, zobacz [dzienniki aprowizacji](../reports-monitoring/concept-provisioning-logs.md).

## <a name="enabling-provisioning-logs"></a>Włączanie dzienników aprowizacji

Powinieneś już znać monitorowanie i Log Analytics platformy Azure. Jeśli nie, przejdź do informacji o nich, a następnie wróć do informacji o dziennikach aprowizacji aplikacji. Aby dowiedzieć się więcej na temat monitorowania platformy Azure, zobacz [Azure monitor Omówienie](../../azure-monitor/overview.md). Aby dowiedzieć się więcej na temat Azure Monitor dzienników i Log Analytics, zobacz [Omówienie zapytań dzienników w Azure monitor](../../azure-monitor/log-query/log-query-overview.md).

Po skonfigurowaniu usługi Azure Monitoring można włączyć obsługę administracyjną w dziennikach. Ta opcja znajduje się na stronie **Ustawienia diagnostyki** .

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="Dostęp do ustawień diagnostycznych" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="Dostęp do ustawień diagnostycznych" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> Jeśli niedawno zainicjowano już obszar roboczy, może upłynąć trochę czasu, zanim będzie można wysłać do niego dzienniki. Jeśli wystąpi błąd, że subskrypcja nie jest zarejestrowana do korzystania z usługi *Microsoft. Insights* , sprawdź ponownie za kilka minut.
 
## <a name="understanding-the-data"></a>Zrozumienie danych
Źródłowy strumień danych, który umożliwia wysyłanie podglądów dzienników, jest prawie identyczny. Dzienniki Azure Monitor są niemal takie same jak interfejs użytkownika Azure Portal i interfejs API platformy Azure. Istnieją tylko pewne **różnice** w polach dziennika, które opisano w poniższej tabeli. Aby dowiedzieć się więcej na temat tych pól, zobacz [Lista provisioningObjectSummary](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http&preserve-view=true).

|Dzienniki usługi Azure Monitor   |Interfejs użytkownika Azure Portal   |Interfejs API platformy Azure |
|----------|-----------|------------|
|errorDescription |reason |resultDescription |
|status |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Skoroszyty usługi Azure Monitor

Azure Monitor skoroszyty zapewniają elastyczną kanwę do analizy danych. Umożliwiają one również tworzenie zaawansowanych raportów wizualnych w ramach Azure Portal. Aby dowiedzieć się więcej, zobacz [Azure monitor skoroszyty — Omówienie](../../azure-monitor/platform/workbooks-overview.md).

Inicjowanie obsługi aplikacji zawiera zestaw wstępnie skompilowanych skoroszytów. Można je znaleźć na stronie skoroszytów. Aby wyświetlić dane, należy upewnić się, że wszystkie filtry (timeRange, jobID, nazwa_aplikacji) są wypełnione. Należy również upewnić się, że zainicjowano udostępnianie aplikacji. w przeciwnym razie nie będzie żadnych danych w dziennikach.

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="Dostęp do ustawień diagnostycznych" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="Dostęp do ustawień diagnostycznych" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>Zapytania niestandardowe

Możesz tworzyć niestandardowe zapytania i wyświetlać dane na pulpitach nawigacyjnych platformy Azure. Aby dowiedzieć się, jak to zrobić, zobacz [Tworzenie i udostępnianie pulpitów nawigacyjnych danych log Analytics](../../azure-monitor/log-query/get-started-queries.md). Pamiętaj również, aby zapoznać się [z omówieniem zapytań dzienników w Azure monitor](../../azure-monitor/log-query/log-query-overview.md).

Poniżej przedstawiono przykłady umożliwiające rozpoczęcie pracy z obsługą aplikacji.

Zbadaj dzienniki dla użytkownika a na podstawie jego identyfikatora w systemie źródłowym:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

Liczba podsumowań na kod błędu:
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

Podsumowanie liczby zdarzeń dziennie według akcji:
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

Podejmij 100 zdarzeń i właściwości klucza projektu:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>Alerty niestandardowe

Azure Monitor umożliwia skonfigurowanie niestandardowych alertów, aby można było otrzymywać powiadomienia o kluczowym zdarzeniu związanym z obsługą administracyjną. Na przykład może być konieczne otrzymywanie alertu w przypadku wystąpienia błędów. Lub prawdopodobnie zostanie przełączane do wyłączania lub usuwania. Innym przykładem, w którym można chcieć otrzymywać alerty, jest brak ewentualnej aprowizacji, co oznacza, że coś jest niewłaściwe.

Aby dowiedzieć się więcej o alertach, zobacz [reagowanie na zdarzenia przy użyciu alertów Azure monitor](../../azure-monitor/learn/tutorial-response.md).

Zgłoś alert, gdy wystąpią błędy. Zastąp jobID identyfikatorem jobID dla aplikacji.

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="Dostęp do ustawień diagnostycznych" lightbox="media/application-provisioning-log-analytics/alert1.png":::

Może wystąpić problem, który spowodował zatrzymanie działania usługi aprowizacji. Użyj poniższego alertu, aby wykryć, kiedy nie ma żadnych zdarzeń aprowizacji w danym przedziale czasu.

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="Dostęp do ustawień diagnostycznych" lightbox="media/application-provisioning-log-analytics/alert2.png":::

Zgłoś alert, gdy nastąpi wyłączenie lub usunięcie elementu.

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="Dostęp do ustawień diagnostycznych" lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>Materiały przekazywane przez społeczność

Podejmujemy podejście oparte na metodzie "open source" i "społeczność" do zapytań i pulpitów nawigacyjnych dotyczących aprowizacji aplikacji. Jeśli utworzono zapytanie, alert lub skoroszyt, które mogą być przydatne innym osobom, należy je opublikować w [repozytorium GitHub AzureMonitorCommunity](https://github.com/microsoft/AzureMonitorCommunity). Następnie podaj wiadomość e-mail z linkiem. Przejdziemy do usługi i opublikujemy ją w usłudze, aby inni mogli korzystać z niej. Możesz skontaktować się z nami pod adresem provisioningfeedback@microsoft.com .

## <a name="next-steps"></a>Następne kroki

- [Analiza dzienników](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Rozpoczynanie pracy z zapytaniami w dziennikach Azure Monitor](../../azure-monitor/log-query/get-started-queries.md)
- [Tworzenie grup alertów i zarządzanie nimi w Azure Portal](../../azure-monitor/platform/action-groups.md)
- [Instalowanie i Używanie widoków usługi log Analytics na potrzeby Azure Active Directory](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [Interfejs API dzienników aprowizacji](https://docs.microsoft.com/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta.md&preserve-view=true)
