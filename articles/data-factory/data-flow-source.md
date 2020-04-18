---
title: Transformacja źródła w przepływie danych mapowania
description: Dowiedz się, jak skonfigurować transformację źródła w przepływie danych mapowania.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: b2f533e8bd9199025260aaca9cff587b13adce64
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606310"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformacja źródła w przepływie danych mapowania 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformacja źródła konfiguruje źródło danych dla przepływu danych. Podczas projektowania przepływów danych, pierwszym krokiem będzie zawsze konfigurowanie transformacji źródła. Aby dodać źródło, kliknij pole **Dodaj źródło** na kanwie przepływu danych.

Każdy przepływ danych wymaga co najmniej jednej transformacji źródła, ale można dodać dowolną liczbę źródeł, ile jest to konieczne do ukończenia przekształceń danych. Można połączyć te źródła wraz z sprzężenia, wyszukiwania lub transformacji unii.

Każda transformacja źródła jest skojarzona z dokładnie jednym zestawem danych usługi Data Factory. Zestaw danych definiuje kształt i lokalizację danych, które mają być zapisywane lub odczytywane. Jeśli używasz zestawu danych opartego na plikach, można użyć symboli wieloznacznych i list plików w źródle do pracy z więcej niż jednym plikiem naraz.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>Obsługiwane łączniki źródłowe w przepływie danych mapowania

Mapowanie przepływu danych następuje wyodrębnić, załadować, przekształcić (ELT) podejście i współpracuje z *przemieszczania* zestawów danych, które są na platformie Azure. Obecnie w transformacji źródłowej można używać następujących zestawów danych:
    
