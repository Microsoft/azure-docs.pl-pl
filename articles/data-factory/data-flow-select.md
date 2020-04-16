---
title: Transformacja wyboru przepływu danych mapowania
description: Transformacja wyboru przepływu danych usługi Azure Data Factory mapowania
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/18/2020
ms.openlocfilehash: 498ce053e8a52897d9087091b92fa51a04e195a4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413252"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Wybieranie transformacji w przepływie danych mapowania

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Użyj transformacji wyboru, aby zmienić nazwę kolumn, upuścić lub zmienić kolejność. Ta transformacja nie zmienia danych wierszy, ale wybiera, które kolumny są propagowane w dół. 

W transformacji wybierając użytkownicy mogą określać stałe mapowania, używać wzorców do mapowania opartego na regułach lub włączyć automatyczne mapowanie. Mapowania stałe i oparte na regułach mogą być używane w ramach tej samej transformacji wyboru. Jeśli kolumna nie pasuje do jednego z zdefiniowanych mapowań, zostanie porzucona.

## <a name="fixed-mapping"></a>Stałe mapowanie

Jeśli w rzutowaniu zdefiniowano mniej niż 50 kolumn, wszystkie zdefiniowane kolumny będą domyślnie miały stałe mapowanie. Stałe mapowanie przyjmuje zdefiniowaną, przychodzącą kolumnę i mapuje ją pod dokładną nazwą.

![Stałe mapowanie](media/data-flow/fixedmapping.png "Stałe mapowanie")

> [!NOTE]
> Nie można mapować ani zmieniać nazwy kolumny dryfował przy użyciu stałego mapowania

### <a name="mapping-hierarchical-columns"></a>Mapowanie kolumn hierarchicznych

Stałe mapowania mogą być używane do mapowania podkolumny kolumny hierarchicznej na kolumnę najwyższego poziomu. Jeśli masz zdefiniowaną hierarchię, użyj listy rozwijanej kolumny, aby wybrać podkolumnę. Transformacja select utworzy nową kolumnę z wartością i typem danych podkolumny.

![mapowanie hierarchiczne](media/data-flow/select-hierarchy.png "mapowanie hierarchiczne")

## <a name="rule-based-mapping"></a>Mapowanie oparte na regułach

Jeśli chcesz mapować wiele kolumn naraz lub przekazać dryfowane kolumny w dół, użyj mapowania opartego na regułach, aby zdefiniować mapowania przy użyciu wzorców kolumn. Dopasuj `name` `type`na `stream`podstawie `position` , , i kolumn. Można mieć dowolną kombinację mapowań stałych i opartych na regułach. Domyślnie wszystkie rzuty z większą niż 50 kolumn domyślnie będzie mapowanie oparte na regułach, który pasuje do każdej kolumny i wyprowadza wprowadzonej nazwy. 

Aby dodać mapowanie oparte na regułach, kliknij pozycję **Dodaj mapowanie** i wybierz pozycję **Mapowanie oparte na regułach**.

![mapowanie oparte na regułach](media/data-flow/rule2.png "Mapowanie oparte na regułach")

Każde mapowanie oparte na regułach wymaga dwóch danych wejściowych: warunek, dla którego ma być zgodny według i co do nazwy każdej zamapowanej kolumny. Obie wartości są wprowadzane za pośrednictwem [konstruktora wyrażeń](concepts-data-flow-expression-builder.md). W lewym polu wyrażenia wprowadź warunek dopasowania logicznego. W prawym polu wyrażenia określ, do jakiej kolumny dopasowanej będzie mapowana.

![mapowanie oparte na regułach](media/data-flow/rule-based-mapping.png "Mapowanie oparte na regułach")

Użyj `$$` składni, aby odwołać się do nazwy wejściowej dopasowanej kolumny. Używając powyższego obrazu jako przykładu, powiedzmy, że użytkownik chce dopasować na wszystkich kolumnach ciągów, których nazwy są krótsze niż sześć znaków. Jeśli nazwano `test`jedną kolumnę `$$ + '_short'` przychodzącą, `test_short`wyrażenie zmieni nazwę kolumny . Jeśli jest to tylko mapowanie, które istnieje, wszystkie kolumny, które nie spełniają warunek zostaną usunięte z danych wyjściowych.

Wzorce pasują zarówno do kolumn dryfowanych, jak i zdefiniowanych. Aby zobaczyć, które zdefiniowane kolumny są mapowane przez regułę, kliknij ikonę okularów obok reguły. Sprawdź dane wyjściowe za pomocą podglądu danych.

### <a name="regex-mapping"></a>Mapowanie regeksu

Po kliknięciu ikony szewronu w dół można określić warunek oddawania wartości. Warunek mapowania wyrażenia regularnego pasuje do wszystkich nazw kolumn, które odpowiadają określonej warunku wyrażenia regularnego. Może to być używane w połączeniu ze standardowymi mapowaniami opartymi na regułach.

