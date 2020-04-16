---
title: Parametryzacja przepływów mapowania danych
description: Dowiedz się, jak parametryzować przepływ danych mapowania z potoków fabryki danych
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 82660cdb4ab6523bae7608fe3b071f20cb3603f8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419174"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametryzacja przepływów mapowania danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Mapowanie przepływów danych w usłudze Azure Data Factory obsługuje użycie parametrów. Można zdefiniować parametry wewnątrz definicji przepływu danych, które można następnie używać w wyrażeniach. Wartości parametrów można ustawić za pomocą potoku wywołującego za pośrednictwem działania Wykonaj przepływ danych. Dostępne są trzy opcje ustawiania wartości w wyrażeniach aktywności przepływu danych:

* Użyj języka wyrażenia przepływu sterowania rurociągiem, aby ustawić wartość dynamiczną
* Ustawianie wartości dynamicznej za pomocą języka wyrażenia przepływu danych
* Użyj albo języka wyrażenia, aby ustawić statyczną wartość literału

Ta funkcja umożliwia elastyczne i wielokrotnego użytku przepływy danych ogólnego przeznaczenia. Za pomocą tych parametrów można parametryzować ustawienia przepływu danych i wyrażenia.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Tworzenie parametrów w przepływie danych mapowania

Aby dodać parametry do przepływu danych, kliknij pustą część obszaru roboczego przepływu danych, aby wyświetlić ogólne właściwości. W okienku ustawień zostanie wyświetlony zakładka o nazwie **Parametr**. Wybierz **pozycję Nowy,** aby wygenerować nowy parametr. Dla każdego parametru należy przypisać nazwę, wybrać typ i opcjonalnie ustawić wartość domyślną.

![Tworzenie parametrów przepływu danych](media/data-flow/create-params.png "Tworzenie parametrów przepływu danych")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Używanie parametrów w przepływie danych mapowania 

Parametry mogą być odwoływane w dowolnym wyrażeniu przepływu danych. Parametry zaczynają się od $ i są niezmienne. Listę dostępnych parametrów znajdziesz wewnątrz Konstruktora wyrażeń na karcie **Parametry.**

![Wyrażenie parametru przepływu danych](media/data-flow/parameter-expression.png "Wyrażenie parametru przepływu danych")

Można szybko dodać dodatkowe parametry, wybierając **nowy parametr** i określając nazwę i typ.

![Wyrażenie parametru przepływu danych](media/data-flow/new-parameter-expression.png "Wyrażenie parametru przepływu danych")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Przekazywanie nazwy kolumny jako parametru

Typowym wzorcem jest przekazywanie w nazwie kolumny jako wartość parametru. Aby odwołać się do kolumny skojarzonej z parametrem, `byName()` należy użyć funkcji. Pamiętaj, aby przerzucać kolumnę do odpowiedniego `toString()`typu za pomocą funkcji odlewania, takiej jak .

Na przykład, jeśli chcesz zamapować kolumnę `columnName`ciągu na podstawie parametru, `toString(byName($columnName))`można dodać transformację kolumny pochodnej równą .

![Przekazywanie nazwy kolumny jako parametru](media/data-flow/parameterize-column-name.png "Przekazywanie nazwy kolumny jako paramete")

## <a name="assign-parameter-values-from-a-pipeline"></a>Przypisywanie wartości parametrów z potoku

Po utworzeniu przepływu danych z parametrami, można go wykonać z potoku z execute data flow działania. Po dodaniu działania do kanwy potoku zostaną wyświetlone dostępne parametry przepływu danych na karcie **Parametry** działania.

![Ustawianie parametru Przepływ danych](media/data-flow/parameter-assign.png "Ustawianie parametru Przepływ danych")

Jeśli typem danych parametru jest ciąg, po kliknięciu pola tekstowego w celu ustawiania wartości parametrów można wprowadzić potok lub wyrażenie przepływu danych. Jeśli wybierzesz wyrażenie potoku, zostanie wyświetlony panel wyrażenia potoku. Upewnij się, że funkcje potoku są `'@{<expression>}'`uwzględniane w składni interpolacji ciągów, na przykład:

```'@{pipeline().RunId}'```

Jeśli parametr nie jest typu ciąg, zawsze będą prezentowane z Konstruktorem wyrażeń przepływu danych. W tym miejscu można wprowadzić dowolne wyrażenia lub wartości literału, które mają być zgodne z typem danych parametru. Poniżej znajdują się przykłady wyrażenia przepływu danych i ciąg literał z konstruktora wyrażeń:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Każdy przepływ danych mapowania może mieć dowolną kombinację parametrów wyrażenia potoku i przepływu danych. 

![Próbka parametrów przepływu danych](media/data-flow/parameter-example.png "Próbka parametrów przepływu danych")



## <a name="next-steps"></a>Następne kroki
* [Wykonywanie działania przepływu danych](control-flow-execute-data-flow-activity.md)
* [Sterowanie wyrażeniami przepływu](control-flow-expression-language-functions.md)
