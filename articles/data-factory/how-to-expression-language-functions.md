---
title: Jak używać parametrów i wyrażeń w Azure Data Factory
description: Ten artykuł zawiera informacje o wyrażeniach i funkcjach, których można użyć podczas tworzenia jednostek fabryki danych.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: 090c738d06b9b8acd53a4c194e97fa3a0515e1d6
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783391"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>Jak używać parametrów, wyrażeń i funkcji w Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-functions-variables.md)
> * [Bieżąca wersja](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym dokumencie będziemy przede wszystkim skupić się na podstawowych pojęciach związanych z różnymi przykładami, aby poznać możliwość tworzenia sparametryzowanych potoków danych w ramach Azure Data Factory. Wyrażenia "parametryzacja" i "dynamiczne" to takie, które są istotnymi dodatkami do usługi ADF, ponieważ mogą zaoszczędzić dużo czasu i umożliwiają znacznie bardziej elastyczne rozwiązanie wyodrębniania, przekształcania, ładowania (ETL) lub wyodrębniania, obciążenia, przekształcania (ELT), co znacznie zmniejsza koszty konserwacji rozwiązań i przyspiesza implementację nowych funkcji w istniejących potokach. Te zyski wynikają z faktu, że parametryzacja minimalizuje ilość twardych kodowania i zwiększa liczbę obiektów wielokrotnego użytku i procesów w rozwiązaniu.

## <a name="azure-data-factory-ui-and-parameters"></a>Interfejs użytkownika i parametry usługi Azure Data Factory

Jeśli jesteś nowym użytkownikiem użycia parametrów usługi Azure Data Factory w interfejsie użytkownika funkcji ADF, przejrzyj [Informacje o interfejsie użytkownika usługi Data Factory dla połączonych usług z parametrami](https://docs.microsoft.com/azure/data-factory/parameterize-linked-services#data-factory-ui)  i [interfejsem użytkownika usługi Data Factory dla potoku opartego na metadanych z parametrami](https://docs.microsoft.com/azure/data-factory/how-to-use-trigger-parameterization#data-factory-ui) dla wyjaśnienia wizualnego.

## <a name="parameter-and-expression-concepts"></a>Pojęcia dotyczące parametrów i wyrażeń 

Za pomocą parametrów można przekazać wartości zewnętrzne do potoków, zestawów danych, połączonych usług i przepływów danych. Po przekazaniu parametru do zasobu nie można go zmienić. Za pomocą zasobów parametryzacja można za każdym razem ponownie użyć różnych wartości. Parametry mogą być używane pojedynczo lub jako część wyrażeń. Wartości JSON w definicji mogą być literałami lub wyrażeniami, które są oceniane w czasie wykonywania.

Na przykład:  
  
```json
"name": "value"
```

 lub  
  
```json
"name": "@pipeline().parameters.password"
```

Wyrażenia mogą znajdować się w dowolnym miejscu w wartości ciągu JSON i zawsze dawać inną wartość JSON. W tym miejscu *hasło* jest parametrem potoku w wyrażeniu. Jeśli wartość JSON jest wyrażeniem, treść wyrażenia jest wyodrębniana przez usunięcie znaku ( \@ ). Jeśli ciąg literału jest wymagany, który zaczyna się od \@ , należy użyć polecenia \@ \@ . W poniższych przykładach pokazano, jak są oceniane wyrażenia.  
  
|Wartość JSON|Wynik|  
|----------------|------------|  
|wejściowe|Znaki "Parameters" są zwracane.|  
|"parametry [1]"|Znaki "parametry [1]" są zwracane.|  
|"\@\@"|Zostanie zwrócony 1 ciąg znaków zawierający element " \@ ".|  
|" \@"|Zostanie zwrócony ciąg 2 znaków zawierający znak " \@ ".|  
  
 Wyrażenia mogą być również wyświetlane wewnątrz ciągów, przy użyciu funkcji zwanej *interpolacją ciągu* , w której wyrażenia są opakowane `@{ ... }` . Na przykład: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Przy użyciu interpolacji ciągów wynik jest zawsze ciągiem. Załóżmy, że zostały `myNumber` zdefiniowane `42` jako  `myString` i  `foo` :  
  
|Wartość JSON|Wynik|  
|----------------|------------|  
|" \@ potok (). Parameters. substring"| Zwraca `foo` jako ciąg.|  
|" \@ {Pipeline (). Parameters.| Zwraca `foo` jako ciąg.|  
|" \@ potok (). Parameters. mój numer"| Zwraca `42` jako *liczbę*.|  
|" \@ {potok (). Parameters}"| Zwraca `42` jako *ciąg*.|  
|"Odpowiedź to: @ {potoku (). Parameters.! Number}"| Zwraca ciąg `Answer is: 42` .|  
|" \@ concat (odpowiedź:", String (potok (). Parameters.-Number)) "| Zwraca ciąg. `Answer is: 42`|  
|"Odpowiedź to: \@ \@ {Pipeline (). Parameters.! Number}"| Zwraca ciąg `Answer is: @{pipeline().parameters.myNumber}` .|  

## <a name="examples-of-using-parameters-in-expressions"></a>Przykłady użycia parametrów w wyrażeniach 

### <a name="complex-expression-example"></a>Przykład wyrażenia złożonego
Poniższy przykład pokazuje złożony przykład, który odwołuje się do głębokiego podobszaru danych wyjściowych działania. Aby odwołać się do parametru potoku, który jest obliczany do podpola, użyj składni [] zamiast kropki (.) (jak w przypadku subfield1 i subfield2)

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

### <a name="dynamic-content-editor"></a>Edytor zawartości dynamicznej

Dynamiczny Edytor zawartości automatycznie wyprowadza znaki w zawartości po zakończeniu edycji. Na przykład następująca zawartość w edytorze zawartości jest interpolacją ciągu z dwoma funkcjami wyrażenia. 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

Edytor zawartości dynamicznej konwertuje powyżej zawartość na wyrażenie `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"` . Wynik tego wyrażenia jest ciągiem formatu JSON poniżej.

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with--parameters"></a>Zestaw danych z parametrami

W poniższym przykładzie BlobDataset przyjmuje parametr o nazwie **Path**. Jej wartość służy do ustawiania wartości właściwości **folderPath** przy użyciu wyrażenia: `dataset().path` . 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with--parameters"></a>Potok z parametrami

W poniższym przykładzie potok przyjmuje parametry **inputPath** i **outputPath** . **Ścieżka** sparametryzowanego zestawu danych obiektów BLOB jest ustawiana za pomocą wartości tych parametrów. Składnia używana tutaj: `pipeline().parameters.parametername` . 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```

  
## <a name="calling-functions-within-expressions"></a>Wywoływanie funkcji w wyrażeniach 

Możesz wywoływać funkcje w wyrażeniach. Poniższe sekcje zawierają informacje o funkcjach, których można użyć w wyrażeniu.  

### <a name="string-functions"></a>Funkcje ciągów  

Aby korzystać z ciągów, można użyć tych funkcji ciągów, a także niektórych [funkcji kolekcji](#collection-functions).
Funkcje ciągów działają tylko na ciągach.

| Funkcja String | Zadanie |
| --------------- | ---- |
| [Concat](control-flow-expression-language-functions.md#concat) | Połącz dwa lub więcej ciągów i zwróć połączony ciąg. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | Sprawdź, czy ciąg jest kończący się określonym podciągiem. |
| [guid](control-flow-expression-language-functions.md#guid) | Generuj unikatowy identyfikator globalny (GUID) jako ciąg. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Zwróć pozycję początkową dla podciągu. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Zwróć pozycję początkową dla ostatniego wystąpienia podciągu. |
| [stępować](control-flow-expression-language-functions.md#replace) | Zamień podciąg na określony ciąg i zwróć zaktualizowany ciąg. |
| [podziału](control-flow-expression-language-functions.md#split) | Zwraca tablicę zawierającą podciągi, rozdzieloną przecinkami, od większego ciągu na podstawie określonego znaku ogranicznika w oryginalnym ciągu. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | Sprawdź, czy ciąg rozpoczyna się od określonego podciągu. |
| [podciąg](control-flow-expression-language-functions.md#substring) | Zwraca znaki z ciągu, zaczynając od określonej pozycji. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Zwraca ciąg w formacie małymi literami. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Zwraca ciąg w formacie wielką literą. |
| [Trim](control-flow-expression-language-functions.md#trim) | Usuń spacje wiodące i końcowe z ciągu, a następnie Zwróć zaktualizowany ciąg. |

### <a name="collection-functions"></a>Funkcje zbierania

Do pracy z kolekcjami, ogólnie tablicami, ciągami i czasami słownikami można używać tych funkcji kolekcji.

| Funkcja kolekcji | Zadanie |
| ------------------- | ---- |
| [wyświetlana](control-flow-expression-language-functions.md#contains) | Sprawdź, czy kolekcja zawiera określony element. |
| [puste](control-flow-expression-language-functions.md#empty) | Sprawdź, czy kolekcja jest pusta. |
| [pierwszego](control-flow-expression-language-functions.md#first) | Zwróć pierwszy element z kolekcji. |
| [część wspólną](control-flow-expression-language-functions.md#intersection) | Zwróć kolekcję, która ma *tylko* wspólne elementy w określonej kolekcji. |
| [Złącza](control-flow-expression-language-functions.md#join) | Zwraca ciąg, który zawiera *wszystkie* elementy z tablicy, oddzielone określonym znakiem. |
| [ostatniego](control-flow-expression-language-functions.md#last) | Zwróć ostatni element z kolekcji. |
| [length](control-flow-expression-language-functions.md#length) | Zwraca liczbę elementów w ciągu lub tablicy. |
| [Skocz](control-flow-expression-language-functions.md#skip) | Usuń elementy z przodu kolekcji i zwróć *wszystkie pozostałe* elementy. |
| [czasochłonn](control-flow-expression-language-functions.md#take) | Zwróć elementy z przodu kolekcji. |
| [Unii](control-flow-expression-language-functions.md#union) | Zwraca kolekcję zawierającą *wszystkie* elementy z określonych kolekcji. | 

### <a name="logical-functions"></a>Funkcje logiczne  

Te funkcje są przydatne w warunkach, ale mogą służyć do szacowania dowolnego typu logiki.  
  
| Logiczna funkcja porównywania | Zadanie |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Sprawdź, czy wszystkie wyrażenia mają wartość PRAWDA. |
| [ubiegł](control-flow-expression-language-functions.md#equals) | Sprawdź, czy obie wartości są równoważne. |
| [greater](control-flow-expression-language-functions.md#greater) | Sprawdź, czy pierwsza wartość jest większa od drugiej wartości. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Sprawdź, czy pierwsza wartość jest większa lub równa drugiej wartości. |
| [if](control-flow-expression-language-functions.md#if) | Sprawdź, czy wyrażenie ma wartość true lub false. W oparciu o wynik Zwraca określoną wartość. |
| [wcześniejsz](control-flow-expression-language-functions.md#less) | Sprawdź, czy pierwsza wartość jest mniejsza od drugiej wartości. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Sprawdź, czy pierwsza wartość jest mniejsza lub równa drugiej wartości. |
| [niemożliwe](control-flow-expression-language-functions.md#not) | Sprawdź, czy wyrażenie ma wartość false. |
| [lub](control-flow-expression-language-functions.md#or) | Sprawdź, czy co najmniej jedno wyrażenie ma wartość true. |
  
### <a name="conversion-functions"></a>Funkcje konwersji  

 Te funkcje są używane do konwersji między poszczególnymi typami natywnymi w języku:  
-   ciąg
-   liczba całkowita
-   float
-   boolean
-   Tablice
-   słownik

| Funkcja konwersji | Zadanie |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | Zwraca tablicę z pojedynczego określonego danych wejściowych. W przypadku wielu danych wejściowych [Zobacz sekcję](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Zwróć wersję z kodowaniem Base64 dla ciągu. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Zwróć wersję binarną dla ciągu zakodowanego algorytmem Base64. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Zwraca wersję ciągu dla ciągu zakodowanego algorytmem Base64. |
| [binarny](control-flow-expression-language-functions.md#binary) | Zwróć wersję binarną dla wartości wejściowej. |
| [bool](control-flow-expression-language-functions.md#bool) | Zwróć wersję logiczną dla wartości wejściowej. |
| [łączonych](control-flow-expression-language-functions.md#coalesce) | Zwróć pierwszą wartość inną niż null z co najmniej jednego parametru. |
| [przearray](control-flow-expression-language-functions.md#createArray) | Zwróć tablicę z wielu danych wejściowych. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Zwróć identyfikator URI danych dla wartości wejściowej. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Zwróć wersję binarną dla identyfikatora URI danych. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Zwraca wersję ciągu dla identyfikatora URI danych. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Zwraca wersję ciągu dla ciągu zakodowanego algorytmem Base64. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Zwróć wersję binarną dla identyfikatora URI danych. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Zwraca ciąg, który zastępuje znaki ucieczki z zdekodowanymi wersjami. |
| [encodeURIComponent —](control-flow-expression-language-functions.md#encodeUriComponent) | Zwraca ciąg, który zastępuje znaki w postaci niebezpiecznej adresów URL. |
| [liczba zmiennoprzecinkowa](control-flow-expression-language-functions.md#float) | Zwróć liczbę zmiennoprzecinkową dla wartości wejściowej. |
| [int](control-flow-expression-language-functions.md#int) | Zwróć wersję całkowitą dla ciągu. |
| [kodu](control-flow-expression-language-functions.md#json) | Zwróć wartość typu JavaScript Object Notation (JSON) lub obiekt dla ciągu lub XML. |
| [ciąg](control-flow-expression-language-functions.md#string) | Zwraca wersję ciągu dla wartości wejściowej. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Zwróć wersję z kodowaniem URI dla wartości wejściowej przez zastępowanie znaków w adresie URL bez znaku ucieczki. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Zwróć wersję binarną dla ciągu zakodowanego za pomocą identyfikatora URI. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Zwraca wersję ciągu dla ciągu zakodowanego przy użyciu identyfikatora URI. |
| [dokument](control-flow-expression-language-functions.md#xml) | Zwraca wersję XML dla ciągu. |
| [Lokalizacja](control-flow-expression-language-functions.md#xpath) | Sprawdź XML dla węzłów lub wartości, które pasują do wyrażenia XPath (język ścieżki XML) i zwracają pasujące węzły lub wartości. |

### <a name="math-functions"></a>Funkcje matematyczne  
 Te funkcje mogą być używane dla każdego typu liczb: **liczb całkowitych** i **zmiennoprzecinkowych**.  

| Funkcja matematyczna | Zadanie |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | Zwróć wynik dodawania dwóch liczb. |
| [div](control-flow-expression-language-functions.md#div) | Zwróć wynik dzielenia dwóch liczb. |
| [Maksymalny](control-flow-expression-language-functions.md#max) | Zwraca najwyższą wartość z zestawu liczb lub tablicy. |
| [min](control-flow-expression-language-functions.md#min) | Zwróć najniższą wartość z zestawu liczb lub tablicy. |
| [Funkcja](control-flow-expression-language-functions.md#mod) | Zwróć resztę z dzielenia dwóch liczb. |
| [mul](control-flow-expression-language-functions.md#mul) | Zwróć produkt z mnożenia dwóch liczb. |
| [Rand](control-flow-expression-language-functions.md#rand) | Zwraca losową liczbę całkowitą z podanego zakresu. |
| [zakresu](control-flow-expression-language-functions.md#range) | Zwróć tablicę liczb całkowitych, która zaczyna się od określonej liczby całkowitej. |
| [Sub](control-flow-expression-language-functions.md#sub) | Zwraca wynik odejmowania drugiej liczby od pierwszej liczby. |
  
### <a name="date-functions"></a>Funkcje daty  

| Funkcja daty lub godziny | Zadanie |
| --------------------- | ---- |
| [Liczba dni](control-flow-expression-language-functions.md#addDays) | Dodaj liczbę dni do sygnatury czasowej. |
| [addgodz.](control-flow-expression-language-functions.md#addHours) | Dodaj liczbę godzin do sygnatury czasowej. |
| [addminut](control-flow-expression-language-functions.md#addMinutes) | Dodaj liczbę minut do sygnatury czasowej. |
| [AddSeconds](control-flow-expression-language-functions.md#addSeconds) | Dodaj liczbę sekund do sygnatury czasowej. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Dodaj liczbę jednostek czasu do sygnatury czasowej. Zobacz również [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Przekształć sygnaturę czasową od uniwersalnego czasu koordynowanego (UTC) na docelową strefę czasową. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Przekonwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na uniwersalne czas uniwersalny (UTC). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Zwróć dzień składnika miesiąca z sygnatury czasowej. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Zwróć dzień składnika tygodnia z sygnatury czasowej. |
| [dzieńroku](control-flow-expression-language-functions.md#dayOfYear) | Zwróć dzień składnika roku z sygnatury czasowej. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Zwróć sygnaturę czasową jako ciąg w formacie opcjonalnym. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Zwróć bieżącą sygnaturę czasową powiększoną o określoną liczbę jednostek czasu. Zobacz również [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Zwróć bieżącą sygnaturę czasową minus określoną liczbę jednostek czasu. Zobacz również [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Zwróć początek dnia dla sygnatury czasowej. |
| [Początek godziny](control-flow-expression-language-functions.md#startOfHour) | Zwróć początek godziny dla sygnatury czasowej. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Zwróć początek miesiąca dla sygnatury czasowej. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Odejmij wiele jednostek czasu od sygnatury czasowej. Zobacz również [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [taktów](control-flow-expression-language-functions.md#ticks) | Zwraca `ticks` wartość właściwości dla określonego sygnatury czasowej. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Zwróć bieżącą sygnaturę czasową jako ciąg. |

## <a name="detailed-examples-for-practice"></a>Szczegółowe przykłady dotyczące ćwiczeń

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>Szczegółowy potok kopiowania usługi Azure Data Factory z parametrami 

Ten [parametr potoku kopiowania usługi Azure Data Factory](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) jest przeszukiwany przez proces przekazywania parametrów między potokiem a działaniem oraz między działaniami.

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>Szczegółowy potok przepływu danych mapowania z parametrami 

Postępuj zgodnie z informacjami o [mapowaniu przepływu danych z parametrami](https://docs.microsoft.com/azure/data-factory/parameters-data-flow) , aby uzyskać pełny przykład użycia parametrów w przepływie danych.

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>Szczegółowy potok oparty na metadanych z parametrami

Aby dowiedzieć się więcej na temat używania parametrów do projektowania potoków opartych na metadanych, należy postępować według [potoku opartego na metadanych z parametrami](https://docs.microsoft.com/azure/data-factory/how-to-use-trigger-parameterization) . Jest to popularny przypadek użycia dla parametrów.


## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę zmiennych systemowych, których można używać w wyrażeniach, zobacz [zmienne systemowe](control-flow-system-variables.md).
