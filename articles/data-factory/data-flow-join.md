---
title: Przekształcenie łączenia w przepływie danych mapowania
description: Łączenie danych z dwóch źródeł danych przy użyciu transformacji sprzężenia w przepływie danych mapowania Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: ac84ce17f53145ffd85ffa31b6633d8b4b184962
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042645"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Przekształcenie łączenia w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj transformacji sprzężenia, aby połączyć dane z dwóch źródeł lub strumieni w przepływie danych mapowania. Strumień wyjściowy będzie uwzględniał wszystkie kolumny z obu źródeł dopasowane na podstawie warunku sprzężenia. 

## <a name="join-types"></a>Typy sprzężeń

Mapowanie przepływów danych obsługuje obecnie pięć różnych typów sprzężeń.

### <a name="inner-join"></a>Sprzężenie wewnętrzne

Sprzężenie wewnętrzne wyświetla tylko wiersze, które mają pasujące wartości w obu tabelach.

### <a name="left-outer"></a>Lewe zewnętrzne

Lewe sprzężenie zewnętrzne zwraca wszystkie wiersze z lewego strumienia i dopasowane rekordy z odpowiedniego strumienia. Jeśli wiersz z lewego strumienia nie ma dopasowania, kolumny danych wyjściowych z odpowiedniego strumienia są ustawione na wartość NULL. Dane wyjściowe będą wierszami zwracanymi przez sprzężenie wewnętrzne oraz niedopasowane wiersze z lewego strumienia.

> [!NOTE]
> Aparat platformy Spark używany przez przepływy danych sporadycznie nie powiedzie się, ponieważ możliwe kartezjańskiego produkty w warunkach sprzężenia. W takim przypadku można przełączyć się do niestandardowego sprzężenia krzyżowego i ręcznie wprowadzić warunek sprzężenia. Może to spowodować wolniejszą wydajność przepływów danych, ponieważ aparat wykonywania może potrzebować obliczyć wszystkie wiersze z obu stron relacji, a następnie filtrować wiersze.

### <a name="right-outer"></a>Prawe zewnętrzne

Prawe sprzężenie zewnętrzne zwraca wszystkie wiersze z odpowiedniego strumienia i dopasowane rekordy z lewego strumienia. Jeśli wiersz z odpowiedniego strumienia nie ma dopasowania, kolumny wyjściowe z lewego strumienia są ustawione na wartość NULL. Dane wyjściowe będą wierszami zwracanymi przez sprzężenie wewnętrzne oraz niedopasowane wiersze z odpowiedniego strumienia.

### <a name="full-outer"></a>Pełny zewnętrzny

Pełne sprzężenie zewnętrzne wyprowadza wszystkie kolumny i wiersze z obu stron z wartościami NULL dla kolumn, które nie pasują.

### <a name="custom-cross-join"></a>Niestandardowe sprzężenie krzyżowe

Sprzężenie krzyżowe wyprowadza iloczyn między dwoma strumieniami w oparciu o warunek. Jeśli używasz warunku, który nie jest równy, Określ wyrażenie niestandardowe jako warunek sprzężenia krzyżowego. Strumień wyjściowy będzie zawierać wszystkie wiersze, które spełniają warunek sprzężenia.

Tego typu sprzężenia można użyć dla sprzężeń i warunków innych niż Equi ```OR``` .

Jeśli chcesz jawnie utworzyć pełny produkt kartezjańskiego, użyj przekształcenia kolumn pochodnych w każdym z dwóch niezależnych strumieni przed przyłączeniem, aby utworzyć klucz syntetyczny do dopasowania. Na przykład utwórz nową kolumnę w kolumnie pochodnej w każdym wywołanym strumieniu ```SyntheticKey``` i ustaw ją jako równą ```1``` . Następnie użyj ```a.SyntheticKey == b.SyntheticKey``` jako wyrażenia sprzężenia niestandardowego.

> [!NOTE]
> Pamiętaj o uwzględnieniu co najmniej jednej kolumny z każdej strony lewej i prawej relacji w przypadku niestandardowego sprzężenia krzyżowego. Wykonywanie sprzężenia krzyżowego z wartościami statycznymi zamiast kolumn z każdego z nich skutkuje pełnymi skanami całego zestawu danych, co sprawia, że przepływ danych jest niewłaściwie wykonywany.

## <a name="configuration"></a>Konfiguracja

1. Wybierz strumień danych, z którym chcesz się połączyć, na liście rozwijanej **odpowiedniego strumienia** .
1. Wybierz **Typ sprzężenia**
1. Wybierz kolumny klucza, dla których chcesz dopasować warunek sprzężenia. Domyślnie przepływ danych wyszukuje równość między jedną kolumną w każdym strumieniu. Aby porównać przez obliczoną wartość, umieść kursor na liście rozwijanej kolumny i wybierz **kolumnę obliczaną** .

![Przekształcanie sprzężenia](media/data-flow/join.png "Join")

