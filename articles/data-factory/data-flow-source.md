---
title: Transformacja źródła w strumieniu danych mapowania
description: Dowiedz się, jak skonfigurować transformację źródłową w mapowaniu przepływu danych.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: b2f533e8bd9199025260aaca9cff587b13adce64
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606310"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformacja źródła w strumieniu danych mapowania 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformacja źródła konfiguruje źródło danych dla przepływu danych. Podczas projektowania przepływów danych pierwszy krok zawsze skonfiguruje transformację źródłową. Aby dodać źródło, kliknij pole **Dodaj źródło** na kanwie przepływu danych.

Każdy przepływ danych wymaga co najmniej jednego przekształcenia źródła, ale w razie potrzeby można dodać dowolną liczbę źródeł do ukończenia transformacji danych. Można przyłączyć te źródła wraz z przyłączaniem, wyszukiwaniem lub przekształceniem Unii.

Każda transformacja źródła jest skojarzona z dokładnie jednym Data Factory zestawem danych. Zestaw danych definiuje kształt i lokalizację danych, które mają być zapisywane lub odczytywane. W przypadku korzystania z zestawu danych opartego na plikach można używać symboli wieloznacznych i list plików w źródle, aby współpracowały z więcej niż jednym plikiem naraz.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>Obsługiwane łączniki źródła w mapowaniu przepływu danych

Mapowanie przepływu danych odbywa się zgodnie z podejściem wyodrębniania, ładowania, przekształcania (ELT) i współdziała z *tymczasowymi* zestawami danych, które są wszystkie na platformie Azure. Obecnie następujące zestawy danych mogą być używane w transformacji źródłowej:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, text, parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, text, parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, text, parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Ustawienia specyficzne dla tych łączników znajdują się na karcie **Opcje źródła** . informacje dotyczące tych ustawień znajdują się w dokumentacji łącznika. 

Azure Data Factory ma dostęp do ponad [90 łączników natywnych](connector-overview.md). Aby dołączyć dane z innych źródeł w przepływie danych, Użyj działania kopiowania w celu załadowania tych danych do jednego z obsługiwanych obszarów tymczasowych.

## <a name="source-settings"></a>Ustawienia źródła

Po dodaniu źródła skonfiguruj go za pomocą karty **Ustawienia źródła** . W tym miejscu możesz wybrać lub utworzyć zestaw danych, do którego prowadzi punkty źródłowe. Możesz również wybrać opcje schematu i próbkowania dla danych.

![Karta Ustawienia źródła](media/data-flow/source1.png "Karta Ustawienia źródła")

**Test Connection:** Sprawdź, czy usługa Spark przepływu danych może pomyślnie nawiązać połączenie z połączoną usługą używaną w źródłowym zestawie danych. Aby można było włączyć tę funkcję, tryb debugowania musi być włączony.

**Dryfowanie schematu:** [dryfowanie schematu](concepts-data-flow-schema-drift.md) to zdolność fabryki danych do natywnej obsługi elastycznych schematów w przepływach danych bez konieczności jawnego definiowania zmian w kolumnach.

* Zaznacz pole **Zezwalaj na dryf schematu** , jeśli kolumny źródłowe będą często zmieniane. To ustawienie umożliwia przepływ wszystkich przychodzących pól źródłowych do przepływu przez przekształcenia do ujścia.

* Wybór **typów kolumn z dryfem** spowoduje, że Fabryka danych będzie wykrywać i definiować typy danych dla każdej wykrytej nowej kolumny. Po wyłączeniu tej funkcji wszystkie kolumny z przeznaczeniem będą typu String.

**Weryfikuj schemat:** Jeśli wybrano opcję Weryfikuj schemat, przepływ danych nie zostanie uruchomiony, jeśli przychodzące dane źródłowe nie są zgodne ze zdefiniowanym schematem zestawu danych.

**Pomiń liczbę wierszy:** Pole Pomiń liczbę wierszy określa liczbę wierszy, które mają być ignorowane na początku zestawu danych.

**Próbkowanie:** Włącz próbkowanie, aby ograniczyć liczbę wierszy ze źródła. Użyj tego ustawienia, gdy testujesz lub przykładowe dane ze źródła do celów debugowania.

