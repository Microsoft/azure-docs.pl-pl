---
title: Parametryzacja przepływów mapowania danych
description: Dowiedz się, jak Sparametryzuj przepływ danych mapowania z potoków usługi Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 8e88e5e8a9fbe1881959c5183dc01b11ac681bdf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82780400"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametryzacja przepływów mapowania danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Mapowanie przepływów danych w Azure Data Factory obsługuje używanie parametrów. Zdefiniuj parametry wewnątrz definicji przepływu danych i używaj ich w całym wyrażeniu. Wartości parametrów są ustawiane przez potok wywołujący za pośrednictwem działania wykonaj przepływ danych. Dostępne są trzy opcje ustawiania wartości w wyrażeniach działania przepływu danych:

* Używanie języka wyrażeń przepływu sterowania potoku do ustawiania wartości dynamicznej
* Używanie języka wyrażeń przepływu danych do ustawiania wartości dynamicznej
* Użyj dowolnego języka wyrażeń, aby ustawić statyczną wartość literału

Użyj tej funkcji, aby zapewnić przepływ danych ogólnego przeznaczenia, elastyczne i wielokrotnego użytku. Za pomocą tych parametrów można Sparametryzuj ustawienia przepływu danych i wyrażenia.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Tworzenie parametrów w mapowaniu przepływu danych

Aby dodać parametry do przepływu danych, kliknij pustą część kanwy przepływu danych, aby wyświetlić właściwości ogólne. W okienku Ustawienia zostanie wyświetlona karta o nazwie **Parameter**. Wybierz pozycję **Nowy** , aby wygenerować nowy parametr. Dla każdego parametru należy przypisać nazwę, wybrać typ i opcjonalnie ustawić wartość domyślną.

![Utwórz parametry przepływu danych](media/data-flow/create-params.png "Utwórz parametry przepływu danych")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Używanie parametrów w mapowaniu przepływu danych 

Parametry mogą być przywoływane w dowolnym wyrażeniu przepływu danych. Parametry zaczynają się od $ i są niezmienne. Listę dostępnych parametrów można znaleźć w Konstruktorze wyrażeń na karcie **Parametry** .

![Wyrażenie parametru przepływu danych](media/data-flow/parameter-expression.png "Wyrażenie parametru przepływu danych")

Możesz szybko dodać dodatkowe parametry, wybierając pozycję **Nowy parametr** i podając nazwę i typ.

![Wyrażenie parametru przepływu danych](media/data-flow/new-parameter-expression.png "Wyrażenie parametru przepływu danych")

## <a name="assign-parameter-values-from-a-pipeline"></a>Przypisywanie wartości parametrów z potoku

Po utworzeniu przepływu danych z parametrami można wykonać je z potoku za pomocą działania Execute Flow. Po dodaniu działania do kanwy potoku zostaną wyświetlone dostępne parametry przepływu danych na karcie **Parametry** działania.

Podczas przypisywania wartości parametrów można użyć [języka wyrażeń potoku](control-flow-expression-language-functions.md) lub [języka wyrażenia przepływu danych](data-flow-expression-functions.md) w oparciu o typy Spark. Każdy przepływ danych mapowania może mieć dowolną kombinację parametrów potoków i przepływów danych.

![Ustawianie parametru przepływu danych](media/data-flow/parameter-assign.png "Ustawianie parametru przepływu danych")

### <a name="pipeline-expression-parameters"></a>Parametry wyrażenia potoku

Parametry wyrażenia potoku umożliwiają odwoływanie się do zmiennych systemowych, funkcji, parametrów potoku i zmiennych, podobnie jak w przypadku innych działań potoku. Kliknięcie **wyrażenia potoku**spowoduje otwarcie strony bocznej, która umożliwia wprowadzanie wyrażenia przy użyciu Konstruktora wyrażeń.

![Ustawianie parametru przepływu danych](media/data-flow/parameter-pipeline.png "Ustawianie parametru przepływu danych")

W przypadku odwoływania się do nich są oceniane parametry potoku, a następnie ich wartość jest używana w języku wyrażeń przepływu danych. Typ wyrażenia potoku nie musi być zgodny z typem parametru przepływu danych. 

#### <a name="string-literals-vs-expressions"></a>Literały ciągu a wyrażenia

