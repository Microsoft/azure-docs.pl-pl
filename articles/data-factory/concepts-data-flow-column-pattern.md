---
title: Wzorce kolumn w przepływie danych mapowania Azure Data Factory
description: Tworzenie uogólnionych wzorców transformacji danych przy użyciu wzorców kolumn w Azure Data Factory mapowania przepływów danych
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 68c211608cfceedaa9d13a595be6d1e5de17f1d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94845010"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Używanie wzorców kolumn w mapowaniu przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Niektóre przekształcenia przepływu danych mapowania umożliwiają odwoływanie się do kolumn szablonu na podstawie wzorców zamiast zakodowanych nazw kolumn. Takie dopasowanie jest znane jako *wzorce kolumn*. Możesz definiować wzorce, aby dopasować kolumny na podstawie nazwy, typu danych, strumienia, źródła lub pozycji, zamiast wymagać dokładnej nazwy pól. Istnieją dwa scenariusze, w których są przydatne wzorce kolumn:

* Jeśli przychodzące pola źródłowe często zmieniają się, na przykład w przypadku zmiany kolumn w plikach tekstowych lub bazach danych NoSQL. Ten scenariusz jest nazywany [dryfem schematu](concepts-data-flow-schema-drift.md).
* Jeśli chcesz wykonać wspólną operację na dużej grupie kolumn. Na przykład, chcemy rzutować każdą kolumnę, która ma wartość "Total" w nazwie kolumny na podwójną.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4Iui1]

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Wzorce kolumn w kolumnie pochodnej i agregowanie

Aby dodać wzorzec kolumny do przekształcenia kolumn pochodnych, agregacji lub okna, kliknij przycisk **Dodaj** powyżej listy kolumn lub ikonę znaku plus obok istniejącej kolumny pochodnej. Wybierz pozycję **Dodaj wzorzec kolumny**.

![Zrzut ekranu przedstawia ikonę znaku plus, aby dodać wzorzec kolumny.](media/data-flow/add-column-pattern.png "Wzorce kolumn")

Użyj [konstruktora wyrażeń](concepts-data-flow-expression-builder.md) , aby wprowadzić warunek dopasowania. Utwórz wyrażenie logiczne, które pasuje do kolumn na podstawie kolumny `name` , `type` , `stream` , `origin` i `position` . Wzorzec będzie miał wpływ na wszystkie kolumny, przenoszące lub zdefiniowane, gdzie warunek zwraca wartość true.

Dwa pola wyrażenia poniżej warunku Match określają nowe nazwy i wartości kolumn, których dotyczy problem. Służy `$$` do odwoływania się do istniejącej wartości dopasowanego pola. Pole wyrażenia Left definiuje nazwę i prawo pole wyrażenia definiuje wartość.

![Zrzut ekranu przedstawia kartę Ustawienia kolumny pochodnej.](media/data-flow/edit-column-pattern.png "Wzorce kolumn")

Powyższy wzorzec kolumny jest zgodny z każdą kolumną typu Double i tworzy jedną kolumnę pochodną dla dopasowania. Podając `$$` jako pole Nazwa kolumny, każda dopasowana kolumna jest aktualizowana o tej samej nazwie. Wartość każdej kolumny to istniejąca wartość zaokrąglona do dwóch punktów dziesiętnych.

Aby sprawdzić, czy odpowiedni warunek jest poprawny, można sprawdzić poprawność schematu wyjściowego zdefiniowanych kolumn na karcie **Inspekcja** lub uzyskać migawkę danych na karcie **Podgląd danych** . 

![Zrzut ekranu przedstawia kartę schemat danych wyjściowych.](media/data-flow/columnpattern3.png "Wzorce kolumn")

### <a name="hierarchical-pattern-matching"></a>Dopasowanie wzorca hierarchicznego

Można również kompilować dopasowanie do wzorca w złożonych strukturach hierarchicznych. Rozwiń sekcję, w `Each MoviesStruct that matches` której zostanie wyświetlony monit o podanie każdej hierarchii w strumieniu danych. Następnie można skompilować pasujące wzorce dla właściwości w tej wybranej hierarchii.

![Zrzut ekranu przedstawia wzorzec kolumny hierarchicznej.](media/data-flow/patterns-hierarchy.png "Wzorce kolumn w hierarchiach")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapowanie oparte na regułach w SELECT i ujścia

Podczas mapowania kolumn w źródle i wybierania transformacji można dodać stałe mapowania lub mapowania oparte na regułach. Dopasowanie na podstawie `name` kolumn, `type` , `stream` , `origin` i `position` . Można mieć dowolną kombinację stałych i mapowań opartych na regułach. Domyślnie wszystkie projekcje zawierające więcej niż 50 kolumn będą domyślnie mapowane na podstawie reguły, które pasują do każdej kolumny i wyprowadzają wydaną nazwę. 

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

### <a name="rule-based-hierarchies"></a>Hierarchie oparte na regułach

Jeśli zdefiniowana projekcja ma hierarchię, można użyć mapowania opartego na regułach, aby zmapować podkolumny hierarchii. Określ warunek dopasowania i kolumnę złożoną, której kolumny mają być mapowane. Każda dopasowana Podkolumna zostanie wykorzystana przy użyciu reguły "name as" określonej po prawej stronie.

![Zrzut ekranu przedstawia mapowanie oparte na regułach przy użyciu dla hierarchii.](media/data-flow/rule-based-hierarchy.png "Mapowanie oparte na regułach")

Powyższy przykład dopasowuje wszystkie podkolumny złożonej kolumny `a` . `a` zawiera dwie podkolumny `b` i `c` . Schemat danych wyjściowych będzie zawierać dwie kolumny `b` , a `c` jako warunek "name as" `$$` .

## <a name="pattern-matching-expression-values"></a>Wartości wyrażeń dopasowania do wzorca.

* `$$` tłumaczy na nazwę lub wartość każdego dopasowania w czasie wykonywania. Należy traktować `$$` jako równoważne `this` .
* `name` reprezentuje nazwę każdej kolumny przychodzącej
* `type` reprezentuje typ danych każdej kolumny przychodzącej
* `stream` reprezentuje nazwę skojarzoną z każdym strumieniem lub transformację w przepływie
* `position` jest pozycją porządkową kolumn w przepływie danych
* `origin` jest przekształceniem, w którym kolumna pochodzi lub została ostatnio zaktualizowana

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [języku wyrażeń](data-flow-expression-functions.md) przepływu danych mapowania dla transformacji danych
* Używanie wzorców kolumn w [transformację ujścia](data-flow-sink.md) i [Wybieranie transformacji](data-flow-select.md) z mapowaniem opartym na regułach