* [Usługa Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Tekst, Parkiet)
* [Usługa Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Tekst, Parkiet)
* [Usługa Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Tekst, Parkiet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Ustawienia specyficzne dla tych łączników znajdują się na karcie **Opcje źródła.** 

Usługa Azure Data Factory ma dostęp do ponad [90 natywnych łączników.](connector-overview.md) Aby uwzględnić dane z tych innych źródeł w przepływie danych, użyj działania kopiowania, aby załadować te dane do jednego z obsługiwanych obszarów przemieszczania.

## <a name="source-settings"></a>Ustawienia źródła

Po dodaniu źródła skonfiguruj go za pomocą karty **Ustawienia źródła.** W tym miejscu można wybrać lub utworzyć zestaw danych, w których są punktami źródłowymi. Można również wybrać opcje schematu i próbkowania danych.

![Karta Ustawienia źródła](media/data-flow/source1.png "Karta Ustawienia źródła")

**Połączenie testowe:** Sprawdź, czy usługa istry przepływu danych może pomyślnie połączyć się z usługą połączaną używaną w źródłowym zestawie danych. Tryb debugowania musi być włączony, aby ta funkcja była włączona.

**Dryf schematu: Dryf** [schematu](concepts-data-flow-schema-drift.md) jest możliwość fabryki danych do natywnie obsługiwać elastyczne schematy w przepływach danych bez konieczności jawnego definiowania zmian kolumn.

* Zaznacz pole **Zezwalaj na dryft schematu,** jeśli kolumny źródłowe będą się często zmieniać. To ustawienie umożliwia przepływ wszystkich przychodzących pól źródłowych przez przekształcenia do ujścia.

* Wybranie **typów kolumn dryfowanych infer spowoduje** poinstruowanie fabryki danych o wykrywaniu i definiowaniu typów danych dla każdej wykrytej nowej kolumny. Po wyłączeniu tej funkcji wszystkie kolumny dryfowane będą typu string.

**Sprawdź poprawność schematu:** Jeśli wybrano schemat sprawdzania poprawności, przepływ danych nie zostanie uruchomiony, jeśli przychodzące dane źródłowe nie są zgodne ze zdefiniowanym schematem zestawu danych.

**Pomiń liczbę wierszy:** Pole zliczania wierszy pomijania określa liczbę wierszy do zignorowania na początku zestawu danych.

**Pobieranie próbek:** Włącz próbkowanie, aby ograniczyć liczbę wierszy ze źródła. Użyj tego ustawienia podczas testowania lub przykładu danych ze źródła do celów debugowania.

**Wiersze wielowierszowe:** Zaznacz wiersze wielowierszowe, jeśli źródłowy plik tekstowy zawiera wartości ciągów obejmujące wiele wierszy, czyli nowe linie wewnątrz wartości. To ustawienie jest dostępne tylko w zestawach danych DelimitedText.

Aby sprawdzić poprawność źródła jest poprawnie skonfigurowany, należy włączyć tryb debugowania i pobrać podgląd danych. Aby uzyskać więcej informacji, zobacz [tryb debugowania](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Gdy tryb debugowania jest włączony, konfiguracja limitu wierszy w ustawieniach debugowania zastąpi ustawienie próbkowania w źródle podczas podglądu danych.

## <a name="projection"></a>Projekcja

Podobnie jak schematy w zestawach danych, projekcja w źródle definiuje kolumny danych, typy i formaty z danych źródłowych. W przypadku większości typów zestawów danych, takich jak SQL i Parkiet, projekcja w źródle jest ustalana w celu odzwierciedlenia schematu zdefiniowanego w zestawie danych. Gdy pliki źródłowe nie są silnie wpisane (na przykład płaskie pliki csv, a nie pliki parkietu), można zdefiniować typy danych dla każdego pola w transformacji źródłowej.

![Ustawienia na karcie Projekcja](media/data-flow/source3.png "Projekcja")

Jeśli plik tekstowy nie ma zdefiniowanego schematu, wybierz opcję **Wykryj typ danych,** aby fabryka danych wypróbowała i wywnioskowała typy danych. Wybierz **pozycję Definiuj format domyślny,** aby automatycznie wykryć domyślne formaty danych.

**Resetowanie schematu** resetuje projekcję do tego, co jest zdefiniowane w zestawie danych, do którego istnieje odwołanie.

Można zmodyfikować typy danych kolumn w przekształceniu w dół kolumny pochodnej. Użyj przekształcenia wyboru, aby zmodyfikować nazwy kolumn.

### <a name="import-schema"></a>Schemat importu

Przycisk **Importuj schemat** na karcie **Rzutowanie** umożliwia utworzenie projekcji schematu za pomocą aktywnego klastra debugowania. Dostępne w każdym typie źródła importowanie schematu w tym miejscu spowoduje zastąpienie projekcji zdefiniowanej w zestawie danych. Obiekt zestawu danych nie zostanie zmieniony.

Jest to przydatne w zestawach danych, takich jak Avro i CosmosDB, które obsługują złożone struktury danych nie wymagają definicji schematu istnieć w zestawie danych.

## <a name="optimize-the-source-transformation"></a>Optymalizacja transformacji źródła

Na **optymalizacji kartę** dla transformacji źródła może być widoczny typ partycji **źródłowej.** Ta opcja jest dostępna tylko wtedy, gdy źródłem jest usługa Azure SQL Database. Jest tak, ponieważ usługa Data Factory próbuje nawiązać połączenia równoległe do uruchamiania dużych zapytań względem źródła bazy danych SQL.

![Ustawienia partycji źródłowej](media/data-flow/sourcepart3.png "Partycjonowanie")

Nie trzeba partycjonować dane w źródle bazy danych SQL, ale partycje są przydatne dla dużych zapytań. Partycję można oprzeć na kolumnie lub kwerendzie.

### <a name="use-a-column-to-partition-data"></a>Partycjonowanie danych za pomocą kolumny

W tabeli źródłowej wybierz kolumnę do partycji. Ustaw również liczbę partycji.

### <a name="use-a-query-to-partition-data"></a>Partycjonowanie danych za pomocą kwerendy

Można podzielić połączenia na partycje na podstawie kwerendy. Wprowadź zawartość predykatu WHERE. Na przykład wprowadź rok > 1980.

Aby uzyskać więcej informacji na temat optymalizacji w przepływie danych mapowania, zobacz [kartę Optymalizuj](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Następne kroki

Rozpocznij tworzenie [transformacji kolumny pochodnej](data-flow-derived-column.md) i [transformacji wybierającej](data-flow-select.md).
