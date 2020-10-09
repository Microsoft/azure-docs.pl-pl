---
title: Transformacja ujścia w przepływie danych mapowania
description: Dowiedz się, jak skonfigurować transformację ujścia w mapowaniu przepływu danych.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/27/2020
ms.openlocfilehash: 06d70012756694dca1fad8fa90db0293bb106bf9
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828145"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformacja ujścia w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Po zakończeniu przekształcania danych Zapisz je w magazynie docelowym przy użyciu transformacji ujścia. Każdy przepływ danych wymaga co najmniej jednego przekształcenia ujścia, ale w razie potrzeby można zapisać do tylu zlewów, aby zakończyć przepływ transformacji. Aby zapisywać w dodatkowych ujściach, Utwórz nowe strumienie za pośrednictwem nowych gałęzi i podziałów warunkowych.

Każda transformacja ujścia jest skojarzona z dokładnie jednym Azure Data Factory obiektem DataSet lub połączoną usługą. Transformacja ujścia określa kształt i lokalizację danych, do których chcesz pisać.

## <a name="inline-datasets"></a>Wbudowane zestawy danych

Podczas tworzenia transformacji ujścia należy określić, czy informacje o ujściach są zdefiniowane wewnątrz obiektu DataSet, czy w ramach transformacji ujścia. Większość formatów jest dostępna tylko w jednym lub drugim. Zapoznaj się z odpowiednim dokumentem łącznika, aby dowiedzieć się, jak używać określonego łącznika.

Jeśli format jest obsługiwany zarówno w przypadku obiektów wbudowanych, jak i w obiekcie DataSet, istnieją zalety obu tych metod. Obiekty DataSet to jednostki wielokrotnego użytku, których można użyć w innych przepływach danych i działaniach, takich jak Kopiuj. Są one szczególnie przydatne w przypadku korzystania ze schematu ze wzmocnionymi zabezpieczeniami. Zestawy danych nie są oparte na platformie Spark i czasami może zaistnieć potrzeba zastąpienia niektórych ustawień lub projekcji schematu w transformację ujścia.

Wbudowane zestawy danych są zalecane w przypadku używania elastycznych schematów, jednokrotnych wystąpień ujścia lub sparametryzowanych zbiorników. Jeśli ujścia jest silnie sparametryzowane, zestawy danych w wierszu umożliwiają nie można utworzyć "fikcyjnego" obiektu. Wbudowane zestawy danych są oparte na platformie Spark, a ich właściwości są natywne dla przepływu danych.

Aby użyć wbudowanego zestawu danych, wybierz odpowiedni format w selektorze **typu ujścia** . Zamiast wybierać zestaw danych ujścia, należy wybrać połączoną usługę, z którą chcesz nawiązać połączenie.

![Wbudowany zestaw danych](media/data-flow/inline-selector.png "Wbudowany zestaw danych")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Obsługiwane typy ujścia

Mapowanie przepływu danych odbywa się zgodnie z podejściem wyodrębniania, ładowania, przekształcania (ELT) i współdziała z *tymczasowymi* zestawami danych, które są wszystkie na platformie Azure. Obecnie następujące zestawy danych mogą być używane w transformacji źródłowej:

