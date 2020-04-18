---
title: Transformacja ujścia w przepływie danych mapowania
description: Dowiedz się, jak skonfigurować transformację ujścia w przepływie danych mapowania.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 4b10a4c98abd6bec4074bf35764a9cbb85d5b157
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605965"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformacja ujścia w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Po przekształcenie danych, można zatopić dane do docelowego zestawu danych. Każdy przepływ danych wymaga co najmniej jednej transformacji ujścia, ale można zapisać do jak największej liczby pochłaniaczy, ile jest to konieczne do ukończenia przepływu transformacji. Aby zapisać do dodatkowych umyków, należy utworzyć nowe strumienie za pośrednictwem nowych gałęzi i podziałów warunkowych.

Każda transformacja ujścia jest skojarzona z dokładnie jednym zestawem danych fabryki danych. Zestaw danych definiuje kształt i lokalizację danych, na których chcesz zapisać.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Obsługiwane złącza ujścia w przepływie danych mapowania

Obecnie następujące zestawy danych mogą być używane w transformacji ujścia:
    
* [Usługa Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Tekst, Parkiet)
* [Usługa Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Tekst, Parkiet)
* [Usługa Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Tekst, Parkiet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Ustawienia specyficzne dla tych łączników znajdują się na karcie **Ustawienia.** 

Usługa Azure Data Factory ma dostęp do ponad [90 natywnych łączników.](connector-overview.md) Aby zapisać dane do tych innych źródeł z przepływu danych, użyj copy activity, aby załadować te dane z jednego z obsługiwanych obszarów przemieszczania po zakończeniu przepływu danych.

## <a name="sink-settings"></a>Ustawienia umywalki

Po dodaniu ujścia, skonfigurować za pośrednictwem zakładki **Ujście.** W tym miejscu można wybrać lub utworzyć zestaw danych, do którego pisze zlew. Poniżej znajduje się film wyjaśniający szereg różnych opcji ujścia dla typów plików rozdzielanych tekstem:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tf7T]

![Ustawienia umywalki](media/data-flow/sink-settings.png "Ustawienia ujścia")

**Dryf schematu: Dryf** [schematu](concepts-data-flow-schema-drift.md) jest możliwość fabryki danych do natywnie obsługiwać elastyczne schematy w przepływach danych bez konieczności jawnego definiowania zmian kolumn. Włącz **zezwalaj na dryft schematu** do zapisu dodatkowych kolumn na górze, co jest zdefiniowane w schemacie danych ujścia.

**Sprawdź poprawność schematu:** Jeśli wybrano schemat sprawdzania poprawności, przepływ danych zakończy się niepowodzeniem, jeśli żadna kolumna przychodzącego schematu źródłowego nie zostanie znaleziona w projekcji źródłowej lub jeśli typy danych nie są zgodne. To ustawienie służy do wymuszania, że dane źródłowe są zgodne z umową zdefiniowanej projekcji. Jest to bardzo przydatne w scenariuszach źródłowych bazy danych, aby zasygnalizować, że nazwy kolumn lub typy uległy zmianie.

## <a name="field-mapping"></a>Mapowanie pola

Podobnie jak w transformacji Wybierz, na karcie **Mapowanie** ujścia można zdecydować, które przychodzące kolumny zostaną zapisane. Domyślnie wszystkie kolumny wejściowe, w tym kolumny dryfowane, są mapowane. Jest to nazywane **automatycznym mapowaniem**.

Po wyłączeniu automatycznego mapowania można dodać stałe mapowania oparte na kolumnach lub mapowania oparte na regułach. Mapowania oparte na regułach umożliwiają pisanie wyrażeń z dopasowywaniem wzorców, podczas gdy mapowanie stałe mapuje nazwy kolumn logicznych i fizycznych. Aby uzyskać więcej informacji na temat mapowania opartego na [regułach, zobacz wzorce kolumn w przepływie danych mapowania](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Niestandardowe zamawianie zlewu

Domyślnie dane są zapisywane w wielu pochłaniacze w kolejności niedeterministycznej. Aparat wykonywania będzie zapisywać dane równolegle, jak logika transformacji jest zakończona i kolejność ujścia może się różnić w każdym uruchomieniu. Aby określić i dokładnie kolejność ujścia, włącz **niestandardowe porządkowanie ujścia** w ogólnej karcie przepływu danych. Po włączeniu, pochłaniacze będą zapisywane sekwencyjnie w kolejności zwiększania.

![Niestandardowe zamawianie zlewu](media/data-flow/custom-sink-ordering.png "Niestandardowe zamawianie zlewu")

## <a name="data-preview-in-sink"></a>Podgląd danych w zlewie

Podczas pobierania podglądu danych w klastrze debugowania żadne dane nie zostaną zapisane w umywie. Migawka, jak wyglądają dane zostaną zwrócone, ale nic nie zostanie zapisane do miejsca docelowego. Aby przetestować zapisywanie danych w zlewie, uruchom debugowanie potoku z kanwy potoku.

## <a name="next-steps"></a>Następne kroki
Po utworzeniu przepływu danych dodaj [do potoku działanie przepływu danych.](concepts-data-flow-overview.md)
