---
title: Monitorowanie i dostrajanie wydajności
description: Przegląd możliwości monitorowania i dostrajania wydajności oraz metodologii w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 03/17/2021
ms.openlocfilehash: 4c9edaea75215bc4b471cd2e0ac50c4e69546bb0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592136"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Monitorowanie i dostrajanie wydajności usługi Azure SQL Database i wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Aby monitorować wydajność bazy danych w Azure SQL Database i wystąpieniu zarządzanym Azure SQL, Zacznij od monitorowania zasobów procesora i operacji we/wy używanych przez obciążenie względem poziomu wydajności bazy danych wybranej w ramach wybierania określonej warstwy usług i poziomu wydajności. Aby to osiągnąć, Azure SQL Database i wyemituj metryki zasobów wystąpienia zarządzanego Azure SQL, które mogą być wyświetlane w Azure Portal lub przy użyciu jednego z następujących narzędzi do zarządzania SQL Server: [Azure Data Studio](/sql/azure-data-studio/what-is) lub [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Azure SQL Database udostępnia wiele klasyfikatorów baz danych, które zapewniają inteligentne zalecenia dotyczące dostrajania wydajności i dostrajania automatycznego, aby zwiększyć wydajność. Ponadto w Szczegółowe informacje o wydajności zapytań przedstawiono szczegółowe informacje o zapytaniach odpowiedzialnych za większość użycia procesora CPU i we/wy dla baz danych z pojedynczym i w puli.

Azure SQL Database i wystąpienie zarządzane usługi Azure SQL zapewniają zaawansowane możliwości monitorowania i dostrajania obsługiwane przez sztuczną inteligencję, aby pomóc w rozwiązywaniu problemów i maksymalizacji wydajności baz danych i rozwiązań. Można skonfigurować [eksport przesyłania strumieniowego](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) tych [Intelligent Insights](intelligent-insights-overview.md) i innych dzienników zasobów bazy danych oraz metryki do jednego z kilku miejsc docelowych na potrzeby użycia i analizy, zwłaszcza przy użyciu [analizy SQL](../../azure-monitor/insights/azure-sql.md). Azure SQL Analytics to zaawansowane rozwiązanie do monitorowania chmurowego do monitorowania wydajności wszystkich baz danych na dużą skalę i w wielu subskrypcjach w jednym widoku. Aby uzyskać listę dzienników i metryk, które można eksportować, zobacz dane [telemetryczne diagnostyki na potrzeby eksportu](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)