Podczas przypisywania parametru wyrażenia potoku typu String, domyślnie dodawane są cudzysłowy, a wartość zostanie oceniona jako literał. Aby odczytać wartość parametru jako wyrażenie przepływu danych, zaznacz pole wyboru obok parametru.

![Ustawianie parametru przepływu danych](media/data-flow/string-parameter.png "Ustawianie parametru przepływu danych")

Jeśli parametr przepływu danych `stringParam` odwołuje się do parametru potoku o wartości `upper(column1)` . 

- Jeśli wyrażenie jest zaznaczone, program `$stringParam` oblicza wartość Kolumna1 wszystkie wielkie litery.
- Jeśli wyrażenie nie jest zaznaczone (zachowanie domyślne), program  `$stringParam` oblicza wartość `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Przekazywanie w sygnaturach czasowych

W języku wyrażeń potoku, zmienne systemowe, takie jak `pipeline().TriggerTime` `utcNow()` zwracają sygnatury czasowe, jako ciągi w formacie "rrrr-mm-dd \' T \' hh: mm: SS. SSSSSSZ'. Aby przekonwertować je na parametry przepływu danych typu sygnatura czasowa, należy użyć interpolacji ciągu, aby uwzględnić odpowiednią sygnaturę czasową w `toTimestamp()` funkcji. Na przykład, aby skonwertować czas wyzwalacza potoku do parametru przepływu danych, można użyć `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` . 

![Ustawianie parametru przepływu danych](media/data-flow/parameter-timestamp.png "Ustawianie parametru przepływu danych")

> [!NOTE]
> Przepływy danych obsługują tylko do 3 milisekund. `left()`Funkcja jest używana do obcinania dodatkowych cyfr.

#### <a name="pipeline-parameter-example"></a>Przykład parametru potoku

Załóżmy, że masz parametr liczby całkowitej `intParam` , który odwołuje się do parametru potoku typu String `@pipeline.parameters.pipelineParam` . 

![Ustawianie parametru przepływu danych](media/data-flow/parameter-pipeline-2.png "Ustawianie parametru przepływu danych")

`@pipeline.parameters.pipelineParam` ma przypisaną wartość `abs(1)` w czasie wykonywania.

![Ustawianie parametru przepływu danych](media/data-flow/parameter-pipeline-4.png "Ustawianie parametru przepływu danych")

Gdy `$intParam` jest przywoływany w wyrażeniu, takim jak kolumna pochodna, obliczy `abs(1)` Return `1` . 

![Ustawianie parametru przepływu danych](media/data-flow/parameter-pipeline-3.png "Ustawianie parametru przepływu danych")

### <a name="data-flow-expression-parameters"></a>Parametry wyrażenia przepływu danych

Wybranie **wyrażenia przepływu danych** spowoduje otwarcie konstruktora wyrażeń przepływu danych. Będziesz mieć możliwość odwoływania się do funkcji, innych parametrów i dowolnej zdefiniowanej kolumny schematu w przepływie danych. To wyrażenie będzie oceniane, gdy występuje odwołanie.

> [!NOTE]
> Jeśli przejdziesz do nieprawidłowego wyrażenia lub odwołujesz się do kolumny schematu, która nie istnieje w tej transformacji, parametr zwróci wartość null.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Przekazywanie nazwy kolumny jako parametru

Typowym wzorcem jest przekazywanie nazwy kolumny jako wartości parametru. Jeśli kolumna jest zdefiniowana w schemacie przepływu danych, można odwołać się do niej bezpośrednio jako wyrażenie ciągu. Jeśli kolumna nie jest zdefiniowana w schemacie, użyj `byName()` funkcji. Należy pamiętać, aby rzutować kolumnę na odpowiedni typ z funkcją rzutowania taką jak `toString()` .

Na przykład jeśli chcesz zmapować kolumnę ciągu opartą na parametrze `columnName` , można dodać pochodną transformację kolumny równą `toString(byName($columnName))` .

![Przekazywanie nazwy kolumny jako parametru](media/data-flow/parameterize-column-name.png "Przekazywanie nazwy kolumny jako parametru")

## <a name="next-steps"></a>Następne kroki
* [Działanie wykonywania przepływu danych](control-flow-execute-data-flow-activity.md)
* [Wyrażenia przepływu sterowania](control-flow-expression-language-functions.md)
