---
title: Parametryzacja przepływów mapowania danych
description: Dowiedz się, jak Sparametryzuj przepływ danych mapowania z potoków usługi Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 82660cdb4ab6523bae7608fe3b071f20cb3603f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419174"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametryzacja przepływów mapowania danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Mapowanie przepływów danych w Azure Data Factory obsługuje używanie parametrów. Można definiować parametry wewnątrz definicji przepływu danych, których można używać w całym wyrażeniu. Wartości parametrów mogą być ustawiane przez potok wywołujący za pośrednictwem działania wykonaj przepływ danych. Dostępne są trzy opcje ustawiania wartości w wyrażeniach działania przepływu danych:

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

### <a name="passing-in-a-column-name-as-a-parameter"></a>Przekazywanie nazwy kolumny jako parametru

Typowym wzorcem jest przekazywanie nazwy kolumny jako wartości parametru. Aby odwołać się do kolumny skojarzonej z parametrem `byName()` , użyj funkcji. Należy pamiętać, aby rzutować kolumnę na odpowiedni typ z funkcją rzutowania taką `toString()`jak.

Na przykład jeśli chcesz zmapować kolumnę ciągu opartą na parametrze `columnName`, można dodać pochodną transformację kolumny równą. `toString(byName($columnName))`

![Przekazywanie nazwy kolumny jako parametru](media/data-flow/parameterize-column-name.png "Przekazywanie w nazwie kolumny jako paramete")

## <a name="assign-parameter-values-from-a-pipeline"></a>Przypisywanie wartości parametrów z potoku

Po utworzeniu przepływu danych z parametrami można wykonać je z potoku za pomocą działania wykonaj przepływ danych. Po dodaniu działania do kanwy potoku zostaną wyświetlone dostępne parametry przepływu danych na karcie **Parametry** działania.

![Ustawianie parametru przepływu danych](media/data-flow/parameter-assign.png "Ustawianie parametru przepływu danych")

Jeśli typ danych parametru to ciąg, po kliknięciu pola tekstowego, aby ustawić wartości parametrów, możesz wprowadzić potok lub wyrażenie przepływu danych. W przypadku wybrania wyrażenia potoku zostanie wyświetlony panel wyrażenia potoku. Upewnij się, że w składni interpolacji ciągu użyto `'@{<expression>}'`funkcji potoku, na przykład:

```'@{pipeline().RunId}'```

Jeśli parametr nie jest typu String, zawsze będzie wyświetlany Konstruktor wyrażeń przepływu danych. W tym miejscu możesz wprowadzić dowolne wartości wyrażenia lub literału, które pasują do typu danych parametru. Poniżej znajdują się przykłady wyrażenia przepływu danych i ciąg literału z konstruktora wyrażeń:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Każdy przepływ danych mapowania może mieć dowolną kombinację parametrów potoków i przepływów danych. 

![Przykład parametrów przepływu danych](media/data-flow/parameter-example.png "Przykład parametrów przepływu danych")



## <a name="next-steps"></a>Następne kroki
* [Działanie wykonywania przepływu danych](control-flow-execute-data-flow-activity.md)
* [Wyrażenia przepływu sterowania](control-flow-expression-language-functions.md)
