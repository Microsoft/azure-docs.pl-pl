---
title: Przepływy danych
description: Omówienie przepływów danych w Azure Synapse Analytics
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 4769cc8abe121625f3bf77785cd681c0f649d166
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567693"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Przepływy danych w Azure Synapse Analytics

## <a name="what-are-data-flows"></a>Co to są przepływy danych?

Przepływy danych to wizualnie zaprojektowane przekształcenia danych w Azure Synapse Analytics. Przepływy danych umożliwiają inżynierom danych opracowywanie logiki przekształcania danych bez pisania kodu. Wynikowe przepływy danych są wykonywane jako działania w ramach Azure Synapse Analytics, które używają skalowanych w Apache Spark klastrach. Działania przepływu danych mogą być zoperacyjne przy użyciu istniejących Azure Synapse Analytics planowania, sterowania, przepływu i monitorowania.

Przepływy danych zapewniają całkowicie wizualne środowisko bez konieczności kodowania. Przepływy danych są uruchamiane w klastrach wykonywania zarządzanych przez usługę Synapse w celu skalowania przetwarzania danych. Azure Synapse Analytics obsługuje wszystkie tłumaczenia kodu, optymalizację ścieżek i wykonywanie zadań przepływu danych.

## <a name="getting-started"></a>Wprowadzenie

Przepływy danych są tworzone w okienku Opracowywanie w programie Synapse Studio. Aby utworzyć przepływ danych, wybierz znak plus obok opcji Opracuj, a następnie wybierz pozycję **Przepływ danych**. 

![Nowy przepływ danych](media/data-flow/new-data-flow.png)

Ta akcja umożliwia utworzenie logiki przekształcania na kanwie przepływu danych. Wybierz **pozycję Dodaj źródło,** aby rozpocząć konfigurowanie przekształcenia źródła. Aby uzyskać więcej informacji, zobacz [Source transformation (Przekształcanie źródła).](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="authoring-data-flows"></a>Tworzenie przepływów danych

Przepływ danych ma unikatową kanwę tworzenia, która ułatwia tworzenie logiki przekształcania. Kanwa przepływu danych jest podzielona na trzy części: górny pasek, graf i panel konfiguracji. 

![Zrzut ekranu przedstawia kanwę przepływu danych z etykietą górny pasek, wykres i panel konfiguracji.](media/data-flow/canvas-1.png)

### <a name="graph"></a>Graph

Wykres wyświetla strumień przekształcenia. Pokazuje on źródło danych podczas ich przepływu do co najmniej jednego ujścia. Aby dodać nowe źródło, wybierz pozycję **Dodaj źródło**. Aby dodać nowe przekształcenie, wybierz znak plus w prawym dolnym rogu istniejącego przekształcenia. Dowiedz się więcej na temat zarządzania [wykresem przepływu danych.](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

![Zrzut ekranu przedstawia część grafową kanwy z polem tekstowym Wyszukaj.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Panel konfiguracji

Panel konfiguracji zawiera ustawienia specyficzne dla aktualnie wybranego przekształcenia. Jeśli nie wybrano przekształcenia, zostanie w nim pokazany przepływ danych. W ogólnej konfiguracji przepływu danych można dodać parametry za pośrednictwem **karty** Parametry. Aby uzyskać więcej informacji, zobacz [Parametry przepływu danych](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Każde przekształcenie zawiera co najmniej cztery karty konfiguracji.

#### <a name="transformation-settings"></a>Ustawienia przekształcania

Pierwsza karta w okienku konfiguracji każdego przekształcenia zawiera ustawienia specyficzne dla tego przekształcenia. Aby uzyskać więcej informacji, zobacz stronę dokumentacji przekształcenia.

![Karta Ustawienia źródła](media/data-flow/source-1.png)

#### <a name="optimize"></a>Optymalizacja

Karta **Optymalizacja** zawiera ustawienia służące do konfigurowania schematów partycjonowania. Aby dowiedzieć się więcej na temat optymalizowania przepływów danych, zobacz przewodnik dotyczący wydajności [przepływu danych mapowania.](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

![Zrzut ekranu przedstawiający kartę Optymalizacja](media/data-flow/optimize.png)

#### <a name="inspect"></a>Sprawdzić

Karta **Inspekcja** zawiera widok metadanych przekształcanych strumieni danych. Możesz zobaczyć liczby kolumn, zmienione kolumny, dodane kolumny, typy danych, kolejność kolumn i odwołania do kolumn. **Inspekcja** to widok tylko do odczytu metadanych. Nie musisz mieć włączonego trybu debugowania, aby wyświetlić metadane w **okienku Inspekcja.**

![Karta Inspekcja](media/data-flow/inspect.png)

Gdy zmienisz kształt danych za pomocą przekształceń, zobaczysz przepływ zmian metadanych w **okienku Inspekcja.** Jeśli w przekształceniu źródła nie ma zdefiniowanego schematu, metadane nie będą widoczne w **okienku Inspekcja.** Brak metadanych jest powszechny w scenariuszach dryfu schematu.

#### <a name="data-preview"></a>Podgląd danych

Jeśli tryb debugowania jest wł., karta **Podgląd danych** zawiera interaktywną migawkę danych w poszczególnych przekształceniach. Aby uzyskać więcej informacji, zobacz [Podgląd danych w trybie debugowania.](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview)

### <a name="top-bar"></a>Górny pasek

Górny pasek zawiera akcje, które mają wpływ na cały przepływ danych, takie jak ustawienia walidacji i debugowania. Możesz również wyświetlić podstawowy kod JSON i skrypt przepływu danych logiki przekształcania.

## <a name="available-transformations"></a>Dostępne przekształcenia

Wyświetl omówienie [przekształcania przepływu danych mapowania,](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) aby uzyskać listę dostępnych przekształceń.

## <a name="data-flow-activity"></a>Działanie przepływu danych

Przepływy danych są zoperacyjne Azure Synapse Analytics potokach przy użyciu [działania przepływu danych](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Wszystko, co użytkownik musi zrobić, to określić, którego środowiska Integration Runtime użyć, i przekazać wartości parametrów. Aby uzyskać więcej informacji, dowiedz się więcej o [środowisku Azure Integration Runtime.](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime)

## <a name="debug-mode"></a>Tryb debugowania

Tryb debugowania umożliwia interaktywne wyświetlanie wyników każdego kroku przekształcania podczas kompilowania i debugowania przepływów danych. Sesji debugowania można używać zarówno w programie podczas tworzenia logiki przepływu danych, jak i uruchamiania przebiegów debugowania potoku z działaniami przepływu danych. Aby dowiedzieć się więcej, zobacz [dokumentację trybu debugowania](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="monitoring-data-flows"></a>Monitorowanie przepływów danych

Przepływ danych integruje się z istniejącymi Azure Synapse Analytics monitorowania. Aby dowiedzieć się, jak zrozumieć dane wyjściowe monitorowania przepływu danych, zobacz [monitorowanie przepływów danych mapowania.](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

Zespół Azure Synapse Analytics utworzył [przewodnik](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) dostosowywania wydajności, który pomoże Ci zoptymalizować czas wykonywania przepływów danych po utworzeniu logiki biznesowej.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć [przekształcenie źródła.](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* Dowiedz się, jak tworzyć przepływy danych w [trybie debugowania.](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
