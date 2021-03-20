---
title: Wybierz transformację w mapowaniu przepływu danych
description: Mapowanie Azure Data Factory Wybieranie przepływu danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/02/2020
ms.openlocfilehash: 2d8c4d1915e22ccabf193f1b34c5fc4797ead549
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93040238"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Wybierz transformację w mapowaniu przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj opcji przekształcenie, aby zmienić nazwę kolumny i usunąć ją. Ta transformacja nie zmienia danych wiersza, ale wybiera kolumny, które są propagowane. 

W ramach transformacji SELECT użytkownicy mogą określić stałe mapowania, używać wzorców do mapowania opartego na regułach lub włączać automapowanie. W ramach tej samej transformacji SELECT można używać zarówno stałych, jak i mapowań opartych na regułach. Jeśli kolumna nie jest zgodna z jednym ze zdefiniowanych mapowań, zostanie usunięta.

## <a name="fixed-mapping"></a>Stałe mapowanie

Jeśli w projekcji zdefiniowano mniej niż 50 kolumn, wszystkie zdefiniowane kolumny domyślnie będą miały stałe mapowanie. Stałe mapowanie przyjmuje zdefiniowaną kolumnę przychodzącą i mapuje ją dokładnie na nazwę.

![Stałe mapowanie](media/data-flow/fixedmapping.png "Stałe mapowanie")

> [!NOTE]
> Nie można mapować lub zmienić nazwy kolumny przeznaczoną do nadzielenia przy użyciu stałego mapowania

### <a name="mapping-hierarchical-columns"></a>Mapowanie kolumn hierarchicznych

Stałe mapowania mogą służyć do mapowania podkolumny kolumny hierarchicznej na kolumnę najwyższego poziomu. Jeśli masz zdefiniowaną hierarchię, Użyj listy rozwijanej kolumna, aby wybrać podkolumnę. Wybór transformacji spowoduje utworzenie nowej kolumny zawierającej wartość i typ danych podkolumny.

![Mapowanie hierarchiczne](media/data-flow/select-hierarchy.png "Mapowanie hierarchiczne")

## <a name="rule-based-mapping"></a>Mapowanie oparte na regułach


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xiXz]

Jeśli chcesz zmapować wiele kolumn jednocześnie lub przekazywać kolumny z przełożeniami, użyj mapowania opartego na regułach, aby zdefiniować mapowania przy użyciu wzorców kolumn. Dopasowanie na podstawie `name` kolumn, `type` , `stream` i `position` . Można mieć dowolną kombinację stałych i mapowań opartych na regułach. Domyślnie wszystkie projekcje zawierające więcej niż 50 kolumn będą domyślnie mapowane na podstawie reguły, które pasują do każdej kolumny i wyprowadzają wydaną nazwę. 

Aby dodać mapowanie oparte na regułach, kliknij przycisk **Dodaj mapowanie** i wybierz **Mapowanie oparte na regułach**.

![Zrzut ekranu przedstawia mapowanie oparte na regułach wybrane na podstawie dodawania mapowania.](media/data-flow/rule2.png "Mapowanie oparte na regułach")

Każde mapowanie oparte na regułach wymaga dwóch danych wejściowych: warunek, dla którego należy dopasować i co należy nazwać każdej mapowanej kolumny. Obie wartości są zwracane za pośrednictwem [konstruktora wyrażeń](concepts-data-flow-expression-builder.md). W polu wyrażenia po lewej stronie wprowadź warunek dopasowania wartości logicznej. W polu wyrażenie po prawej stronie Określ, do czego zostanie zamapowana pasująca kolumna.

![Zrzut ekranu przedstawia mapowanie.](media/data-flow/rule-based-mapping.png "Mapowanie oparte na regułach")

Użyj `$$` składni, aby odwołać się do nazwy wejściowej dopasowanej kolumny. Używając powyższego obrazu na przykład, Załóżmy, że użytkownik chce dopasować wszystkie kolumny ciągów, których nazwy są krótsze niż sześć znaków. Jeśli jedna kolumna przychodząca `test` ma nazwę, wyrażenie zmieni `$$ + '_short'` nazwę kolumny `test_short` . Jeśli jest to jedyne mapowanie, które istnieje, wszystkie kolumny, które nie spełniają warunku, zostaną usunięte z danych, które zostały wydane.

Wzorce pasują do kolumn przeznaczonych i zdefiniowanych. Aby zobaczyć, które zdefiniowane kolumny są mapowane przez regułę, kliknij ikonę okularów obok reguły. Sprawdź dane wyjściowe przy użyciu podglądu danych.

### <a name="regex-mapping"></a>Mapowanie wyrażenia regularnego

Jeśli klikniesz ikonę z dolnym cudzysłowem, możesz określić warunek mapowania wyrażenia regularnego. Warunek mapowania wyrażenia regularnego pasuje do wszystkich nazw kolumn, które pasują do określonego warunku wyrażenia regularnego. Można go używać w połączeniu z standardowymi mapowaniami opartymi na regułach.

