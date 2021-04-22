---
title: Dzienniki usługi Azure Monitor
description: Opisuje Azure Monitor, które są używane do zaawansowanej analizy danych monitorowania.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 9794c5f048b8795652e4b31e0134b36a77715abe
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873383"
---
# <a name="azure-monitor-logs-overview"></a>Przegląd dzienników usługi Azure Monitor
Azure Monitor Logs to funkcja usługi Azure Monitor, która zbiera i organizuje dane dzienników i wydajności z [monitorowanych zasobów.](../monitor-reference.md) Dane z różnych [](../essentials/platform-logs-overview.md) źródeł, takie jak dzienniki platformy z usług platformy Azure, dane [](../app/app-insights-overview.md) dzienników i wydajności z agentów maszyn [wirtualnych,](../agents/agents-overview.md)a dane użycia i wydajności z aplikacji można skonsolidować w jednym obszarze roboczym, aby można było je analizować razem przy użyciu zaawansowanego języka zapytań, który może szybko analizować miliony rekordów. Możesz wykonać proste zapytanie, które po prostu pobiera określony zestaw rekordów, lub wykonać zaawansowaną analizę danych w celu zidentyfikowania wzorców krytycznych w danych monitorowania. Interaktywna praca z zapytaniami dzienników i ich wynikami przy użyciu usługi Log Analytics, używanie ich w zasadach alertów w celu proaktywnego powiadamiania o problemach lub wizualizowanie wyników w skoroszycie lub pulpicie nawigacyjnym.

> [!NOTE]
> Azure Monitor Logs to połowa platformy danych, która obsługuje Azure Monitor. Drugi to [metryki Azure Monitor, które](../essentials/data-platform-metrics.md) przechowuje dane liczbowe w bazie danych szeregów czasu. Dzięki temu te dane są bardziej uproszczone niż dane w dziennikach Azure Monitor i mogą obsługiwać scenariusze niemal w czasie rzeczywistym, dzięki czemu są szczególnie przydatne do alertów i szybkiego wykrywania problemów. Metryki mogą jednak przechowywać tylko dane liczbowe w określonej strukturze, natomiast dzienniki mogą przechowywać różne typy danych, z których każdy ma własną strukturę. Można również wykonywać złożone analizy danych dzienników przy użyciu zapytań dziennika, których nie można użyć do analizy danych metryk.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Co można zrobić za pomocą Azure Monitor dzienników?
W poniższej tabeli opisano niektóre z różnych sposobów używania dzienników w Azure Monitor:

