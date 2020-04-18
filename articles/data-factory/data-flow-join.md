---
title: Dołącz transformację w przepływie danych mapowania
description: Łączenie danych z dwóch źródeł danych przy użyciu transformacji sprzężenia w przepływie danych mapowania usługi Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 5c388dd2b3e4f40fbf2ed75cf3f1b8ab31aee394
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606419"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Dołącz transformację w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformacja sprzężenia służy do łączenia danych z dwóch źródeł lub strumieni w przepływie danych mapowania. Strumień wyjściowy będzie zawierać wszystkie kolumny z obu źródeł dopasowane na podstawie warunku sprzężenia. 

## <a name="join-types"></a>Typy sprzężenia

Mapowanie przepływów danych obsługuje obecnie pięć różnych typów sprzężeń.

### <a name="inner-join"></a>Sprzężenie wewnętrzne

Sprzężenie wewnętrzne tylko wyprowadza wiersze, które mają pasujące wartości w obu tabelach.

### <a name="left-outer"></a>Lewa zewnętrzna

Lewe sprzężenie zewnętrzne zwraca wszystkie wiersze z lewego strumienia i dopasowane rekordy z prawego strumienia. Jeśli wiersz z lewego strumienia nie ma dopasowania, kolumny wyjściowe z prawego strumienia są ustawione na NULL. Dane wyjściowe będą wiersze zwrócone przez sprzężenie wewnętrzne plus niedopasowane wiersze z lewego strumienia.

> [!NOTE]
> Aparat Platformy Spark używany przez przepływy danych czasami ulegnie awarii ze względu na możliwe produkty kartezjańskie w warunkach sprzężenia. W takim przypadku można przełączyć się na niestandardowe sprzężenie krzyżowe i ręcznie wprowadzić warunek sprzężenia. Może to spowodować mniejszą wydajność przepływów danych, ponieważ aparat wykonywania może być konieczne obliczenie wszystkich wierszy z obu stron relacji, a następnie filtrowanie wierszy.

### <a name="right-outer"></a>Prawy zewnętrzny

Prawe sprzężenie zewnętrzne zwraca wszystkie wiersze z prawego strumienia i dopasowane rekordy z lewego strumienia. Jeśli wiersz z prawego strumienia nie ma dopasowania, kolumny wyjściowe z lewego strumienia są ustawione na NULL. Dane wyjściowe będą wiersze zwrócone przez sprzężenie wewnętrzne plus niedopasowane wiersze z prawego strumienia.

### <a name="full-outer"></a>Pełna zewnętrzna

Pełne sprzężenie zewnętrzne wyprowadza wszystkie kolumny i wiersze z obu stron z wartościami NULL dla kolumn, które nie są dopasowane.

### <a name="custom-cross-join"></a>Niestandardowe sprzężenie krzyżowe

Sprzężenie krzyżowe wyprowadza produkt krzyżowy dwóch strumieni na podstawie warunku. Jeśli używasz warunku, który nie jest równość, należy określić wyrażenie niestandardowe jako warunek sprzężenia krzyżowego. Strumień wyjściowy będzie wszystkie wiersze, które spełniają warunek sprzężenia.

Tego typu sprzężenia można użyć ```OR``` dla sprzężeń innych niż równość i warunków.

Jeśli chcesz jawnie utworzyć pełny produkt kartezjański, użyj transformacji kolumny pochodnej w każdym z dwóch niezależnych strumieni przed sprzężeniem, aby utworzyć klucz syntetyczny do dopasowania. Na przykład utwórz nową kolumnę w kolumnie pochodnej w każdym wywoływanym ```SyntheticKey``` strumieniu i ustaw ją równą ```1```. Następnie ```a.SyntheticKey == b.SyntheticKey``` użyj jako wyrażenia sprzężenia niestandardowego.

> [!NOTE]
> Pamiętaj, aby uwzględnić co najmniej jedną kolumnę z każdej strony lewej i prawej relacji w sprzęcie krzyżowym niestandardowym. Wykonywanie sprzężeń krzyżowych z wartościami statycznymi zamiast kolumn z każdej strony powoduje pełne skanowanie całego zestawu danych, powodując słabe wykonanie przepływu danych.

## <a name="configuration"></a>Konfigurowanie

