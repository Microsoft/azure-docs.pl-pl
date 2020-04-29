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
ms.date: 12/12/2019
ms.openlocfilehash: 4b10a4c98abd6bec4074bf35764a9cbb85d5b157
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605965"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformacja ujścia w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Po przekształceniu danych można je ujścia do docelowego zestawu danych. Każdy przepływ danych wymaga co najmniej jednego przekształcenia ujścia, ale w razie potrzeby można zapisać do tylu zlewów, aby zakończyć przepływ transformacji. Aby zapisywać w dodatkowych ujściach, Utwórz nowe strumienie za pośrednictwem nowych gałęzi i podziałów warunkowych.

Każda transformacja ujścia jest skojarzona z dokładnie jednym Data Factory zestawem danych. Zestaw danych definiuje kształt i lokalizację danych, do których chcesz pisać.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Obsługiwane łączniki ujścia w mapowaniu przepływu danych

Obecnie następujące zestawy danych mogą być używane w transformację ujścia:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, text, parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, text, parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, text, parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Ustawienia specyficzne dla tych łączników znajdują się na karcie **Ustawienia** . informacje dotyczące tych ustawień znajdują się w dokumentacji łącznika. 

Azure Data Factory ma dostęp do ponad [90 łączników natywnych](connector-overview.md). Aby zapisać dane do tych innych źródeł z przepływu danych, Użyj działania kopiowania w celu załadowania danych z jednego z obsługiwanych obszarów przemieszczania po zakończeniu przepływu danych.

## <a name="sink-settings"></a>Ustawienia ujścia

Po dodaniu ujścia skonfiguruj go za pomocą karty **ujścia** . W tym miejscu możesz wybrać lub utworzyć zestaw danych, do którego ma zostać zapisywany obiekt ujścia. Poniżej znajduje się film przedstawiający szereg różnych opcji ujścia dla typów plików rozdzielanych tekstem:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tf7T]

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
