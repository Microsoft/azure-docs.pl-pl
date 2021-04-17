---
title: Sortowanie przekształcenia w przepływie danych mapowania
description: Azure Data Factory przekształcania sortowania danych mapowania
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 4a6567f8576e2507704956233bc593b203b48239
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588738"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Sortowanie przekształcenia w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Przekształcenie sortowania umożliwia sortowanie wierszy przychodzących w bieżącym strumieniu danych. Możesz wybrać poszczególne kolumny i posortować je w kolejności rosnącej lub malejącej.

> [!NOTE]
> Przepływy danych mapowania są wykonywane w klastrach Spark, które dystrybuują dane między wieloma węzłami i partycjami. Jeśli zdecydujesz się na ponowne partycjonowanie danych w kolejnym przekształceniu, możesz utracić sortowanie z powodu ponownego dufowania danych. Najlepszym sposobem utrzymania kolejności sortowania w przepływie danych jest ustawienie pojedynczej partycji na karcie Optymalizacja przekształcenia i zachowanie przekształcenia sortowania jak najbardziej zbliżonego do ujścia.

## <a name="configuration"></a>Konfigurowanie

![Ustawienia sortowania](media/data-flow/sort.png "Sortowanie")

**Bez uwzględniania liter:** Określa, czy chcesz zignorować przypadek podczas sortowania pól ciągów lub tekstu

**Sortuj tylko w ramach partycji:** Ponieważ przepływy danych są uruchamiane na spark, każdy strumień danych jest dzielony na partycje. To ustawienie sortuje dane tylko w partycjach przychodzących, zamiast sortować cały strumień danych. 

**Warunki sortowania:** Wybierz kolumny, według których chcesz sortować i w jakiej kolejności odbywa się sortowanie. Kolejność określa priorytet sortowania. Określ, czy wartości null będą wyświetlane na początku lub na końcu strumienia danych.

### <a name="computed-columns"></a>Kolumny obliczane

Aby zmodyfikować lub wyodrębnić wartość kolumny przed zastosowaniem sortowania, umieść kursor nad kolumną i wybierz pozycję "kolumna obliczana". Spowoduje to otwarcie konstruktora wyrażeń w celu utworzenia wyrażenia dla operacji sortowania zamiast używania wartości kolumny.

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Przykład

![Ustawienia sortowania](media/data-flow/sort.png "Sortowanie")

Skrypt przepływu danych dla powyższej konfiguracji sortowania znajduje się w poniższym fragmencie kodu.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Następne kroki

Po posortowaniu możesz użyć przekształcenia [agregacji](data-flow-aggregate.md)
