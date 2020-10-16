---
title: Transformacja źródła w strumieniu danych mapowania
description: Dowiedz się, jak skonfigurować transformację źródłową w mapowaniu przepływu danych.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2020
ms.openlocfilehash: 0efcc82617c75089ff2bf396133f9fbc7ec701ea
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107695"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformacja źródła w strumieniu danych mapowania 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformacja źródła konfiguruje źródło danych dla przepływu danych. Podczas projektowania przepływów danych pierwszy krok zawsze skonfiguruje transformację źródłową. Aby dodać źródło, kliknij pole **Dodaj źródło** na kanwie przepływu danych.

Każdy przepływ danych wymaga co najmniej jednego przekształcenia źródła, ale w razie potrzeby można dodać dowolną liczbę źródeł do ukończenia transformacji danych. Można przyłączyć te źródła wraz z przyłączaniem, wyszukiwaniem lub przekształceniem Unii.

Każda transformacja źródła jest skojarzona z dokładnie jednym zestawem danych lub połączoną usługą. Zestaw danych definiuje kształt i lokalizację danych, które mają być zapisywane lub odczytywane. W przypadku korzystania z zestawu danych opartego na plikach można używać symboli wieloznacznych i list plików w źródle, aby współpracowały z więcej niż jednym plikiem naraz.

## <a name="inline-datasets"></a>Wbudowane zestawy danych

Pierwsza decyzja podejmowana podczas tworzenia transformacji źródłowej polega na tym, czy informacje źródłowe są zdefiniowane wewnątrz obiektu DataSet, czy w ramach transformacji źródłowej. Większość formatów jest dostępna tylko w jednym lub drugim. Zapoznaj się z odpowiednim dokumentem łącznika, aby dowiedzieć się, jak używać określonego łącznika.

Jeśli format jest obsługiwany zarówno w przypadku obiektów wbudowanych, jak i w obiekcie DataSet, istnieją zalety obu tych metod. Obiekty DataSet to jednostki wielokrotnego użytku, których można użyć w innych przepływach danych i działaniach, takich jak Kopiuj. Są one szczególnie przydatne w przypadku korzystania ze schematu ze wzmocnionymi zabezpieczeniami. Zestawy danych nie są oparte na platformie Spark i czasami może zaistnieć potrzeba zastąpienia niektórych ustawień lub projekcji schematu w transformacji źródłowej.

Wbudowane zestawy danych są zalecane w przypadku używania elastycznych schematów, jednostronnych wystąpień źródłowych lub źródeł sparametryzowanych. Jeśli źródło jest silnie sparametryzowane, zestawy danych w wierszu umożliwiają nie można utworzyć "fikcyjnego" obiektu. Wbudowane zestawy danych są oparte na platformie Spark, a ich właściwości są natywne dla przepływu danych.

Aby użyć wbudowanego zestawu danych, wybierz odpowiedni format w selektorze **typu źródła** . Zamiast wybierać źródłowy zestaw danych, wybierz połączoną usługę, z którą chcesz nawiązać połączenie.

![Wbudowany zestaw danych](media/data-flow/inline-selector.png "Wbudowany zestaw danych")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> Obsługiwane typy źródeł

Mapowanie przepływu danych odbywa się zgodnie z podejściem wyodrębniania, ładowania, przekształcania (ELT) i współdziała z *tymczasowymi* zestawami danych, które są wszystkie na platformie Azure. Obecnie następujące zestawy danych mogą być używane w transformacji źródłowej:

