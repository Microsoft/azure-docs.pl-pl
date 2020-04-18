---
title: Wzorce kolumn w przepływie danych mapowania usługi Azure Data Factory
description: Tworzenie uogólnionych wzorców transformacji danych przy użyciu wzorców kolumn w przepływach danych mapowania usługi Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: aacec8830948e08f66d71da88897670f7ef43788
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606134"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Używanie wzorców kolumn w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Kilka przekształceń przepływu danych mapowania umożliwia odwoływanie się do kolumn szablonów na podstawie wzorców zamiast zakodowanych nazw kolumn. To dopasowanie jest znane jako *wzorce kolumn*. Można zdefiniować wzorce, aby dopasować kolumny na podstawie nazwy, typu danych, strumienia lub pozycji zamiast wymagać dokładnych nazw pól. Istnieją dwa scenariusze, w których wzorce kolumn są przydatne:

* Jeśli przychodzące pola źródłowe często się zmieniają, na przykład w przypadku zmiany kolumn w plikach tekstowych lub bazach danych NoSQL. Ten scenariusz jest znany jako [dryf schematu](concepts-data-flow-schema-drift.md).
* Jeśli chcesz wykonać wspólną operację na dużej grupie kolumn. Na przykład, chcąc rzutować każdą kolumnę, która ma "suma" w nazwie kolumny do podwójnego.

Wzorce kolumn są obecnie dostępne w przekształceniach pochodnych, agregacji, zaznaczania i ujścia.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Wzorce kolumn w kolumnie pochodnej i agregacji

Aby dodać wzorzec kolumny w kolumnie pochodnej lub na karcie Agregucie transformacji agregacji, kliknij ikonę plus po prawej stronie istniejącej kolumny. Wybierz **pozycję Dodaj wzór kolumny**. 

![wzory kolumn](media/data-flow/columnpattern.png "Wzorce kolumn")

Użyj [konstruktora wyrażeń,](concepts-data-flow-expression-builder.md) aby wprowadzić warunek dopasowania. Utwórz wyrażenie logiczne, które dopasowuje `stream`kolumny `position` na `name`podstawie `type`, , i kolumny. Wzorzec wpłynie na dowolną kolumnę, dryfował lub zdefiniowano, gdzie warunek zwraca wartość true.

Dwa pola wyrażeń poniżej warunku dopasowania określają nowe nazwy i wartości kolumn, których dotyczy problem. Służy `$$` do odwoływania się do istniejącej wartości dopasowanego pola. Lewe pole wyrażenia definiuje nazwę, a prawe pole wyrażenia definiuje wartość.

![wzory kolumn](media/data-flow/columnpattern2.png "Wzorce kolumn")

Powyższy wzorzec kolumny pasuje do każdej kolumny typu dwukrotnie i tworzy jedną kolumnę agregacji na dopasowanie. Nazwa nowej kolumny jest dopasowana nazwa kolumny połączona z "_total". Wartość nowej kolumny jest zaokrągloną, zagregowana suma istniejącej podwójnej wartości.

Aby sprawdzić, czy warunek dopasowania jest poprawny, można sprawdzić poprawność schematu wyjściowego zdefiniowanych kolumn na karcie **Inspekcja** lub uzyskać migawkę danych na karcie **Podgląd danych.** 

![wzory kolumn](media/data-flow/columnpattern3.png "Wzorce kolumn")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapowanie oparte na regułach w zaznaczeniu i ujściu

Podczas mapowania kolumn w źródle i wybierania przekształceń można dodać mapowanie stałe lub mapowania oparte na regułach. Dopasuj `name` `type`na `stream`podstawie `position` , , i kolumn. Można mieć dowolną kombinację mapowań stałych i opartych na regułach. Domyślnie wszystkie rzuty z większą niż 50 kolumn domyślnie będzie mapowanie oparte na regułach, który pasuje do każdej kolumny i wyprowadza wprowadzonej nazwy. 

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

### <a name="rule-based-hierarchies"></a>Hierarchie oparte na regułach

Jeśli zdefiniowana projekcja ma hierarchię, można użyć mapowania opartego na regułach do mapowania podkolumnów hierarchii. Określ pasujący warunek i kolumnę złożoną, której podkolumny chcesz zamapować. Każdy dopasowany podkolumn zostanie wysunięty przy użyciu reguły "Nazwa jako" określonej po prawej stronie.

![mapowanie oparte na regułach](media/data-flow/rule-based-hierarchy.png "Mapowanie oparte na regułach")

Powyższy przykład pasuje do wszystkich podkolumnów złożonej kolumny `a`. `a`zawiera dwa podkolumny `b` i `c`. Schemat danych wyjściowych `b` będzie zawierał `c` dwie kolumny, a `$$`warunkiem "Nazwa jako" jest .

## <a name="pattern-matching-expression-values"></a>Wartości wyrażenia dopasowania wzorca.

* `$$`przekłada się na nazwę lub wartość każdego dopasowania w czasie wykonywania
* `name`reprezentuje nazwę każdej kolumny przychodzącej
* `type`reprezentuje typ danych każdej kolumny przychodzącej
* `stream`reprezentuje nazwę skojarzoną z każdym strumieniem lub transformację w przepływie
* `position`jest pozycją porządkową kolumn w przepływie danych

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [języku wyrażeń](data-flow-expression-functions.md) przepływu danych mapowania dla przekształceń danych
* Użyj wzorców kolumn w [transformacji](data-flow-sink.md) ujścia i [wybierz transformację](data-flow-select.md) za pomocą mapowania opartego na regułach
