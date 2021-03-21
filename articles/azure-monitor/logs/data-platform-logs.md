---
title: Dzienniki usługi Azure Monitor
description: Opisuje dzienniki Azure Monitor, które są używane do zaawansowanej analizy danych monitorowania.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 5066264777c66369205489fb27a6f9206c5da521
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708003"
---
# <a name="azure-monitor-logs-overview"></a>Przegląd dzienników usługi Azure Monitor
Azure Monitor logs to funkcja Azure Monitor, która gromadzi i organizuje dane dziennika i wydajności z [monitorowanych zasobów](../monitor-reference.md). Dane z różnych źródeł, takich jak [dzienniki platformy](../essentials/platform-logs-overview.md) z usług platformy Azure, dane dziennika i wydajności z [agentów maszyn wirtualnych](../agents/agents-overview.md)oraz dane dotyczące użycia i wydajności z [aplikacji](../app/app-insights-overview.md) , można skonsolidować w jednym obszarze roboczym, dzięki czemu można je analizować przy użyciu zaawansowanego języka zapytań, który umożliwia szybkie analizowanie milionów rekordów. Można wykonać proste zapytanie, które po prostu Pobiera określony zestaw rekordów lub przeprowadza zaawansowane analizy danych w celu zidentyfikowania kluczowych wzorców w danych monitorowania. Pracuj z kwerendami dzienników i ich wyniki interaktywnie przy użyciu Log Analytics, korzystaj z nich w regułach alertów, aby otrzymywać powiadomienia o problemach lub wizualizować ich wyniki w skoroszycie lub pulpicie nawigacyjnym.

> [!NOTE]
> Azure Monitor dzienników to jedna połowa platformy danych obsługującej Azure Monitor. Druga [Azure monitor metryki](../essentials/data-platform-metrics.md) , które przechowują dane liczbowe w bazie danych szeregów czasowych. Dzięki temu dane są bardziej uproszczone niż dane w Azure Monitor dziennikach i mogą obsługiwać niemal scenariusze w czasie rzeczywistym, dzięki czemu są szczególnie przydatne w przypadku alertów i szybkiego wykrywania problemów. Metryki, które mogą przechowywać dane liczbowe tylko w określonej strukturze, podczas gdy dzienniki mogą przechowywać różne różne typy danych z ich własnymi strukturami. Możesz również wykonać złożoną analizę danych dzienników przy użyciu zapytań dziennika, które nie mogą być używane do analizy danych metryk.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Co możesz zrobić z dziennikami Azure Monitor?
W poniższej tabeli opisano niektóre różne sposoby używania dzienników w Azure Monitor:

