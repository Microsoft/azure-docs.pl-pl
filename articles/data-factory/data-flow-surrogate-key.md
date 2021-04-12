---
title: Przekształcanie klucza zastępczego w mapowaniu przepływu danych
description: Jak używać transformacji klucza zastępczego przepływu danych Azure Data Factory mapowania, aby generować sekwencyjne wartości klucza
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/30/2020
ms.openlocfilehash: d1f8993b1adc297b1bfadba114df76a66e59afa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93147188"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Przekształcanie klucza zastępczego w mapowaniu przepływu danych 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj przekształcenia klucza dwuskładnikowego, aby dodać przyrostową wartość klucza do każdego wiersza danych. Jest to przydatne podczas projektowania tabel wymiarów w modelu danych analitycznych schematu gwiazdy. W schemacie gwiazdy każdy element członkowski w tabelach wymiarów wymaga unikatowego klucza, który jest kluczem niebiznesowym.

## <a name="configuration"></a>Konfigurowanie

![Przekształcanie klucza zastępczego](media/data-flow/surrogate.png "Przekształcanie klucza zastępczego")

**Kolumna klucza:** Nazwa wygenerowanej kolumny klucza zastępczego.

**Wartość początkowa:** Najniższa wartość klucza, która zostanie wygenerowana.

## <a name="increment-keys-from-existing-sources"></a>Przyrostowe klawisze z istniejących źródeł

Aby rozpocząć sekwencję z wartości, która istnieje w źródle, zalecamy użycie ujścia pamięci podręcznej w celu zapisania tej wartości i użycie przekształceń kolumn pochodnych do dodawania dwóch wartości jednocześnie. Użyj buforowanego wyszukiwania, aby uzyskać dane wyjściowe i dołączyć je do wygenerowanego klucza. Aby uzyskać więcej informacji, zobacz [ujścia pamięci podręcznej](data-flow-sink.md#cache-sink) i [buforowane wyszukiwania](concepts-data-flow-expression-builder.md#cached-lookup).

![Wyszukiwanie klucza zastępczego](media/data-flow/cached-lookup-example.png "Wyszukiwanie klucza zastępczego")

### <a name="increment-from-existing-maximum-value"></a>Zwiększ z istniejącej wartości maksymalnej

Aby obsłużyć wartość klucza z poprzednią maksymalną, istnieją dwie techniki, których można użyć w zależności od tego, gdzie znajdują się dane źródłowe.

#### <a name="database-sources"></a>Źródła bazy danych

Użyj opcji zapytania SQL, aby wybrać wartość MAX () ze źródła. Na przykład `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`.

![Zapytanie klucza zastępczego](media/data-flow/surrogate-key-max-database.png "Zapytanie przekształcenia klucza zastępczego")

#### <a name="file-sources"></a>Źródła plików

Jeśli poprzednia maksymalna wartość znajduje się w pliku, użyj `max()` funkcji w przekształceniu agregacji, aby uzyskać poprzednią wartość maksymalną:

![Plik klucza zastępczego](media/data-flow/surrogate-key-max-file.png "Plik klucza zastępczego")

W obu przypadkach należy zapisać w ujścia pamięci podręcznej i przeszukać wartość. 


## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Przykład

![Przekształcanie klucza zastępczego](media/data-flow/surrogate.png "Przekształcanie klucza zastępczego")

Skrypt przepływu danych dla powyższej konfiguracji klucza zastępczego znajduje się w poniższym fragmencie kodu.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Następne kroki

W poniższych przykładach użyto [sprzężeń](data-flow-join.md) i przekształceń [kolumn pochodnych](data-flow-derived-column.md) .
