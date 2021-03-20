---
title: Sortowanie transformacji w strumieniu danych mapowania
description: Przekształcenie sortowania Azure Data Factory danych mapowania
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81606334"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Sortowanie transformacji w strumieniu danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformacja sortowania pozwala sortować wiersze przychodzące w bieżącym strumieniu danych. Można wybrać poszczególne kolumny i posortować je w kolejności rosnącej lub malejącej.

> [!NOTE]
> Mapowanie przepływów danych jest wykonywane w klastrach Spark, które dystrybuują dane między wieloma węzłami i partycjami. Jeśli zdecydujesz się na ponowne Partycjonowanie danych w kolejnej transformacji, możesz utracić sortowanie ze względu na reshuffling danych.

## <a name="configuration"></a>Konfigurowanie

![Ustawienia sortowania](media/data-flow/sort.png "Sortowanie")

Bez **uwzględniania wielkości liter:** Określa, czy należy zignorować wielkość liter podczas sortowania ciągów lub pól tekstowych

**Sortuj tylko w obrębie partycji:** Ponieważ przepływy danych są uruchamiane na platformie Spark, każdy strumień danych jest podzielony na partycje. To ustawienie sortuje dane tylko w ramach partycji przychodzących, a nie sortuje cały strumień danych. 

**Warunki sortowania:** Wybierz kolumny, według których ma być wykonywane sortowanie, i kolejność sortowania. Kolejność określa priorytet sortowania. Określ, czy wartości null będą wyświetlane na początku, czy na końcu strumienia danych.

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

Po sortowaniu możesz chcieć użyć [przekształcenia agregacji](data-flow-aggregate.md)