![Zrzut ekranu przedstawia warunek mapowania wyrażenia regularnego z dopasowaniem poziomu hierarchii i nazwy.](media/data-flow/regex-matching.png "Mapowanie oparte na regułach")

Powyższy przykład pasuje do wzorca wyrażenia regularnego `(r)` lub nazwy kolumny zawierającej małe litery r. Podobnie jak w przypadku standardowego mapowania opartego na regułach, wszystkie dopasowane kolumny są modyfikowane przez warunek po prawej stronie `$$` składni.

Jeśli w nazwie kolumny występuje wiele odpowiedników wyrażenia regularnego, można odwołać się do określonych dopasowań przy użyciu, `$n` gdzie "n" odwołuje się do tego, co jest zgodne. Na przykład "$2" odwołuje się do drugiego dopasowania w obrębie nazwy kolumny.

### <a name="rule-based-hierarchies"></a>Hierarchie oparte na regułach

Jeśli zdefiniowana projekcja ma hierarchię, można użyć mapowania opartego na regułach, aby zmapować podkolumny hierarchii. Określ warunek dopasowania i kolumnę złożoną, której kolumny mają być mapowane. Każda dopasowana Podkolumna zostanie wykorzystana przy użyciu reguły "name as" określonej po prawej stronie.

![Zrzut ekranu przedstawia mapowanie oparte na regułach przy użyciu dla hierarchii.](media/data-flow/rule-based-hierarchy.png "Mapowanie oparte na regułach")

Powyższy przykład dopasowuje wszystkie podkolumny złożonej kolumny `a` . `a` zawiera dwie podkolumny `b` i `c` . Schemat danych wyjściowych będzie zawierać dwie kolumny `b` , a `c` jako warunek "name as" `$$` .

### <a name="parameterization"></a>Parametryzacja

Można Sparametryzuj nazwy kolumn przy użyciu mapowania opartego na regułach. Użyj słowa kluczowego, ```name``` Aby dopasować nazwy kolumn przychodzących do parametru. Na przykład, jeśli masz parametr przepływu danych, możesz ```mycolumn``` utworzyć regułę zgodną z nazwą kolumny, która jest równa ```mycolumn``` . Można zmienić nazwę dopasowanej kolumny na zakodowany ciąg, taki jak "klucz biznesowy" i odwołać się do niego jawnie. W tym przykładzie warunek dopasowania to ```name == $mycolumn``` i warunek nazwy to "klucz biznesowy". 

## <a name="auto-mapping"></a>Mapowanie

Podczas dodawania transformacji SELECT można włączyć **automapowanie** , przełączając suwak automapowania. Dzięki automapowaniu wybór wybierz mapuje wszystkie kolumny przychodzące, wykluczając duplikaty z taką samą nazwą jak ich dane wejściowe. Spowoduje to uwzględnienie kolumn z przeznaczeniem, co oznacza, że dane wyjściowe mogą zawierać kolumny, które nie zostały zdefiniowane w schemacie. Aby uzyskać więcej informacji na temat kolumn przewidzianych, zobacz [dryfowanie schematu](concepts-data-flow-schema-drift.md).

![Mapowanie](media/data-flow/automap.png "Mapowanie")

Po wybraniu opcji automapowanie w programie wybór zostanie zastosowany do pomijania zduplikowanych ustawień i podania nowego aliasu dla istniejących kolumn. Aliasowanie jest przydatne podczas wykonywania wielu sprzężeń lub wyszukiwań w tym samym strumieniu i w scenariuszach samosprzężenia. 

## <a name="duplicate-columns"></a>Zduplikowane kolumny

Domyślnie wybranie przekształcenia powoduje upuszczenie zduplikowanych kolumn zarówno w projekcji wejściowej, jak i wyjściowej. Zduplikowane kolumny wejściowe często pochodzą z przekształceń sprzężenia i wyszukiwania, w których nazwy kolumn są zduplikowane po każdej stronie sprzężenia. Zduplikowane kolumny wyjściowe mogą wystąpić, jeśli mapujesz dwie różne kolumny wejściowe na taką samą nazwę. Wybierz, czy porzucać i przekazywać zduplikowane kolumny, przełączając pole wyboru.

![Pomiń duplikaty](media/data-flow/select-skip-dup.png "Pomiń duplikaty")

## <a name="ordering-of-columns"></a>Porządkowanie kolumn

Kolejność mapowań określa kolejność kolumn wyjściowych. Jeśli kolumna wejściowa jest mapowana wiele razy, zostanie uznane tylko pierwsze mapowanie. W przypadku każdej zduplikowanej kolumny zostanie zachowane pierwsze dopasowanie.

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>Przykład

Poniżej znajduje się przykład mapowania SELECT i jego skryptu przepływu danych:

![Wybierz przykład skryptu](media/data-flow/select-script-example.png "Wybierz przykład skryptu")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>Następne kroki
* Po użyciu opcji wybierz, aby zmienić nazwę, kolejność i aliasowanie kolumn Użyj [transformacji ujścia](data-flow-sink.md) , aby wyrównać dane do magazynu danych.
