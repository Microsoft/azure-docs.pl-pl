---
title: Parametryzacja przepływów mapowania danych
description: Dowiedz się, jak sparametryzować przepływ danych mapowania z potoków fabryki danych
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: 22c4fc0680d8666d8c2dfafb8829436e27cf1ebd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725714"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametryzacja przepływów mapowania danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Przepływy danych mapowania w Azure Data Factory i Azure Synapse Analytics obsługują użycie parametrów. Zdefiniuj parametry wewnątrz definicji przepływu danych i używaj ich w wyrażeniach. Wartości parametrów są ustawiane przez potok wywołujący za pośrednictwem działania execute Przepływ danych. Dostępne są trzy opcje ustawiania wartości w wyrażeniach działań przepływu danych:

* Ustawianie wartości dynamicznej przy użyciu języka wyrażeń przepływu sterowania potokiem
* Ustawianie wartości dynamicznej przy użyciu języka wyrażeń przepływu danych
* Ustawianie statycznej wartości literału przy użyciu jednego z języków wyrażeń

Ta funkcja umożliwia przepływom danych ogólnego przeznaczenia, elastyczne i wielokrotnego użytku. Za pomocą tych parametrów można parametryzować ustawienia przepływu danych i wyrażenia.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Tworzenie parametrów w przepływie danych mapowania

Aby dodać parametry do przepływu danych, kliknij pustą część kanwy przepływu danych, aby wyświetlić właściwości ogólne. W okienku ustawień zobaczysz kartę o nazwie **Parametr**. Wybierz **pozycję Nowy,** aby wygenerować nowy parametr. Dla każdego parametru należy przypisać nazwę, wybrać typ i opcjonalnie ustawić wartość domyślną.

![Tworzenie Przepływ danych parametrów](media/data-flow/create-params.png "Tworzenie Przepływ danych parametrów")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Używanie parametrów w przepływie danych mapowania 

Do parametrów można odwoływać się w dowolnym wyrażeniu przepływu danych. Parametry zaczynają się od $ i są niezmienne. Lista dostępnych parametrów znajduje się w konstruktorze wyrażeń na **karcie** Parametry.

![Zrzut ekranu przedstawia dostępne parametry na karcie Parametry.](media/data-flow/parameter-expression.png "Wyrażenie parametru przepływu danych")

Możesz szybko dodać dodatkowe parametry, wybierając pozycję **Nowy parametr** i określając nazwę i typ.

![Zrzut ekranu przedstawia parametry na karcie Parametry z dodanymi nowymi parametrami.](media/data-flow/new-parameter-expression.png "Wyrażenie parametru przepływu danych")

## <a name="assign-parameter-values-from-a-pipeline"></a>Przypisywanie wartości parametrów z potoku

Po utworzeniu przepływu danych z parametrami możesz go wykonać z potoku za pomocą polecenia Execute Przepływ danych Activity (Wykonaj przepływ danych). Po dodaniu działania do kanwy potoku na karcie Parametry działania zostaną przedstawione dostępne parametry **przepływu** danych.

Podczas przypisywania wartości parametrów można [](control-flow-expression-language-functions.md) użyć języka wyrażeń potoku lub języka wyrażeń przepływu [danych](data-flow-expression-functions.md) na podstawie typów platformy Spark. Każdy przepływ danych mapowania może mieć dowolną kombinację parametrów potoku i wyrażenia przepływu danych.

![Zrzut ekranu przedstawia kartę Parametry z Przepływ danych wyrażeniem wybranym dla wartości myparam.](media/data-flow/parameter-assign.png "Ustawianie parametru Przepływ danych parametru")

### <a name="pipeline-expression-parameters"></a>Parametry wyrażenia potoku

Parametry wyrażenia potoku umożliwiają odwołania do zmiennych systemowych, funkcji, parametrów potoku i zmiennych podobnych do innych działań potoku. Po kliknięciu **przycisku Wyrażenie potoku** zostanie otwarte okno nav po stronie umożliwiające wprowadzenie wyrażenia przy użyciu konstruktora wyrażeń.

![Zrzut ekranu przedstawia okienko konstruktora wyrażeń.](media/data-flow/parameter-pipeline.png "Ustawianie parametru Przepływ danych parametru")

W przypadku odwołania parametry potoku są oceniane, a następnie ich wartość jest używana w języku wyrażeń przepływu danych. Typ wyrażenia potoku nie musi odpowiadać typowi parametru przepływu danych. 

#### <a name="string-literals-vs-expressions"></a>Literały ciągu a wyrażenia

