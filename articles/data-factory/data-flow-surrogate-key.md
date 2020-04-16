---
title: Transformacja klucza zastępczego w przepływie danych mapowania
description: Jak używać transformacji klucza zastępczego przepływu danych usługi Azure Data Factory do generowania sekwencyjnych wartości kluczy
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: 6f0310f13ab23b5b3d4fba649d0faf0da4dad5b8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413138"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Transformacja klucza zastępczego w przepływie danych mapowania 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Transformacja klucza zastępczego służy do dodawania wartości klucza przyrostowego do każdego wiersza danych. Jest to przydatne podczas projektowania tabel wymiarów w modelu danych analitycznych schematu gwiazdy. W schemacie gwiazdy każdy element członkowski w tabelach wymiarów wymaga unikatowego klucza, który jest kluczem niebiznesowym.

## <a name="configuration"></a>Konfigurowanie

![Przekształcenie klucza zastępczego](media/data-flow/surrogate.png "Transformacja klucza zastępczego")

**Kolumna klucza:** Nazwa wygenerowanej kolumny klucza zastępczego.

**Wartość początkowa:** Najniższa wartość klucza, która zostanie wygenerowana.

## <a name="increment-keys-from-existing-sources"></a>Klucze przyrostowe z istniejących źródeł

Aby rozpocząć sekwencję od wartości, która istnieje w źródle, użyj transformacji kolumny pochodnej po transformacji klucza zastępczego, aby dodać dwie wartości razem:

![SK dodaj Maks.](media/data-flow/sk006.png "Maksymalna wartość przekształcenia klucza zastępczego")

### <a name="increment-from-existing-maximum-value"></a>Przyrost od istniejącej wartości maksymalnej

Aby wysiewu wartość klucza z poprzednim max, istnieją dwie techniki, których można użyć na podstawie tego, gdzie znajdują się dane źródłowe.

#### <a name="database-sources"></a>Źródła bazy danych

Użyj opcji kwerendy SQL, aby wybrać MAX() ze źródła. Na przykład,`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Kwerenda klucza zastępczego](media/data-flow/sk002.png "Kwerenda transformacji klucza zastępczego")

#### <a name="file-sources"></a>Źródła plików

Jeśli poprzednia wartość maksymalna znajduje `max()` się w pliku, użyj funkcji w transformacji agregującej, aby uzyskać poprzednią wartość maksymalną:

![Plik klucza zastępczego](media/data-flow/sk008.png "Plik klucza zastępczego")

W obu przypadkach należy dołączyć przychodzące nowe dane wraz ze źródłem, który zawiera poprzednią wartość maksymalną.

![Sprzężenie klucza zastępczego](media/data-flow/sk004.png "Sprzężenie klucza zastępczego")

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

![Przekształcenie klucza zastępczego](media/data-flow/surrogate.png "Transformacja klucza zastępczego")

Skrypt przepływu danych dla powyższej konfiguracji klucza zastępczego znajduje się w poniższym fragmentie kodu.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Następne kroki

W tych przykładach użyto przekształceń [sprzężenia](data-flow-join.md) i [kolumny pochodnej.](data-flow-derived-column.md)
