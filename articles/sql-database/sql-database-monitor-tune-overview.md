---
title: Monitorowanie i dostrajanie wydajności
description: Przegląd możliwości monitorowania i dostrajania wydajności oraz metodologii w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 837d88665c1fdffe902c9c478e5d6dc65a2e402a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268732"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Monitorowanie i dostrajanie wydajności w Azure SQL Database

Aby monitorować wydajność bazy danych w Azure SQL Database, Zacznij od monitorowania zasobów procesora i we/wy używanych przez obciążenie względem poziomu wydajności bazy danych wybranej w ramach wybierania określonej warstwy usług i poziomu wydajności. Aby to osiągnąć, Azure SQL Database emituje metryki zasobów, które mogą być wyświetlane w Azure Portal lub przy użyciu jednej z tych narzędzi do zarządzania SQL: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) lub [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

W przypadku baz danych z pojedynczym i pulą Azure SQL Database zawiera wiele doradców baz danych, które zapewniają inteligentne zalecenia dotyczące dostrajania wydajności i dostrajania automatycznego w celu zwiększenia wydajności. Ponadto w Szczegółowe informacje o wydajności zapytań przedstawiono szczegółowe informacje o zapytaniach odpowiedzialnych za większość użycia procesora CPU i we/wy dla baz danych z pojedynczym i w puli.

Azure SQL Database zapewnia dodatkowe możliwości monitorowania i dostrajania obsługiwane przez sztuczną inteligencję, aby pomóc w rozwiązywaniu problemów i maksymalizacji wydajności baz danych i rozwiązań. Istnieje możliwość skonfigurowania [eksportu strumieniowego](sql-database-metrics-diag-logging.md) tych [Intelligent Insights](sql-database-intelligent-insights.md) i innych dzienników zasobów SQL Database i metryk do jednego z kilku miejsc docelowych na potrzeby użycia i analizy, szczególnie przy użyciu [analizy SQL](../azure-monitor/insights/azure-sql.md)). Azure SQL Analytics to zaawansowane rozwiązanie do monitorowania chmurowego służące do monitorowania wydajności wszystkich baz danych SQL platformy Azure na dużą skalę i wielu subskrypcjach w jednym widoku. Aby uzyskać listę dzienników i metryk, które można eksportować, zobacz dane [telemetryczne diagnostyki na potrzeby eksportu](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)

