---
title: Sortowanie transformacji w przepływie danych mapowania
description: Transformacja sortowania danych mapowania danych usługi Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606334"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Sortowanie transformacji w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformacja sortowania umożliwia sortowanie przychodzących wierszy w bieżącym strumieniu danych. Można wybrać poszczególne kolumny i sortować je w porządku rosnącym lub malejącym.

> [!NOTE]
> Mapowanie przepływów danych są wykonywane na klastrach iskrowych, które rozdzielają dane w wielu węzłach i partycjach. Jeśli zdecydujesz się na partycjonowanie danych w kolejnej transformacji, możesz utracić sortowanie z powodu przetasowania danych.

## <a name="configuration"></a>Konfigurowanie

![Ustawienia sortowania](media/data-flow/sort.png "Sortowanie")

**Bez uwzględniania wielkości liter:** Określa, czy podczas sortowania pól tekstowych lub tekstowych chcesz zignorować przypadek

**Sortuj tylko w obrębie partycji:** Ponieważ przepływy danych są uruchamiane na iskrę, każdy strumień danych jest podzielony na partycje. To ustawienie sortuje dane tylko w obrębie partycji przychodzących, a nie sortuje cały strumień danych. 

**Warunki sortowania:** Wybierz kolumny, według których sortujesz i w jakiej kolejności odbywa się sortowanie. Kolejność określa priorytet sortowania. Wybierz, czy wartości null będą wyświetlane na początku czy na końcu strumienia danych.

### <a name="computed-columns"></a>Kolumny obliczane

Aby zmodyfikować lub wyodrębnić wartość kolumny przed zastosowaniem sortowania, umieść wskaźnik myszy na kolumnie i wybierz "kolumnę obliczoną". Spowoduje to otwarcie konstruktora wyrażeń, aby utworzyć wyrażenie dla operacji sortowania zamiast wartości kolumny.

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

Skrypt przepływu danych dla powyższej konfiguracji sortowania znajduje się we wzorze kodu poniżej.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Następne kroki

Po sortowaniu można użyć [agregacji transformacji](data-flow-aggregate.md)
