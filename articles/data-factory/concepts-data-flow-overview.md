---
title: Przepływy danych mapowania
description: Omówienie mapowania przepływów danych w Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/10/2020
ms.openlocfilehash: 1529d2d546227880fee71d1823482e040d2e57c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564324"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Mapowanie przepływów danych w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>Czym są przepływy danych mapowania?

Mapowanie przepływów danych to wizualnie zaprojektowane przekształcenia danych w Azure Data Factory. Przepływy danych umożliwiają inżynierom danych Tworzenie logiki transformacji danych bez pisania kodu. Wyniki przepływów danych są wykonywane jako działania w ramach potoków Azure Data Factory, które korzystają ze skalowania Apache Spark klastrów. Działania związane z przepływem danych mogą być operacyjne przy użyciu istniejących Azure Data Factory funkcji planowania, kontroli, przepływu i monitorowania.

Mapowanie przepływów danych zapewnia całkowicie wizualizację, bez konieczności kodowania. Przepływy danych są uruchamiane na klastrach wykonywania zarządzanych przez ADF na potrzeby przetwarzania danych skalowanych w poziomie. Azure Data Factory obsługuje wszystkie tłumaczenia kodu, optymalizację ścieżki i wykonywanie zadań przepływu danych.

## <a name="getting-started"></a>Wprowadzenie

Przepływy danych są tworzone z poziomu okienka zasoby fabryki, takich jak potoki i zestawy danych. Aby utworzyć przepływ danych, wybierz znak plus obok pozycji **zasoby fabryki**, a następnie wybierz pozycję **przepływ danych**. 

![Nowy przepływ danych](media/data-flow/new-data-flow.png)

Ta akcja spowoduje przejście do kanwy przepływu danych, w której można utworzyć logikę transformacji. Wybierz pozycję **Dodaj źródło** , aby rozpocząć konfigurowanie transformacji źródłowej. Aby uzyskać więcej informacji, zobacz [Źródło transformacji](data-flow-source.md).

## <a name="authoring-data-flows"></a>Tworzenie przepływów danych

Mapowanie przepływu danych ma unikatową kanwę tworzenia, która umożliwia łatwe tworzenie logiki transformacji. Kanwa przepływu danych jest podzielony na trzy części: górny pasek, wykres i panel konfiguracja. 

![Zrzut ekranu przedstawia kanwę przepływu danych z paskiem górnego, wykresu i panelu konfiguracji z etykietą.](media/data-flow/canvas-1.png "Kanwa")

### <a name="graph"></a>Graph

Wykres przedstawia strumień transformacji. Pokazuje on dane źródłowe w miarę ich przepływu w jednym lub większej liczbie zlewów. Aby dodać nowe źródło, wybierz pozycję **Dodaj źródło**. Aby dodać nową transformację, wybierz znak plus w prawym dolnym rogu istniejącej transformacji. Dowiedz się więcej na temat [zarządzania wykresem przepływu danych](concepts-data-flow-manage-graph.md).

