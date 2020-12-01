---
title: Zbieranie & analizowanie dzienników zasobów
description: Rejestruj i Analizuj zdarzenia dziennika zasobów dla Azure Container Registry takich jak uwierzytelnianie, wypychanie obrazów i ściąganie obrazów.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 8b05d34e6c50fea3760e30d28f59e55d8c5f211a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348589"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Dzienniki Azure Container Registry na potrzeby oceny i inspekcji diagnostyki

W tym artykule wyjaśniono, jak zbierać dane dziennika dla usługi Azure Container Registry przy użyciu funkcji programu [Azure monitor](../azure-monitor/overview.md). Azure Monitor zbiera [dzienniki zasobów](../azure-monitor/platform/platform-logs-overview.md) (wcześniej nazywane *dziennikami diagnostycznymi*) dla zdarzeń sterowanych przez użytkownika w rejestrze. Zbieraj dane i korzystaj z nich w celu zaspokajania potrzeb, takich jak:

* Inspekcja zdarzeń uwierzytelniania rejestru w celu zapewnienia bezpieczeństwa i zgodności 

* Zapewnij kompletną aktywność dla artefaktów rejestru, takich jak zdarzenia ściągania i ściągania, dzięki czemu można zdiagnozować problemy operacyjne z rejestrem 