1. Wybierz strumień danych, do którego łączysz się w menu rozwijanym **Prawy strumień.**
1. Wybierz **typ sprzężenia**
1. Wybierz, które kolumny kluczy chcesz dopasować, aby warunek sprzężenia. Domyślnie przepływ danych wyszukuje równość między jedną kolumną w każdym strumieniu. Aby porównać za pomocą obliczonej wartości, umieść wskaźnik myszy na rozwijanej kolumnie i wybierz **kolumnę Obliczona**.

![Dołącz do transformacji](media/data-flow/join.png "Join")

## <a name="optimizing-join-performance"></a>Optymalizacja wydajności sprzężenia

W przeciwieństwie do scalania sprzężenia w narzędziach, takich jak SSIS, transformacja sprzężenia nie jest operacją dołączania do korespondencji seryjnej. Klucze sprzężenia nie wymagają sortowania. Operacja sprzężenia odbywa się na podstawie operacji sprzężenia optymalnego w programie Spark, sprzężenia nadawanego lub po stronie mapy.

![Optymalizacja transformacji sprzężenia](media/data-flow/joinoptimize.png "Optymalizacja dołączania")

Jeśli jeden lub oba strumienie danych mieszczą się w pamięci węzła procesu roboczego, należy dodatkowo zoptymalizować wydajność, włączając **emisję** na karcie optymalizacja. Można również podzielić dane na operację sprzężenia, tak aby lepiej pasowały do pamięci na pracownika.

## <a name="self-join"></a>Samodzielne sprzężenie

Aby samodzielnie połączyć strumień danych z samym sobą, alias istniejącego strumienia z transformacją wybierz. Utwórz nową gałąź, klikając ikonę plus obok transformacji i wybierając **pozycję Nowa gałąź**. Dodaj transformację wyboru do aliasu oryginalnego strumienia. Dodaj transformację sprzężenia i wybierz oryginalny strumień jako **strumień lewy** i transformację wyboru jako **strumień prawy**.

![Samodzielne łączenie](media/data-flow/selfjoin.png "Samodzielne łączenie")

## <a name="testing-join-conditions"></a>Warunki przystąpienia do testowania

Podczas testowania przekształceń sprzężenia z podglądem danych w trybie debugowania należy użyć niewielkiego zestawu znanych danych. Podczas próbkowania wierszy z dużego zestawu danych nie można przewidzieć, które wiersze i klucze będą odczytywane do testowania. Wynik nie jest deterministyczny, co oznacza, że warunki sprzężenia mogą nie zwracać żadnych dopasowań.

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Przykład sprzężenia wewnętrznego

Poniższy przykład to transformacja sprzężenia o nazwie, `JoinMatchedData` która pobiera strumień `TripData` lewy i prawy `TripFare`strumień.  Warunek sprzężenia `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` jest wyrażeniem, `vendor_id`które `pickup_datetime` zwraca wartość `hack_license`true, `medallion`jeśli , , i kolumny w każdym dopasowaniu strumienia. Jest `joinType` `'inner'`. Włączamy nadawanie tylko w lewym `broadcast` strumieniu, więc ma wartość `'left'`.

W ux fabryki danych ta transformacja wygląda jak poniższy obraz:

![Dołącz przykład](media/data-flow/join-script1.png "Dołącz przykład")

Skrypt przepływu danych dla tej transformacji znajduje się we urywce poniżej:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>Przykład niestandardowego sprzężenia krzyżowego

Poniższy przykład to transformacja sprzężenia o nazwie, `JoiningColumns` która pobiera strumień `LeftStream` lewy i prawy `RightStream`strumień. Transformacja ta odbywa się w dwóch strumieniach `leftstreamcolumn` i łączy `rightstreamcolumn`ze sobą wszystkie wiersze, w których kolumna jest większa niż kolumna . Jest `joinType` `cross`. Nadawanie nie `broadcast` jest `'none'`włączone ma wartość .

W ux fabryki danych ta transformacja wygląda jak poniższy obraz:

![Dołącz przykład](media/data-flow/join-script2.png "Dołącz przykład")

Skrypt przepływu danych dla tej transformacji znajduje się we urywce poniżej:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Następne kroki

Po połączeniu danych utwórz [kolumnę pochodną](data-flow-derived-column.md) i [przejmij](data-flow-sink.md) dane do docelowego magazynu danych.
