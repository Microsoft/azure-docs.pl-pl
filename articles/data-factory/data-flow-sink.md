---
title: Transformacja ujścia w przepływie danych mapowania
description: Dowiedz się, jak skonfigurować transformację ujścia w mapowaniu przepływu danych.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/17/2020
ms.openlocfilehash: da89d4fbc3f9e03e76d901c2215e4f16c5273013
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621114"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformacja ujścia w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Po zakończeniu przekształcania danych Zapisz je w magazynie docelowym przy użyciu transformacji ujścia. Każdy przepływ danych wymaga co najmniej jednego przekształcenia ujścia, ale w razie potrzeby można zapisać do tylu zlewów, aby zakończyć przepływ transformacji. Aby zapisywać w dodatkowych ujściach, Utwórz nowe strumienie za pośrednictwem nowych gałęzi i podziałów warunkowych.

Każda transformacja ujścia jest skojarzona z dokładnie jednym Azure Data Factory obiektem DataSet lub połączoną usługą. Transformacja ujścia określa kształt i lokalizację danych, do których chcesz pisać.

## <a name="inline-datasets"></a>Wbudowane zestawy danych

Podczas tworzenia transformacji ujścia należy określić, czy informacje o ujściach są zdefiniowane wewnątrz obiektu DataSet, czy w ramach transformacji ujścia. Większość formatów jest dostępna tylko w jednym lub drugim. Aby dowiedzieć się, jak używać określonego łącznika, zapoznaj się z odpowiednim dokumentem łącznika.

Jeśli format jest obsługiwany zarówno w przypadku obiektów wbudowanych, jak i w obiekcie DataSet, istnieją zalety obu tych metod. Obiekty DataSet to jednostki wielokrotnego użytku, które mogą być używane w innych przepływach danych i działaniach, takich jak Kopiuj. Te jednostki wielokrotnego użytku są szczególnie przydatne w przypadku korzystania ze schematu ze wzmocnionymi zabezpieczeniami. Zestawy danych nie są oparte na platformie Spark. Czasami może być konieczne przesłonięcie niektórych ustawień lub projekcji schematu w transformację ujścia.

Wbudowane zestawy danych są zalecane w przypadku używania elastycznych schematów, jednokrotnych wystąpień ujścia lub sparametryzowanych zbiorników. Jeśli ujścia jest silnie sparametryzowane, wbudowane zestawy danych pozwalają nie tworzyć "fikcyjnego" obiektu. Wbudowane zestawy danych są oparte na platformie Spark, a ich właściwości są natywne dla przepływu danych.

Aby użyć wbudowanego zestawu danych, wybierz odpowiedni format w selektorze **typu ujścia** . Zamiast wybierać zestaw danych ujścia, należy wybrać połączoną usługę, z którą chcesz nawiązać połączenie.

![Zrzut ekranu, który pokazuje zaznaczone w tekście.](media/data-flow/inline-selector.png "Zrzut ekranu, który pokazuje zaznaczone w tekście.")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Obsługiwane typy ujścia

Mapowanie przepływu danych odbywa się zgodnie z podejściem wyodrębniania, ładowania i przekształcania (ELT) i współdziała z *tymczasowymi* zestawami danych, które są wszystkie na platformie Azure. Obecnie następujące zestawy danych mogą być używane w transformacji źródłowej.