**Wiersze wielowierszowe:** Zaznacz wiersze wielowierszowe, Jeśli źródłowy plik tekstowy zawiera wartości ciągów, które rozciągają się na wiele wierszy, tzn. nowego wiersza wewnątrz wartości. To ustawienie jest dostępne tylko w zestawach danych DelimitedText.

Aby sprawdzić, czy źródło jest prawidłowo skonfigurowane, Włącz tryb debugowania i Pobierz Podgląd danych. Aby uzyskać więcej informacji, zobacz [tryb debugowania](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Gdy tryb debugowania jest włączony, konfiguracja limitu wierszy w ustawieniach debugowania spowoduje zastąpienie ustawienia próbkowania w źródle podczas wyświetlania podglądu danych.

## <a name="projection"></a>Projekcja

Podobnie jak w przypadku schematów w zestawach danych, projekcja w źródle definiuje kolumny, typy i formaty dane z danych źródłowych. W przypadku większości typów zestawu danych, takich jak SQL i Parquet, projekcja w źródle jest stała, aby odzwierciedlała schemat zdefiniowany w zestawie danych. Jeśli pliki źródłowe nie są jednoznacznie wpisane (na przykład pliki w formacie Flat CSV, a nie pliki parquet), można zdefiniować typy danych dla każdego pola w transformacji źródłowej.

![Ustawienia na karcie projekcja](media/data-flow/source3.png "Projekcja")

Jeśli plik tekstowy nie ma zdefiniowanego schematu, wybierz pozycję **Wykryj typ danych** , aby Data Factory próbkować i wywnioskować typy danych. Wybierz opcję **Definiuj domyślny format** , aby automatycznie wykrywać domyślne formaty danych.

**Zresetuj schemat resetuje** projekcję do elementów zdefiniowanych w zestawie danych, do których się odwołuje.

Typy danych kolumny można modyfikować w transformacjach kolumn pochodnych w dół i w dół. Użyj przekształcenia SELECT, aby zmodyfikować nazwy kolumn.

### <a name="import-schema"></a>Importuj schemat

Przycisk **Importuj schemat** na karcie **projekcje** umożliwia korzystanie z aktywnego klastra debugowania w celu utworzenia projekcji schematu. W tym miejscu są dostępne w każdym typie źródłowym, co spowoduje zastępowanie projekcji zdefiniowanej w zestawie danych. Obiekt DataSet nie zostanie zmieniony.

Jest to przydatne w zestawach danych, takich jak Avro i CosmosDB, które obsługują złożone struktury, nie wymagają, aby definicje schematu istniały w zestawie danych.

## <a name="optimize-the-source-transformation"></a>Optymalizuj transformację źródłową

Na karcie **Optymalizacja** dla transformacji źródłowej może zostać wyświetlony typ partycji **źródłowej** . Ta opcja jest dostępna tylko wtedy, gdy źródło jest Azure SQL Database. Dzieje się tak, ponieważ Data Factory próbuje nawiązać połączenie równolegle, aby uruchamiać duże zapytania względem źródła SQL Database.

![Ustawienia partycji źródłowej](media/data-flow/sourcepart3.png "podziału")

Nie ma potrzeby partycjonowania danych w źródle SQL Database, ale partycje są przydatne w przypadku dużych zapytań. Partycję można oprzeć na kolumnie lub zapytaniu.

### <a name="use-a-column-to-partition-data"></a>Używanie kolumny do partycjonowania danych

Z tabeli źródłowej wybierz kolumnę do partycjonowania. Należy również ustawić liczbę partycji.

### <a name="use-a-query-to-partition-data"></a>Używanie zapytania do partycjonowania danych

Możesz wybrać opcję partycjonowania połączeń na podstawie zapytania. Wprowadź zawartość predykatu WHERE. Na przykład wprowadź Year > 1980.

Aby uzyskać więcej informacji na temat optymalizacji w ramach mapowania przepływu danych, zobacz [kartę Optymalizacja](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Następne kroki

Zacznij tworzyć [transformację pochodną kolumny](data-flow-derived-column.md) i [przekształcenie SELECT](data-flow-select.md).
