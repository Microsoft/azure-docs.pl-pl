---
title: Wyrażenie i funkcje w Azure Data Factory
description: Ten artykuł zawiera informacje o wyrażeniach i funkcjach, których można użyć podczas tworzenia jednostek fabryki danych.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 829afda7ba49d60e51f3a074d38e5a1d0ca924a4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050056"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Wyrażenia i funkcje w usłudze Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-functions-variables.md)
> * [Bieżąca wersja](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ten artykuł zawiera szczegółowe informacje na temat wyrażeń i funkcji obsługiwanych przez Azure Data Factory. 

## <a name="expressions"></a>Wyrażenia

Wartości JSON w definicji mogą być literałami lub wyrażeniami, które są oceniane w czasie wykonywania. Na przykład:  
  
```json
"name": "value"
```

 lub  
  
```json
"name": "@pipeline().parameters.password"
```

Wyrażenia mogą znajdować się w dowolnym miejscu w wartości ciągu JSON i zawsze dawać inną wartość JSON. Jeśli wartość JSON jest wyrażeniem, treść wyrażenia jest wyodrębniana przez usunięcie znaku ( \@ ). Jeśli ciąg literału jest wymagany, który zaczyna się od \@ , należy użyć polecenia \@ \@ . W poniższych przykładach pokazano, jak są oceniane wyrażenia.  
  
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

## <a name="examples"></a>Przykłady

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

### <a name="a-dataset-with-a-parameter"></a>Zestaw danych z parametrem
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

### <a name="a-pipeline-with-a-parameter"></a>Potok z parametrem
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
### <a name="tutorial"></a>Samouczek
Ten [samouczek](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) przeprowadzi Cię przez proces przekazywania parametrów między potokiem a działaniem oraz między działaniami.

  
## <a name="functions"></a>Funkcje

Możesz wywoływać funkcje w wyrażeniach. Poniższe sekcje zawierają informacje o funkcjach, których można użyć w wyrażeniu.  

## <a name="string-functions"></a>Funkcje ciągów  

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

## <a name="collection-functions"></a>Funkcje zbierania

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

## <a name="logical-functions"></a>Funkcje logiczne  

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
| [oraz](control-flow-expression-language-functions.md#or) | Sprawdź, czy co najmniej jedno wyrażenie ma wartość true. |
  
## <a name="conversion-functions"></a>Funkcje konwersji  

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

## <a name="math-functions"></a>Funkcje matematyczne  
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
  
## <a name="date-functions"></a>Funkcje daty  

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

## <a name="function-reference"></a>Odwołanie do funkcji

Ta sekcja zawiera listę wszystkich dostępnych funkcji w kolejności alfabetycznej.

<a name="add"></a>

### <a name="add"></a>add

Zwróć wynik dodawania dwóch liczb.

```
add(<summand_1>, <summand_2>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Tak | Liczba całkowita, zmiennoprzecinkowa lub mieszana | Liczby do dodania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*wynik — suma*> | Liczba całkowita lub zmiennoprzecinkowa | Wynik dodawania określonych liczb |
||||

*Przykład*

Ten przykład dodaje określone liczby:

```
add(1, 1.5)
```

I zwraca ten wynik: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>Liczba dni

Dodaj liczbę dni do sygnatury czasowej.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*dni*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba dni do dodania |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowane — znacznik czasu*> | Ciąg | Sygnatura czasowa powiększona o określoną liczbę dni  |
||||

*Przykład 1*

Ten przykład dodaje 10 dni do określonej sygnatury czasowej:

```
addDays('2018-03-15T13:00:00Z', 10)
```

I zwraca ten wynik: `"2018-03-25T00:00:0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć dni od określonej sygnatury czasowej:

```
addDays('2018-03-15T00:00:00Z', -5)
```

I zwraca ten wynik: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addgodz.

Dodaj liczbę godzin do sygnatury czasowej.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*liczb*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba godzin do dodania |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowane — znacznik czasu*> | Ciąg | Sygnatura czasowa powiększona o określoną liczbę godzin  |
||||

*Przykład 1*

Ten przykład dodaje 10 godzin do określonej sygnatury czasowej:

```
addHours('2018-03-15T00:00:00Z', 10)
```

I zwraca ten wynik: `"2018-03-15T10:00:0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć godzin od określonej sygnatury czasowej:

```
addHours('2018-03-15T15:00:00Z', -5)
```

I zwraca ten wynik: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addminut

Dodaj liczbę minut do sygnatury czasowej.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*minut*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba minut do dodania |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowane — znacznik czasu*> | Ciąg | Sygnatura czasowa powiększona o określoną liczbę minut |
||||

*Przykład 1*

Ten przykład dodaje 10 minut do określonej sygnatury czasowej:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

I zwraca ten wynik: `"2018-03-15T00:20:00.0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć minut od określonej sygnatury czasowej:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

I zwraca ten wynik: `"2018-03-15T00:15:00.0000000Z"`

<a name="addSeconds"></a>

### <a name="addseconds"></a>AddSeconds

Dodaj liczbę sekund do sygnatury czasowej.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*s*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba sekund do dodania |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowane — znacznik czasu*> | Ciąg | Sygnatura czasowa powiększona o określoną liczbę sekund.  |
||||

*Przykład 1*

Ten przykład dodaje 10 sekund do określonej sygnatury czasowej:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

I zwraca ten wynik: `"2018-03-15T00:00:10.0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć sekund do określonej sygnatury czasowej:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

I zwraca ten wynik: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Dodaj liczbę jednostek czasu do sygnatury czasowej.
Zobacz również [getFutureTime ()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*dat*> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do dodania |
| <*timeUnit*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem*: "sekunda", "minuta", "godzina", "dzień", "tydzień", "Month", "Year" |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowane — znacznik czasu*> | Ciąg | Sygnatura czasowa powiększona o określoną liczbę jednostek czasu  |
||||

*Przykład 1*

Ten przykład dodaje jeden dzień do określonego znacznika czasu:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

I zwraca ten wynik: `"2018-01-02T00:00:00.0000000Z"`

*Przykład 2*

Ten przykład dodaje jeden dzień do określonego znacznika czasu:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

I zwraca wynik przy użyciu opcjonalnego formatu "D": `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>oraz

Sprawdź, czy oba wyrażenia mają wartość true.
Zwraca wartość true, jeśli oba wyrażenia są prawdziwe, lub zwraca wartość false, jeśli co najmniej jedno wyrażenie ma wartość false.

```
and(<expression1>, <expression2>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wyrażenie1*>, <*wyrażenie2*> | Tak | Wartość logiczna | Wyrażenia do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, jeśli oba wyrażenia mają wartość true. Zwraca wartość false, jeśli co najmniej jedno wyrażenie ma wartość false. |
||||

*Przykład 1*

Te przykłady sprawdzają, czy określone wartości logiczne są spełnione:

```
and(true, true)
and(false, true)
and(false, false)
```

I zwraca te wyniki:

* Pierwszy przykład: oba wyrażenia są prawdziwe, dlatego zwraca `true` .
* Drugi przykład: jedno wyrażenie ma wartość false, więc zwraca wartość `false` .
* Trzeci przykład: oba wyrażenia mają wartość false, więc zwraca wartość `false` .

*Przykład 2*

Te przykłady sprawdzają, czy określone wyrażenia są spełnione:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

I zwraca te wyniki:

* Pierwszy przykład: oba wyrażenia są prawdziwe, dlatego zwraca `true` .
* Drugi przykład: jedno wyrażenie ma wartość false, więc zwraca wartość `false` .
* Trzeci przykład: oba wyrażenia mają wartość false, więc zwraca wartość `false` .

<a name="array"></a>

### <a name="array"></a>array

Zwraca tablicę z pojedynczego określonego danych wejściowych.
W przypadku wielu danych wejściowych zobacz polecenie " [XmlArray" ()](#createArray).

```
array('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg do tworzenia tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*wartość*>] | Tablica | Tablica zawierająca pojedyncze określone dane wejściowe |
||||

*Przykład*

Ten przykład tworzy tablicę z ciągu "Hello":

```
array('hello')
```

I zwraca ten wynik: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Zwróć wersję z kodowaniem Base64 dla ciągu.

```
base64('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg wejściowy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ciąg Base64*> | Ciąg | Wersja zakodowana algorytmem Base64 dla ciągu wejściowego |
||||

*Przykład*

Ten przykład konwertuje ciąg "Hello" na ciąg szyfrowany algorytmem Base64:

```
base64('hello')
```

I zwraca ten wynik: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Zwróć wersję binarną dla ciągu zakodowanego algorytmem Base64.

```
base64ToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg zakodowany w formacie base64 do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Binary-for-Base64-String*> | Ciąg | Wersja binarna dla ciągu zakodowanego algorytmem Base64 |
||||

*Przykład*

Ten przykład konwertuje ciąg "aGVsbG8 =" zakodowany algorytmem Base64 na ciąg binarny:

```
base64ToBinary('aGVsbG8=')
```

I zwraca ten wynik:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Zwraca wersję ciągu dla ciągu zakodowanego algorytmem Base64, efektywnie dekodowanie ciągu Base64.
Użyj tej funkcji zamiast [decodeBase64 ()](#decodeBase64).
Chociaż obie funkcje działają w ten sam sposób, `base64ToString()` jest preferowane.

```
base64ToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Zakodowany w formacie base64 ciąg do zdekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zdekodowane-Base64-String*> | Ciąg | Wersja ciągu dla ciągu zakodowanego algorytmem Base64 |
||||

*Przykład*

Ten przykład konwertuje ciąg "aGVsbG8 =" zakodowany algorytmem Base64 na tylko ciąg:

```
base64ToString('aGVsbG8=')
```

I zwraca ten wynik: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binarny

Zwróć wersję binarną ciągu.

```
binary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*binarne — wartość wejściowa*> | Ciąg | Wersja binarna określonego ciągu |
||||

*Przykład*

Ten przykład konwertuje ciąg "Hello" na ciąg binarny:

```
binary('hello')
```

I zwraca ten wynik:

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>bool

Zwróć wersję logiczną dla wartości.

```
bool(<value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Dowolne | Wartość do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Wersja logiczna dla określonej wartości |
||||

*Przykład*

Te przykłady umożliwiają konwersję określonych wartości na wartości logiczne:

```
bool(1)
bool(0)
```

I zwraca te wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>łączonych

Zwróć pierwszą wartość inną niż null z co najmniej jednego parametru.
Puste ciągi, puste tablice i puste obiekty nie mają wartości null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>,... | Tak | Any, można mieszać typy | Co najmniej jeden element do sprawdzenia dla wartości null |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*element pierwszy-inny niż null*> | Dowolne | Pierwszy element lub wartość, która nie jest równa null. Jeśli wszystkie parametry mają wartość null, ta funkcja zwraca wartość null. |
||||

*Przykład*

Te przykłady zwracają pierwszą wartość o wartości innej niż null z określonych wartości lub wartości null, jeśli wszystkie wartości mają wartość null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

I zwraca te wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `"hello"`
* Trzeci przykład: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Połącz dwa lub więcej ciągów i zwróć połączony ciąg.

```
concat('<text1>', '<text2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*tekst1*>, <*Tekst2*>,... | Tak | Ciąg | Co najmniej dwa ciągi do połączenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Ciąg | Ciąg utworzony na podstawie połączonych ciągów wejściowych |
||||

*Przykład*

Ten przykład łączy ciągi "Hello" i "World":

```
concat('Hello', 'World')
```

I zwraca ten wynik: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>zawiera

Sprawdź, czy kolekcja zawiera określony element.
Zwraca wartość true, jeśli element zostanie znaleziony lub zwraca wartość false, jeśli nie znaleziono.
Ta funkcja uwzględnia wielkość liter.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

W przypadku tej funkcji działa ona na następujących typach kolekcji:

* *Ciąg* , aby znaleźć *podciąg*
* *Tablica* , aby znaleźć *wartość*
* *Słownik* służący do znajdowania *klucza*

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Ciąg, tablica lub słownik | Kolekcja do sprawdzenia |
| <*wartościami*> | Tak | Odpowiednio ciąg, tablica lub słownik | Element do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, jeśli element zostanie znaleziony. Zwraca wartość false, jeśli nie znaleziono. |
||||

*Przykład 1*

Ten przykład sprawdza ciąg "Hello World" dla podciągu "World" i zwraca wartość true:

```
contains('hello world', 'world')
```

*Przykład 2*

Ten przykład sprawdza ciąg "Hello World" dla podciągu "Universe" i zwraca wartość false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Przekształć sygnaturę czasową od uniwersalnego czasu koordynowanego (UTC) na docelową strefę czasową.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*destinationTimeZone*> | Tak | Ciąg | Nazwa docelowej strefy czasowej. W przypadku nazw stref czasowych zobacz [wartości indeksów strefy czasowej firmy Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale może być konieczne usunięcie znaków interpunkcyjnych z nazwy strefy czasowej. |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*przekonwertowano sygnaturę czasową*> | Ciąg | Sygnatura czasowa konwertowana na docelową strefę czasową |
||||

*Przykład 1*

Ten przykład konwertuje sygnaturę czasową na określoną strefę czasową:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

I zwraca ten wynik: `"2018-01-01T00:00:00Z"`

*Przykład 2*

Ten przykład konwertuje sygnaturę czasową na określoną strefę czasową i format:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Przekonwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*sourceTimeZone*> | Tak | Ciąg | Nazwa źródłowej strefy czasowej. W przypadku nazw stref czasowych zobacz [wartości indeksów strefy czasowej firmy Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale może być konieczne usunięcie znaków interpunkcyjnych z nazwy strefy czasowej. |
| <*destinationTimeZone*> | Tak | Ciąg | Nazwa docelowej strefy czasowej. W przypadku nazw stref czasowych zobacz [wartości indeksów strefy czasowej firmy Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale może być konieczne usunięcie znaków interpunkcyjnych z nazwy strefy czasowej. |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*przekonwertowano sygnaturę czasową*> | Ciąg | Sygnatura czasowa konwertowana na docelową strefę czasową |
||||

*Przykład 1*

Ten przykład konwertuje źródłową strefę czasową na docelową strefę czasową:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

I zwraca ten wynik: `"2018-01-01T00:00:00.0000000"`

*Przykład 2*

Ten przykład konwertuje strefę czasową na określoną strefę czasową i format:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na uniwersalne czas uniwersalny (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*sourceTimeZone*> | Tak | Ciąg | Nazwa źródłowej strefy czasowej. W przypadku nazw stref czasowych zobacz [wartości indeksów strefy czasowej firmy Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale może być konieczne usunięcie znaków interpunkcyjnych z nazwy strefy czasowej. |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*przekonwertowano sygnaturę czasową*> | Ciąg | Sygnatura czasowa konwertowana na UTC |
||||

*Przykład 1*

Ten przykład konwertuje sygnaturę czasową na czas UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

I zwraca ten wynik: `"2018-01-01T08:00:00.0000000Z"`

*Przykład 2*

Ten przykład konwertuje sygnaturę czasową na czas UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>przearray

Zwróć tablicę z wielu danych wejściowych.
Dla pojedynczych tablic wejściowych zobacz [Array ()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*obiekt1*>, <*Obiekt2*>,... | Tak | Wszystkie, ale nie mieszane | Co najmniej dwa elementy, aby utworzyć tablicę |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*obiekt1*>, <*Obiekt2*>,...] | Tablica | Tablica utworzona na podstawie wszystkich elementów wejściowych |
||||

*Przykład*

Ten przykład tworzy tablicę z następujących danych wejściowych:

```
createArray('h', 'e', 'l', 'l', 'o')
```

I zwraca ten wynik: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Zwróć identyfikator URI (Uniform Resource Identifier) danych dla ciągu.

```
dataUri('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Identyfikator URI danych*> | Ciąg | Identyfikator URI danych dla ciągu wejściowego |
||||

*Przykład*

W tym przykładzie tworzony jest identyfikator URI danych dla ciągu "Hello":

```
dataUri('hello')
```

I zwraca ten wynik: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Zwróć wersję binarną dla identyfikatora URI (Uniform Resource Identifier) danych.
Użyj tej funkcji zamiast [decodeDataUri ()](#decodeDataUri).
Chociaż obie funkcje działają w ten sam sposób, `dataUriBinary()` jest preferowane.

```
dataUriToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Identyfikator URI danych do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Binary-for-Data-URI*> | Ciąg | Wersja binarna identyfikatora URI danych |
||||

*Przykład*

W tym przykładzie tworzona jest wersja binarna dla tego identyfikatora URI danych:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Zwraca wersję ciągu dla identyfikatora URI (Uniform Resource Identifier) danych.

```
dataUriToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Identyfikator URI danych do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ciąg-for-Data-URI*> | Ciąg | Wersja ciągu dla identyfikatora URI danych |
||||

*Przykład*

Ten przykład tworzy ciąg dla tego identyfikatora URI danych:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Zwróć dzień miesiąca z sygnatury czasowej.

```
dayOfMonth('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień miesiąca*> | Liczba całkowita | Dzień miesiąca od określonej sygnatury czasowej |
||||

*Przykład*

Ten przykład zwraca liczbę dla dnia miesiąca z tej sygnatury czasowej:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

I zwraca ten wynik: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Zwróć dzień tygodnia z sygnatury czasowej.

```
dayOfWeek('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień tygodnia*> | Liczba całkowita | Dzień tygodnia od określonej sygnatury czasowej, gdzie Niedziela ma wartość 0, poniedziałek wynosi 1 itd. |
||||

*Przykład*

Ten przykład zwraca liczbę dni tygodnia od tej sygnatury czasowej:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

I zwraca ten wynik: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Zwróć dzień roku z sygnatury czasowej.

```
dayOfYear('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień roku*> | Liczba całkowita | Dzień roku od określonej sygnatury czasowej |
||||

*Przykład*

Ten przykład zwraca numer dnia roku z tej sygnatury czasowej:

```
dayOfYear('2018-03-15T13:27:36Z')
```

I zwraca ten wynik: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Zwraca wersję ciągu dla ciągu zakodowanego algorytmem Base64, efektywnie dekodowanie ciągu Base64.
Rozważ użycie [base64ToString ()](#base64ToString) zamiast `decodeBase64()` .
Chociaż obie funkcje działają w ten sam sposób, `base64ToString()` jest preferowane.

```
decodeBase64('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Zakodowany w formacie base64 ciąg do zdekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zdekodowane-Base64-String*> | Ciąg | Wersja ciągu dla ciągu zakodowanego algorytmem Base64 |
||||

*Przykład*

Ten przykład tworzy ciąg dla ciągu zakodowanego algorytmem Base64:

```
decodeBase64('aGVsbG8=')
```

I zwraca ten wynik: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Zwróć wersję binarną dla identyfikatora URI (Uniform Resource Identifier) danych.
Rozważ użycie [dataUriToBinary ()](#dataUriToBinary), a nie `decodeDataUri()` .
Chociaż obie funkcje działają w ten sam sposób, `dataUriToBinary()` jest preferowane.

```
decodeDataUri('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg identyfikatora URI danych do zdekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Binary-for-Data-URI*> | Ciąg | Wersja binarna ciągu identyfikatora URI danych |
||||

*Przykład*

Ten przykład zwraca wersję binarną dla tego identyfikatora URI danych:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Zwraca ciąg, który zastępuje znaki ucieczki z zdekodowanymi wersjami.

```
decodeUriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg znaków ucieczki do zdekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zdekodowane-URI*> | Ciąg | Zaktualizowany ciąg z zdekodowanymi znakami ucieczki |
||||

*Przykład*

Ten przykład zastępuje znaki ucieczki w tym ciągu z zdekodowanymi wersjami:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Zwraca liczbę całkowitą z wyniku dzielenia dwóch liczb.
Aby uzyskać resztę, zobacz [mod ()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*płacone*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba do podzielenia przez *dzielnik* |
| <*dzielnik*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba dzieląca *dzielną*, ale nie może być równa 0. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wynik ilorazu*> | Liczba całkowita | Wynik z wartości całkowitej dzielącej pierwszą liczbę przez drugą liczbę |
||||

*Przykład*

Oba przykłady dzielą pierwszą liczbę o drugą liczbę:

```
div(10, 5)
div(11, 5)
```

I zwróć ten wynik: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeURIComponent —

Zwróć jednolity identyfikator zasobów (URI) dla ciągu przez zastępowanie znaków w adresie URL bez znaku ucieczki.
Rozważ użycie [uriComponent ()](#uriComponent), a nie `encodeUriComponent()` .
Chociaż obie funkcje działają w ten sam sposób, `uriComponent()` jest preferowane.

```
encodeUriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg do przekonwertowania na format zakodowany przy użyciu identyfikatora URI |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zakodowany identyfikator URI*> | Ciąg | Ciąg znaków w formacie URI z znakami ucieczki |
||||

*Przykład*

W tym przykładzie tworzona jest wersja zakodowana przy użyciu identyfikatora URI dla tego ciągu:

```
encodeUriComponent('https://contoso.com')
```

I zwraca ten wynik: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>puste

Sprawdź, czy kolekcja jest pusta.
Zwraca wartość true, jeśli kolekcja jest pusta lub zwraca wartość false, jeśli nie jest pusta.

```
empty('<collection>')
empty([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Ciąg, tablica lub obiekt | Kolekcja do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, jeśli kolekcja jest pusta. Zwraca wartość false, jeśli nie jest pusta. |
||||

*Przykład*

Te przykłady sprawdzają, czy określone kolekcje są puste:

```
empty('')
empty('abc')
```

I zwraca te wyniki:

* Pierwszy przykład: przekazuje pusty ciąg, aby funkcja zwracała wartość `true` .
* Drugi przykład: przekazuje ciąg "ABC", więc funkcja zwraca wartość `false` .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Sprawdź, czy ciąg jest kończący się określonym podciągiem.
Zwraca wartość true, jeśli znaleziono podciąg lub zwraca wartość false, jeśli nie znaleziono.
Ta funkcja nie uwzględnia wielkości liter.

```
endsWith('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Tak | Ciąg | Ciąg do sprawdzenia |
| <*Tekstprzeszukiwany*> | Tak | Ciąg | Końcowy podciąg do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false  | Wartość logiczna | Zwraca wartość true, gdy zostanie znaleziony końcowy podciąg. Zwraca wartość false, jeśli nie znaleziono. |
||||

*Przykład 1*

Ten przykład sprawdza, czy ciąg "Hello World" jest zakończony ciągiem "World":

```
endsWith('hello world', 'world')
```

I zwraca ten wynik: `true`

*Przykład 2*

Ten przykład sprawdza, czy ciąg "Hello World" jest zakończony ciągiem "Universe":

```
endsWith('hello world', 'universe')
```

I zwraca ten wynik: `false`

<a name="equals"></a>

### <a name="equals"></a>equals

Sprawdź, czy obie wartości, wyrażenia lub obiekty są równoważne.
Zwraca wartość true, jeśli oba są równoważne, lub zwraca wartość false, jeśli nie są równoważne.

```
equals('<object1>', '<object2>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*obiekt1*>, <*Obiekt2*> | Tak | Poszczególne | Wartości, wyrażenia lub obiekty do porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, jeśli oba są równoważne. Zwraca wartość false, jeśli nie jest równoważne. |
||||

*Przykład*

Te przykłady sprawdzają, czy określone dane wejściowe są równoważne.

```
equals(true, 1)
equals('abc', 'abcd')
```

I zwraca te wyniki:

* Pierwszy przykład: obie wartości są równoważne, więc funkcja zwraca wartość `true` .
* Drugi przykład: obie wartości nie są równoważne, więc funkcja zwraca wartość `false` .

<a name="first"></a>

### <a name="first"></a>pierwszego

Zwróć pierwszy element z ciągu lub tablicy.

```
first('<collection>')
first([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Ciąg lub tablica | Kolekcja, w której ma zostać znaleziony pierwszy element. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*pierwsza kolekcja-element*> | Dowolne | Pierwszy element w kolekcji |
||||

*Przykład*

W tych przykładach znaleziono pierwszy element w tych kolekcjach:

```
first('hello')
first(createArray(0, 1, 2))
```

I zwróć następujące wyniki:

* Pierwszy przykład: `"h"`
* Drugi przykład: `0`

<a name="float"></a>

### <a name="float"></a>float

Konwertuj wersję ciągu dla liczby zmiennoprzecinkowej na rzeczywistą liczbę zmiennoprzecinkową.

```
float('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg, który ma prawidłową liczbę zmiennoprzecinkową do przekonwertowania. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Wartość zmiennoprzecinkowa*> | Float | Liczba zmiennoprzecinkowa dla podanego ciągu. |
||||

*Przykład*

W tym przykładzie tworzona jest wersja ciągu dla tej liczby zmiennoprzecinkowej:

```
float('10.333')
```

I zwraca ten wynik: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Zwróć sygnaturę czasową w określonym formacie.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ponowne formatowanie — znacznik czasu*> | Ciąg | Zaktualizowana sygnatura czasowa w określonym formacie |
||||

*Przykład*

Ten przykład konwertuje sygnaturę czasową do określonego formatu:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

I zwraca ten wynik: `"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Zwróć bieżącą sygnaturę czasową powiększoną o określoną liczbę jednostek czasu.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*dat*> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do dodania |
| <*timeUnit*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem*: "sekunda", "minuta", "godzina", "dzień", "tydzień", "Month", "Year" |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowane — znacznik czasu*> | Ciąg | Bieżąca sygnatura czasowa powiększona o określoną liczbę jednostek czasu |
||||

*Przykład 1*

Załóżmy, że bieżąca sygnatura czasowa to "2018 r-03-01T00:00:00.0000000 Z".
Ten przykład dodaje pięć dni do tej sygnatury czasowej:

```
getFutureTime(5, 'Day')
```

I zwraca ten wynik: `"2018-03-06T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że bieżąca sygnatura czasowa to "2018 r-03-01T00:00:00.0000000 Z".
Ten przykład dodaje pięć dni i konwertuje wynik na format "D":

```
getFutureTime(5, 'Day', 'D')
```

I zwraca ten wynik: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Zwróć bieżącą sygnaturę czasową minus określoną liczbę jednostek czasu.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*dat*> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do odjęcia |
| <*timeUnit*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem*: "sekunda", "minuta", "godzina", "dzień", "tydzień", "Month", "Year" |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowane — znacznik czasu*> | Ciąg | Bieżąca sygnatura czasowa pomniejszona o określoną liczbę jednostek czasu |
||||

*Przykład 1*

Załóżmy, że bieżąca sygnatura czasowa to "2018 r-02-01T00:00:00.0000000 Z".
Ten przykład odejmuje pięć dni od tej sygnatury czasowej:

```
getPastTime(5, 'Day')
```

I zwraca ten wynik: `"2018-01-27T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że bieżąca sygnatura czasowa to "2018 r-02-01T00:00:00.0000000 Z".
Ten przykład odejmuje pięć dni i konwertuje wynik na format "D":

```
getPastTime(5, 'Day', 'D')
```

I zwraca ten wynik: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Sprawdź, czy pierwsza wartość jest większa od drugiej wartości.
Zwraca wartość true, jeśli pierwsza wartość jest większa lub zwraca wartość false, jeśli jest mniejsza.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Liczba całkowita, zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy większa niż druga wartość |
| <*compareTo*> | Tak | Odpowiednio liczby całkowite, zmiennoprzecinkowe lub ciąg | Wartość porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, jeśli pierwsza wartość jest większa niż druga wartość. Zwraca wartość false, gdy pierwsza wartość jest równa lub mniejsza od drugiej wartości. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest większa niż druga wartość:

```
greater(10, 5)
greater('apple', 'banana')
```

I zwróć następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Sprawdź, czy pierwsza wartość jest większa lub równa drugiej wartości.
Zwraca wartość true, jeśli pierwsza wartość jest większa lub równa lub zwraca wartość false, gdy pierwsza wartość jest mniejsza.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Liczba całkowita, zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy większa lub równa drugiej wartości |
| <*compareTo*> | Tak | Odpowiednio liczby całkowite, zmiennoprzecinkowe lub ciąg | Wartość porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, jeśli pierwsza wartość jest większa lub równa drugiej wartości. Zwraca wartość false, jeśli pierwsza wartość jest mniejsza od drugiej wartości. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest większa lub równa drugiej wartości:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

I zwróć następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Generuj unikatowy identyfikator globalny (GUID) jako ciąg, na przykład "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Ponadto można określić inny format dla identyfikatora GUID innego niż format domyślny, "D", który jest 32 cyfr oddzielonych łącznikiem.

```
guid('<format>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Formatowanie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu](/dotnet/api/system.guid.tostring) dla ZWRÓCONEGO identyfikatora GUID. Domyślnie formatem jest "D", ale można użyć "N", "D", "B", "P" lub "X". |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Wartość identyfikatora GUID*> | Ciąg | Losowo wygenerowany identyfikator GUID |
||||

*Przykład*

Ten przykład generuje ten sam identyfikator GUID, ale jako 32 cyfr, rozdzielone łącznikami i ujęte w nawiasy:

```
guid('P')
```

I zwraca ten wynik: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Sprawdź, czy wyrażenie ma wartość true lub false.
W oparciu o wynik Zwraca określoną wartość.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wyrażenia*> | Tak | Wartość logiczna | Wyrażenie do sprawdzenia |
| <*valueIfTrue*> | Tak | Dowolne | Wartość, która ma zostać zwrócona, gdy wyrażenie ma wartość true. |
| <*valueIfFalse*> | Tak | Dowolne | Wartość, która ma zostać zwrócona, gdy wyrażenie ma wartość false. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*określona — zwraca wartość*> | Dowolne | Określona wartość, która zwraca w zależności od tego, czy wyrażenie ma wartość Prawda czy fałsz. |
||||

*Przykład*

Ten przykład zwraca, `"yes"` ponieważ określone wyrażenie zwraca wartość true.
W przeciwnym razie przykład zwraca `"no"` :

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Zwraca pozycję początkową lub wartość indeksu podciągu.
W tej funkcji nie jest rozróżniana wielkość liter, a indeksy zaczynają się od liczby 0.

```
indexOf('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Tak | Ciąg | Ciąg, który zawiera podciąg do znalezienia |
| <*Tekstprzeszukiwany*> | Tak | Ciąg | Podciąg do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość indeksu*>| Liczba całkowita | Pozycja początkowa lub wartość indeksu dla podanego podciągu. <p>Jeśli ciąg nie zostanie znaleziony, Zwróć liczbę-1. |
||||

*Przykład*

W tym przykładzie znaleziono początkową wartość indeksu dla podciągu "World" w ciągu "Hello World":

```
indexOf('hello world', 'world')
```

I zwraca ten wynik: `6`

<a name="int"></a>

### <a name="int"></a>int

Zwróć wersję całkowitą dla ciągu.

```
int('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Liczba całkowita-wynik*> | Liczba całkowita | Wersja całkowita dla podanego ciągu |
||||

*Przykład*

Ten przykład tworzy wersję całkowitą dla ciągu "10":

```
int('10')
```

I zwraca ten wynik: `10`

<a name="json"></a>

### <a name="json"></a>json

Zwróć wartość typu JavaScript Object Notation (JSON) lub obiekt dla ciągu lub XML.

```
json('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg lub XML | Ciąg lub XML do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Wynik JSON*> | Natywny typ lub obiekt JSON | Wartość lub obiekt natywnego typu JSON dla określonego ciągu lub XML. Jeśli ciąg ma wartość null, funkcja zwraca pusty obiekt. |
||||

*Przykład 1*

Ten przykład konwertuje ten ciąg na wartość JSON:

```
json('[1, 2, 3]')
```

I zwraca ten wynik: `[1, 2, 3]`

*Przykład 2*

Ten przykład konwertuje ten ciąg na format JSON:

```
json('{"fullName": "Sophia Owen"}')
```

I zwraca ten wynik:

```
{
  "fullName": "Sophia Owen"
}
```

*Przykład 3*

Ten przykład konwertuje ten kod XML na format JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

I zwraca ten wynik:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>część wspólną

Zwróć kolekcję, która ma *tylko* wspólne elementy w określonej kolekcji.
Aby pojawił się w wyniku, element musi znajdować się we wszystkich kolekcjach przekazaniu do tej funkcji.
Jeśli co najmniej jeden element ma taką samą nazwę, w wyniku zostanie wyświetlony ostatni element o tej nazwie.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*kolekcji collection1*>, <*Collection2*>,... | Tak | Tablica lub obiekt, ale nie oba | Kolekcje, z których mają być *tylko* wspólne elementy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*elementy wspólne*> | Odpowiednio tablica lub obiekt | Kolekcja, która ma tylko wspólne elementy w określonej kolekcji |
||||

*Przykład*

Ten przykład umożliwia znalezienie typowych elementów w następujących tablicach:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

I zwraca tablicę zawierającą *tylko* te elementy: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Zwraca ciąg, który zawiera wszystkie elementy z tablicy i zawiera każdy znak oddzielony *ogranicznikiem*.

```
join([<collection>], '<delimiter>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Tablica | Tablica zawierająca elementy do przyłączenia. |
| <*ogranicznik*> | Tak | Ciąg | Separator, który pojawia się między poszczególnymi znakami w ciągu wynikiem |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*CHAR1* >< *ogranicznik* >< *CHAR2* ><> *ogranicznika* ... | Ciąg | Powstały ciąg utworzony na podstawie wszystkich elementów w określonej tablicy. |
||||

*Przykład*

Ten przykład tworzy ciąg ze wszystkich elementów w tej tablicy z określonym znakiem jako ogranicznikiem:

```
join(createArray('a', 'b', 'c'), '.')
```

I zwraca ten wynik: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>ostatni

Zwróć ostatni element z kolekcji.

```
last('<collection>')
last([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Ciąg lub tablica | Kolekcja, w której ma zostać znaleziony ostatni element |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Ostatnia kolekcja-element*> | Odpowiednio ciąg lub tablicę | Ostatni element w kolekcji |
||||

*Przykład*

Te przykłady znajdują ostatni element w tych kolekcjach:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

I zwraca te wyniki:

* Pierwszy przykład: `"d"`
* Drugi przykład: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Zwraca pozycję początkową lub wartość indeksu dla ostatniego wystąpienia podciągu.
W tej funkcji nie jest rozróżniana wielkość liter, a indeksy zaczynają się od liczby 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Tak | Ciąg | Ciąg, który zawiera podciąg do znalezienia |
| <*Tekstprzeszukiwany*> | Tak | Ciąg | Podciąg do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość końcowa indeksu*> | Liczba całkowita | Pozycja początkowa lub wartość indeksu dla ostatniego wystąpienia określonego podciągu. <p>Jeśli ciąg nie zostanie znaleziony, Zwróć liczbę-1. |
||||

*Przykład*

W tym przykładzie znaleziono początkową wartość indeksu dla ostatniego wystąpienia podciągu "World" w ciągu "Hello World":

```
lastIndexOf('hello world', 'world')
```

I zwraca ten wynik: `6`

<a name="length"></a>

### <a name="length"></a>length

Zwraca liczbę elementów w kolekcji.

```
length('<collection>')
length([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Ciąg lub tablica | Kolekcja zawierająca elementy do zliczenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Długość-lub-liczba*> | Liczba całkowita | Liczba elementów w kolekcji |
||||

*Przykład*

Te przykłady zliczają liczbę elementów w tych kolekcjach:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

I zwróć ten wynik: `4`

<a name="less"></a>

### <a name="less"></a>less

Sprawdź, czy pierwsza wartość jest mniejsza od drugiej wartości.
Zwraca wartość true, jeśli pierwsza wartość jest mniejsza lub zwraca wartość false, gdy pierwsza wartość jest większa.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Liczba całkowita, zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy mniejsza od drugiej wartości |
| <*compareTo*> | Tak | Odpowiednio liczby całkowite, zmiennoprzecinkowe lub ciąg | Element porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, jeśli pierwsza wartość jest mniejsza od drugiej wartości. Zwraca wartość false, gdy pierwsza wartość jest równa lub większa od drugiej wartości. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest mniejsza od drugiej wartości.

```
less(5, 10)
less('banana', 'apple')
```

I zwróć następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Sprawdź, czy pierwsza wartość jest mniejsza lub równa drugiej wartości.
Zwraca wartość true, jeśli pierwsza wartość jest mniejsza lub równa lub zwraca wartość false, gdy pierwsza wartość jest większa.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Liczba całkowita, zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy mniejsza lub równa drugiej wartości |
| <*compareTo*> | Tak | Odpowiednio liczby całkowite, zmiennoprzecinkowe lub ciąg | Element porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false  | Wartość logiczna | Zwraca wartość true, jeśli pierwsza wartość jest mniejsza lub równa drugiej wartości. Zwraca wartość false, gdy pierwsza wartość jest większa niż druga wartość. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest mniejsza lub równa drugiej wartości.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

I zwróć następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="max"></a>

### <a name="max"></a>max

Zwraca najwyższą wartość z listy lub tablicy z liczbami, które są włącznie na obu końcach.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*liczba1*>, <*liczba2*>,... | Tak | Liczba całkowita, zmiennoprzecinkowa lub oba | Zbiór liczb, z których ma być najwyższa wartość |
| [<*liczba1*>, <*liczba2*>,...] | Tak | Array-Integer, float lub Both | Tablica liczb, z której ma być najwyższa wartość |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Max-Value*> | Liczba całkowita lub zmiennoprzecinkowa | Najwyższa wartość w określonej tablicy lub zestawie liczb |
||||

*Przykład*

Te przykłady uzyskują najwyższą wartość z zestawu liczb i tablicy:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

I zwróć ten wynik: `3`

<a name="min"></a>

### <a name="min"></a>min

Zwróć najniższą wartość z zestawu liczb lub tablicy.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*liczba1*>, <*liczba2*>,... | Tak | Liczba całkowita, zmiennoprzecinkowa lub oba | Zbiór liczb, z których ma być najmniejsza wartość. |
| [<*liczba1*>, <*liczba2*>,...] | Tak | Array-Integer, float lub Both | Tablica liczb, z której ma być najmniejsza wartość. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość minimalna*> | Liczba całkowita lub zmiennoprzecinkowa | Najniższa wartość w określonym zestawie liczb lub określonej tablicy. |
||||

*Przykład*

Te przykłady uzyskują najniższą wartość w zestawie liczb i tablicy:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

I zwróć ten wynik: `1`

<a name="mod"></a>

### <a name="mod"></a>Funkcja

Zwróć resztę z dzielenia dwóch liczb.
Aby uzyskać wynik całkowity, zobacz [DIV ()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*płacone*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba do podzielenia przez *dzielnik* |
| <*dzielnik*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba dzieląca *dzielną*, ale nie może być równa 0. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*modulo — wynik*> | Liczba całkowita lub zmiennoprzecinkowa | Reszta z dzielenia pierwszej liczby przez drugą liczbę |
||||

*Przykład*

Ten przykład dzieli pierwszą liczbę przez drugą liczbę:

```
mod(3, 2)
```

I zwróć ten wynik: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Zwróć produkt z mnożenia dwóch liczb.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba do pomnożenia przez *multiplicand2* |
| <*multiplicand2*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba, która *multiplicand1* wielokrotność |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*produkt — wynik*> | Liczba całkowita lub zmiennoprzecinkowa | Produkt nie pomnożyć pierwszej liczby przez drugą liczbę |
||||

*Przykład*

Te przykłady wielokrotności pierwszej liczby przez drugą liczbę:

```
mul(1, 2)
mul(1.5, 2)
```

I zwróć następujące wyniki:

* Pierwszy przykład: `2`
* Drugi przykład `3`

<a name="not"></a>

### <a name="not"></a>not

Sprawdź, czy wyrażenie ma wartość false.
Zwraca wartość true, jeśli wyrażenie ma wartość false, lub zwraca wartość false, gdy wartość jest równa true.

```json
not(<expression>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wyrażenia*> | Tak | Wartość logiczna | Wyrażenie do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, jeśli wyrażenie ma wartość false. Zwraca wartość false, jeśli wyrażenie ma wartość true. |
||||

*Przykład 1*

Te przykłady sprawdzają, czy określone wyrażenia mają wartość false:

```json
not(false)
not(true)
```

I zwróć następujące wyniki:

* Pierwszy przykład: wyrażenie ma wartość false, więc funkcja zwraca wartość `true` .
* Drugi przykład: wyrażenie ma wartość true, więc funkcja zwraca wartość `false` .

*Przykład 2*

Te przykłady sprawdzają, czy określone wyrażenia mają wartość false:

```json
not(equals(1, 2))
not(equals(1, 1))
```

I zwróć następujące wyniki:

* Pierwszy przykład: wyrażenie ma wartość false, więc funkcja zwraca wartość `true` .
* Drugi przykład: wyrażenie ma wartość true, więc funkcja zwraca wartość `false` .

<a name="or"></a>

### <a name="or"></a>lub

Sprawdź, czy co najmniej jedno wyrażenie ma wartość true.
Zwraca wartość true, jeśli co najmniej jedno wyrażenie ma wartość true lub zwraca wartość false, jeśli oba mają wartość false.

```
or(<expression1>, <expression2>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wyrażenie1*>, <*wyrażenie2*> | Tak | Wartość logiczna | Wyrażenia do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, jeśli co najmniej jedno wyrażenie ma wartość true. Zwraca wartość false, jeśli oba wyrażenia mają wartość false. |
||||

*Przykład 1*

Te przykłady sprawdzają, czy co najmniej jedno wyrażenie jest prawdziwe:

```json
or(true, false)
or(false, false)
```

I zwróć następujące wyniki:

* Pierwszy przykład: co najmniej jedno wyrażenie ma wartość true, więc funkcja zwraca wartość `true` .
* Drugi przykład: oba wyrażenia mają wartość false, więc funkcja zwraca wartość `false` .

*Przykład 2*

Te przykłady sprawdzają, czy co najmniej jedno wyrażenie jest prawdziwe:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

I zwróć następujące wyniki:

* Pierwszy przykład: co najmniej jedno wyrażenie ma wartość true, więc funkcja zwraca wartość `true` .
* Drugi przykład: oba wyrażenia mają wartość false, więc funkcja zwraca wartość `false` .

<a name="rand"></a>

### <a name="rand"></a>rand

Zwraca losową liczbę całkowitą z podanego zakresu, która jest dopuszczająca tylko do końca początkowego.

```
rand(<minValue>, <maxValue>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Tak | Liczba całkowita | Najmniejsza liczba całkowita z zakresu |
| <*maxValue*> | Tak | Liczba całkowita | Liczba całkowita, która następuje po największej liczbie całkowitej z zakresu, który może zwracać funkcja |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wynik losowy*> | Liczba całkowita | Losowa liczba całkowita zwrócona z określonego zakresu |
||||

*Przykład*

Ten przykład pobiera losową liczbę całkowitą z podanego zakresu, z wyłączeniem wartości maksymalnej:

```
rand(1, 5)
```

I zwraca jedną z tych liczb jako wynik: `1` , `2` , `3` lub `4`

<a name="range"></a>

### <a name="range"></a>range

Zwróć tablicę liczb całkowitych, która zaczyna się od określonej liczby całkowitej.

```
range(<startIndex>, <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Indeks*> | Tak | Liczba całkowita | Wartość całkowita, która uruchamia tablicę jako pierwszy element |
| <*liczbą*> | Tak | Liczba całkowita | Liczba liczb całkowitych w tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*zakres — wynik*>] | Tablica | Tablica z liczbami całkowitymi rozpoczynającymi się od określonego indeksu |
||||

*Przykład*

Ten przykład tworzy tablicę liczb całkowitych, która zaczyna się od określonego indeksu i ma określoną liczbę liczb całkowitych:

```
range(1, 4)
```

I zwraca ten wynik: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>Zastąp

Zamień podciąg na określony ciąg i zwróć ciąg wynikowy. Ta funkcja uwzględnia wielkość liter.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Tak | Ciąg | Ciąg zawierający podciąg, który ma zostać zamieniony. |
| <*oldText*> | Tak | Ciąg | Podciąg, który ma zostać zamieniony. |
| <*newText*> | Tak | Ciąg | Ciąg zamienny |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany — tekst*> | Ciąg | Zaktualizowany ciąg po zamianie podciągu <p>Jeśli podciąg nie zostanie znaleziony, zwróć oryginalny ciąg. |
||||

*Przykład*

Ten przykład wyszukuje "stary" podciąg w "starym ciągu" i zamienia "stary" z "New":

```
replace('the old string', 'old', 'new')
```

I zwraca ten wynik: `"the new string"`

<a name="skip"></a>

### <a name="skip"></a>Pomiń

Usuń elementy z przodu kolekcji i zwróć *wszystkie pozostałe* elementy.

```
skip([<collection>], <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Tablica | Kolekcja, której elementy chcesz usunąć. |
| <*liczbą*> | Tak | Liczba całkowita | Dodatnia liczba całkowita liczby elementów do usunięcia na przedniej |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*Zaktualizowano — zbieranie*>] | Tablica | Zaktualizowana kolekcja po usunięciu określonych elementów |
||||

*Przykład*

Ten przykład usuwa jeden element, liczbę 0 z przodu określonej tablicy:

```
skip(createArray(0, 1, 2, 3), 1)
```

I zwraca tę tablicę z pozostałymi elementami: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Zwraca tablicę zawierającą podciągi, rozdzieloną przecinkami, na podstawie określonego znaku ogranicznika w oryginalnym ciągu.

```
split('<text>', '<delimiter>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Tak | Ciąg | Ciąg do rozdzielenia na podciągi na podstawie określonego ogranicznika w oryginalnym ciągu |
| <*ogranicznik*> | Tak | Ciąg | Znak w oryginalnym ciągu, który ma być używany jako ogranicznik |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*podciąg1*>, <*subciąg2*>,...] | Tablica | Tablica zawierająca podciągi z oryginalnego ciągu, rozdzielona przecinkami |
||||

*Przykład*

Ten przykład tworzy tablicę z podciągami z określonego ciągu na podstawie określonego znaku jako ogranicznika:

```
split('a_b_c', '_')
```

I zwraca tę tablicę jako wynik: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Zwróć początek dnia dla sygnatury czasowej.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowane — znacznik czasu*> | Ciąg | Określona sygnatura czasowa, ale rozpoczynająca się od znaku zero godziny na dzień |
||||

*Przykład*

Ten przykład umożliwia znalezienie początku dnia dla tej sygnatury czasowej:

```
startOfDay('2018-03-15T13:30:30Z')
```

I zwraca ten wynik: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>Początek godziny

Zwróć początek godziny dla sygnatury czasowej.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowane — znacznik czasu*> | Ciąg | Określona sygnatura czasowa, ale rozpoczynająca się od znaku 0 minuty dla godziny |
||||

*Przykład*

Ten przykład umożliwia znalezienie początku godziny dla tej sygnatury czasowej:

```
startOfHour('2018-03-15T13:30:30Z')
```

I zwraca ten wynik: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Zwróć początek miesiąca dla sygnatury czasowej.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowane — znacznik czasu*> | Ciąg | Określona sygnatura czasowa, ale rozpoczyna się pierwszego dnia miesiąca w znaku 0-godzinnym |
||||

*Przykład*

Ten przykład zwraca początek miesiąca dla tej sygnatury czasowej:

```
startOfMonth('2018-03-15T13:30:30Z')
```

I zwraca ten wynik: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Sprawdź, czy ciąg rozpoczyna się od określonego podciągu.
Zwraca wartość true, jeśli znaleziono podciąg lub zwraca wartość false, jeśli nie znaleziono.
Ta funkcja nie uwzględnia wielkości liter.

```
startsWith('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Tak | Ciąg | Ciąg do sprawdzenia |
| <*Tekstprzeszukiwany*> | Tak | Ciąg | Ciąg początkowy do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false  | Wartość logiczna | Zwraca wartość true, gdy zostanie znaleziony początkowy podciąg. Zwraca wartość false, jeśli nie znaleziono. |
||||

*Przykład 1*

Ten przykład sprawdza, czy ciąg "Hello World" rozpoczyna się od ciągu "Hello":

```
startsWith('hello world', 'hello')
```

I zwraca ten wynik: `true`

*Przykład 2*

Ten przykład sprawdza, czy ciąg "Hello World" rozpoczyna się od ciągu "Greetings":

```
startsWith('hello world', 'greetings')
```

I zwraca ten wynik: `false`

<a name="string"></a>

### <a name="string"></a>ciąg

Zwraca wersję ciągu dla wartości.

```
string(<value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Dowolne | Wartość do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość ciągu*> | Ciąg | Wersja ciągu dla określonej wartości. |
||||

*Przykład 1*

W tym przykładzie zostanie utworzona wersja ciągu dla tej liczby:

```
string(10)
```

I zwraca ten wynik: `"10"`

*Przykład 2*

Ten przykład tworzy ciąg dla określonego obiektu JSON i używa znaku ukośnika odwrotnego ( \\ ) jako znaku ucieczki dla podwójnego znaku cudzysłowu (").

```
string( { "name": "Sophie Owen" } )
```

I zwraca ten wynik: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>Sub

Zwraca wynik odejmowania drugiej liczby od pierwszej liczby.

```
sub(<minuend>, <subtrahend>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*odjemna*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba, z której ma zostać odjęta *odjemnik* |
| <*odjemnik*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba, która ma zostać odjęta od *odjemna* |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wynika*> | Liczba całkowita lub zmiennoprzecinkowa | Wynik odejmowania drugiej liczby od pierwszej liczby |
||||

*Przykład*

Ten przykład odejmuje drugą liczbę od pierwszej liczby:

```
sub(10.3, .3)
```

I zwraca ten wynik: `10`

<a name="substring"></a>

### <a name="substring"></a>podciąg

Zwraca znaki z ciągu, zaczynając od określonej pozycji lub indeksu.
Wartości indeksu zaczynają się od liczby 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Tak | Ciąg | Ciąg, którego znaki mają być potrzebne. |
| <*Indeks*> | Tak | Liczba całkowita | Liczba dodatnia równa lub większa od 0, która ma być używana jako pozycja początkowa lub wartość indeksu. |
| <*Długość*> | Tak | Liczba całkowita | Dodatnia liczba znaków, które mają być używane w podciągu. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*podciąg — wynik*> | Ciąg | Podciąg z określoną liczbą znaków, zaczynając od określonej pozycji indeksu w ciągu źródłowym. |
||||

*Przykład*

Ten przykład tworzy znak podciągu pięć znaków z określonego ciągu, rozpoczynając od wartości indeksu 6:

```
substring('hello world', 6, 5)
```

I zwraca ten wynik: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Odejmij wiele jednostek czasu od sygnatury czasowej.
Zobacz również [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*dat*> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do odjęcia |
| <*timeUnit*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem*: "sekunda", "minuta", "godzina", "dzień", "tydzień", "Month", "Year" |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowane — znacznik czasu*> | Ciąg | Sygnatura czasowa pomniejszona o określoną liczbę jednostek czasu |
||||

*Przykład 1*

Ten przykład odejmuje jeden dzień od tej sygnatury czasowej:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

I zwraca ten wynik: `"2018-01-01T00:00:00:0000000Z"`

*Przykład 2*

Ten przykład odejmuje jeden dzień od tej sygnatury czasowej:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

I zwraca ten wynik przy użyciu opcjonalnego formatu "D": `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take (pobierz)

Zwróć elementy z przodu kolekcji.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Ciąg lub tablica | Kolekcja, której elementy chcesz |
| <*liczbą*> | Tak | Liczba całkowita | Dodatnia liczba całkowita liczby elementów, które mają być od przodu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*podzbiór*> lub [ *podzbiór* <>] | Odpowiednio ciąg lub tablicę | Ciąg lub tablica, która ma określoną liczbę elementów pobranych z przodu oryginalnej kolekcji |
||||

*Przykład*

Te przykłady pobierają określoną liczbę elementów z przodu tych kolekcji:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

I zwróć następujące wyniki:

* Pierwszy przykład: `"abc"`
* Drugi przykład: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>taktów

Zwraca `ticks` wartość właściwości dla określonego sygnatury czasowej.
*Cykl* jest interwałem 100-nanosekund.

```
ticks('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*znacznik czasu*> | Tak | Ciąg | Ciąg dla sygnatury czasowej |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Takty — liczba*> | Liczba całkowita | Liczba taktów od określonej sygnatury czasowej |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Zwraca ciąg w formacie małymi literami. Jeśli znak w ciągu nie ma małych wersji, ten znak pozostaje niezmieniony w zwracanym ciągu.

```
toLower('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Tak | Ciąg | Ciąg do zwrócenia w formacie małymi literami |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*małe litery tekstu*> | Ciąg | Oryginalny ciąg w formacie małymi literami |
||||

*Przykład*

Ten przykład konwertuje ten ciąg na małe litery:

```
toLower('Hello World')
```

I zwraca ten wynik: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Zwraca ciąg w formacie wielką literą. Jeśli znak w ciągu nie ma Wielkiej wersji, ten znak pozostaje niezmieniony w zwracanym ciągu.

```
toUpper('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Tak | Ciąg | Ciąg do zwrócenia w formacie wielką literą |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wielkie litery — tekst*> | Ciąg | Oryginalny ciąg w formacie wielką literą |
||||

*Przykład*

Ten przykład konwertuje ten ciąg na wielkie litery:

```
toUpper('Hello World')
```

I zwraca ten wynik: `"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>Trim

Usuń spacje wiodące i końcowe z ciągu, a następnie Zwróć zaktualizowany ciąg.

```
trim('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Tak | Ciąg | Ciąg, który zawiera spacje wiodące i końcowe do usunięcia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updatedText*> | Ciąg | Zaktualizowana wersja oryginalnego ciągu bez spacji wiodących lub końcowych |
||||

*Przykład*

Ten przykład usuwa spacje wiodące i końcowe z ciągu "Hello world":

```
trim(' Hello World  ')
```

I zwraca ten wynik: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>unia

Zwraca kolekcję zawierającą *wszystkie* elementy z określonych kolekcji.
Aby pojawił się w wyniku, element może pojawić się w dowolnej kolekcji przekazaną do tej funkcji. Jeśli co najmniej jeden element ma taką samą nazwę, w wyniku zostanie wyświetlony ostatni element o tej nazwie.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*kolekcji collection1*>, <*Collection2*>,...  | Tak | Tablica lub obiekt, ale nie oba | Kolekcje, z których mają być *wszystkie* elementy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Zaktualizowano*> | Odpowiednio tablica lub obiekt | Kolekcja zawierająca wszystkie elementy z określonych kolekcji — brak duplikatów |
||||

*Przykład*

Ten przykład pobiera *wszystkie* elementy z tych kolekcji:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

I zwraca ten wynik: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Zwróć jednolity identyfikator zasobów (URI) dla ciągu przez zastępowanie znaków w adresie URL bez znaku ucieczki.
Użyj tej funkcji zamiast [encodeURIComponent — ()](#encodeUriComponent).
Chociaż obie funkcje działają w ten sam sposób, `uriComponent()` jest preferowane.

```
uriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg do przekonwertowania na format zakodowany przy użyciu identyfikatora URI |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zakodowany identyfikator URI*> | Ciąg | Ciąg znaków w formacie URI z znakami ucieczki |
||||

*Przykład*

W tym przykładzie tworzona jest wersja zakodowana przy użyciu identyfikatora URI dla tego ciągu:

```
uriComponent('https://contoso.com')
```

I zwraca ten wynik: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Zwróć wersję binarną dla składnika Uniform Resource Identifier (URI).

```
uriComponentToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg zakodowany przy użyciu identyfikatora URI do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*binarny dla kodowanego identyfikatora URI*> | Ciąg | Wersja binarna dla ciągu zakodowanego przy użyciu identyfikatora URI. Zawartość binarna jest zakodowana algorytmem Base64 i reprezentowana przez `$content` . |
||||

*Przykład*

W tym przykładzie tworzona jest wersja binarna dla tego ciągu zakodowanego przy użyciu identyfikatora URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Zwróć wersję ciągu dla zakodowanego ciągu identyfikatora URI (Uniform Resource Identifier) w celu efektywnego dekodowania ciągu zakodowanego przy użyciu identyfikatora URI.

```
uriComponentToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg zakodowany przy użyciu identyfikatora URI, który ma zostać zdekodowany |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zdekodowane-URI*> | Ciąg | Zdekodowana wersja dla ciągu zakodowanego przy użyciu identyfikatora URI |
||||

*Przykład*

Ten przykład tworzy zdekodowaną wersję ciągu dla tego ciągu kodowanego URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik: `"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Zwróć bieżącą sygnaturę czasową.

```
utcNow('<format>')
```

Opcjonalnie można określić inny format z parametrem> <*Format* .

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Current — znacznik czasu*> | Ciąg | Bieżąca data i godzina |
||||

*Przykład 1*

Załóżmy, że dzisiaj to 15 kwietnia 2018 o 1:00:00 PM.
Ten przykład pobiera bieżącą sygnaturę czasową:

```
utcNow()
```

I zwraca ten wynik: `"2018-04-15T13:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że dzisiaj to 15 kwietnia 2018 o 1:00:00 PM.
Ten przykład pobiera bieżącą sygnaturę czasową przy użyciu opcjonalnego formatu "D":

```
utcNow('D')
```

I zwraca ten wynik: `"Sunday, April 15, 2018"`

<a name="xml"></a>

### <a name="xml"></a>xml

Zwróć wersję XML dla ciągu, który zawiera obiekt JSON.

```
xml('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg z obiektem JSON do przekonwertowania <p>Obiekt JSON musi mieć tylko jedną właściwość root, która nie może być tablicą. <br>Użyj znaku ukośnika odwrotnego ( \\ ) jako znaku ucieczki dla podwójnego cudzysłowu ("). |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Wersja XML*> | Obiekt | Zakodowany kod XML dla określonego ciągu lub obiektu JSON. |
||||

*Przykład 1*

W tym przykładzie jest tworzona wersja XML dla tego ciągu, który zawiera obiekt JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

I zwraca ten wynik XML:

```xml
<name>Sophia Owen</name>
```

*Przykład 2*

Załóżmy, że masz ten obiekt JSON:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Ten przykład tworzy kod XML dla ciągu, który zawiera ten obiekt JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

I zwraca ten wynik XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>Lokalizacja

Sprawdź XML dla węzłów lub wartości, które pasują do wyrażenia XPath (język ścieżki XML) i zwracają pasujące węzły lub wartości. Wyrażenie XPath lub "XPath" ułatwia nawigowanie po strukturze dokumentu XML, dzięki czemu można wybrać węzły lub wartości obliczeniowe w zawartości XML.

```
xpath('<xml>', '<xpath>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*dokument*> | Tak | Dowolne | Ciąg XML służący do wyszukiwania węzłów lub wartości, które pasują do wartości wyrażenia XPath. |
| <*Lokalizacja*> | Tak | Dowolne | Wyrażenie XPath używane do znajdowania pasujących węzłów lub wartości XML |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*węzeł XML*> | XML | Węzeł XML, gdy tylko jeden węzeł pasuje do określonego wyrażenia XPath |
| <*wartościami*> | Dowolne | Wartość z węzła XML, gdy tylko jedna wartość jest zgodna z określonym wyrażeniem XPath |
| [<*XML-węzeł1*>, <*xml-Węzeł2*>,...] </br>-lub- </br>[<*wartość1*>, <*wartość2*>,...] | Tablica | Tablica z węzłami lub wartościami XML, które pasują do określonego wyrażenia XPath |
||||

*Przykład 1*

Jak pokazano na przykładzie 1, ten przykład odnajduje węzły, które pasują do `<count></count>` węzła i dodaje te wartości węzła przy użyciu `sum()` funkcji:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

I zwraca ten wynik: `30`

*Przykład 2*

W tym przykładzie oba wyrażenia znajdą węzły, które pasują do `<location></location>` węzła, w określonych argumentach, które zawierają XML z przestrzenią nazw. Wyrażenia używają znaku ukośnika odwrotnego ( \\ ) jako znaku ucieczki dla podwójnego cudzysłowu (").

* *Wyrażenie 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Wyrażenie 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Oto argumenty:

* Ten plik XML, który zawiera przestrzeń nazw dokumentu XML, `xmlns="http://contoso.com"` :

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Wyrażenie XPath tutaj:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Oto węzeł wynikowy zgodny z `<location></location>` węzłem:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Przykład 3*

Poniższy przykład dotyczy znalezienia wartości w `<location></location>` węźle:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

I zwraca ten wynik: `"Paris"`

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę zmiennych systemowych, których można używać w wyrażeniach, zobacz [zmienne systemowe](control-flow-system-variables.md).