| Łącznik | Format | Zestaw danych/wbudowany |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Tekst rozdzielany](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (wersja zapoznawcza)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- |
| [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Tekst rozdzielany](format-delimited-text.md#mapping-data-flow-properties) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Tekst rozdzielany](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (wersja zapoznawcza)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Common Data Model (wersja zapoznawcza)](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure CosmosDB (interfejs API SQL)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Ustawienia specyficzne dla tych łączników znajdują się na karcie **Ustawienia** . Przykłady skryptów informacji i przepływu danych dotyczące tych ustawień znajdują się w dokumentacji łącznika. 

Usługa Azure Data Factory ma dostęp do ponad [90 natywnych łączników](connector-overview.md). Aby zapisać dane do tych innych źródeł z przepływu danych, Użyj działania kopiowania w celu załadowania danych z obsługiwanego ujścia.

## <a name="sink-settings"></a>Ustawienia ujścia

Po dodaniu ujścia skonfiguruj go za pomocą karty **ujścia** . W tym miejscu możesz wybrać lub utworzyć zestaw danych, do którego ma zostać zapisywany obiekt ujścia. Wartości programistyczne dla parametrów zestawu danych można skonfigurować w [ustawieniach debugowania](concepts-data-flow-debug-mode.md) (wymaga włączenia trybu debugowania).

Poniżej znajduje się film przedstawiający szereg różnych opcji ujścia dla typów plików rozdzielanych tekstem:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![Ustawienia ujścia](media/data-flow/sink-settings.png "Ustawienia ujścia")

**Dryfowanie schematu:** [dryfowanie schematu](concepts-data-flow-schema-drift.md) to zdolność fabryki danych do natywnej obsługi elastycznych schematów w przepływach danych bez konieczności jawnego definiowania zmian w kolumnach. Włącz funkcję **Zezwalaj na dryfowanie schematu** w celu zapisania dodatkowych kolumn na górze zdefiniowanej w schemacie danych ujścia.

**Weryfikuj schemat:** Jeśli wybrano opcję Weryfikuj schemat, przepływ danych zakończy się niepowodzeniem, jeśli w projekcji źródłowej nie zostanie znaleziona żadna kolumna przychodzącego schematu źródłowego lub typy danych nie są zgodne. Użyj tego ustawienia, aby wymusić, że dane źródłowe spełniają kontrakt zdefiniowanej projekcji. Jest to bardzo przydatne w scenariuszach ze źródłami danych, aby sygnalizować, że nazwy kolumn lub typy zostały zmienione.

## <a name="field-mapping"></a>Mapowanie pola

Podobnie jak w przypadku transformacji SELECT, na karcie **Mapowanie** obiektu ujścia można zdecydować, które kolumny przychodzące będą zapisywane. Domyślnie wszystkie kolumny wejściowe, w tym kolumny przedzielone, są mapowane. Jest to nazywane **automapowaniem**.

Po wyłączeniu automapowania będziesz mieć możliwość dodawania stałych mapowań opartych na kolumnach lub mapowań opartych na regułach. Mapowania oparte na regułach umożliwiają pisanie wyrażeń z dopasowaniem do wzorca, podczas gdy stałe mapowanie spowoduje zamapowanie nazw kolumn logicznych i fizycznych. Aby uzyskać więcej informacji na temat mapowania opartego na regułach, zobacz [wzorce kolumn w mapowaniu przepływu danych](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Niestandardowe porządkowanie obiektów sink

Domyślnie dane są zapisywane w wielu ujściach w kolejności niedeterministycznej. Aparat wykonawczy będzie zapisywać dane równolegle, ponieważ logika transformacji zostanie zakończona, a kolejność zbiornika może się różnić w zależności od każdego uruchomienia. Aby określić i dokładnie ustalić kolejność zbiorników, Włącz **niestandardowe porządkowanie obiektów sink** na karcie Ogólne przepływu danych. Po włączeniu ujścia są zapisywane sekwencyjnie w kolejności rosnącej.

![Niestandardowe porządkowanie obiektów sink](media/data-flow/custom-sink-ordering.png "Niestandardowe porządkowanie obiektów sink")

## <a name="data-preview-in-sink"></a>Podgląd danych w usłudze ujścia

Podczas pobierania podglądu danych w klastrze debugowania żadne dane nie zostaną zazapisywane w ujścia. Zostanie zwrócona migawka danych, na które wyglądają dane, ale nic nie zostanie zazapisywane w miejscu docelowym. Aby przetestować zapisywanie danych w ujścia, uruchom debugowanie potoku z kanwy potoku.

## <a name="next-steps"></a>Następne kroki
Po utworzeniu przepływu danych Dodaj [działanie przepływu danych do potoku](concepts-data-flow-overview.md).
