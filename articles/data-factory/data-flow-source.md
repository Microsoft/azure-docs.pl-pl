---
title: Transformacja źródła w strumieniu danych mapowania
description: Dowiedz się, jak skonfigurować transformację źródłową w mapowaniu przepływu danych.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 310a062a8600539750935c93c7d10a1cf17a885d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016393"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformacja źródła w strumieniu danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformacja źródła konfiguruje źródło danych dla przepływu danych. Podczas projektowania przepływów danych pierwszym krokiem jest zawsze skonfigurowanie transformacji źródłowej. Aby dodać źródło, zaznacz pole **Dodaj źródło** na kanwie przepływu danych.

Każdy przepływ danych wymaga co najmniej jednego przekształcenia źródła, ale w razie potrzeby można dodać dowolną liczbę źródeł do ukończenia transformacji danych. Można przyłączyć te źródła wraz z przyłączaniem, wyszukiwaniem lub przekształceniem Unii.

Każda transformacja źródła jest skojarzona z dokładnie jednym zestawem danych lub połączoną usługą. Zestaw danych definiuje kształt i lokalizację danych, które mają być zapisywane lub odczytywane. Jeśli używasz zestawu danych opartego na plikach, możesz użyć symboli wieloznacznych i list plików w źródle do pracy z więcej niż jednym plikiem w danym momencie.

## <a name="inline-datasets"></a>Wbudowane zestawy danych

Pierwsza decyzja podejmowana podczas tworzenia transformacji źródłowej polega na tym, czy informacje źródłowe są zdefiniowane wewnątrz obiektu DataSet, czy w ramach transformacji źródłowej. Większość formatów jest dostępna tylko w jednym lub drugim. Aby dowiedzieć się, jak używać określonego łącznika, zapoznaj się z odpowiednim dokumentem łącznika.

Jeśli format jest obsługiwany zarówno w przypadku obiektów wbudowanych, jak i w obiekcie DataSet, istnieją zalety obu tych metod. Obiekty DataSet to jednostki wielokrotnego użytku, które mogą być używane w innych przepływach danych i działaniach, takich jak Kopiuj. Te jednostki wielokrotnego użytku są szczególnie przydatne w przypadku korzystania ze schematu ze wzmocnionymi zabezpieczeniami. Zestawy danych nie są oparte na platformie Spark. Czasami może być konieczne przesłonięcie niektórych ustawień lub projekcji schematu w transformacji źródłowej.

Wbudowane zestawy danych są zalecane w przypadku używania elastycznych schematów, jednostronnych wystąpień źródłowych lub źródeł sparametryzowanych. Jeśli źródło jest silnie sparametryzowane, wbudowane zestawy danych pozwalają nie tworzyć "fikcyjnego" obiektu. Wbudowane zestawy danych są oparte na platformie Spark, a ich właściwości są natywne dla przepływu danych.

Aby użyć wbudowanego zestawu danych, wybierz odpowiedni format w selektorze **typu źródła** . Zamiast wybierać źródłowy zestaw danych, wybierz połączoną usługę, z którą chcesz nawiązać połączenie.

![Zrzut ekranu, który pokazuje zaznaczone w tekście.](media/data-flow/inline-selector.png "Zrzut ekranu, który pokazuje zaznaczone w tekście.")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> Obsługiwane typy źródeł

Mapowanie przepływu danych odbywa się zgodnie z podejściem wyodrębniania, ładowania i przekształcania (ELT) i współdziała z *tymczasowymi* zestawami danych, które są wszystkie na platformie Azure. Obecnie następujące zestawy danych mogą być używane w transformacji źródłowej.