|  |  |
|:---|:---|
| **Analiza** | Użyj [log Analytics](./log-analytics-tutorial.md) w Azure Portal, aby napisać [zapytania dzienników](./log-query-overview.md) i interaktywnie analizować dane dzienników przy użyciu zaawansowanego aparatu analizy |
| **Alert** | Skonfiguruj [regułę alertu dziennika](../alerts/alerts-log.md) , która wysyła powiadomienie lub wykonuje [automatyczne działanie](../alerts/action-groups.md) , gdy wyniki zapytania pasują do określonego wyniku. |
| **Wizualizacja** | Przypnij wyniki zapytania jako tabele lub wykresy do [pulpitu nawigacyjnego platformy Azure](../../azure-portal/azure-portal-dashboards.md).<br>Utwórz [skoroszyt](../visualize/workbooks-overview.md) , aby połączyć się z wieloma zestawami danych w raporcie interaktywnym. <br>Eksportuj wyniki zapytania, aby [Power BI](../visualize/powerbi.md) do używania różnych wizualizacji i udostępniania użytkownikom spoza platformy Azure.<br>Eksportuj wyniki zapytania do [Grafana](../visualize/grafana-plugin.md) , aby wykorzystać jego pulpit nawigacyjny i połączyć się z innymi źródłami danych.|
| **Insights** | Obsługa [szczegółowych](../monitor-reference.md#insights-and-core-solutions) informacji, które zapewniają dostosowane środowisko monitorowania dla określonych aplikacji i usług.  |
| **Odczytać** | Uzyskiwanie dostępu do wyników zapytania dziennika z wiersza polecenia przy użyciu [interfejsu CLI platformy Azure](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Uzyskuj dostęp do wyników zapytania dziennika z wiersza polecenia przy użyciu [poleceń cmdlet programu PowerShell](/powershell/module/az.operationalinsights).<br>Uzyskiwanie dostępu do wyników zapytania dziennika z aplikacji niestandardowej przy użyciu [interfejsu API REST](https://dev.loganalytics.io/). |
| **Eksportowanie** | Skonfiguruj [Automatyczne eksportowanie danych dziennika](./logs-data-export.md) do konta usługi Azure Storage lub usługi Azure Event Hubs.<br>Utwórz przepływ pracy w celu pobrania danych dziennika i skopiuj go do lokalizacji zewnętrznej przy użyciu [Logic Apps](./logicapp-flow-connector.md). |

![Dzienniki — Omówienie](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Zbieranie danych
Po utworzeniu obszaru roboczego Log Analytics należy skonfigurować różne źródła, aby wysłać ich dane. Żadne dane nie są zbierane automatycznie. Ta konfiguracja będzie różnić się w zależności od źródła danych. Na przykład [Tworzenie ustawień diagnostycznych](../essentials/diagnostic-settings.md) w celu wysyłania dzienników zasobów z zasobów platformy Azure do obszaru roboczego. [Włączenie usługi VM Insights](../vm/vminsights-enable-overview.md) w celu zbierania danych z maszyn wirtualnych. Skonfiguruj [źródła danych w obszarze roboczym](../agents/data-sources.md) , aby zbierać dodatkowe zdarzenia i dane wydajności.

- Zobacz, [co jest monitorowane przez Azure monitor?](../monitor-reference.md) , aby uzyskać pełną listę źródeł danych, które można skonfigurować w celu wysyłania danych do dzienników Azure monitor.


## <a name="log-analytics-workspaces"></a>Obszary robocze usługi Log Analytics
Dane zbierane przez dzienniki Azure Monitor są przechowywane w co najmniej jednym [log Analytics obszarach roboczych](./design-logs-deployment.md). Obszar roboczy definiuje lokalizację geograficzną danych, prawa dostępu definiujące użytkowników, którzy mają dostęp do danych, oraz ustawienia konfiguracji, takie jak warstwa cenowa i przechowywanie danych.  

Należy utworzyć co najmniej jeden obszar roboczy, aby użyć dzienników Azure Monitor. Jeden obszar roboczy może być wystarczający dla wszystkich danych monitorowania lub może utworzyć wiele obszarów roboczych w zależności od wymagań. Na przykład możesz mieć jeden obszar roboczy dla danych produkcyjnych i drugi do testowania. 

- Zobacz [Tworzenie obszaru roboczego log Analytics w Azure Portal,](./quick-create-workspace.md) aby utworzyć nowy obszar roboczy.
- Zapoznaj się z tematem [projektowanie wdrożenia dzienników Azure monitor](design-logs-deployment.md) , aby uzyskać informacje dotyczące tworzenia wielu obszarów roboczych.

## <a name="data-structure"></a>Struktura danych
Zapytania dzienników pobierają swoje dane z obszaru roboczego Log Analytics. Każdy obszar roboczy zawiera wiele tabel, które są zorganizowane w oddzielne kolumny z wieloma wierszami danych. Każda tabela jest definiowana przez unikatowy zestaw kolumn, które są współużytkowane przez wiersze danych dostarczone przez źródło danych. 

[![Struktura dzienników Azure Monitor](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Dane dziennika z Application Insights są również przechowywane w dziennikach Azure Monitor, ale są przechowywane w zależności od sposobu skonfigurowania aplikacji. W przypadku aplikacji opartych na obszarze roboczym dane są przechowywane w obszarze roboczym Log Analytics w standardowym zestawie tabel do przechowywania danych, takich jak żądania aplikacji, wyjątki i wyświetlenia stron. Wiele aplikacji może korzystać z tego samego obszaru roboczego. W przypadku klasycznej aplikacji dane nie są przechowywane w obszarze roboczym Log Analytics. Używa tego samego języka zapytań i tworzysz i uruchamiasz zapytania przy użyciu tego samego narzędzia Log Analytics w Azure Portal. Dane dla klasycznych aplikacji są przechowywane niezależnie od siebie. Jej ogólna struktura jest taka sama jak w przypadku aplikacji opartych na obszarze roboczym, chociaż nazwy tabel i kolumn są różne. Zobacz [zmiany zasobów na podstawie obszaru roboczego](../app/apm-tables.md) , aby zapoznać się ze szczegółowym porównaniem schematu dla aplikacji opartych na obszarze roboczym i klasycznym.


> [!NOTE]
> Firma Microsoft zapewnia pełną zgodność z poprzednimi wersjami Application Insights klasycznych zapytań zasobów, skoroszytów i alertów opartych na dziennikach w ramach Application Insights środowiska. Aby zbadać/wyświetlić [nową strukturę lub schemat tabeli opartych na obszarze roboczym](../app/apm-tables.md) , musisz najpierw przejść do obszaru roboczego log Analytics. W trakcie korzystania z wersji zapoznawczej Wybieranie **dzienników** z poziomu okienek Application Insights umożliwi dostęp do klasycznego zapytania Application Insights. Aby uzyskać więcej informacji, zobacz [zakres zapytań](./scope.md) .


[![Azure Monitor strukturę dzienników dla Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Rejestrowanie zapytań
Dane są pobierane z obszaru roboczego Log Analytics przy użyciu zapytania dziennika, które jest żądaniem tylko do odczytu, aby przetwarzać dane i zwracać wyniki. Zapytania dzienników są zapisywane w [języku Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/), który jest tym samym językiem zapytań, który jest używany przez usługę Azure Eksplorator danych. Zapytania dzienników można pisać w Log Analytics, aby interaktywnie analizować ich wyniki, używać ich w regułach alertów, aby otrzymywać powiadomienia o problemach lub dołączać ich wyniki do skoroszytów lub pulpitów nawigacyjnych. Szczegółowe dane obejmują wbudowane zapytania do obsługi widoków i skoroszytów.

- Zobacz [zapytania dzienników w Azure monitor](./log-query-overview.md) , aby uzyskać listę, gdzie są używane zapytania dzienników, oraz informacje o samouczkach i innych dokumentach, aby rozpocząć pracę.

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Użyj Log Analytics, czyli narzędzia w Azure Portal, do edytowania i uruchamiania zapytań dzienników i interaktywnego analizowania ich wyników. Następnie można użyć tworzonych przez Ciebie kwerend do obsługi innych funkcji w Azure Monitor, takich jak alerty i skoroszyty zapytań dzienników. Log Analytics dostęp z poziomu opcji **dzienniki** w menu Azure monitor lub z większości innych usług w Azure Portal.

- Opis Log Analytics można znaleźć [w temacie omówienie log Analytics w Azure monitor](./log-analytics-overview.md) . 
- Zobacz [samouczek log Analytics](./log-analytics-tutorial.md) , aby dowiedzieć się, jak za pomocą funkcji log Analytics utworzyć prostą kwerendę dziennika i przeanalizować jej wyniki.



## <a name="relationship-to-azure-data-explorer"></a>Relacja do Eksplorator danych platformy Azure
Dzienniki Azure Monitor opierają się na usłudze Azure Eksplorator danych. Obszar roboczy Log Analytics jest w przybliżeniu odpowiednikiem bazy danych w usłudze Azure Eksplorator danych, tabele są takie same, a oba używają tego samego Kusto Query Language (KQL). Środowisko korzystania z Log Analytics do pracy z zapytaniami Azure Monitor w Azure Portal jest podobne do środowiska przy użyciu interfejsu użytkownika sieci Web Eksplorator danych platformy Azure. Możesz nawet [uwzględnić dane z obszaru roboczego log Analytics w zapytaniu Eksplorator danych platformy Azure](/azure/data-explorer/query-monitor-data). 


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [zapytań dzienników](./log-query-overview.md) dotyczących pobierania i analizowania danych z obszaru roboczego log Analytics.
- Dowiedz się więcej o [metrykach w Azure monitor](../essentials/data-platform-metrics.md).
- Poznaj [dane monitorowania dostępne](../agents/data-sources.md) dla różnych zasobów na platformie Azure.