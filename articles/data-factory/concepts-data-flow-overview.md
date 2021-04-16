---
title: Przepływy danych mapowania
description: Omówienie przepływów danych mapowania w programie Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/11/2021
ms.openlocfilehash: 26d119fb6880f4c539e0064db424699bc5d22f96
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478679"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Mapowanie przepływów danych w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>Czym są przepływy danych mapowania?

Przepływy danych mapowania to wizualnie zaprojektowane przekształcenia danych w Azure Data Factory. Przepływy danych umożliwiają inżynierom danych opracowywanie logiki przekształcania danych bez pisania kodu. Wynikowe przepływy danych są wykonywane jako działania w ramach Azure Data Factory, które używają skalowanych w Apache Spark klastrach. Działania przepływu danych mogą być zoperacyjne przy użyciu istniejących Azure Data Factory planowania, sterowania, przepływu i monitorowania.

Przepływy danych mapowania zapewniają całkowicie wizualne środowisko bez konieczności kodowania. Przepływy danych są uruchamiane w klastrach wykonywania zarządzanych przez usługę ADF w celu skalowania w dół przetwarzania danych. Azure Data Factory obsługuje wszystkie tłumaczenia kodu, optymalizację ścieżek i wykonywanie zadań przepływu danych.

## <a name="getting-started"></a>Wprowadzenie

Przepływy danych są tworzone z okienka zasobów fabryki, takiego jak potoki i zestawy danych. Aby utworzyć przepływ danych, wybierz znak plus obok opcji **Zasoby** fabryki, a następnie wybierz pozycję **Przepływ danych**. 

![Nowy przepływ danych](media/data-flow/new-data-flow.png)

Ta akcja przenosi cię do kanwy przepływu danych, gdzie możesz utworzyć logikę przekształcania. Wybierz **pozycję Dodaj źródło,** aby rozpocząć konfigurowanie przekształcenia źródła. Aby uzyskać więcej informacji, zobacz [Source transformation (Przekształcenie źródła).](data-flow-source.md)

## <a name="authoring-data-flows&quot;></a>Tworzenie przepływów danych

Przepływ danych mapowania ma unikatową kanwę tworzenia, która ułatwia tworzenie logiki transformacji. Kanwa przepływu danych jest podzielona na trzy części: górny pasek, graf i panel konfiguracji. 