| Łącznik | Format | Zestaw danych/wbudowany |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Tekst rozdzielany](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (wersja zapoznawcza)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- |
| [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Tekst rozdzielany](format-delimited-text.md#mapping-data-flow-properties) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Tekst rozdzielany](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (wersja zapoznawcza)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Common Data Model (wersja zapoznawcza)](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Wystąpienie zarządzane Azure SQL (wersja zapoznawcza)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB (interfejs API SQL)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Ustawienia specyficzne dla tych łączników znajdują się na karcie **Ustawienia** . Przykłady skryptów informacji i przepływu danych dotyczące tych ustawień znajdują się w dokumentacji łącznika.

Azure Data Factory ma dostęp do ponad [90 łączników natywnych](connector-overview.md). Aby zapisać dane do tych innych źródeł z przepływu danych, Użyj działania kopiowania w celu załadowania danych z obsługiwanego ujścia.

## <a name="sink-settings"></a>Ustawienia ujścia

Po dodaniu ujścia skonfiguruj go za pomocą karty **ujścia** . W tym miejscu możesz wybrać lub utworzyć zestaw danych, do którego ma zostać zapisywany obiekt ujścia. Wartości programistyczne dla parametrów zestawu danych można skonfigurować w [ustawieniach debugowania](concepts-data-flow-debug-mode.md). (Tryb debugowania musi być włączony).

Poniższy film wideo wyjaśnia różne opcje ujścia dla typów plików rozdzielonych tekstem.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![Zrzut ekranu pokazujący ustawienia ujścia.](media/data-flow/sink-settings.png "Zrzut ekranu pokazujący ustawienia ujścia.")

**Dryfowanie schematu**: [dryfowanie schematu](concepts-data-flow-schema-drift.md) jest możliwością Data Factory natywnie obsługiwać elastyczne schematy w przepływach danych bez konieczności jawnego definiowania zmian w kolumnach. Włącz funkcję **Zezwalaj na dryfowanie schematu** w celu zapisania dodatkowych kolumn na podstawie zawartości zdefiniowanej w schemacie danych ujścia.

**Sprawdź poprawność schematu**: Jeśli wybrano opcję Weryfikuj schemat, przepływ danych zakończy się niepowodzeniem, jeśli w projekcji źródłowej nie zostanie znaleziona żadna kolumna przychodzącego schematu źródłowego lub typy danych nie są zgodne. Użyj tego ustawienia, aby wymusić, że dane źródłowe spełniają kontrakt zdefiniowanej projekcji. Jest to przydatne w scenariuszach ze źródłami danych w celu sygnalizowania, że nazwy kolumn lub typy zostały zmienione.

## <a name="cache-sink"></a>Ujścia pamięci podręcznej

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4HKt1]
 
*Ujścia pamięci podręcznej* polega na tym, że przepływ danych zapisuje dane w pamięci podręcznej Spark zamiast w magazynie danych. W mapowaniu przepływów danych można odwoływać się do tych danych w tym samym przepływie wielokrotnie przy użyciu funkcji *wyszukiwania w pamięci podręcznej*. Jest to przydatne, gdy chcesz odwoływać się do danych w ramach wyrażenia, ale nie chcesz jawnie dołączać do kolumn. Typowe przykłady, w których obiekt ujścia pamięci podręcznej może pomóc w wyszukiwaniu maksymalnej wartości w magazynie danych i dopasowania kodów błędów do bazy danych komunikatów o błędach. 

Aby zapisać dane w ujściach pamięci podręcznej, Dodaj transformację ujścia i wybierz **pamięć podręczną** jako typ ujścia. W przeciwieństwie do innych typów ujścia, nie musisz wybierać zestawu danych ani połączonej usługi, ponieważ nie zapisujesz w sklepie zewnętrznym. 

![Wybieranie ujścia pamięci podręcznej](media/data-flow/select-cache-sink.png "Wybieranie ujścia pamięci podręcznej")

W ustawieniach ujścia można opcjonalnie określić kolumny klucza ujścia pamięci podręcznej. Są one używane jako warunki dopasowywania podczas korzystania z `lookup()` funkcji w odnośniku pamięci podręcznej. W przypadku określenia kolumn kluczy nie można użyć `outputs()` funkcji w odnośniku pamięci podręcznej. Aby dowiedzieć się więcej o składni wyszukiwania w pamięci podręcznej, zobacz [buforowane wyszukiwania](concepts-data-flow-expression-builder.md#cached-lookup).

![Kolumny klucza ujścia pamięci podręcznej](media/data-flow/cache-sink-key-columns.png "Kolumny klucza ujścia pamięci podręcznej")

Na przykład, jeśli określim pojedynczą kolumnę klucza `column1` w ujścia pamięci podręcznej o nazwie `cacheExample` , wywołanie `cacheExample#lookup()` może mieć jeden parametr określający wiersz w ujścia pamięci podręcznej, według którego ma być zgodny. Funkcja wyprowadza pojedynczą kolumnę złożoną z podkolumnami dla każdej mapowanej kolumny.

> [!NOTE]
> Obiekt sink pamięci podręcznej musi znajdować się w całkowicie niezależnym strumieniu danych z dowolnego przekształcenia, który odwołuje się do niego za pośrednictwem wyszukiwania Ujścia pamięci podręcznej musi również następować pierwszy obiekt ujścia. 

## <a name="field-mapping"></a>Mapowanie pola

Podobnie jak w przypadku transformacji SELECT, na karcie **Mapowanie** obiektu ujścia można zdecydować, które kolumny przychodzące będą zapisywane. Domyślnie wszystkie kolumny wejściowe, w tym kolumny przedzielone, są mapowane. Takie zachowanie jest nazywane *automapowaniem*.

Po wyłączeniu automapowanie można dodać stałe mapowania oparte na kolumnach lub mapowania oparte na regułach. Przy użyciu mapowań opartych na regułach można pisać wyrażenia z dopasowaniem do wzorca. Stałe mapowanie mapuje nazwy kolumn logicznych i fizycznych. Aby uzyskać więcej informacji na temat mapowania opartego na regułach, zobacz [wzorce kolumn w mapowaniu przepływu danych](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Niestandardowe porządkowanie obiektów sink

Domyślnie dane są zapisywane w wielu ujściach w kolejności niedeterministycznej. Aparat wykonywania zapisuje dane równolegle, ponieważ logika transformacji została zakończona, a kolejność zbiornika może się różnić w zależności od przebiegu. Aby określić dokładną kolejność obiektów, Włącz **niestandardowe porządkowanie** obiektów na karcie **Ogólne** przepływu danych. Po włączeniu ujścia są zapisywane sekwencyjnie w kolejności rosnącej.

![Zrzut ekranu przedstawiający niestandardowe porządkowanie obiektów sink.](media/data-flow/custom-sink-ordering.png "Zrzut ekranu przedstawiający niestandardowe porządkowanie obiektów sink.")

> [!NOTE]
> Przy korzystaniu z [buforowanych wyszukiwań](./concepts-data-flow-expression-builder.md#cached-lookup)upewnij się, że kolejność obiektów ujścia w pamięci podręcznej jest ustawiona na 1, najniższy (lub pierwszy) w kolejności.

![Niestandardowe porządkowanie obiektów sink](media/data-flow/cache-2.png "Niestandardowe porządkowanie obiektów sink")

### <a name="sink-groups"></a>Grupy ujścia

Można grupować ujścia ze sobą, stosując ten sam numer zamówienia dla serii ujścia. ADF będzie traktować te ujścia jako grupy, które mogą być wykonywane równolegle. Opcje wykonywania równoległego będą należeć do działania przepływu danych potoku.

## <a name="error-row-handling"></a>Obsługa wierszy błędów

Podczas zapisywania do baz danych niektóre wiersze danych mogą kończyć się niepowodzeniem z powodu ograniczeń ustawionych przez miejsce docelowe. Domyślnie uruchomienie przepływu danych zakończy się niepowodzeniem przy pierwszym błędzie, który pobiera. W niektórych łącznikach można wybrać opcję **Kontynuuj przy błędzie** , która pozwala na ukończenie przepływu danych, nawet jeśli pojedyncze wiersze mają Błędy. Obecnie ta funkcja jest dostępna tylko w Azure SQL Database. Aby uzyskać więcej informacji, zobacz [Obsługa wierszy błędów w usłudze Azure SQL DB](connector-azure-sql-database.md#error-row-handling).

Poniżej znajduje się samouczek wideo dotyczący sposobu automatycznego używania wierszy błędów bazy danych w transformację ujścia.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4IWne]

## <a name="data-preview-in-sink"></a>Podgląd danych w usłudze ujścia

Podczas pobierania podglądu danych w klastrze debugowania żadne dane nie zostaną zazapisywane w ujścia. Zostanie zwrócona migawka danych, na które wyglądają dane, ale nic nie zostanie zazapisywane w miejscu docelowym. Aby przetestować zapisywanie danych w ujścia, uruchom debugowanie potoku z kanwy potoku.

## <a name="next-steps"></a>Następne kroki

Po utworzeniu przepływu danych Dodaj [działanie przepływu danych do potoku](concepts-data-flow-overview.md).