Na koniec program SQL ma własne możliwości monitorowania i diagnostyki, [SQL Server magazyn zapytań](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) i [dynamiczne widoki zarządzania (widoków DMV)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Aby monitorować wiele problemów z wydajnością, zobacz [monitorowanie za pomocą widoków DMV](sql-database-monitoring-with-dmvs.md) .

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Możliwości monitorowania i dostrajania w Azure Portal

W Azure Portal Azure SQL Database zapewnia monitorowanie metryk zasobów dla wszystkich typów wdrożeń. Dodatkowo w przypadku baz danych o pojedynczej i puli, klasyfikatorów baz danych i Szczegółowe informacje o wydajności zapytań zapewniają zalecenia dotyczące dostrajania zapytań i analizy wydajności zapytań. Na koniec Azure Portal można włączyć automatyczne dla serwerów logicznych i ich baz danych w puli.

### <a name="sql-database-resource-monitoring"></a>Monitorowanie zasobów SQL Database

Następujące metryki zasobów można szybko monitorować w Azure Portal w widoku **metryki** :

- **Użycie jednostek DTU**

  Sprawdź, czy baza danych lub Pula elastyczna osiągnie 100 procent użycia jednostek DTU przez dłuższy czas. Wysokie użycie jednostek DTU wskazuje, że obciążenie może potrzebować większej ilości zasobów procesora CPU lub operacji we/wy. Mogą także wskazywać zapytania, które muszą być zoptymalizowane.
- **Użycie procesora**

  Sprawdź, czy baza danych, Pula elastyczna lub wystąpienie zarządzane osiągają 100 procent użycia procesora CPU przez dłuższy czas. Wysoki procesor CPU wskazuje, że obciążenie może potrzebować większej ilości zasobów procesora CPU lub operacji we/wy. Mogą także wskazywać zapytania, które muszą być zoptymalizowane.
- **Użycie we/wy**

  Sprawdź, czy wystąpienie bazy danych, elastycznej puli lub zarządzania "osiąga limit operacji we/wy podstawowego magazynu. Wysokie użycie we/wy wskazuje, że obciążenie może potrzebować większej ilości zasobów procesora CPU lub operacji we/wy. Mogą także wskazywać zapytania, które muszą być zoptymalizowane.

  ![Metryki zasobów](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Klasyfikatory baz danych
"Azure SQL Database obejmuje [klasyfikatory baz danych](sql-database-advisor.md) , które zapewniają zalecenia dotyczące dostrajania wydajności dla jednej i puli baz danych. Te zalecenia są dostępne w Azure Portal, a także przy użyciu [programu PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). Możesz również włączyć [dostrajanie automatyczne](sql-database-automatic-tuning.md) , aby SQL Database mogły automatycznie zaimplementować te zalecenia dostrajania.

### <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań

[Szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) przedstawia wydajność w Azure Portal z góry i najdłuższych uruchomionych zapytań dla baz danych w jednej i w puli.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Generuj inteligentne oceny problemów z wydajnością

Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights.md) używa wbudowanej analizy do ciągłego monitorowania użycia bazy danych za pomocą sztucznej analizy i wykrywania zdarzeń powodujących zakłócenia, które powodują niską wydajność. Intelligent Insights automatycznie wykrywa problemy z wydajnością z bazami danych w Azure SQL Database na podstawie czasów oczekiwania, błędów lub limitów czasu wykonywania zapytania. Po wykryciu zostanie utworzona Szczegółowa analiza, która generuje dziennik zasobów (o nazwie SQLInsights) z [inteligentną oceną problemów](sql-database-intelligent-insights-troubleshoot-performance.md). Ta ocena polega na analizie głównej przyczyny problemu z wydajnością bazy danych i, jeśli to możliwe, zalecenia dotyczące ulepszeń wydajności.

Intelligent Insights to unikatowa funkcja analizy wbudowanej platformy Azure, która zapewnia następującą wartość:

- Aktywne monitorowanie
- Dostosowane szczegółowe informacje o wydajności
- Wczesne wykrywanie obniżenia wydajności bazy danych
- Analiza głównych przyczyn problemów wykrytych
- Zalecenia dotyczące poprawy wydajności
- Możliwość skalowania w poziomie na setkach tysięcy baz danych
- Pozytywny wpływ na zasoby DevOps i całkowity koszt posiadania

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Włącz eksportowanie strumieniowe metryk i dzienników zasobów

[Eksport danych telemetrycznych diagnostyki przesyłania strumieniowego](sql-database-metrics-diag-logging.md) można włączyć i skonfigurować do jednego z kilku miejsc docelowych, w tym dziennika zasobów Intelligent Insights. Użyj [analizy SQL](../azure-monitor/insights/azure-sql.md) i innych funkcji, aby korzystać z tej dodatkowej telemetrii diagnostycznej w celu identyfikowania i rozwiązywania problemów z wydajnością.

Ustawienia diagnostyczne można skonfigurować do przesyłania strumieniowego kategorii metryk i dzienników zasobów dla pojedynczych baz danych, baz danych w puli, pul elastycznych, wystąpień zarządzanych i baz danych wystąpień do jednego z następujących zasobów platformy Azure.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics obszar roboczy w usłudze Azure monitor

Metryki i dzienniki zasobów można przesyłać do [obszaru roboczego log Analytics w Azure monitor](../azure-monitor/platform/resource-logs-collect-workspace.md). Przesyłane strumieniowo dane mogą być używane przez usługę [SQL Analytics](../azure-monitor/insights/azure-sql.md), która jest rozwiązaniem do monitorowania tylko w chmurze, które zapewnia inteligentne monitorowanie baz danych, które obejmują raporty wydajności, alerty i zalecenia zaradcze. Dane przesyłane strumieniowo do obszaru roboczego Log Analytics mogą być analizowane przy użyciu innych zebranych danych monitorowania, a także umożliwiają korzystanie z innych funkcji Azure Monitor, takich jak alerty i wizualizacje.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Metryki i dzienniki zasobów można przesyłać strumieniowo do [usługi Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md). Przesyłaj strumieniowo dane telemetryczne diagnostyki do centrów zdarzeń, aby zapewnić następujące funkcje:

- **Przesyłanie strumieniowe dzienników do systemów rejestrowania i telemetrii innej firmy**

  Przesyłaj strumieniowo wszystkie metryki i dzienniki zasobów do jednego centrum zdarzeń w celu przełączenia danych dziennika do narzędzia SIEM lub log Analytics innej firmy.
- **Utwórz niestandardową platformę telemetrii i rejestrowania**

  Wysoce skalowalny charakter publikowania/subskrybowania centrów zdarzeń umożliwia elastyczne pozyskiwanie metryk i dzienników zasobów na niestandardową platformę telemetrii. Aby uzyskać szczegółowe informacje [, zobacz Projektowanie i ustalanie rozmiaru globalnej platformy telemetrii w usłudze Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
- **Wyświetl kondycję usługi przez przesyłanie strumieniowe danych do Power BI**

  Używaj Event Hubs, Stream Analytics i Power BI do przekształcania danych diagnostycznych na niemal w czasie rzeczywistym wglądu w dane usługi platformy Azure. Zobacz [Stream Analytics i Power BI: pulpit nawigacyjny analizy w czasie rzeczywistym na potrzeby przesyłania strumieniowego danych](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) w celu uzyskania szczegółowych informacji dotyczących tego rozwiązania.

### <a name="azure-storage"></a>Azure Storage

Przesyłaj strumieniowo metryki i dzienniki zasobów do [usługi Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md). Usługa Azure Storage umożliwia archiwizowanie ogromnych ilości danych telemetrycznych diagnostyki w przypadku części kosztów poprzednich dwóch opcji przesyłania strumieniowego.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Używanie zdarzeń rozszerzonych w aparacie bazy danych SQL

Ponadto możesz użyć [rozszerzonych zdarzeń](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) w programie SQL, aby uzyskać dodatkowe zaawansowane monitorowanie i rozwiązywanie problemów. Architektura zdarzeń rozszerzonych umożliwia użytkownikom zbieranie jak najmniejszej ilości danych, co jest niezbędne do rozwiązywania problemów lub identyfikowania problemów z wydajnością. Aby uzyskać informacje na temat używania zdarzeń rozszerzonych w SQL Database, zobacz [zdarzenia rozszerzone w SQL Database](sql-database-xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o inteligentnych zaleceniach dotyczących wydajności dla jednej i puli baz danych, zobacz [zalecenia dotyczące wydajności klasyfikatora bazy danych](sql-database-advisor.md).
- Aby uzyskać więcej informacji o automatycznym monitorowaniu wydajności bazy danych za pomocą zautomatyzowanej diagnostyki i głównej przyczyny problemów z wydajnością, zobacz [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
'''''''''