Zbieranie danych dzienników zasobów przy użyciu Azure Monitor może pociągnąć za sobą dodatkowe koszty. Zobacz [cennik Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Zdarzenia repozytorium

Następujące zdarzenia na poziomie repozytorium dla obrazów i innych artefaktów są obecnie rejestrowane:

* **Wydajności**
* **Pobierać**
* **Untag**
* **Usuń** (łącznie ze zdarzeniami usunięcia repozytorium)
* **Wyczyść tag** i **Przeczyść manifest**

> [!NOTE]
> Zdarzenia przeczyszczania są rejestrowane tylko wtedy, gdy skonfigurowano [zasady przechowywania](container-registry-retention-policy.md) rejestru.

## <a name="registry-resource-logs"></a>Dzienniki zasobów rejestru

Dzienniki zasobów zawierają informacje wyemitowane przez zasoby platformy Azure opisujące ich wewnętrzną operację. W przypadku usługi Azure Container Registry dzienniki zawierają zdarzenia uwierzytelniania i na poziomie repozytorium przechowywane w poniższych tabelach. 

* **ContainerRegistryLoginEvents**  — zdarzenia i stan uwierzytelniania rejestru, w tym przychodzącą tożsamość i adres IP
* **ContainerRegistryRepositoryEvents** — operacje, takie jak wypychanie i ściąganie dla obrazów i innych artefaktów w repozytoriach rejestru
* **AzureMetrics**  -  [Metryki rejestru kontenerów](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) , takie jak agregowane liczby wypychania i ściągania.

W przypadku operacji dane dziennika obejmują:
  * Stan powodzenia lub niepowodzenia
  * Sygnatury czasowe rozpoczęcia i zakończenia

Oprócz dzienników zasobów platforma Azure udostępnia [Dziennik aktywności](../azure-monitor/platform/platform-logs-overview.md), pojedynczy rekord poziomu subskrypcji zdarzeń zarządzania platformy Azure, taki jak tworzenie lub usuwanie rejestru kontenerów.

## <a name="enable-collection-of-resource-logs"></a>Włącz zbieranie dzienników zasobów

Kolekcja dzienników zasobów dla rejestru kontenerów nie jest domyślnie włączona. Jawnie Włącz ustawienia diagnostyczne dla każdego rejestru, który ma być monitorowany. Aby uzyskać opcje włączania ustawień diagnostycznych, zobacz [Tworzenie ustawień diagnostycznych w celu zbierania dzienników platformy i metryk na platformie Azure](../azure-monitor/platform/diagnostic-settings.md).

Aby na przykład wyświetlić dzienniki i metryki dla rejestru kontenerów niemal w czasie rzeczywistym w Azure Monitor, Zbierz dzienniki zasobów w Log Analytics obszarze roboczym. Aby włączyć to ustawienie diagnostyczne przy użyciu Azure Portal:

1. Jeśli nie masz jeszcze obszaru roboczego, Utwórz obszar roboczy przy użyciu [Azure Portal](../azure-monitor/learn/quick-create-workspace.md). Aby zminimalizować opóźnienie w zbieraniu danych, upewnij się, że obszar roboczy znajduje się w tym **samym regionie** co rejestr kontenerów.
1. W portalu wybierz rejestr, a następnie wybierz pozycję **monitorowanie > ustawienia diagnostyczne > Dodaj ustawienie diagnostyczne**.
1. Wprowadź nazwę ustawienia, a następnie wybierz pozycję **Wyślij do log Analytics**.
1. Wybierz obszar roboczy dzienników diagnostycznych rejestru.
1. Wybierz dane dziennika, które chcesz zebrać, a następnie kliknij przycisk **Zapisz**.

Na poniższej ilustracji przedstawiono tworzenie ustawień diagnostycznych dla rejestru przy użyciu portalu.

![Włączanie ustawień diagnostycznych](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Zbierz tylko potrzebne dane, Zrównoważ koszt i wymagania dotyczące monitorowania. Na przykład, jeśli konieczne jest tylko Inspekcja zdarzeń uwierzytelniania, należy wybrać tylko dziennik **ContainerRegistryLoginEvents** . 

## <a name="view-data-in-azure-monitor"></a>Wyświetlanie danych w Azure Monitor

Po włączeniu zbierania dzienników diagnostycznych w Log Analytics może upłynąć kilka minut, zanim dane będą widoczne w Azure Monitor. Aby wyświetlić dane w portalu, wybierz rejestr, a następnie wybierz pozycję **monitorowanie > dzienników**. Wybierz jedną z tabel zawierających dane dla rejestru. 

Uruchom zapytania, aby wyświetlić dane. Dostępne są kilka przykładowych zapytań lub własne. Na przykład następujące zapytanie pobiera ostatnie 24 godziny danych z tabeli **ContainerRegistryRepositoryEvents** :

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

Na poniższej ilustracji przedstawiono przykładowe dane wyjściowe:

![Dane dziennika zapytań](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Aby zapoznać się z samouczkiem dotyczącym używania Log Analytics w Azure Portal, zobacz [wprowadzenie do Azure Monitor Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md)lub wypróbuj [środowisko demonstracyjne](https://portal.loganalytics.io/demo)log Analytics. 

Aby uzyskać więcej informacji na temat zapytań dzienników, zobacz [Omówienie zapytań dzienników w Azure monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="query-examples"></a>Przykłady zapytań

### <a name="error-events-from-the-last-hour"></a>Zdarzenia błędów z ostatniej godziny

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>100 ostatnich zdarzeń rejestru

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>Tożsamość użytkownika lub obiektu, który usunął repozytorium

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>Tożsamość użytkownika lub obiektu, który usunął tag

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>Niepowodzenia operacji na poziomie repozytorium

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>Błędy uwierzytelniania rejestru

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="additional-log-destinations"></a>Dodatkowe miejsca docelowe dzienników

Oprócz wysyłania dzienników do Log Analytics lub jako alternatywy typowym scenariuszem jest wybranie konta usługi Azure Storage jako miejsca docelowego dziennika. Aby zarchiwizować dzienniki w usłudze Azure Storage, należy utworzyć konto magazynu przed włączeniem archiwizowania za pomocą ustawień diagnostycznych.

Zdarzenia dzienników diagnostycznych można przesyłać strumieniowo do [centrum zdarzeń platformy Azure](../event-hubs/event-hubs-about.md). Event Hubs może pozyskać miliony zdarzeń na sekundę, które można następnie przekształcać i przechowywać za pomocą dowolnego dostawcy analiz w czasie rzeczywistym. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat używania [log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md) i tworzenia [zapytań dzienników](../azure-monitor/log-query/get-started-queries.md).
* Zobacz [Omówienie dzienników platformy Azure](../azure-monitor/platform/platform-logs-overview.md) , aby dowiedzieć się więcej o dziennikach platformy, które są dostępne na różnych warstwach platformy Azure.