![Zrzut ekranu przedstawia kanwę przepływu danych z etykietą górny pasek, wykres i panel konfiguracji.](media/data-flow/canvas-1.png &quot;Kanwa")

### <a name="graph"></a>Graph

Wykres wyświetla strumień przekształceń. Pokazuje on źródło danych podczas ich przepływu do co najmniej jednego ujścia. Aby dodać nowe źródło, wybierz pozycję **Dodaj źródło**. Aby dodać nowe przekształcenie, wybierz znak plus w prawym dolnym rogu istniejącego przekształcenia. Dowiedz się więcej na temat zarządzania [wykresem przepływu danych.](concepts-data-flow-manage-graph.md)

![Zrzut ekranu przedstawiający część grafową kanwy z polem tekstowym Wyszukaj.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Panel konfiguracji

Panel konfiguracji zawiera ustawienia specyficzne dla aktualnie wybranego przekształcenia. Jeśli nie wybrano przekształcenia, zostanie w nim pokazany przepływ danych. W ogólnej konfiguracji przepływu danych można dodać parametry za pośrednictwem **karty** Parametry. Aby uzyskać więcej informacji, zobacz [Mapowanie parametrów przepływu danych](parameters-data-flow.md).

Każde przekształcenie zawiera co najmniej cztery karty konfiguracji.

#### <a name="transformation-settings"></a>Ustawienia przekształcania

Pierwsza karta w okienku konfiguracji każdego przekształcenia zawiera ustawienia specyficzne dla tego przekształcenia. Aby uzyskać więcej informacji, zobacz stronę dokumentacji przekształcenia.

![Karta Ustawienia źródła](media/data-flow/source1.png "Karta Ustawienia źródła")

#### <a name="optimize"></a>Optymalizacja

Karta **Optymalizacja** zawiera ustawienia służące do konfigurowania schematów partycjonowania. Aby dowiedzieć się więcej na temat optymalizowania przepływów danych, zobacz przewodnik dotyczący wydajności [przepływu danych mapowania.](concepts-data-flow-performance.md)

![Zrzut ekranu przedstawiający kartę Optymalizacja, która zawiera opcje Partycja, Typ partycji i Liczba partycji.](media/data-flow/optimize.png)

#### <a name="inspect&quot;></a>Sprawdzić

Karta **Inspekcja** zawiera widok metadanych przekształcanych strumieni danych. Możesz zobaczyć liczby kolumn, zmienione kolumny, dodane kolumny, typy danych, kolejność kolumn i odwołania do kolumn. **Inspekcja** to widok tylko do odczytu metadanych. Nie musisz mieć włączonego trybu debugowania, aby wyświetlić metadane w **okienku Inspekcja.**

![Sprawdzić](media/data-flow/inspect1.png &quot;Sprawdzić")

Gdy zmienisz kształt danych za pomocą przekształceń, zobaczysz przepływ zmian metadanych w **okienku Inspekcja.** Jeśli w przekształceniu źródła nie ma zdefiniowanego schematu, metadane nie będą widoczne w **okienku Inspekcja.** Brak metadanych jest często spotykany w scenariuszach dryfu schematu.

#### <a name="data-preview"></a>Podgląd danych

Jeśli tryb debugowania jest wł., karta **Podgląd** danych zawiera interaktywną migawkę danych w poszczególnych przekształceniach. Aby uzyskać więcej informacji, zobacz [Podgląd danych w trybie debugowania.](concepts-data-flow-debug-mode.md#data-preview)

### <a name="top-bar"></a>Górny pasek

Górny pasek zawiera akcje, które mają wpływ na cały przepływ danych, takie jak zapisywanie i walidacja. Możesz również wyświetlić źródłowy kod JSON i skrypt przepływu danych logiki przekształcania. Aby uzyskać więcej informacji, zapoznaj się ze [skryptem przepływu danych](data-flow-script.md).

## <a name="available-transformations"></a>Dostępne przekształcenia

Wyświetl omówienie [przekształcania przepływu danych mapowania,](data-flow-transformation-overview.md) aby uzyskać listę dostępnych przekształceń.

## <a name="data-flow-data-types"></a>Typy danych przepływu danych

binarna wartość logiczna tablicy złożonej liczby dziesiętnej liczba zmiennoprzecinkowa liczby całkowitej długiej mapy krótkiego znacznika czasu ciągu

## <a name="data-flow-activity"></a>Działanie przepływu danych

Przepływy danych mapowania są zoperacyjne w potokach usługi ADF przy użyciu [działania przepływu danych](control-flow-execute-data-flow-activity.md). Użytkownik musi tylko określić, którego środowiska Integration Runtime użyć, i przekazać wartości parametrów. Aby uzyskać więcej informacji, dowiedz się więcej o [środowisku Azure Integration Runtime.](concepts-integration-runtime.md#azure-integration-runtime)

## <a name="debug-mode"></a>Tryb debugowania

Tryb debugowania umożliwia interaktywne wyświetlanie wyników poszczególnych kroków przekształcania podczas kompilowania i debugowania przepływów danych. Sesji debugowania można używać zarówno w programie podczas tworzenia logiki przepływu danych, jak i uruchamiania przebiegów debugowania potoku z działaniami przepływu danych. Aby dowiedzieć się więcej, zobacz [dokumentację trybu debugowania](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flows"></a>Monitorowanie przepływów danych

Przepływ danych mapowania integruje się z istniejącymi Azure Data Factory monitorowania. Aby dowiedzieć się, jak zrozumieć dane wyjściowe monitorowania przepływu danych, zobacz [monitorowanie przepływów danych mapowania.](concepts-data-flow-monitoring.md)

Zespół Azure Data Factory utworzył [przewodnik](concepts-data-flow-performance.md) dostosowywania wydajności, który pomoże Ci zoptymalizować czas wykonywania przepływów danych po utworzeniu logiki biznesowej.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć [przekształcenie źródła](data-flow-source.md).
* Dowiedz się, jak tworzyć przepływy danych w [trybie debugowania.](concepts-data-flow-debug-mode.md)