|  | Opis |
|:---|:---|
| **Analiza** | Używanie [usługi Log Analytics](./log-analytics-tutorial.md) w Azure Portal do pisania zapytań [dzienników](./log-query-overview.md) i interaktywnego analizowania danych dzienników przy użyciu zaawansowanego aparatu analizy |
| **Alert** | Skonfiguruj [regułę alertu dziennika,](../alerts/alerts-log.md) [](../alerts/action-groups.md) która wysyła powiadomienie lub podejmuje akcję automatyczną, gdy wyniki zapytania są zgodne z określonym wynikiem. |
| **Wizualizacja** | Przypnij wyniki zapytania renderowane jako tabele lub wykresy do pulpitu [nawigacyjnego platformy Azure.](../../azure-portal/azure-portal-dashboards.md)<br>Utwórz skoroszyt [w](../visualize/workbooks-overview.md) celu połączenia z wieloma zestawami danych w interakcyjnych raportach. <br>Wyeksportuj wyniki zapytania do usługi [Power BI,](../visualize/powerbi.md) aby używać różnych wizualizacji i udostępniać je użytkownikom spoza platformy Azure.<br>Wyeksportuj wyniki zapytania do programu [Grafana,](../visualize/grafana-plugin.md) aby korzystać z pulpitów nawigacyjnych i łączyć je z innymi źródłami danych.|
| **Insights** | Obsługa [szczegółowych informacji,](../monitor-reference.md#insights-and-core-solutions) które zapewniają dostosowane środowisko monitorowania dla określonych aplikacji i usług.  |
| **Pobrać** | Uzyskiwanie dostępu do wyników zapytania dziennika z wiersza polecenia przy użyciu interfejsu [wiersza polecenia platformy Azure.](/cli/azure/monitor/log-analytics)<br>Uzyskiwanie dostępu do wyników zapytania dziennika z wiersza polecenia przy użyciu [poleceń cmdlet programu PowerShell.](/powershell/module/az.operationalinsights)<br>Uzyskiwanie dostępu do wyników zapytania dziennika z aplikacji niestandardowej przy użyciu [interfejsu API REST.](https://dev.loganalytics.io/) |
| **Eksportowanie** | Skonfiguruj [zautomatyzowany eksport danych dziennika na](./logs-data-export.md) konto usługi Azure Storage lub Azure Event Hubs.<br>Skompilowanie przepływu pracy w celu pobrania danych dziennika i skopiowania ich do lokalizacji zewnętrznej przy [użyciu Logic Apps](./logicapp-flow-connector.md). |

![Omówienie dzienników](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Zbieranie danych
Po utworzeniu obszaru roboczego usługi Log Analytics należy skonfigurować różne źródła do wysyłania danych. Żadne dane nie są zbierane automatycznie. Ta konfiguracja będzie się różnić w zależności od źródła danych. Możesz na przykład [utworzyć ustawienia diagnostyczne w celu](../essentials/diagnostic-settings.md) wysyłania dzienników zasobów z zasobów platformy Azure do obszaru roboczego. [Włącz szczegółowe informacje o maszynach wirtualnych,](../vm/vminsights-enable-overview.md) aby zbierać dane z maszyn wirtualnych. Skonfiguruj [źródła danych w obszarze roboczym, aby](../agents/data-sources.md) zbierać dodatkowe zdarzenia i dane dotyczące wydajności.

- Zobacz [Co jest monitorowane przez Azure Monitor?](../monitor-reference.md) aby uzyskać pełną listę źródeł danych, które można skonfigurować w celu wysyłania danych do Azure Monitor Dzienników.


## <a name="log-analytics-workspaces"></a>Obszary robocze usługi Log Analytics
Dane zbierane przez Azure Monitor są przechowywane w co najmniej jednym obszarze roboczym [usługi Log Analytics.](./design-logs-deployment.md) Obszar roboczy definiuje lokalizację geograficzną danych, prawa dostępu definiujące, którzy użytkownicy mogą uzyskać dostęp do danych, oraz ustawienia konfiguracji, takie jak warstwa cenowa i przechowywanie danych.  

Należy utworzyć co najmniej jeden obszar roboczy, aby używać Azure Monitor Dziennikach. Pojedynczy obszar roboczy może być wystarczający dla wszystkich danych monitorowania lub może zdecydować się na utworzenie wielu obszarów roboczych w zależności od wymagań. Na przykład możesz mieć jeden obszar roboczy dla danych produkcyjnych i inny do testowania. 

- Zobacz [Tworzenie obszaru roboczego usługi Log Analytics w Azure Portal,](./quick-create-workspace.md) aby utworzyć nowy obszar roboczy.
- Aby [uzyskać informacje na temat Azure Monitor tworzenia](design-logs-deployment.md) wielu obszarów roboczych, zobacz Designing your Azure Monitor Logs deployment (Projektowanie wdrożenia dzienników sieciowych).

## <a name="data-structure"></a>Struktura danych
Zapytania dzienników pobierają dane z obszaru roboczego usługi Log Analytics. Każdy obszar roboczy zawiera wiele tabel, które są zorganizowane w oddzielne kolumny z wieloma wierszami danych. Każda tabela jest definiowana przez unikatowy zestaw kolumn współużytkowany przez wiersze danych dostarczone przez źródło danych. 

[![Azure Monitor Struktury dzienników](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Dane dzienników Application Insights są również przechowywane w dziennikach usługi Azure Monitor, ale są przechowywane inaczej w zależności od konfiguracji aplikacji. W przypadku aplikacji opartej na obszarze roboczym dane są przechowywane w obszarze roboczym usługi Log Analytics w standardowym zestawie tabel do przechowywania danych, takich jak żądania aplikacji, wyjątki i wyświetlenia stron. Wiele aplikacji może używać tego samego obszaru roboczego. W przypadku aplikacji klasycznej dane nie są przechowywane w obszarze roboczym usługi Log Analytics. Używa tego samego języka zapytań, a zapytania tworzy się i uruchamia za pomocą tego samego narzędzia Log Analytics w Azure Portal. Dane dla aplikacji klasycznych są jednak przechowywane oddzielnie. Jej ogólna struktura jest taka sama jak w przypadku aplikacji opartych na obszarze roboczym, chociaż nazwy tabel i kolumn są różne. Zobacz [Zmiany zasobów oparte na obszarze roboczym,](../app/apm-tables.md) aby uzyskać szczegółowe porównanie schematu dla aplikacji klasycznych i opartych na obszarze roboczym.


> [!NOTE]
> Nadal zapewniamy pełną zgodność z poprzednimi Application Insights zapytań dotyczących zasobów klasycznych, skoroszytów i alertów opartych na dziennikach w Application Insights środowiska. Aby wykonać zapytanie/wyświetlić [nową](../app/apm-tables.md) strukturę/schemat tabeli opartej na obszarze roboczym, należy najpierw przejść do obszaru roboczego usługi Log Analytics. W wersji zapoznawczej wybranie opcji **Dzienniki** w okienkach Application Insights dostępu do klasycznego Application Insights zapytań. Aby [uzyskać więcej informacji,](./scope.md) zobacz Zakres zapytań.


[![Azure Monitor Logs structure for Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Rejestrowanie zapytań
Dane są pobierane z obszaru roboczego usługi Log Analytics przy użyciu zapytania dziennika, które jest żądaniem tylko do odczytu w celu przetwarzania danych i zwracania wyników. Zapytania dziennika są pisane w [języku Kusto Query Language (KQL),](/azure/data-explorer/kusto/query/)który jest tym samym językiem zapytań, który jest używany przez Azure Data Explorer. Możesz pisać zapytania dotyczące dzienników w u usługi Log Analytics, aby interaktywnie analizować ich wyniki, używać ich w zasadach alertów w celu proaktywnego powiadamiania o problemach lub dołączać ich wyniki do skoroszytów lub pulpitów nawigacyjnych. Szczegółowe informacje obejmują wstępnie utworzone zapytania do obsługi widoków i skoroszytów.

- Zobacz [Zapytania dziennika w Azure Monitor,](./log-query-overview.md) aby uzyskać listę używanych zapytań dziennika oraz odwołania do samouczków i innej dokumentacji, aby rozpocząć pracę.

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Użyj usługi Log Analytics, która jest narzędziem w Azure Portal, aby edytować i uruchamiać zapytania dziennika oraz interaktywnie analizować ich wyniki. Następnie możesz użyć zapytań, które tworzysz, aby obsługiwać inne funkcje usługi Azure Monitor, takie jak alerty zapytań dzienników i skoroszyty. Dostęp do usługi Log Analytics **można** uzyskać za pomocą opcji Dzienniki w menu Azure Monitor lub z większości innych usług w Azure Portal.

- Opis [usługi Log Analytics można](./log-analytics-overview.md) znaleźć Azure Monitor Omówienie usługi Log Analytics w chmurze. 
- Zobacz [samouczek usługi Log Analytics,](./log-analytics-tutorial.md) aby przejść przez proces tworzenia prostego zapytania dziennika i analizowania jego wyników przy użyciu funkcji usługi Log Analytics.



## <a name="relationship-to-azure-data-explorer"></a>Relacja z Azure Data Explorer
Azure Monitor oparte na Azure Data Explorer. Obszar roboczy usługi Log Analytics jest w przybliżeniu odpowiednikiem bazy danych na platformie Azure Data Explorer, tabele mają tę samą strukturę i używają tego samego języka zapytań Kusto (KQL). Środowisko korzystania z usługi Log Analytics do pracy z zapytaniami Azure Monitor na Azure Portal jest podobne do korzystania z interfejsu użytkownika Azure Data Explorer web. Dane z obszaru roboczego usługi Log Analytics można [nawet uwzględnić w zapytaniu Azure Data Explorer .](/azure/data-explorer/query-monitor-data) 


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [o zapytaniach dzienników](./log-query-overview.md) w celu pobierania i analizowania danych z obszaru roboczego usługi Log Analytics.
- Dowiedz się więcej [o metrykach w Azure Monitor](../essentials/data-platform-metrics.md).
- Dowiedz się więcej o [danych monitorowania dostępnych](../agents/data-sources.md) dla różnych zasobów na platformie Azure.