### <a name="non-equi-joins"></a>Sprzężenia inne niż Equi

Aby użyć warunkowego operatora, takiego jak not Equals (! =) lub większe niż (>) w warunkach sprzężenia, Zmień listę rozwijaną operatora między dwiema kolumnami. Sprzężenia inne niż Equi wymagają, aby co najmniej jeden z dwóch strumieni był emitowany przy użyciu **stałej** emisji na karcie **Optymalizacja** .

![Sprzężenie inne niż Equi](media/data-flow/non-equi-join.png "Sprzężenie inne niż Equi")

## <a name="optimizing-join-performance"></a>Optymalizowanie wydajności dołączania

W przeciwieństwie do łączenia scalania w narzędziach takich jak SSIS, transformacja sprzężenia nie jest obowiązkową operacją scalania sprzężenia. Klucze sprzężenia nie wymagają sortowania. Operacja join odbywa się na podstawie optymalnej operacji JOIN w platformie Spark, emisji lub sprzężenia po stronie mapy.

![Optymalizacja transformacji sprzężeń](media/data-flow/joinoptimize.png "Optymalizacja dołączania")

W sprzężeniach, wyszukiwaniu i przekształceniu istnieje, jeśli jeden lub oba strumienie danych mieszczą się w pamięci węzła procesu roboczego, można zoptymalizować wydajność, włączając **emisję** . Domyślnie aparat Spark automatycznie zdecyduje, czy ma być emitowana jedna strona. Aby ręcznie wybrać opcję emisji, wybierz pozycję **naprawione** .

Nie zaleca się wyłączania emisji za pośrednictwem opcji **off** , chyba że sprzężenia są uruchamiane w przypadku błędów limitu czasu.

## <a name="self-join"></a>Self-Join

Aby dołączyć do siebie strumień danych z samym sobą, aliasuje istniejący strumień z przekształceniem SELECT. Utwórz nową gałąź, klikając ikonę znaku plus obok przekształcenia i wybierając pozycję **nowe rozgałęzienie** . Dodaj wybraną transformację do aliasowania oryginalnego strumienia. Dodaj transformację sprzężenia i wybierz oryginalny strumień jako **lewy strumień** i wybierz transformację jako **właściwy strumień** .

![Samosprzężenie](media/data-flow/selfjoin.png "Samosprzężenie")

## <a name="testing-join-conditions"></a>Testowanie warunków sprzężenia

Podczas testowania transformacji sprzężenia z podglądem danych w trybie debugowania należy użyć małego zestawu znanych danych. Podczas próbkowania wierszy z dużego zestawu danych nie można przewidzieć, które wiersze i klucze mają być odczytywane w celu przetestowania. Wynik jest niejednoznaczny, co oznacza, że warunki sprzężenia mogą nie zwracać żadnych dopasowań.

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Przykład sprzężenia wewnętrznego

Poniższy przykład to transformacja sprzężenia o nazwie `JoinMatchedData` , która pobiera strumień strumienia `TripData` i właściwy strumień `TripFare` .  Warunek sprzężenia jest wyrażeniem `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` zwracającym wartość true `hack_license` , jeśli `medallion` kolumny,, `vendor_id` i `pickup_datetime` w każdym dopasowaniu strumienia. `joinType`Ma wartość `'inner'` . Włączamy emisję tylko w lewym strumieniu, więc `broadcast` ma wartość `'left'` .

W Data Factory środowisku użytkownika Ta transformacja wygląda jak na poniższym obrazie:

![Zrzut ekranu przedstawia transformację z wybraną kartą Ustawienia sprzężenia i typ sprzężenia wewnętrznego.](media/data-flow/join-script1.png "Przykład sprzężenia")

Skrypt przepływu danych dla tego przekształcenia znajduje się w poniższym fragmencie kodu:

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

Poniższy przykład to transformacja sprzężenia o nazwie `JoiningColumns` , która pobiera strumień strumienia `LeftStream` i właściwy strumień `RightStream` . Ta transformacja wykonuje dwa strumienie i łączy ze sobą wszystkie wiersze, w których kolumna `leftstreamcolumn` jest większa niż kolumna `rightstreamcolumn` . `joinType`Ma wartość `cross` . Emisja nie jest włączona, `broadcast` ma wartość `'none'` .

W Data Factory środowisku użytkownika Ta transformacja wygląda jak na poniższym obrazie:

![Zrzut ekranu przedstawia transformację z wybraną kartą Ustawienia sprzężenia i typ sprzężenia niestandardowego (krzyżowego).](media/data-flow/join-script2.png "Przykład sprzężenia")

Skrypt przepływu danych dla tego przekształcenia znajduje się w poniższym fragmencie kodu:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Następne kroki

Po dołączeniu danych Utwórz [kolumnę pochodną](data-flow-derived-column.md) i [ujścia](data-flow-sink.md) danych do docelowego magazynu danych.