![mapowanie oparte na regułach](media/data-flow/regex-matching.png "Mapowanie oparte na regułach")

Powyższy przykład pasuje do `(r)` wzorca wyrażenia regularnego lub dowolnej nazwy kolumny zawierającej małe litery r. Podobnie jak w przypadku mapowania opartego na regułach standardowych, wszystkie `$$` dopasowane kolumny są zmieniane przez warunek po prawej stronie przy użyciu składni.

Jeśli w nazwie kolumny znajduje się wiele dopasowań wyrażenia `$n` regularnego, możesz odwołać się do określonych dopasowań, używając, gdzie "n" odnosi się do tego, które dopasowanie. Na przykład "$2" odnosi się do drugiego dopasowania w nazwie kolumny.

### <a name="rule-based-hierarchies"></a>Hierarchie oparte na regułach

Jeśli zdefiniowana projekcja ma hierarchię, można użyć mapowania opartego na regułach do mapowania podkolumnów hierarchii. Określ pasujący warunek i kolumnę złożoną, której podkolumny chcesz zamapować. Każdy dopasowany podkolumn zostanie wysunięty przy użyciu reguły "Nazwa jako" określonej po prawej stronie.

![mapowanie oparte na regułach](media/data-flow/rule-based-hierarchy.png "Mapowanie oparte na regułach")

Powyższy przykład pasuje do wszystkich podkolumnów złożonej kolumny `a`. `a`zawiera dwa podkolumny `b` i `c`. Schemat danych wyjściowych `b` będzie zawierał `c` dwie kolumny, a `$$`warunkiem "Nazwa jako" jest .

### <a name="parameterization"></a>Parametryzacja

Nazwy kolumn można sparametryzować za pomocą mapowania opartego na regułach. Użyj słowa ```name``` kluczowego, aby dopasować nazwy kolumn przychodzących do parametru. Na przykład, jeśli masz parametr ```mycolumn```przepływu danych, można utworzyć regułę, która ```mycolumn```pasuje do dowolnej nazwy kolumny, która jest równa . Dopasowaną kolumnę można zmienić na ciąg kodowany na stałe, taki jak "klucz biznesowy", i odwołać się do niego jawnie. W tym przykładzie warunek ```name == $mycolumn``` dopasowania jest i warunek nazwy jest "klucz biznesowy". 

## <a name="auto-mapping"></a>Automatyczne mapowanie

Podczas dodawania transformacji wyboru **automatyczne mapowanie** można włączyć, przełączając suwak Automatycznego mapowania. W przypadku automatycznego mapowania wybierz transformację mapuje wszystkie przychodzące kolumny, z wyłączeniem duplikatów, o takiej samej nazwie jak ich dane wejściowe. Będzie to obejmować dryfowane kolumny, co oznacza, że dane wyjściowe mogą zawierać kolumny niezdefiniowane w schemacie. Aby uzyskać więcej informacji na temat kolumn dryfowanych, zobacz [dryf schematu](concepts-data-flow-schema-drift.md).

![Automatyczne mapowanie](media/data-flow/automap.png "Automatyczne mapowanie")

Po wł., po wł., opcja przekształcenie wybierz będzie honorować zduplikowane ustawienia pomijania i będzie dostarczać nowy alias dla istniejących kolumn. Aliasing jest przydatne podczas wykonywania wielu sprzężeń lub odnośnych w tym samym strumieniu i w scenariuszach samodzielnego sprzężenia. 

## <a name="duplicate-columns"></a>Zduplikowane kolumny

Domyślnie transformacja select porzuca zduplikowane kolumny zarówno w projekcji wejściowej, jak i wyjściowej. Zduplikowane kolumny wejściowe często pochodzą z przekształceń sprzężenia i wyszukiwania, w których nazwy kolumn są duplikowane po każdej stronie sprzężenia. Zduplikowane kolumny wyjściowe mogą wystąpić, jeśli mapujesz dwie różne kolumny wejściowe o tej samej nazwie. Wybierz, czy mają być upuszczane, czy przekazywanie zduplikowanych kolumn, przełączanie do pola wyboru.

![Pomiń duplikaty](media/data-flow/select-skip-dup.png "Pomiń duplikaty")

## <a name="ordering-of-columns"></a>Kolejność kolumn

Kolejność mapowań określa kolejność kolumn wyjściowych. Jeśli kolumna wejściowa jest mapowana wiele razy, tylko pierwsze mapowanie zostanie honorowane. Dla każdego zduplikowanego upuszczenia kolumny, pierwszy mecz zostanie zachowany.

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

Poniżej znajduje się przykład mapowania wyboru i jego skryptu przepływu danych:

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
* Po użyciu Wybierz do zmiany nazwy, zmienić kolejność i kolumny alias, użyj [sink transformacji](data-flow-sink.md) do ziemi danych do magazynu danych.