![Zrzut ekranu przedstawia część wykresu kanwy z polem tekstowym wyszukiwania.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Panel konfiguracji

Panel konfiguracja przedstawia ustawienia specyficzne dla aktualnie wybranego przekształcenia. Jeśli żadna transformacja nie jest zaznaczona, przepływ danych zostanie wyświetlony. W ogólnej konfiguracji przepływu danych można dodawać parametry za pomocą karty **Parametry** . Aby uzyskać więcej informacji, zobacz [Mapowanie parametrów przepływu danych](parameters-data-flow.md).

Każda transformacja zawiera co najmniej cztery karty konfiguracyjne.

#### <a name="transformation-settings"></a>Ustawienia transformacji

Pierwsza karta w okienku Konfiguracja każdej transformacji zawiera ustawienia specyficzne dla tej transformacji. Aby uzyskać więcej informacji, zobacz stronę dokumentacji przekształcenia.

![Karta Ustawienia źródła](media/data-flow/source1.png "Karta Ustawienia źródła")

#### <a name="optimize"></a>Optymalizacja

Karta **Optymalizacja** zawiera ustawienia umożliwiające skonfigurowanie schematów partycjonowania. Aby dowiedzieć się więcej na temat optymalizowania przepływów danych, zobacz [Przewodnik dotyczący wydajności przepływu danych](concepts-data-flow-performance.md).

![Zrzut ekranu przedstawia kartę Optymalizacja, która zawiera opcję partycji, typ partycji i liczbę partycji.](media/data-flow/optimize.png)

#### <a name="inspect"></a>Skontrol

Karta **Inspekcja** umożliwia wyświetlenie metadanych strumienia danych, który jest przekształcany. Możesz zobaczyć liczby kolumn, kolumny zmienione, kolumny dodane, typy danych, kolejność kolumn i odwołania do kolumn. **Inspekcja** to widok metadanych w trybie tylko do odczytu. Nie musisz mieć włączonego trybu debugowania, aby wyświetlić metadane w okienku **Inspekcja** .

![Skontrol](media/data-flow/inspect1.png "Skontrol")

Gdy zmienisz kształt danych za pomocą transformacji, przepływ zmian metadanych zostanie wyświetlony w okienku **Inspekcja** . Jeśli w transformacji źródłowej nie ma zdefiniowanego schematu, metadane nie będą widoczne w okienku **Inspekcja** . Brak metadanych jest powszechny w scenariuszach dryfowania schematu.

#### <a name="data-preview"></a>Podgląd danych

Jeśli tryb debugowania jest włączony, karta **Podgląd danych** zapewnia interaktywną migawkę danych w każdej transformacji. Aby uzyskać więcej informacji, zobacz [Podgląd danych w trybie debugowania](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Górny pasek

Górny pasek zawiera akcje, które mają wpływ na cały przepływ danych, takie jak zapisywanie i walidacja. Możesz również wyświetlić źródłowy kod JSON i skrypt przepływu danych logiki transformacji. Aby uzyskać więcej informacji, zapoznaj się z [skryptem przepływu danych](data-flow-script.md).

## <a name="available-transformations"></a>Dostępne przekształcenia

Wyświetl [Informacje o mapowaniu przekształceń przepływu danych](data-flow-transformation-overview.md) , aby uzyskać listę dostępnych transformacji.

## <a name="data-flow-activity"></a>Działanie przepływu danych

Mapowanie przepływów danych jest wykonywane w potokach ADF przy użyciu [działania przepływu danych](control-flow-execute-data-flow-activity.md). Dla wszystkich użytkowników należy określić, które środowisko Integration Runtime ma być używane i przekazać wartości parametrów. Aby uzyskać więcej informacji, zapoznaj się z tematem [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

## <a name="debug-mode"></a>Tryb debugowania

Tryb debugowania pozwala interaktywnie zobaczyć wyniki każdego kroku przekształcenia podczas kompilowania i debugowania przepływów danych. Sesja debugowania może być używana zarówno podczas kompilowania logiki przepływu danych, jak i uruchamiania debugowania potoku z działaniami przepływu danych. Aby dowiedzieć się więcej, zobacz [dokumentację trybu debugowania](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flows"></a>Monitorowanie przepływów danych

Mapowanie przepływu danych integruje się z istniejącymi możliwościami monitorowania Azure Data Factory. Aby dowiedzieć się, jak zrozumieć dane wyjściowe monitorowania przepływu danych, zobacz [monitorowanie przepływów danych](concepts-data-flow-monitoring.md).

Zespół Azure Data Factory utworzył [Przewodnik dostrajania wydajności](concepts-data-flow-performance.md) , który pomaga zoptymalizować czas wykonywania przepływów danych po skompilowaniu logiki biznesowej.

## <a name="available-regions"></a>Dostępne regiony

= = = = = = = = Mapowanie przepływów danych jest dostępne w następujących regionach w podajniku APD:

| Region platformy Azure | Przepływy danych w usłudze ADF |
| ------------ | ----------------- |
|  Australia Środkowa | |
| Australia Środkowa 2 | |
| Australia Wschodnia | ✓ |
| Australia Południowo-Wschodnia   | ✓ |
| Brazylia Południowa  | ✓ |
| Kanada Środkowa | ✓ |
| Indie Środkowe | ✓ |
| Central US    | ✓ |
| Chiny Wschodnie |      |
| Chiny Wschodnie 2  |   |
| Chiny nieregionalne | |
| Chiny Północne |     |
| Chiny Północne 2 | |
| Azja Wschodnia | ✓ |
| East US   | ✓ |
| Wschodnie stany USA 2 | ✓ |
| Francja Środkowa | ✓ |
| Francja Południowa  | |
| Niemcy środkowe (suwerenne) | |
| Niemcy (inne niż regionalne) (suwerenne) | |
| Niemcy Północne (Publiczny) | |
| Niemcy północno-wschodnie | |
| Niemcy Środkowo-Zachodnie (Publiczny) |  |
| Japonia Wschodnia | ✓ |
| Japonia Zachodnia |  |
| Korea Środkowa | ✓ |
| Korea Południowa | |
| Północno-środkowe stany USA  | ✓ |
| Europa Północna  | ✓ |
| Norwegia Wschodnia | |
| Norwegia Zachodnia | |
| Północna Republika Południowej Afryki    | ✓ |
| Zachodnia Republika Południowej Afryki |  |
| South Central US  | |
| Indie Południowe | |
| Southeast Asia    | ✓ |
| Szwajcaria Północna |   |
| Szwajcaria Zachodnia | |
| Środkowy Zjednoczone Emiraty Arabskie | |
| Północne Zjednoczone Emiraty Arabskie |  |
| Południowe Zjednoczone Królestwo  | ✓ |
| Zachodnie Zjednoczone Królestwo |     |
| US DoD (region środkowy) | |
| US DoD (region wschodni) | |
| US Gov Arizona |      |
| US Gov (inne niż regionalne) | |
| US Gov Teksas | |
| US Gov Wirginia |     |
| Zachodnio-środkowe stany USA |     |
| West Europe   | ✓ |
| Indie Zachodnie | |
| Zachodnie stany USA   | ✓ |
| Zachodnie stany USA 2 | ✓ |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć [transformację źródłową](data-flow-source.md).
* Dowiedz się, jak tworzyć przepływy danych w [trybie debugowania](concepts-data-flow-debug-mode.md).
