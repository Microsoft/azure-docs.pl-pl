---
title: Przepływy danych
description: Przegląd przepływów danych w usłudze Azure Synapse Analytics
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 18322afbdca94b12ef142f6e37c4d2a22170436b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592669"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Przepływy danych w usłudze Azure Synapse Analytics

## <a name="what-are-data-flows"></a>Co to są przepływy danych?

Przepływy danych są wizualnie zaprojektowanymi transformacjami danych w usłudze Azure Synapse Analytics. Przepływy danych umożliwiają inżynierom danych Tworzenie logiki transformacji danych bez pisania kodu. Wyniki przepływów danych są wykonywane jako działania w ramach potoków analizy Synapse Azure, które korzystają z skalowalnych w poziomie klastrów Apache Spark. Działania przepływu danych mogą być operacyjne przy użyciu istniejących możliwości planowania, kontrolowania, przepływu i monitorowania usługi Azure Synapse Analytics.

Przepływy danych zapewniają całkowicie wizualizację, bez konieczności kodowania. Przepływy danych są uruchamiane na zarządzanych przez Synapse klastrach wykonywania na potrzeby przetwarzania danych skalowanych w poziomie. Usługa Azure Synapse Analytics obsługuje wszystkie tłumaczenia kodu, optymalizację ścieżki i wykonywanie zadań przepływu danych.

## <a name="getting-started"></a>Wprowadzenie

Przepływy danych są tworzone w okienku opracowywanie w programie Synapse Studio. Aby utworzyć przepływ danych, wybierz znak plus obok pozycji **opracowywanie**, a następnie wybierz pozycję **przepływ danych**. 

![Nowy przepływ danych](media/data-flow/new-data-flow.png)

Ta akcja spowoduje przejście do kanwy przepływu danych, w której można utworzyć logikę transformacji. Wybierz pozycję **Dodaj źródło** , aby rozpocząć konfigurowanie transformacji źródłowej. Aby uzyskać więcej informacji, zobacz [Źródło transformacji](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="authoring-data-flows"></a>Tworzenie przepływów danych

Przepływ danych ma unikatową kanwę tworzenia, która umożliwia łatwe tworzenie logiki transformacji. Kanwa przepływu danych jest podzielony na trzy części: górny pasek, wykres i panel konfiguracja. 

![Zrzut ekranu przedstawia kanwę przepływu danych z paskiem górnego, wykresu i panelu konfiguracji z etykietą.](media/data-flow/canvas-1.png)

### <a name="graph"></a>Graph

Wykres przedstawia strumień transformacji. Pokazuje on dane źródłowe w miarę ich przepływu w jednym lub większej liczbie zlewów. Aby dodać nowe źródło, wybierz pozycję **Dodaj źródło**. Aby dodać nową transformację, wybierz znak plus w prawym dolnym rogu istniejącej transformacji. Dowiedz się więcej na temat [zarządzania wykresem przepływu danych](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Zrzut ekranu przedstawia część wykresu kanwy z polem tekstowym wyszukiwania.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Panel konfiguracji

Panel konfiguracja przedstawia ustawienia specyficzne dla aktualnie wybranego przekształcenia. Jeśli żadna transformacja nie jest zaznaczona, przepływ danych zostanie wyświetlony. W ogólnej konfiguracji przepływu danych można dodawać parametry za pomocą karty **Parametry** . Aby uzyskać więcej informacji, zobacz [parametry przepływu danych](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Każda transformacja zawiera co najmniej cztery karty konfiguracyjne.

#### <a name="transformation-settings"></a>Ustawienia transformacji

Pierwsza karta w okienku Konfiguracja każdej transformacji zawiera ustawienia specyficzne dla tej transformacji. Aby uzyskać więcej informacji, zobacz stronę dokumentacji przekształcenia.

![Karta Ustawienia źródła](media/data-flow/source-1.png)

#### <a name="optimize"></a>Optymalizacja

Karta **Optymalizacja** zawiera ustawienia umożliwiające skonfigurowanie schematów partycjonowania. Aby dowiedzieć się więcej na temat optymalizowania przepływów danych, zobacz [Przewodnik dotyczący wydajności przepływu danych](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Zrzut ekranu przedstawia kartę Optymalizacja](media/data-flow/optimize.png)

#### <a name="inspect"></a>Skontrol

Karta **Inspekcja** umożliwia wyświetlenie metadanych strumienia danych, który jest przekształcany. Możesz zobaczyć liczby kolumn, kolumny zmienione, kolumny dodane, typy danych, kolejność kolumn i odwołania do kolumn. **Inspekcja** to widok metadanych w trybie tylko do odczytu. Nie musisz mieć włączonego trybu debugowania, aby wyświetlić metadane w okienku **Inspekcja** .

![Karta Inspekcja](media/data-flow/inspect.png)

Gdy zmienisz kształt danych za pomocą transformacji, przepływ zmian metadanych zostanie wyświetlony w okienku **Inspekcja** . Jeśli w transformacji źródłowej nie ma zdefiniowanego schematu, metadane nie będą widoczne w okienku **Inspekcja** . Brak metadanych jest powszechny w scenariuszach dryfowania schematu.

#### <a name="data-preview"></a>Podgląd danych

Jeśli tryb debugowania jest włączony, karta **Podgląd danych** zapewnia interaktywną migawkę danych w każdej transformacji. Aby uzyskać więcej informacji, zobacz [Podgląd danych w trybie debugowania](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview).

### <a name="top-bar"></a>Górny pasek

Górny pasek zawiera akcje, które mają wpływ na cały przepływ danych, takie jak ustawienia walidacji i debugowania. Możesz również wyświetlić źródłowy kod JSON i skrypt przepływu danych logiki transformacji.

## <a name="available-transformations"></a>Dostępne przekształcenia

Wyświetl [Informacje o mapowaniu przekształceń przepływu danych](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , aby uzyskać listę dostępnych transformacji.

## <a name="data-flow-activity"></a>Działanie przepływu danych

Przepływy danych są operacyjne w ramach potoków analizy Synapse na platformie Azure za pomocą [działania przepływu danych](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Dla wszystkich użytkowników należy określić, które środowisko Integration Runtime ma być używane i przekazać wartości parametrów. Aby uzyskać więcej informacji, zapoznaj się z tematem [Azure Integration Runtime](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime).

## <a name="debug-mode"></a>Tryb debugowania

Tryb debugowania pozwala interaktywnie zobaczyć wyniki każdego kroku przekształcenia podczas kompilowania i debugowania przepływów danych. Sesja debugowania może być używana zarówno podczas kompilowania logiki przepływu danych, jak i uruchamiania debugowania potoku z działaniami przepływu danych. Aby dowiedzieć się więcej, zobacz [dokumentację trybu debugowania](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="monitoring-data-flows"></a>Monitorowanie przepływów danych

Przepływ danych integruje się z istniejącymi możliwościami monitorowania usługi Azure Synapse Analytics. Aby dowiedzieć się, jak zrozumieć dane wyjściowe monitorowania przepływu danych, zobacz [monitorowanie przepływów danych](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Zespół usługi Azure Synapse Analytics utworzył [Przewodnik dostrajania wydajności](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , który pomaga zoptymalizować czas wykonywania przepływów danych po skompilowaniu logiki biznesowej.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć [transformację źródłową](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Dowiedz się, jak tworzyć przepływy danych w [trybie debugowania](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