| Łącznik | Format | Zestaw danych/wbudowany |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Tekst rozdzielany](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta (wersja zapoznawcza)](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>-/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Tekst rozdzielany](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Common Data Model (wersja zapoznawcza)](format-common-data-model.md#source-properties)<br>[Tekst rozdzielany](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta (wersja zapoznawcza)](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br/>-/✓<br>✓/-<br>-/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Baza danych SQL Azure](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Wystąpienie zarządzane Azure SQL (wersja zapoznawcza)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB (interfejs API SQL)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Ustawienia specyficzne dla tych łączników znajdują się na karcie **Opcje źródła** . Przykłady skryptów informacji i przepływu danych dotyczące tych ustawień znajdują się w dokumentacji łącznika. 

Usługa Azure Data Factory ma dostęp do ponad [90 natywnych łączników](connector-overview.md). Aby dołączyć dane z innych źródeł w przepływie danych, Użyj działania kopiowania w celu załadowania tych danych do jednego z obsługiwanych obszarów tymczasowych.

## <a name="source-settings"></a>Ustawienia źródła

Po dodaniu źródła skonfiguruj go za pomocą karty **Ustawienia źródła** . W tym miejscu możesz wybrać lub utworzyć zestaw danych, do którego prowadzi punkty źródłowe. Możesz również wybrać opcje schematu i próbkowania dla danych. 

Wartości programistyczne dla parametrów zestawu danych można skonfigurować w [ustawieniach debugowania](concepts-data-flow-debug-mode.md) (wymaga włączenia trybu debugowania).

![Karta Ustawienia źródła](media/data-flow/source1.png "Karta Ustawienia źródła")

**Nazwa strumienia wyjściowego:** Nazwa transformacji źródłowej.

**Typ źródła:** Wybierz, czy chcesz użyć wbudowanego zestawu danych, czy istniejącego obiektu zestawu danych.

**Test Connection:** Sprawdź, czy usługa Spark przepływu danych może pomyślnie nawiązać połączenie z połączoną usługą używaną w źródłowym zestawie danych. Aby można było włączyć tę funkcję, tryb debugowania musi być włączony.

**Dryfowanie schematu:** [dryfowanie schematu](concepts-data-flow-schema-drift.md) to zdolność fabryki danych do natywnej obsługi elastycznych schematów w przepływach danych bez konieczności jawnego definiowania zmian w kolumnach.

* Zaznacz pole **Zezwalaj na dryf schematu** , jeśli kolumny źródłowe będą często zmieniane. To ustawienie umożliwia przepływ wszystkich przychodzących pól źródłowych do przepływu przez przekształcenia do ujścia.

* Wybór **typów kolumn z dryfem** spowoduje, że Fabryka danych będzie wykrywać i definiować typy danych dla każdej wykrytej nowej kolumny. Po wyłączeniu tej funkcji wszystkie kolumny z przeznaczeniem będą typu String.

**Weryfikuj schemat:** Jeśli wybrano opcję Weryfikuj schemat, przepływ danych nie zostanie uruchomiony, jeśli przychodzące dane źródłowe nie są zgodne ze zdefiniowanym schematem zestawu danych.

**Pomiń liczbę wierszy:** Pole Pomiń liczbę wierszy określa liczbę wierszy, które mają być ignorowane na początku zestawu danych.

**Próbkowanie:** Włącz próbkowanie, aby ograniczyć liczbę wierszy ze źródła. Użyj tego ustawienia, gdy testujesz lub przykładowe dane ze źródła do celów debugowania.

Aby sprawdzić, czy źródło jest prawidłowo skonfigurowane, Włącz tryb debugowania i Pobierz Podgląd danych. Aby uzyskać więcej informacji, zobacz [tryb debugowania](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Gdy tryb debugowania jest włączony, konfiguracja limitu wierszy w ustawieniach debugowania spowoduje zastąpienie ustawienia próbkowania w źródle podczas wyświetlania podglądu danych.

## <a name="source-options"></a>Opcje źródła

Karta Opcje źródła zawiera ustawienia specyficzne dla wybranego łącznika i formatu. Więcej informacji i przykładów można znaleźć w dokumentacji dotyczącej odpowiednich [łączników](#supported-sources).

## <a name="projection"></a>Rzut

Podobnie jak w przypadku schematów w zestawach danych, projekcja w źródle definiuje kolumny, typy i formaty dane z danych źródłowych. W przypadku większości typów zestawu danych, takich jak SQL i Parquet, projekcja w źródle jest stała, aby odzwierciedlała schemat zdefiniowany w zestawie danych. Jeśli pliki źródłowe nie są jednoznacznie wpisane (na przykład pliki w formacie Flat CSV, a nie pliki parquet), można zdefiniować typy danych dla każdego pola w transformacji źródłowej.

![Ustawienia na karcie projekcja](media/data-flow/source3.png "Rzut")

Jeśli plik tekstowy nie ma zdefiniowanego schematu, wybierz pozycję **Wykryj typ danych** , aby Data Factory próbkować i wywnioskować typy danych. Wybierz opcję **Definiuj domyślny format** , aby automatycznie wykrywać domyślne formaty danych.

**Zresetuj schemat resetuje** projekcję do elementów zdefiniowanych w zestawie danych, do których się odwołuje.

Typy danych kolumny można modyfikować w transformacjach kolumn pochodnych w dół i w dół. Użyj przekształcenia SELECT, aby zmodyfikować nazwy kolumn.

### <a name="import-schema"></a>Importuj schemat

Przycisk **Importuj schemat** na karcie **projekcje** umożliwia korzystanie z aktywnego klastra debugowania w celu utworzenia projekcji schematu. W tym miejscu są dostępne w każdym typie źródłowym, co spowoduje zastępowanie projekcji zdefiniowanej w zestawie danych. Obiekt DataSet nie zostanie zmieniony.

Jest to przydatne w zestawach danych, takich jak Avro i Azure Cosmos DB, które obsługują złożone struktury, nie wymagają, aby definicje schematu istniały w zestawie danych. W przypadku wbudowanych zestawów danych jest to jedyny sposób odwoływania się do metadanych kolumn bez dryfowania schematu.

## <a name="optimize-the-source-transformation"></a>Optymalizuj transformację źródłową

Karta **Optymalizacja** umożliwia edytowanie informacji o partycji w każdym kroku transformacji. W większości przypadków **Użyj bieżącego partycjonowania** na potrzeby idealnej struktury partycjonowania dla źródła.

W przypadku odczytywania danych ze źródła Azure SQL Database niestandardowe partycjonowanie **źródła** prawdopodobnie odczyta dane najszybciej. ADF odczytuje duże zapytania, tworząc równolegle połączenia ze swoją bazą danych. To partycjonowanie źródła można wykonać w kolumnie lub za pomocą zapytania.

![Ustawienia partycji źródłowej](media/data-flow/sourcepart3.png "podziału")

Aby uzyskać więcej informacji na temat optymalizacji w ramach mapowania przepływu danych, zobacz [kartę Optymalizacja](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Następne kroki

Rozpocznij budowanie przepływu danych przy użyciu [transformacji kolumn pochodnych](data-flow-derived-column.md) oraz [transformacji SELECT](data-flow-select.md).