| Łącznik | Format | Zestaw danych/wbudowany |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Tekst rozdzielany](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br>✓/✓<br>✓/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Cosmos DB (interfejs API SQL)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Tekst rozdzielany](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br>✓/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Common Data Model](format-common-data-model.md#source-properties)<br>[Tekst rozdzielany](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br/>-/✓<br>✓/✓<br>✓/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Database for MySQL](connector-azure-database-for-mysql.md) |  | ✓/✓ |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Wystąpienie zarządzane Azure SQL](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Hive](connector-hive.md#mapping-data-flow-properties) | | -/✓ |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Ustawienia specyficzne dla tych łączników znajdują się na karcie **Opcje źródła** . Przykłady skryptów informacji i przepływu danych dotyczące tych ustawień znajdują się w dokumentacji łącznika.

Azure Data Factory ma dostęp do ponad [90 łączników natywnych](connector-overview.md). Aby dołączyć dane z innych źródeł w przepływie danych, Użyj działania kopiowania w celu załadowania tych danych do jednego z obsługiwanych obszarów tymczasowych.

## <a name="source-settings"></a>Ustawienia źródła

Po dodaniu źródła skonfiguruj go za pomocą karty **Ustawienia źródła** . W tym miejscu możesz wybrać lub utworzyć zestaw danych, do którego prowadzi punkty źródłowe. Możesz również wybrać opcje schematu i próbkowania dla danych.

Wartości programistyczne dla parametrów zestawu danych można skonfigurować w [ustawieniach debugowania](concepts-data-flow-debug-mode.md). (Tryb debugowania musi być włączony).

![Zrzut ekranu przedstawiający kartę Ustawienia źródła.](media/data-flow/source1.png "Zrzut ekranu przedstawiający kartę Ustawienia źródła.")

**Nazwa strumienia wyjściowego**: Nazwa transformacji źródłowej.

**Typ źródła**: Wybierz, czy chcesz użyć wbudowanego zestawu danych, czy istniejącego obiektu DataSet.

**Test connection**: Sprawdź, czy usługa Spark przepływu danych może pomyślnie nawiązać połączenie z połączoną usługą używaną w źródłowym zestawie danych. Aby można było włączyć tę funkcję, tryb debugowania musi być włączony.

**Dryfowanie schematu**: [dryfowanie schematu](concepts-data-flow-schema-drift.md) jest możliwością Data Factory natywnie obsługiwać elastyczne schematy w przepływach danych bez konieczności jawnego definiowania zmian w kolumnach.

* Zaznacz pole wyboru **Zezwalaj na dryf schematu** , jeśli kolumny źródłowe będą często zmieniane. To ustawienie umożliwia przepływ wszystkich przychodzących pól źródłowych do przepływu przez przekształcenia do ujścia.

* Wybranie pozycji **wywnioskowanie typów kolumn** z przekazaniem powoduje, że Data Factory wykrywać i definiować typy danych dla każdej wykrytej nowej kolumny. Po wyłączeniu tej funkcji wszystkie kolumny z przeznaczeniem będą typu String.

**Weryfikuj schemat:** Jeśli wybrano opcję **Weryfikuj schemat** , przepływ danych nie zostanie uruchomiony, jeśli przychodzące dane źródłowe nie są zgodne ze zdefiniowanym schematem zestawu danych.

**Pomiń liczbę wierszy**: pole **Pomiń liczbę** wierszy określa liczbę wierszy do zignorowania na początku zestawu danych.

**Próbkowanie**: Włącz **próbkowanie** , aby ograniczyć liczbę wierszy ze źródła. Użyj tego ustawienia, gdy testujesz lub przykładowe dane ze źródła do celów debugowania. Jest to bardzo przydatne w przypadku wykonywania przepływów danych w trybie debugowania z potoku.

Aby sprawdzić, czy źródło jest prawidłowo skonfigurowane, Włącz tryb debugowania i Pobierz Podgląd danych. Aby uzyskać więcej informacji, zobacz [tryb debugowania](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Gdy tryb debugowania jest włączony, konfiguracja limitu wierszy w ustawieniach debugowania spowoduje zastąpienie ustawienia próbkowania w źródle podczas wyświetlania podglądu danych.

## <a name="source-options"></a>Opcje źródła

Karta **Opcje źródła** zawiera ustawienia specyficzne dla wybranego łącznika i formatu. Aby uzyskać więcej informacji i przykładów, zobacz odpowiednią [dokumentację dotyczącą łączników](#supported-sources).

## <a name="projection"></a>Projekcja

Podobnie jak w przypadku schematów w zestawach danych, projekcja w źródle definiuje kolumny, typy i formaty dane z danych źródłowych. W przypadku większości typów zestawu danych, takich jak SQL i Parquet, projekcja w źródle jest stała, aby odzwierciedlała schemat zdefiniowany w zestawie danych. Jeśli pliki źródłowe nie są jednoznacznie wpisane (na przykład pliki Flat. csv, a nie pliki parquet), można zdefiniować typy danych dla każdego pola w transformacji źródłowej.

![Zrzut ekranu przedstawiający ustawienia na karcie projekcja.](media/data-flow/source3.png "Zrzut ekranu przedstawiający ustawienia na karcie projekcja.")

Jeśli plik tekstowy nie ma zdefiniowanego schematu, wybierz pozycję **Wykryj typ danych** , aby Data Factory próbkować i wywnioskować typy danych. Wybierz opcję **Definiuj domyślny format** , aby automatycznie wykrywać domyślne formaty danych.

**Zresetuj schemat resetuje** projekcję do elementów zdefiniowanych w zestawie danych, do których się odwołuje.

Można modyfikować typy danych kolumny w przekształceniu kolumn pochodnych podrzędnych. Użyj przekształcenia SELECT, aby zmodyfikować nazwy kolumn.

### <a name="import-schema"></a>Importuj schemat

Wybierz przycisk **Importuj schemat** na karcie **projekcja** , aby użyć aktywnego klastra debugowania do utworzenia projekcji schematu. Jest ona dostępna w każdym typie źródła. Zaimportowanie schematu spowoduje zastąpienie projekcji zdefiniowanej w zestawie danych. Obiekt DataSet nie zostanie zmieniony.

Importowanie schematu jest przydatne w zestawach danych, takich jak Avro i Azure Cosmos DB, które obsługują złożone struktury, które nie wymagają, aby definicje schematu istniały w zestawie danych. W przypadku wbudowanych zestawów danych importowanie schematu jest jedynym sposobem odwoływania się do metadanych kolumn bez dryfowania schematu.

## <a name="optimize-the-source-transformation"></a>Optymalizuj transformację źródłową

Karta **Optymalizacja** umożliwia edytowanie informacji o partycji w każdym kroku transformacji. W większości przypadków **Użyj bieżącego partycjonowania** na potrzeby idealnej struktury partycjonowania dla źródła.

W przypadku odczytywania danych ze źródła Azure SQL Database niestandardowe partycjonowanie **źródła** prawdopodobnie odczyta dane najszybciej. Data Factory będzie odczytywać duże zapytania przez równoczesne nawiązywanie połączeń z bazą danych. To partycjonowanie źródła można wykonać w kolumnie lub za pomocą zapytania.

![Zrzut ekranu pokazujący ustawienia partycji źródłowej.](media/data-flow/sourcepart3.png "Zrzut ekranu pokazujący ustawienia partycji źródłowej.")

Aby uzyskać więcej informacji na temat optymalizacji w ramach mapowania przepływu danych, zobacz [kartę Optymalizacja](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Następne kroki

Rozpocznij budowanie przepływu danych przy użyciu [transformacji kolumn pochodnych](data-flow-derived-column.md) oraz [transformacji SELECT](data-flow-select.md).
