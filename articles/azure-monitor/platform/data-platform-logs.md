---
title: Logowanie w Azure Monitor | Microsoft Docs
description: Zawiera opis dzienników w Azure Monitor, które są używane do zaawansowanej analizy danych monitorowania.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 09/09/2020
ms.author: bwren
ms.openlocfilehash: e08c649b9a1d7e8b909a413ee435fce30a8d7e48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90032784"
---
# <a name="azure-monitor-logs-overview"></a>Omówienie dzienników Azure Monitor
Azure Monitor logs to funkcja Azure Monitor, która gromadzi i organizuje dane dzienników z różnych źródeł i udostępnia je do analizy przy użyciu zaawansowanego języka zapytań. Dane z różnych źródeł można skonsolidować w jednym obszarze roboczym i analizować je w celu wykonywania takich zadań i analizy trendów, alertów i wizualizacji.

## <a name="relationship-to-azure-monitor-metrics"></a>Relacje z metrykami Azure Monitor
Metryki Azure Monitor przechowują dane liczbowe w bazie danych szeregów czasowych, co sprawia, że te dane są bardziej uproszczone niż Azure Monitor dzienników i umożliwiają obsługę niemal scenariuszy w czasie rzeczywistym, dzięki czemu są one szczególnie przydatne w przypadku alertów i szybkiego wykrywania problemów. Metryki, które mogą przechowywać dane liczbowe tylko w określonej strukturze, podczas gdy dzienniki mogą przechowywać różne różne typy danych z ich własnymi strukturami. Możesz również wykonać złożoną analizę danych dzienników przy użyciu zapytań dziennika, które nie mogą być używane do analizy danych metryk.

Dane liczbowe są często wysyłane ze źródeł danych do dzienników oprócz metryk. Istnieje dodatkowa opłata za gromadzenie i przechowywanie tych danych w dziennikach, dzięki czemu można dołączać dane metryki do zapytań dzienników i analizować je przy użyciu innych danych monitorowania.

## <a name="relationship-to-azure-data-explorer"></a>Relacja do Eksplorator danych platformy Azure
Dzienniki Azure Monitor opierają się na usłudze Azure Eksplorator danych. Obszar roboczy Log Analytics jest w przybliżeniu odpowiednikiem bazy danych w usłudze Azure Eksplorator danych, tabele są takie same, a oba używają tego samego Kusto Query Language (KQL). Środowisko korzystania z Log Analytics do pracy z zapytaniami Azure Monitor w Azure Portal jest podobne do środowiska przy użyciu interfejsu użytkownika sieci Web Eksplorator danych platformy Azure. Możesz nawet [uwzględnić dane z obszaru roboczego log Analytics w zapytaniu Eksplorator danych platformy Azure](/azure/data-explorer/query-monitor-data). 


## <a name="structure-of-data"></a>Struktura danych
Dane zbierane przez dzienniki Azure Monitor są przechowywane w [log Analytics obszarze roboczym](./design-logs-deployment.md) zawierającym wiele tabel, które przechowują dane z konkretnego źródła. Obszar roboczy definiuje lokalizację geograficzną danych, prawa dostępu definiujące użytkowników, którzy mają dostęp do danych, oraz ustawienia konfiguracji, takie jak warstwa cenowa i przechowywanie danych. Możesz użyć jednego obszaru roboczego dla wszystkich danych monitorowania lub utworzyć wiele obszarów roboczych w zależności od wymagań. Zapoznaj się z tematem [projektowanie wdrożenia dzienników Azure monitor](design-logs-deployment.md) , aby uzyskać informacje dotyczące tworzenia wielu obszarów roboczych.

Każdy obszar roboczy zawiera wiele tabel, które są zorganizowane w oddzielne kolumny z wieloma wierszami danych. Każda tabela jest definiowana przez unikatowy zestaw kolumn, które są współużytkowane przez wiersze danych dostarczone przez źródło danych. 

[![Struktura dzienników Azure Monitor](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Dane dziennika z Application Insights są również przechowywane w dziennikach Azure Monitor, ale są przechowywane w zależności od sposobu skonfigurowania aplikacji. W przypadku aplikacji opartych na obszarze roboczym dane są przechowywane w obszarze roboczym Log Analytics w standardowym zestawie tabel do przechowywania danych, takich jak żądania aplikacji, wyjątki i wyświetlenia stron. Wiele aplikacji może korzystać z tego samego obszaru roboczego. W przypadku klasycznej aplikacji dane nie są przechowywane w obszarze roboczym Log Analytics. Używa tego samego języka zapytań i tworzysz i uruchamiasz zapytania przy użyciu tego samego narzędzia Log Analytics w Azure Portal. Dane dla klasycznych aplikacji są przechowywane niezależnie od siebie. Jej ogólna struktura jest taka sama jak w przypadku aplikacji opartych na obszarze roboczym, chociaż nazwy tabel i kolumn są różne. Zobacz [zmiany zasobów na podstawie obszaru roboczego](../app/apm-tables.md) , aby zapoznać się ze szczegółowym porównaniem tych dwóch.


> [!NOTE]
> Firma Microsoft zapewnia pełną zgodność z poprzednimi wersjami Application Insights klasycznych zapytań zasobów, skoroszytów i alertów opartych na dziennikach w ramach Application Insights środowiska. Aby zbadać/wyświetlić [nową strukturę lub schemat tabeli opartych na obszarze roboczym](../app/apm-tables.md) , musisz najpierw przejść do obszaru roboczego log Analytics. W trakcie korzystania z wersji zapoznawczej Wybieranie **dzienników** z poziomu okienek Application Insights umożliwi dostęp do klasycznego zapytania Application Insights. Aby uzyskać więcej informacji, zobacz [zakres zapytań](../log-query/scope.md) .


[![Azure Monitor strukturę dzienników dla Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="log-queries"></a>Rejestrowanie zapytań
Dane są pobierane z obszaru roboczego Log Analytics przy użyciu [zapytania dziennika](../log-query/log-query-overview.md) , które jest żądaniem tylko do odczytu, aby przetwarzać dane i zwracać wyniki. Zapytania dzienników są zapisywane w [języku Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/), który jest językiem zapytań używanym przez usługę Azure Eksplorator danych. Użyj Log Analytics, czyli narzędzia w Azure Portal, aby edytować i uruchamiać zapytania dzienników i interaktywnie analizować ich wyniki. Następnie można użyć tworzonych przez Ciebie kwerend do obsługi innych funkcji w Azure Monitor, takich jak alerty i skoroszyty zapytań dzienników.


## <a name="sources-of-data-for-azure-monitor-logs"></a>Źródła danych dla dzienników Azure Monitor
Azure Monitor zbiera dane dzienników z różnych źródeł, w tym zasobów Azure Monitor i agentów działających na maszynach wirtualnych. Zobacz, [co jest monitorowane przez Azure monitor?](../monitor-reference.md) , aby uzyskać pełną listę źródeł danych wysyłanych do log Analytics obszaru roboczego.



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [zapytań dzienników](../log-query/log-query-overview.md) dotyczących pobierania i analizowania danych z obszaru roboczego log Analytics.
- Dowiedz się więcej o [metrykach w Azure monitor](data-platform-metrics.md).
- Poznaj [dane monitorowania dostępne](data-sources.md) dla różnych zasobów na platformie Azure.