Podczas przypisywania parametru wyrażenia potoku typu ciąg domyślnie dodawane są cudzysłowy, a wartość będzie oceniana jako literał. Aby odczytać wartość parametru jako wyrażenie przepływu danych, zaznacz pole wyrażenia obok parametru.

![Zrzut ekranu przedstawia okienko Parametry przepływu danych Wyrażenie wybrane dla parametru.](media/data-flow/string-parameter.png "Ustawianie parametru Przepływ danych parametru")

Jeśli parametr przepływu danych `stringParam` odwołuje się do parametru potoku z wartością `upper(column1)` . 

- Jeśli wyrażenie jest zaznaczone, `$stringParam` oblicza wartość kolumny column1 z wielkich liter.
- Jeśli wyrażenie nie jest zaznaczone (zachowanie domyślne), wartość  `$stringParam` to `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Przekazywanie znaczników czasu

W języku wyrażeń potoku zmienne systemowe, takie jak i , zwracają znaczniki czasu jako ciągi w formacie `pipeline().TriggerTime` `utcNow()` 'yyyy-MM-dd \' T \' HH:mm:ss. SSSSSSZ". Aby przekonwertować je na parametry przepływu danych typu sygnatura czasowa, użyj interpolacji ciągów, aby uwzględnić żądany znacznik czasu w `toTimestamp()` funkcji. Aby na przykład przekonwertować czas wyzwalacza potoku na parametr przepływu danych, możesz użyć parametru `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` . 

![Zrzut ekranu przedstawia kartę Parametry, na której można wprowadzić czas wyzwalacza.](media/data-flow/parameter-timestamp.png "Ustawianie parametru Przepływ danych parametru")

> [!NOTE]
> Przepływy danych mogą obsługiwać maksymalnie 3 milisekundy. Funkcja `left()` jest używana do przycinania dodatkowych cyfr.

#### <a name="pipeline-parameter-example"></a>Przykład parametru potoku

Załóżmy, że masz parametr liczby całkowitej, który odwołuje się `intParam` do parametru potoku typu Ciąg, `@pipeline.parameters.pipelineParam` . 

![Zrzut ekranu przedstawia kartę Parametry z parametrami o nazwach stringParam i intParam.](media/data-flow/parameter-pipeline-2.png "Ustawianie parametru Przepływ danych parametru")

`@pipeline.parameters.pipelineParam` ma przypisaną wartość w `abs(1)` czasie wykonywania.

![Zrzut ekranu przedstawia kartę Parametry z wybraną wartością b (1).](media/data-flow/parameter-pipeline-4.png "Ustawianie parametru Przepływ danych parametru")

Gdy `$intParam` odwołanie zostanie przywołyne w wyrażeniu, takim jak kolumna pochodna, zwróci `abs(1)` wartość `1` . 

![Zrzut ekranu przedstawia wartość kolumn.](media/data-flow/parameter-pipeline-3.png "Ustawianie parametru Przepływ danych parametru")

### <a name="data-flow-expression-parameters"></a>Parametry wyrażenia przepływu danych

Wybranie **opcji Wyrażenie przepływu danych** spowoduje otwarcie konstruktora wyrażeń przepływu danych. Będziesz mieć możliwość odwołania się do funkcji, innych parametrów i dowolnej zdefiniowanej kolumny schematu w całym przepływie danych. To wyrażenie zostanie ocenione w taki sposób, jak w przypadku odwołania.

> [!NOTE]
> W przypadku przekazania nieprawidłowego wyrażenia lub odwołania do kolumny schematu, która nie istnieje w tym przekształceniu, parametr zostanie oceniony jako null.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Przekazywanie nazwy kolumny jako parametru

Powszechnym wzorcem jest podanie nazwy kolumny jako wartości parametru. Jeśli kolumna jest zdefiniowana w schemacie przepływu danych, możesz odwoływać się do jej bezpośrednio jako wyrażenia ciągu. Jeśli kolumna nie jest zdefiniowana w schemacie, użyj `byName()` funkcji . Pamiętaj, aby rzutować kolumnę na odpowiedni typ za pomocą funkcji rzutowania, takiej jak `toString()` .

Jeśli na przykład chcesz zamapować kolumnę ciągu na podstawie parametru , możesz dodać pochodne przekształcenie `columnName` kolumny równe `toString(byName($columnName))` .

![Przekazywanie nazwy kolumny jako parametru](media/data-flow/parameterize-column-name.png "Przekazywanie nazwy kolumny jako parametru")

## <a name="next-steps"></a>Następne kroki
* [Działanie wykonywania przepływu danych](control-flow-execute-data-flow-activity.md)
* [Wyrażenia przepływu sterowania](control-flow-expression-language-functions.md)