SQL Server ma własne możliwości monitorowania i diagnostyki, które SQL Database i wykorzystanie wystąpienia zarządzanego przez SQL, takie jak [Magazyn zapytań](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) i [dynamiczne widoki zarządzania (widoków DMV)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Aby monitorować wiele problemów z wydajnością, zobacz [monitorowanie za pomocą widoków DMV](monitoring-with-dmvs.md) .

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Możliwości monitorowania i dostrajania w Azure Portal

W Azure Portal, Azure SQL Database i wystąpienie zarządzane usługi Azure SQL zapewniają monitorowanie metryk zasobów. Azure SQL Database zapewnia klasyfikatory baz danych, a Szczegółowe informacje o wydajności zapytań udostępnia zalecenia dotyczące dostrajania zapytań i analizy wydajności zapytań. W Azure Portal można włączyć dostrajanie automatyczne dla [logicznych serwerów SQL](logical-servers.md) i ich pojedyncze i w puli baz danych.

> [!NOTE]
> Bazy danych z bardzo niskim użyciem mogą być widoczne w portalu z mniejszą ilością niż rzeczywiste użycie. Ze względu na sposób, w jaki dane telemetryczne są emitowane podczas konwersji wartości podwójnej do najbliższej liczby całkowitej, niektóre wartości użycia mniejsze niż 0,5 będą zaokrąglane do 0, co powoduje utratę stopnia szczegółowości emitowanych danych telemetrycznych. Aby uzyskać szczegółowe informacje, zobacz [metryki niska baza danych i Pula elastyczna zaokrąglanie do zera](#low-database-and-elastic-pool-metrics-rounding-to-zero).

### <a name="monitor-with-sql-insights"></a>Monitoruj przy użyciu usługi SQL Insights

[Azure monitor SQL Insights](../../azure-monitor/insights/sql-insights-overview.md) to narzędzie do monitorowania wystąpień zarządzanych usługi Azure SQL, baz danych SQL Azure i wystąpień SQL Server na maszynach wirtualnych Azure SQL. Ta usługa używa agenta zdalnego do przechwytywania danych z dynamicznych widoków zarządzania (widoków DMV) i kieruje dane do usługi Azure Log Analytics, gdzie można monitorować i analizować. Te dane można wyświetlić [Azure monitor](../../azure-monitor/overview.md) w określonych widokach lub uzyskać dostęp do danych dziennika bezpośrednio w celu uruchamiania zapytań i analizowania trendów. Aby rozpocząć korzystanie z Azure Monitor usługi SQL Insights, zobacz [Włączanie usługi SQL Insights](../../azure-monitor/insights/sql-insights-enable.md).

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Azure SQL Database i monitorowanie zasobów wystąpienia zarządzanego usługi Azure SQL

W Azure Portal w widoku **metryk** można szybko monitorować różne metryki zasobów. Te metryki umożliwiają sprawdzenie, czy baza danych osiągnie 100% zasobów procesora, pamięci lub operacji we/wy. Wysoka wartość procentowa jednostek DTU lub procesora, jak również wysoka wartość procentowa, wskazuje, że obciążenie może potrzebować większej ilości zasobów procesora CPU lub operacji we/wy. Mogą także wskazywać zapytania, które muszą być zoptymalizowane.

  ![Metryki zasobów](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Klasyfikatory baz danych w Azure SQL Database

Azure SQL Database obejmuje [klasyfikatory baz danych](database-advisor-implement-performance-recommendations.md) , które zapewniają zalecenia dotyczące dostrajania wydajności dla jednej i puli baz danych. Te zalecenia są dostępne w Azure Portal, a także przy użyciu [programu PowerShell](/powershell/module/az.sql/get-azsqldatabaseadvisor). Możesz również włączyć [dostrajanie automatyczne](automatic-tuning-overview.md) , aby Azure SQL Database mogły automatycznie zaimplementować te zalecenia dostrajania.

### <a name="query-performance-insight-in-azure-sql-database"></a>Szczegółowe informacje o wydajności zapytań w Azure SQL Database

[Szczegółowe informacje o wydajności zapytań](query-performance-insight-use.md) przedstawia wydajność w Azure Portal z góry i najdłuższych uruchomionych zapytań dla baz danych w jednej i w puli.

### <a name="low-database-and-elastic-pool-metrics-rounding-to-zero"></a>Metryki niskiej bazy danych i puli elastycznej zaokrąglania do zera

Począwszy od września 2020, bazy danych o bardzo niskim wykorzystaniu mogą być widoczne w portalu i mniejsze niż rzeczywiste użycie. Ze względu na sposób, w jaki dane telemetryczne są emitowane podczas konwersji wartości podwójnej do najbliższej liczby całkowitej, niektóre wartości użycia mniejsze niż 0,5 będą zaokrąglane do 0, co powoduje utratę stopnia szczegółowości emitowanych danych telemetrycznych.

Na przykład: należy wziąć pod uwagę 1-minutowy okno z następującymi czterema punktami danych: 0,1, 0,1, 0,1, 0,1, te niskie wartości są zaokrąglane w dół do 0, 0, 0, 0, i stanowią średnią 0. Jeśli którykolwiek z punktów danych jest większy niż 0,5, na przykład: 0,1, 0,1, 0,9, 0,1, są zaokrąglane do 0, 0, 1, 0 i Pokaż średnią z 0,25.

Metryki bazy danych, których to dotyczy:
- cpu_percent
- log_write_percent
- workers_percent
- sessions_percent
- physical_data_read_percent
- dtu_consumption_percent2
- xtp_storage_percent

Metryki elastycznej puli, których to dotyczy:
- cpu_percent
- physical_data_read_percent
- log_write_percent
- memory_usage_percent
- data_storage_percent
- peak_worker_percent
- peak_session_percent
- xtp_storage_percent
- allocated_data_storage_percent


## <a name="generate-intelligent-assessments-of-performance-issues"></a>Generuj inteligentne oceny problemów z wydajnością

[Intelligent Insights](intelligent-insights-overview.md) dla Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL używają wbudowanej analizy do ciągłego monitorowania użycia bazy danych za pomocą sztucznej analizy i wykrywania zdarzeń powodujących zakłócenia, które powodują niską wydajność. Intelligent Insights automatycznie wykrywa problemy z wydajnością z bazami danych w oparciu o czasy oczekiwania wykonywania zapytania, błędy lub limity czasu. Po wykryciu zostanie utworzona Szczegółowa analiza, która generuje dziennik zasobów (o nazwie SQLInsights) z [inteligentną oceną problemów](intelligent-insights-troubleshoot-performance.md). Ta ocena polega na analizie głównej przyczyny problemu z wydajnością bazy danych i, jeśli to możliwe, zalecenia dotyczące ulepszeń wydajności.

Intelligent Insights to unikatowa funkcja analizy wbudowanej platformy Azure, która zapewnia następującą wartość:

- Aktywne monitorowanie
- Dostosowane szczegółowe informacje o wydajności
- Wczesne wykrywanie obniżenia wydajności bazy danych
- Analiza głównych przyczyn problemów wykrytych
- Zalecenia dotyczące poprawy wydajności
- Możliwość skalowania w poziomie na setkach tysięcy baz danych
- Pozytywny wpływ na zasoby DevOps i całkowity koszt posiadania

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Włącz eksportowanie strumieniowe metryk i dzienników zasobów

[Eksport danych telemetrycznych diagnostyki przesyłania strumieniowego](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) można włączyć i skonfigurować do jednego z kilku miejsc docelowych, w tym dziennika zasobów Intelligent Insights. Użyj [analizy SQL](../../azure-monitor/insights/azure-sql.md) i innych funkcji, aby korzystać z tej dodatkowej telemetrii diagnostycznej w celu identyfikowania i rozwiązywania problemów z wydajnością.

Ustawienia diagnostyczne można skonfigurować do przesyłania strumieniowego kategorii metryk i dzienników zasobów dla pojedynczych baz danych, baz danych w puli, pul elastycznych, wystąpień zarządzanych i baz danych wystąpień do jednego z następujących zasobów platformy Azure.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics obszar roboczy w Azure Monitor

Metryki i dzienniki zasobów można przesyłać do [obszaru roboczego log Analytics w Azure monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). Przesyłane strumieniowo dane mogą być używane przez usługę [SQL Analytics](../../azure-monitor/insights/azure-sql.md), która jest rozwiązaniem do monitorowania tylko w chmurze, które zapewnia inteligentne monitorowanie baz danych, które obejmują raporty wydajności, alerty i zalecenia zaradcze. Dane przesyłane strumieniowo do obszaru roboczego Log Analytics mogą być analizowane przy użyciu innych zebranych danych monitorowania, a także umożliwiają korzystanie z innych funkcji Azure Monitor, takich jak alerty i wizualizacje.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Metryki i dzienniki zasobów można przesyłać strumieniowo do [usługi Azure Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs). Przesyłaj strumieniowo dane telemetryczne diagnostyki do centrów zdarzeń, aby zapewnić następujące funkcje:

- **Przesyłanie strumieniowe dzienników do systemów rejestrowania i telemetrii innych firm**

  Przesyłaj strumieniowo wszystkie metryki i dzienniki zasobów do jednego centrum zdarzeń w celu przełączenia danych dziennika do narzędzia SIEM lub log Analytics innej firmy.
- **Tworzenie niestandardowej platformy telemetrii i rejestrowania**

  Wysoce skalowalny charakter publikowania/subskrybowania centrów zdarzeń umożliwia elastyczne pozyskiwanie metryk i dzienników zasobów na niestandardową platformę telemetrii. Aby uzyskać szczegółowe informacje [, zobacz Projektowanie i ustalanie rozmiaru globalnej platformy telemetrii w usłudze Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
- **Wyświetlanie kondycji usługi przez przesyłanie strumieniowe danych do usługi Power BI**

  Używaj Event Hubs, Stream Analytics i Power BI do przekształcania danych diagnostycznych na niemal w czasie rzeczywistym wglądu w dane usługi platformy Azure. Zobacz [Stream Analytics i Power BI: pulpit nawigacyjny analizy w czasie rzeczywistym na potrzeby przesyłania strumieniowego danych](../../stream-analytics/stream-analytics-power-bi-dashboard.md) w celu uzyskania szczegółowych informacji dotyczących tego rozwiązania.

### <a name="azure-storage"></a>Azure Storage

Przesyłaj strumieniowo metryki i dzienniki zasobów do [usługi Azure Storage](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Usługa Azure Storage umożliwia archiwizowanie ogromnych ilości danych telemetrycznych diagnostyki w przypadku części kosztów poprzednich dwóch opcji przesyłania strumieniowego.

## <a name="use-extended-events"></a>Korzystanie z zdarzeń rozszerzonych 

Ponadto można użyć [zdarzeń rozszerzonych](/sql/relational-databases/extended-events/extended-events) w SQL Server na potrzeby zaawansowanego monitorowania i rozwiązywania problemów. Architektura zdarzeń rozszerzonych umożliwia użytkownikom zbieranie jak najmniejszej ilości danych, co jest niezbędne do rozwiązywania problemów lub identyfikowania problemów z wydajnością. Aby uzyskać informacje na temat używania zdarzeń rozszerzonych w Azure SQL Database, zobacz [zdarzenia rozszerzone w Azure SQL Database](xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o inteligentnych zaleceniach dotyczących wydajności dla jednej i puli baz danych, zobacz [zalecenia dotyczące wydajności klasyfikatora bazy danych](database-advisor-implement-performance-recommendations.md).
- Aby uzyskać więcej informacji o automatycznym monitorowaniu wydajności bazy danych za pomocą zautomatyzowanej diagnostyki i głównej przyczyny problemów z wydajnością, zobacz [Azure SQL Intelligent Insights](intelligent-insights-overview.md).