---
title: Wyrażenie i funkcje w fabryce danych platformy Azure
description: Ten artykuł zawiera informacje o wyrażeniach i funkcjach, których można używać podczas tworzenia jednostek fabryki danych.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: c456e486217886510a1fd2ef1aeabae47d35b53c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417206"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Wyrażenia i funkcje w usłudze Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-functions-variables.md)
> * [Aktualna wersja](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ten artykuł zawiera szczegółowe informacje na temat wyrażeń i funkcji obsługiwanych przez usługę Azure Data Factory. 

## <a name="expressions"></a>Wyrażenia

Wartości JSON w definicji mogą być dosłowne lub wyrażenia, które są oceniane w czasie wykonywania. Przykład:  
  
```json
"name": "value"
```

 lub  
  
```json
"name": "@pipeline().parameters.password"
```

Wyrażenia mogą pojawić się w dowolnym miejscu wartości ciągu JSON i zawsze skutkują inną wartością JSON. Jeśli wartość JSON jest wyrażeniem, treść wyrażenia jest wyodrębniany przez usunięcie\@znaku at ( ). Jeśli potrzebny jest ciąg dosłowny, który zaczyna się od \@, musi zostać zmieniony za pomocą programu \@ \@. Poniższe przykłady pokazują, jak wyrażenia są oceniane.  
  
|Wartość JSON|Wynik|  
|----------------|------------|  
|"parametry"|Znaki "parametry" są zwracane.|  
|"parametry[1]"|Znaki "parametry[1]" są zwracane.|  
|"\@\@"|Zwracany jest ciąg 1 znaków zawierający '\@.|  
|" \@"|Zwracany jest ciąg 2 znaków zawierający ' \@.|  
  
 Wyrażenia mogą również pojawiać się wewnątrz ciągów, przy użyciu funkcji `@{ ... }`zwanej *interpolacją ciągów,* w której wyrażenia są zawijane w . Na przykład: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Za pomocą interpolacji ciągu wynik jest zawsze ciągiem. Powiedzmy, `myNumber` że `42` `myString` mam `foo`zdefiniowane jako i jako:  
  
|Wartość JSON|Wynik|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| Zwraca `foo` jako ciąg.|  
|"\@{pipeline().parameters.myString}"| Zwraca `foo` jako ciąg.|  
|"\@pipeline().parameters.myNumber"| Zwraca `42` jako *liczbę*.|  
|"\@{pipeline().parameters.myNumber}"| Zwraca `42` jako *ciąg*.|  
|"Odpowiedź to: @{pipeline().parameters.myNumber}"| Zwraca ciąg `Answer is: 42`.|  
|"\@concat('Odpowiedź brzmi: ', string(pipeline().parameters.myNumber))"| Zwraca ciąg`Answer is: 42`|  
|"Odpowiedź to: \@ \@{pipeline().parameters.myNumber}"| Zwraca ciąg `Answer is: @{pipeline().parameters.myNumber}`.|  
  
## <a name="examples"></a>Przykłady

### <a name="a-dataset-with-a-parameter"></a>Zestaw danych z parametrem
W poniższym przykładzie zestaw blobdataset przyjmuje parametr o nazwie **ścieżka**. Jego wartość służy do ustawiania wartości właściwości **folderPath** `dataset().path`przy użyciu wyrażenia: . 

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
W poniższym przykładzie potoku przyjmuje **inputPath** i **outputPath** parametry. **Ścieżka** dla sparametryzowanego zestawu danych obiektów blob jest ustawiana przy użyciu wartości tych parametrów. Składnia używana w tym `pipeline().parameters.parametername`miejscu to: . 

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
W tym [samouczku](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) opisano sposób przekazywania parametrów między potokiem a działaniem, a także między działaniami.

  
## <a name="functions"></a>Funkcje

Funkcje można wywoływać w wyrażeniach. Poniższe sekcje zawierają informacje o funkcjach, które mogą być używane w wyrażeniu.  

## <a name="string-functions"></a>Funkcje ciągów  

Aby pracować z ciągami, można użyć tych funkcji ciągu, a także niektórych [funkcji zbierania](#collection-functions).
Funkcje ciągów działają tylko na ciągach.

| Ciąg, funkcja | Zadanie |
| --------------- | ---- |
| [Concat](control-flow-expression-language-functions.md#concat) | Połącz dwa lub więcej ciągów i zwróć połączony ciąg. |
| [Endswith](control-flow-expression-language-functions.md#endswith) | Sprawdź, czy ciąg kończy się określonym podciągiem. |
| [Identyfikator guid](control-flow-expression-language-functions.md#guid) | Generowanie unikatowego identyfikatora (GUID) jako ciągu. |
| [Indexof](control-flow-expression-language-functions.md#indexof) | Zwraca pozycję początkową podciągu. |
| [Lastindexof](control-flow-expression-language-functions.md#lastindexof) | Zwraca pozycję początkową dla ostatniego wystąpienia podciągu. |
| [Zastąpić](control-flow-expression-language-functions.md#replace) | Zamień podciąg na określony ciąg i zwróć zaktualizowany ciąg. |
| [split](control-flow-expression-language-functions.md#split) | Zwraca tablicę zawierającą podciągi, oddzielone przecinkami, z większego ciągu opartego na określonym znaku ogranicznika w oryginalnym ciągu. |
| [Startswith](control-flow-expression-language-functions.md#startswith) | Sprawdź, czy ciąg zaczyna się od określonego podciągu. |
| [Podciąg](control-flow-expression-language-functions.md#substring) | Zwraca znaki z ciągu, zaczynając od określonej pozycji. |
| [Tolower](control-flow-expression-language-functions.md#toLower) | Zwraca ciąg w formacie małych liter. |
| [Toupper](control-flow-expression-language-functions.md#toUpper) | Zwraca ciąg wielkimi literami. |
| [Przycinanie](control-flow-expression-language-functions.md#trim) | Usuń interweniujący i końcowy biały znak z ciągu i zwraca zaktualizowany ciąg. |

## <a name="collection-functions"></a>Funkcje zbierania

Aby pracować z kolekcji, zazwyczaj tablice, ciągi i czasami słowniki, można użyć tych funkcji kolekcji.

| Kolekcja, funkcja | Zadanie |
| ------------------- | ---- |
| [Zawiera](control-flow-expression-language-functions.md#contains) | Sprawdź, czy kolekcja ma określony element. |
| [Pusty](control-flow-expression-language-functions.md#empty) | Sprawdź, czy kolekcja jest pusta. |
| [Pierwszym](control-flow-expression-language-functions.md#first) | Zwraca pierwszy element z kolekcji. |
| [Przecięcia](control-flow-expression-language-functions.md#intersection) | Zwraca kolekcję, która ma *tylko* wspólne elementy w określonych kolekcjach. |
| [Dołączyć](control-flow-expression-language-functions.md#join) | Zwraca ciąg, który ma *wszystkie* elementy z tablicy, oddzielone określonym znakiem. |
| [Ostatnio](control-flow-expression-language-functions.md#last) | Zwróć ostatni element z kolekcji. |
| [Długość](control-flow-expression-language-functions.md#length) | Zwraca liczbę elementów w ciągu lub tablicy. |
| [Pominąć](control-flow-expression-language-functions.md#skip) | Usuń elementy z przodu kolekcji i zwróć *wszystkie pozostałe* elementy. |
| [wziąć](control-flow-expression-language-functions.md#take) | Zwracaj przedmioty z przodu kolekcji. |
| [Unii](control-flow-expression-language-functions.md#union) | Zwraca kolekcję, która ma *wszystkie* elementy z określonych kolekcji. | 

## <a name="logical-functions"></a>Funkcje logiczne  

Te funkcje są przydatne w warunkach wewnętrznych, mogą służyć do oceny dowolnego typu logiki.  
  
| Funkcja porównywania logicznego | Zadanie |
| --------------------------- | ---- |
| [I](control-flow-expression-language-functions.md#and) | Sprawdź, czy wszystkie wyrażenia są prawdziwe. |
| [equals](control-flow-expression-language-functions.md#equals) | Sprawdź, czy obie wartości są równoważne. |
| [greater](control-flow-expression-language-functions.md#greater) | Sprawdź, czy pierwsza wartość jest większa niż druga wartość. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Sprawdź, czy pierwsza wartość jest większa lub równa drugiej wartości. |
| [if](control-flow-expression-language-functions.md#if) | Sprawdź, czy wyrażenie jest prawdziwe czy fałszywe. Na podstawie wyniku zwraca określoną wartość. |
| [less](control-flow-expression-language-functions.md#less) | Sprawdź, czy pierwsza wartość jest mniejsza niż druga wartość. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Sprawdź, czy pierwsza wartość jest mniejsza lub równa drugiej wartości. |
| [Nie](control-flow-expression-language-functions.md#not) | Sprawdź, czy wyrażenie jest fałszywe. |
| [Lub](control-flow-expression-language-functions.md#or) | Sprawdź, czy co najmniej jedno wyrażenie jest prawdziwe. |
  
## <a name="conversion-functions"></a>Funkcje konwersji  

 Te funkcje są używane do konwersji między każdym z typów macierzystych w języku:  
-   ciąg
-   liczba całkowita
-   float
-   wartość logiczna
-   Tablice
-   Słowniki

| Funkcja konwersji | Zadanie |
| ------------------- | ---- |
| [tablica](control-flow-expression-language-functions.md#array) | Zwraca tablicę z jednego określonego wejścia. Dla wielu wejść, zobacz [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Zwraca wersję zakodowaną w base64 dla ciągu. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Zwraca wersję binarną dla ciągu zakodowanego base64. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Zwraca wersję ciągu dla ciągu zakodowanego base64. |
| [binarny](control-flow-expression-language-functions.md#binary) | Zwraca wersję binarną dla wartości wejściowej. |
| [bool](control-flow-expression-language-functions.md#bool) | Zwraca wersję logiczną dla wartości wejściowej. |
| [Łączonej](control-flow-expression-language-functions.md#coalesce) | Zwraca pierwszą wartość niekwaśną z jednego lub więcej parametrów. |
| [tworzenieArray](control-flow-expression-language-functions.md#createArray) | Zwraca tablicę z wielu danych wejściowych. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Zwraca identyfikator URI danych dla wartości wejściowej. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Zwraca wersję binarną dla identyfikatora URI danych. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Zwraca wersję ciągu dla identyfikatora URI danych. |
| [dekodowanieBase64](control-flow-expression-language-functions.md#decodeBase64) | Zwraca wersję ciągu dla ciągu zakodowanego base64. |
| [dekodowanieDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Zwraca wersję binarną dla identyfikatora URI danych. |
| [dekodowanieskładeksu](control-flow-expression-language-functions.md#decodeUriComponent) | Zwraca ciąg, który zastępuje znaki ucieczki z dekodowanych wersji. |
| [Encodeuricomponent](control-flow-expression-language-functions.md#encodeUriComponent) | Zwraca ciąg, który zastępuje znaki niebezpieczne dla adresów URL znakami ucieczki. |
| [float](control-flow-expression-language-functions.md#float) | Zwraca numer zmiennoprzecinkowy dla wartości wejściowej. |
| [int](control-flow-expression-language-functions.md#int) | Zwraca wersję całkowitą dla ciągu. |
| [Json](control-flow-expression-language-functions.md#json) | Zwraca wartość typu ISON (JavaScript Object Notation) lub obiekt dla ciągu lub pliku XML. |
| [Ciąg](control-flow-expression-language-functions.md#string) | Zwraca wersję ciągu dla wartości wejściowej. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Zwraca wersję zakodowaną w identyfikatorze URI dla wartości wejściowej, zastępując znaki niebezpieczne dla adresów URL znakami ucieczki. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Zwraca wersję binarną dla ciągu zakodowanego zgodnie z identyfikatorem URI. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Zwraca wersję ciągu dla ciągu zakodowanego w identyfikatorze URI. |
| [Xml](control-flow-expression-language-functions.md#xml) | Zwraca wersję XML dla ciągu. |
| [Xpath](control-flow-expression-language-functions.md#xpath) | Sprawdź xml dla węzłów lub wartości, które pasują do wyrażenia XPath (XML Path Language) i zwraca pasujące węzły lub wartości. |

## <a name="math-functions"></a>Funkcje matematyczne  
 Te funkcje mogą być używane dla obu typów liczb: **liczb całkowitych** i **pływaków.**  

| Funkcja matematyczna | Zadanie |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | Zwraca wynik z dodawania dwóch liczb. |
| [div](control-flow-expression-language-functions.md#div) | Zwraca wynik z dzielenia dwóch liczb. |
| [Max](control-flow-expression-language-functions.md#max) | Zwraca najwyższą wartość z zestawu liczb lub tablicy. |
| [Min](control-flow-expression-language-functions.md#min) | Zwraca najniższą wartość z zestawu liczb lub tablicy. |
| [Mod](control-flow-expression-language-functions.md#mod) | Zwróć pozostałą część z podzielenia dwóch liczb. |
| [Mul](control-flow-expression-language-functions.md#mul) | Zwróć produkt z pomnożenia dwóch liczb. |
| [Rand](control-flow-expression-language-functions.md#rand) | Zwraca losową ćę całkowitą z określonego zakresu. |
| [Zakres](control-flow-expression-language-functions.md#range) | Zwraca tablicę całkowitą, która rozpoczyna się od określonej liczby całkowitej. |
| [Sub](control-flow-expression-language-functions.md#sub) | Zwraca wynik z odejmowania drugiej liczby od pierwszej liczby. |
  
## <a name="date-functions"></a>Funkcje daty  

| Funkcja daty lub godziny | Zadanie |
| --------------------- | ---- |
| [Adddays](control-flow-expression-language-functions.md#addDays) | Dodaj liczbę dni do sygnatury czasowej. |
| [Addhours](control-flow-expression-language-functions.md#addHours) | Dodaj liczbę godzin do sygnatury czasowej. |
| [Addminutes](control-flow-expression-language-functions.md#addMinutes) | Dodaj liczbę minut do sygnatury czasowej. |
| [Addseconds](control-flow-expression-language-functions.md#addSeconds) | Dodaj liczbę sekund do sygnatury czasowej. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Dodaj liczbę jednostek czasu do sygnatury czasowej. Zobacz też [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Konwertuj sygnaturę czasową z uniwersalnej strefy czasowej (UTC) na docelową strefę czasową. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na uniwersalną koordynacyjną (UTC). |
| [dzieńPomiesiętny](control-flow-expression-language-functions.md#dayOfMonth) | Zwraca składnik dzień miesiąca z sygnatury czasowej. |
| [Dayofweek](control-flow-expression-language-functions.md#dayOfWeek) | Zwraca składnik dzień tygodnia z sygnatury czasowej. |
| [Dayofyear](control-flow-expression-language-functions.md#dayOfYear) | Zwraca składnik dzień roku z sygnatury czasowej. |
| [Formatdatetime](control-flow-expression-language-functions.md#formatDateTime) | Zwraca datę z sygnatury czasowej. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Zwraca bieżący sygnatura czasowa oraz określone jednostki czasu. Zobacz też [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Zwraca bieżący sygnatura czasowa minus określone jednostki czasu. Zobacz też [otracaćFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Zwraca początek dnia dla sygnatury czasowej. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Zwraca początek godziny dla sygnatury czasowej. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Zwraca początek miesiąca dla sygnatury czasowej. |
| [odejmowanieOdczasu](control-flow-expression-language-functions.md#subtractFromTime) | Odejmij liczbę jednostek czasu od sygnatury czasowej. Zobacz też [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [Kleszcze](control-flow-expression-language-functions.md#ticks) | Zwraca `ticks` wartość właściwości dla określonej sygnatury czasowej. |
| [Utcnow](control-flow-expression-language-functions.md#utcNow) | Zwraca bieżący sygnatura czasowa jako ciąg. |

## <a name="function-reference"></a>Odwołanie do funkcji

W tej sekcji wymieniono wszystkie dostępne funkcje w kolejności alfabetycznej.

<a name="add"></a>

### <a name="add"></a>add

Zwraca wynik z dodawania dwóch liczb.

```
add(<summand_1>, <summand_2>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, *<summand_2*> | Tak | Całkowita, Float lub mieszana | Liczby do dodania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*suma wyniku*> | Całkowita lub float | Wynik z dodania określonych liczb |
||||

*Przykład*

W tym przykładzie dodano określone liczby:

```
add(1, 1.5)
```

I zwraca ten wynik:`2.5`

<a name="addDays"></a>

### <a name="adddays"></a>Adddays

Dodaj liczbę dni do sygnatury czasowej.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Dni*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba dni do dodania |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Sygnatura czasowa plus określona liczba dni  |
||||

*Przykład 1*

W tym przykładzie dodaje 10 dni do określonego sygnatury czasowej:

```
addDays('2018-03-15T13:00:00Z', 10)
```

I zwraca ten wynik:`"2018-03-25T00:00:0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięć dni od określonego sygnatury czasowej:

```
addDays('2018-03-15T00:00:00Z', -5)
```

I zwraca ten wynik:`"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>Addhours

Dodaj liczbę godzin do sygnatury czasowej.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Godzin*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba godzin do dodania |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Sygnatura czasowa plus określona liczba godzin  |
||||

*Przykład 1*

W tym przykładzie dodaje 10 godzin do określonego sygnatury czasowej:

```
addHours('2018-03-15T00:00:00Z', 10)
```

I zwraca ten wynik:`"2018-03-15T10:00:0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięć godzin od określonego sygnatury czasowej:

```
addHours('2018-03-15T15:00:00Z', -5)
```

I zwraca ten wynik:`"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>Addminutes

Dodaj liczbę minut do sygnatury czasowej.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Minut*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba minut do dodania |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Sygnatura czasowa plus określona liczba minut |
||||

*Przykład 1*

W tym przykładzie dodaje 10 minut do określonego sygnatury czasowej:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

I zwraca ten wynik:`"2018-03-15T00:20:00.0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięć minut od określonego sygnatury czasowej:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

I zwraca ten wynik:`"2018-03-15T00:15:00.0000000Z"`

<a name="addSeconds"></a>

### <a name="addseconds"></a>Addseconds

Dodaj liczbę sekund do sygnatury czasowej.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Sekund*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba sekund do dodania |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Sygnatura czasowa plus określona liczba sekund  |
||||

*Przykład 1*

W tym przykładzie dodaje 10 sekund do określonego sygnatury czasowej:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

I zwraca ten wynik:`"2018-03-15T00:00:10.0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięć sekund do określonego sygnatury czasowej:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

I zwraca ten wynik:`"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Dodaj liczbę jednostek czasu do sygnatury czasowej.
Zobacz też [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Interwał*> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do dodania |
| <*jednostka czasowa*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem:*"Drugi", "Minuta", "Godzina", "Dzień", "Tydzień", "Miesiąc", "Rok" |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Sygnatura czasowa plus określona liczba jednostek czasu  |
||||

*Przykład 1*

W tym przykładzie dodaje jeden dzień do określonego sygnatury czasowej:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

I zwraca ten wynik:`"2018-01-02T00:00:00.0000000Z"`

*Przykład 2*

W tym przykładzie dodaje jeden dzień do określonego sygnatury czasowej:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

I zwraca wynik przy użyciu opcjonalnego formatu "D":`"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>i

Sprawdź, czy wszystkie wyrażenia są prawdziwe.
Zwraca wartość true, gdy wszystkie wyrażenia są prawdziwe, lub zwraca wartość false, gdy co najmniej jedno wyrażenie jest false.

```
and(<expression1>, <expression2>, ...)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Tak | Wartość logiczna | Wyrażenia do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy wszystkie wyrażenia są prawdziwe. Zwraca wartość false, gdy co najmniej jedno wyrażenie jest fałszywe. |
||||

*Przykład 1*

Te przykłady sprawdzić, czy określone wartości logiczne są prawdziwe:

```
and(true, true)
and(false, true)
and(false, false)
```

I zwraca te wyniki:

* Pierwszy przykład: Oba wyrażenia są `true`prawdziwe, więc zwraca .
* Drugi przykład: Jedno wyrażenie jest `false`fałszywe, więc zwraca .
* Trzeci przykład: Oba wyrażenia są `false`fałszywe, więc zwraca .

*Przykład 2*

Te przykłady sprawdzić, czy określone wyrażenia są prawdziwe:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

I zwraca te wyniki:

* Pierwszy przykład: Oba wyrażenia są `true`prawdziwe, więc zwraca .
* Drugi przykład: Jedno wyrażenie jest `false`fałszywe, więc zwraca .
* Trzeci przykład: Oba wyrażenia są `false`fałszywe, więc zwraca .

<a name="array"></a>

### <a name="array"></a>tablica

Zwraca tablicę z jednego określonego wejścia.
Dla wielu wejść zobacz [createArray()](#createArray).

```
array('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg do tworzenia tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [> wartości *<]* | Tablica | Tablica zawierająca pojedyncze określone dane wejściowe |
||||

*Przykład*

W tym przykładzie tworzy tablicę z ciągu "hello":

```
array('hello')
```

I zwraca ten wynik:`["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Zwraca wersję zakodowaną w base64 dla ciągu.

```
base64('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg wejściowy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ciąg podstawowy64*> | Ciąg | Wersja zakodowana w base64 dla ciągu wejściowego |
||||

*Przykład*

W tym przykładzie konwertuje ciąg "hello" na ciąg zakodowany base64:

```
base64('hello')
```

I zwraca ten wynik:`"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Zwraca wersję binarną dla ciągu zakodowanego base64.

```
base64ToBinary('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg zakodowany w kodowaniu base64 do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ciągbinowy dla base64*> | Ciąg | Wersja binarna dla ciągu zakodowanego base64 |
||||

*Przykład*

W tym przykładzie konwertuje ciąg "aGVsbG8=" zakodowany base64 na ciąg binarny:

```
base64ToBinary('aGVsbG8=')
```

I zwraca ten wynik:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Zwraca wersję ciągu dla ciągu zakodowanego base64, skutecznie dekodując ciąg base64.
Użyj tej funkcji, a nie [dekodowaniaBase64()](#decodeBase64).
Chociaż obie funkcje działają `base64ToString()` w ten sam sposób, jest preferowany.

```
base64ToString('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg kodowany base64 do dekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dekodowany-base64-string*> | Ciąg | Wersja ciągu dla ciągu zakodowanego base64 |
||||

*Przykład*

W tym przykładzie konwertuje ciąg "aGVsbG8=" kodowany base64 na ciąg:

```
base64ToString('aGVsbG8=')
```

I zwraca ten wynik:`"hello"`

<a name="binary"></a>

### <a name="binary"></a>binarny

Zwraca wersję binarną dla ciągu.

```
binary('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość binarna dla wejścia*> | Ciąg | Wersja binarna dla określonego ciągu |
||||

*Przykład*

W tym przykładzie konwertuje ciąg "hello" na ciąg binarny:

```
binary('hello')
```

I zwraca ten wynik:

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>bool

Zwraca wersję logiczną dla wartości.

```
bool(<value>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Dowolne | Wartość do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Wersja logiczna dla określonej wartości |
||||

*Przykład*

W tych przykładach konwertowane są określone wartości na wartości logiczne:

```
bool(1)
bool(0)
```

I zwraca te wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="coalesce"></a>

### <a name="coalesce"></a>Łączonej

Zwraca pierwszą wartość niekwaśną z jednego lub więcej parametrów.
Puste ciągi, puste tablice i puste obiekty nie mają wartości null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, *<object_2*>, ... | Tak | Dowolna, może mieszać typy | Jeden lub więcej elementów do sprawdzenia wartości null |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*element pierwszy bez wartości null*> | Dowolne | Pierwszy element lub wartość, która nie jest null. Jeśli wszystkie parametry mają wartość null, ta funkcja zwraca wartość null. |
||||

*Przykład*

Te przykłady zwracają pierwszą wartość nie null z określonych wartości lub null, gdy wszystkie wartości są null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

I zwraca te wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`"hello"`
* Trzeci przykład:`null`

<a name="concat"></a>

### <a name="concat"></a>concat

Połącz dwa lub więcej ciągów i zwróć połączony ciąg.

```
concat('<text1>', '<text2>', ...)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Tak | Ciąg | Co najmniej dwa ciągi do połączenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Ciąg | Ciąg utworzony na podstawie połączonych ciągów wejściowych |
||||

*Przykład*

W tym przykładzie łączy ciągi "Hello" i "World":

```
concat('Hello', 'World')
```

I zwraca ten wynik:`"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>zawiera

Sprawdź, czy kolekcja ma określony element.
Zwraca wartość true po znalezieniu elementu lub zwraca wartość false, gdy nie zostanie znaleziony.
W tej funkcji rozróżniana jest wielkość liter.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

W szczególności ta funkcja działa na tych typach kolekcji:

* *Ciąg,* aby znaleźć *podciąg*
* *Tablica* do znajdowania *wartości*
* *Słownik,* aby znaleźć *klucz*

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Ciąg, tablica lub słownik | Kolekcja do sprawdzenia |
| <*Wartość*> | Tak | Odpowiednio ciąg, tablica lub słownik | Element do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true po znalezieniu elementu. Zwraca wartość false, gdy nie znaleziono. |
||||

*Przykład 1*

W tym przykładzie sprawdza ciąg "hello world" dla podciąg "świat" i zwraca true:

```
contains('hello world', 'world')
```

*Przykład 2*

W tym przykładzie sprawdza ciąg "hello world" dla podciąg "universe" i zwraca false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Konwertuj sygnaturę czasową z uniwersalnej strefy czasowej (UTC) na docelową strefę czasową.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*destinationTimeZone*> | Tak | Ciąg | Nazwa docelowej strefy czasowej. Aby zapoznać się z nazwami stref czasowych, zobacz [Wartości indeksu stref czasowych firmy Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale może być trzeba usunąć wszelkie znaki interpunkcyjne z nazwy strefy czasowej. |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*sygnatura czasowa konwersji*> | Ciąg | Sygnatura czasowa przekonwertowana na docelową strefę czasową |
||||

*Przykład 1*

W tym przykładzie sygnatura czasowa konwertuje określoną strefę czasową:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

I zwraca ten wynik:`"2018-01-01T00:00:00.0000000"`

*Przykład 2*

W tym przykładzie sygnatura czasowa konwertuje określoną strefę czasową i format:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik:`"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Sourcetimezone*> | Tak | Ciąg | Nazwa źródłowej strefy czasowej. Aby zapoznać się z nazwami stref czasowych, zobacz [Wartości indeksu stref czasowych firmy Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale może być trzeba usunąć wszelkie znaki interpunkcyjne z nazwy strefy czasowej. |
| <*destinationTimeZone*> | Tak | Ciąg | Nazwa docelowej strefy czasowej. Aby zapoznać się z nazwami stref czasowych, zobacz [Wartości indeksu stref czasowych firmy Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale może być trzeba usunąć wszelkie znaki interpunkcyjne z nazwy strefy czasowej. |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*sygnatura czasowa konwersji*> | Ciąg | Sygnatura czasowa przekonwertowana na docelową strefę czasową |
||||

*Przykład 1*

W tym przykładzie konwertuje strefę czasową źródła do docelowej strefy czasowej:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

I zwraca ten wynik:`"2018-01-01T00:00:00.0000000"`

*Przykład 2*

W tym przykładzie konwertuje strefę czasową do określonej strefy czasowej i formatu:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik:`"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na uniwersalną koordynacyjną (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Sourcetimezone*> | Tak | Ciąg | Nazwa źródłowej strefy czasowej. Aby zapoznać się z nazwami stref czasowych, zobacz [Wartości indeksu stref czasowych firmy Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale może być trzeba usunąć wszelkie znaki interpunkcyjne z nazwy strefy czasowej. |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*sygnatura czasowa konwersji*> | Ciąg | Sygnatura czasowa przekonwertowana na utc |
||||

*Przykład 1*

W tym przykładzie konwertuje sygnaturę czasową na UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

I zwraca ten wynik:`"2018-01-01T08:00:00.0000000Z"`

*Przykład 2*

W tym przykładzie konwertuje sygnaturę czasową na UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik:`"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>tworzenieArray

Zwraca tablicę z wielu danych wejściowych.
W przypadku pojedynczych tablic wejściowych zobacz [tablica()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | Tak | Wszelkie, ale nie mieszane | Co najmniej dwa elementy do utworzenia tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Tablica | Tablica utworzona na podstawie wszystkich elementów wejściowych |
||||

*Przykład*

W tym przykładzie tworzy tablicę z tych danych wejściowych:

```
createArray('h', 'e', 'l', 'l', 'o')
```

I zwraca ten wynik:`["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Zwraca identyfikator jednolitego zasobu danych (URI) dla ciągu.

```
dataUri('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dane-uri*> | Ciąg | Identyfikator URI danych dla ciągu wejściowego |
||||

*Przykład*

W tym przykładzie tworzy identyfikator URI danych dla ciągu "hello":

```
dataUri('hello')
```

I zwraca ten wynik:`"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Zwraca wersję binarną dla identyfikatora jednolitego zasobu danych (URI).
Użyj tej funkcji, a nie [dekodowaniaDataUri()](#decodeDataUri).
Chociaż obie funkcje działają `dataUriBinary()` w ten sam sposób, jest preferowany.

```
dataUriToBinary('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Identyfikator URI danych do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*bin-for-data-uri*> | Ciąg | Wersja binarna dla identyfikatora URI danych |
||||

*Przykład*

W tym przykładzie tworzy wersję binarną dla tego identyfikatora URI danych:

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

Zwraca wersję ciągu dla identyfikatora zasobów jednolitych danych (URI).

```
dataUriToString('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Identyfikator URI danych do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ciąg-dla danych-uri*> | Ciąg | Wersja ciągu dla identyfikatora URI danych |
||||

*Przykład*

W tym przykładzie tworzy ciąg dla tego identyfikatora URI danych:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik:`"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Zwróć dzień miesiąca z sygnatury czasowej.

```
dayOfMonth('<timestamp>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień miesiąca*> | Liczba całkowita | Dzień miesiąca z określonej sygnatury czasowej |
||||

*Przykład*

W tym przykładzie zwraca liczbę dla dnia miesiąca z tej sygnatury czasowej:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

I zwraca ten wynik:`15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Zwróć dzień tygodnia z sygnatury czasowej.

```
dayOfWeek('<timestamp>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień tygodnia*> | Liczba całkowita | Dzień tygodnia od określonego sygnatury czasowej, w którym niedziela jest 0, poniedziałek to 1, i tak dalej |
||||

*Przykład*

W tym przykładzie zwraca liczbę dla dnia tygodnia z tej sygnatury czasowej:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

I zwraca ten wynik:`3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Zwróć dzień roku z sygnatury czasowej.

```
dayOfYear('<timestamp>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień w roku*> | Liczba całkowita | Dzień roku z określonej sygnatury czasowej |
||||

*Przykład*

W tym przykładzie zwraca numer dnia roku z tej sygnatury czasowej:

```
dayOfYear('2018-03-15T13:27:36Z')
```

I zwraca ten wynik:`74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>dekodowanieBase64

Zwraca wersję ciągu dla ciągu zakodowanego base64, skutecznie dekodując ciąg base64.
Należy rozważyć użycie [base64ToString()](#base64ToString) zamiast `decodeBase64()`.
Chociaż obie funkcje działają `base64ToString()` w ten sam sposób, jest preferowany.

```
decodeBase64('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg kodowany base64 do dekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dekodowany-base64-string*> | Ciąg | Wersja ciągu dla ciągu zakodowanego base64 |
||||

*Przykład*

W tym przykładzie tworzy ciąg dla ciągu zakodowanego base64:

```
decodeBase64('aGVsbG8=')
```

I zwraca ten wynik:`"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>dekodowanieDataUri

Zwraca wersję binarną dla identyfikatora jednolitego zasobu danych (URI).
Należy rozważyć użycie [dataUriToBinary()](#dataUriToBinary)zamiast `decodeDataUri()`.
Chociaż obie funkcje działają `dataUriToBinary()` w ten sam sposób, jest preferowany.

```
decodeDataUri('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg identyfikatora URI danych do dekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*bin-for-data-uri*> | Ciąg | Wersja binarna dla ciągu identyfikatora URI danych |
||||

*Przykład*

W tym przykładzie zwraca wersję binarną dla tego identyfikatora URI danych:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>dekodowanieskładeksu

Zwraca ciąg, który zastępuje znaki ucieczki z dekodowanych wersji.

```
decodeUriComponent('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg ze znakami ucieczki do dekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dekodowane-uri*> | Ciąg | Zaktualizowany ciąg z zdekodowanymi znakami ucieczki |
||||

*Przykład*

W tym przykładzie znaki ucieczki w tym ciągu zdekodowane wersje:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik:`"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Zwraca wynik liczby całkowitej z podzielenia dwóch liczb.
Aby uzyskać pozostały wynik, zobacz [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Dywidendy*> | Tak | Całkowita lub float | Liczba do podzielenia przez *dzielnik* |
| <*Dzielnik*> | Tak | Całkowita lub float | Liczba, która dzieli *dywidendę,* ale nie może być 0 |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wynik ilorazu*> | Liczba całkowita | Wynik liczby całkowitej z podzielenia pierwszej liczby przez drugą liczbę |
||||

*Przykład*

Oba przykłady dzielą pierwszą liczbę przez drugą liczbę:

```
div(10, 5)
div(11, 5)
```

I zwrócić ten wynik:`2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>Encodeuricomponent

Zwraca wersję zakodowaną w jednolitym identyfikatorze zasobu (URI) dla ciągu, zastępując znaki niebezpieczne dla adresów URL znakami ucieczki.
Należy rozważyć użycie [uriComponent()](#uriComponent), a nie `encodeUriComponent()`.
Chociaż obie funkcje działają `uriComponent()` w ten sam sposób, jest preferowany.

```
encodeUriComponent('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg do konwersji do formatu zakodowanego na URI |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zakodowane-uri*> | Ciąg | Ciąg zakodowany w kodach URI ze znakami ucieczki |
||||

*Przykład*

W tym przykładzie tworzy wersję zakodowaną w identyfikatorze URI dla tego ciągu:

```
encodeUriComponent('https://contoso.com')
```

I zwraca ten wynik:`"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

Sprawdź, czy kolekcja jest pusta.
Zwraca wartość true, gdy kolekcja jest pusta lub zwraca false, gdy nie jest pusty.

```
empty('<collection>')
empty([<collection>])
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Ciąg, tablica lub obiekt | Kolekcja do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy kolekcja jest pusta. Zwraca wartość false, gdy nie jest pusta. |
||||

*Przykład*

Te przykłady sprawdzić, czy określone kolekcje są puste:

```
empty('')
empty('abc')
```

I zwraca te wyniki:

* Pierwszy przykład: Przekazuje pusty ciąg, `true`więc funkcja zwraca .
* Drugi przykład: Przekazuje ciąg "abc", `false`więc funkcja zwraca .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Sprawdź, czy ciąg kończy się określonym podciągiem.
Zwraca wartość true po znalezieniu podciągów lub zwraca wartość false, gdy nie zostanie znaleziony.
W tej funkcji nie jest rozróżniana wielkość liter.

```
endsWith('<text>', '<searchText>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg do sprawdzenia |
| <*tekst wyszukiwania*> | Tak | Ciąg | Podciąg końcowy, aby znaleźć |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false  | Wartość logiczna | Zwraca wartość true po znalezieniu końcowego podciągu. Zwraca wartość false, gdy nie znaleziono. |
||||

*Przykład 1*

W tym przykładzie sprawdza, czy ciąg "hello world" kończy się ciągiem "świat":

```
endsWith('hello world', 'world')
```

I zwraca ten wynik:`true`

*Przykład 2*

W tym przykładzie sprawdza, czy ciąg "hello world" kończy się ciągiem "wszechświat":

```
endsWith('hello world', 'universe')
```

I zwraca ten wynik:`false`

<a name="equals"></a>

### <a name="equals"></a>equals

Sprawdź, czy obie wartości, wyrażenia lub obiekty są równoważne.
Zwracaj wartość true, gdy oba są równoważne lub zwracaj false, gdy nie są równoważne.

```
equals('<object1>', '<object2>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*obiekt2*> | Tak | Różnych | Wartości, wyrażenia lub obiekty do porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy oba są równoważne. Zwraca wartość false, gdy nie jest odpowiednikiem. |
||||

*Przykład*

Te przykłady sprawdzić, czy określone dane wejściowe są równoważne.

```
equals(true, 1)
equals('abc', 'abcd')
```

I zwraca te wyniki:

* Pierwszy przykład: Obie wartości są równoważne, więc funkcja zwraca `true`.
* Drugi przykład: Obie wartości nie są równoważne, więc funkcja zwraca `false`.

<a name="first"></a>

### <a name="first"></a>Pierwszym

Zwraca pierwszy element z ciągu lub tablicy.

```
first('<collection>')
first([<collection>])
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Ciąg lub tablica | Kolekcja, w której można znaleźć pierwszy element |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*element pierwszej kolekcji*> | Dowolne | Pierwszy element w kolekcji |
||||

*Przykład*

Te przykłady znaleźć pierwszy element w tych kolekcjach:

```
first('hello')
first(createArray(0, 1, 2))
```

I zwrócić te wyniki:

* Pierwszy przykład:`"h"`
* Drugi przykład:`0`

<a name="float"></a>

### <a name="float"></a>float

Konwertuj wersję ciągu dla liczby zmiennoprzecinkowej na rzeczywistą liczbę zmiennoprzecinkową.

```
float('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg, który ma prawidłową liczbę zmiennoprzecinkową do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość float*> | Liczba zmiennoprzecinkowa | Numer zmiennoprzecinkowy dla określonego ciągu |
||||

*Przykład*

W tym przykładzie tworzy wersję ciągu dla tej liczby zmiennoprzecinkowej:

```
float('10.333')
```

I zwraca ten wynik:`10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>Formatdatetime

Zwraca sygnaturę czasową w określonym formacie.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*sygnatura czasowa sformatowana w formatach*> | Ciąg | Zaktualizowany sygnatura czasowa w określonym formacie |
||||

*Przykład*

W tym przykładzie sygnatura czasowa konwertuje do określonego formatu:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

I zwraca ten wynik:`"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Zwraca bieżący sygnatura czasowa oraz określone jednostki czasu.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Interwał*> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do dodania |
| <*jednostka czasowa*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem:*"Drugi", "Minuta", "Godzina", "Dzień", "Tydzień", "Miesiąc", "Rok" |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Bieżący sygnatura czasowa plus określona liczba jednostek czasu |
||||

*Przykład 1*

Załóżmy, że bieżący sygnatura czasowa to "2018-03-01T00:00:00.0000000Z".
W tym przykładzie dodaje pięć dni do tego sygnatury czasowe:

```
getFutureTime(5, 'Day')
```

I zwraca ten wynik:`"2018-03-06T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że bieżący sygnatura czasowa to "2018-03-01T00:00:00.0000000Z".
W tym przykładzie dodaje pięć dni i konwertuje wynik do formatu "D":

```
getFutureTime(5, 'Day', 'D')
```

I zwraca ten wynik:`"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Zwraca bieżący sygnatura czasowa minus określone jednostki czasu.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Interwał*> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do odjęciu |
| <*jednostka czasowa*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem:*"Drugi", "Minuta", "Godzina", "Dzień", "Tydzień", "Miesiąc", "Rok" |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Bieżący sygnatura czasowa pomniejszona o określoną liczbę jednostek czasu |
||||

*Przykład 1*

Załóżmy, że bieżący sygnatura czasowa to "2018-02-01T00:00:00.0000000Z".
W tym przykładzie odejmuje pięć dni od tego sygnatury czasowych:

```
getPastTime(5, 'Day')
```

I zwraca ten wynik:`"2018-01-27T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że bieżący sygnatura czasowa to "2018-02-01T00:00:00.0000000Z".
W tym przykładzie odejmuje pięć dni i konwertuje wynik na format "D":

```
getPastTime(5, 'Day', 'D')
```

I zwraca ten wynik:`"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Sprawdź, czy pierwsza wartość jest większa niż druga wartość.
Zwraca wartość true, gdy pierwsza wartość jest większa, lub zwraca wartość false, gdy mniej.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Całkowita, Float lub Ciąg | Pierwsza wartość, aby sprawdzić, czy większa niż druga wartość |
| <*Compareto*> | Tak | Odpowiednio całkowita, float lub string | Wartość porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy pierwsza wartość jest większa niż druga wartość. Zwraca wartość false, gdy pierwsza wartość jest równa lub mniejsza niż druga wartość. |
||||

*Przykład*

Te przykłady sprawdzić, czy pierwsza wartość jest większa niż druga wartość:

```
greater(10, 5)
greater('apple', 'banana')
```

I zwrócić te wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Sprawdź, czy pierwsza wartość jest większa lub równa drugiej wartości.
Zwraca wartość true, gdy pierwsza wartość jest większa lub równa, lub zwraca wartość false, gdy pierwsza wartość jest mniejsza.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Całkowita, Float lub Ciąg | Pierwsza wartość, aby sprawdzić, czy większa lub równa drugiej wartości |
| <*Compareto*> | Tak | Odpowiednio całkowita, float lub string | Wartość porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy pierwsza wartość jest większa lub równa drugiej wartości. Zwraca wartość false, gdy pierwsza wartość jest mniejsza niż druga wartość. |
||||

*Przykład*

Te przykłady sprawdzić, czy pierwsza wartość jest większa lub równa niż druga wartość:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

I zwrócić te wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="guid"></a>

### <a name="guid"></a>Identyfikator GUID

Generowanie unikatowego identyfikatora globalnego (GUID) jako ciągu, na przykład "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Ponadto można określić inny format identyfikatora GUID innego niż domyślny format "D", który jest 32 cyframi oddzielonymi myślnikami.

```
guid('<format>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Formacie*> | Nie | Ciąg | Specyfikator pojedynczego [formatu](https://msdn.microsoft.com/library/97af8hh4) zwracanego identyfikatora GUID. Domyślnie format to "D", ale można użyć "N", "D", "B", "P" lub "X". |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Wartość GUID*> | Ciąg | Losowo generowany identyfikator GUID |
||||

*Przykład*

W tym przykładzie generuje ten sam identyfikator GUID, ale jako 32 cyfry, oddzielone łącznikami i ujęte w nawiasy:

```
guid('P')
```

I zwraca ten wynik:`"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Sprawdź, czy wyrażenie jest prawdziwe czy fałszywe.
Na podstawie wyniku zwraca określoną wartość.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wyrażenie*> | Tak | Wartość logiczna | Wyrażenie do sprawdzenia |
| <*wartośćIfTrue*> | Tak | Dowolne | Wartość zwracana, gdy wyrażenie jest prawdziwe |
| <*wartośćIfFalse*> | Tak | Dowolne | Wartość zwracana, gdy wyrażenie jest fałszywe |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*określona wartość zwrotu*> | Dowolne | Określona wartość zwracana na podstawie tego, czy wyrażenie jest prawdziwe, czy fałszywe |
||||

*Przykład*

W tym `"yes"` przykładzie zwraca, ponieważ określone wyrażenie zwraca true.
W przeciwnym razie `"no"`w przykładzie zwraca:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>Indexof

Zwraca pozycję początkową lub wartość indeksu dla podciągów.
W tej funkcji nie jest rozróżniana wielkość liter, a indeksy zaczynają się od liczby 0.

```
indexOf('<text>', '<searchText>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg, który ma podciąg do znalezienia |
| <*tekst wyszukiwania*> | Tak | Ciąg | Podciąg, aby znaleźć |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość indeksu*>| Liczba całkowita | Pozycja początkowa lub wartość indeksu dla określonego podciągu. <p>Jeśli ciąg nie zostanie znaleziony, zwróć liczbę -1. |
||||

*Przykład*

W tym przykładzie znajduje wartość indeksu początkowego dla podciągu "świat" w ciągu "hello world":

```
indexOf('hello world', 'world')
```

I zwraca ten wynik:`6`

<a name="int"></a>

### <a name="int"></a>int

Zwraca wersję całkowitą dla ciągu.

```
int('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wynik liczby całkowitej*> | Liczba całkowita | Wersja całkowita dla określonego ciągu |
||||

*Przykład*

W tym przykładzie tworzy wersję całkowitą dla ciągu "10":

```
int('10')
```

I zwraca ten wynik:`10`

<a name="json"></a>

### <a name="json"></a>json

Zwraca wartość typu ISON (JavaScript Object Notation) lub obiekt dla ciągu lub pliku XML.

```
json('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg lub kod XML | Ciąg lub XML do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Wynik JSON*> | Typ macierzysty JSON lub obiekt macierzysty | Wartość typu macierzystego JSON lub obiekt dla określonego ciągu lub XML. Jeśli ciąg ma wartość null, funkcja zwraca pusty obiekt. |
||||

*Przykład 1*

W tym przykładzie konwertuje ten ciąg na wartość JSON:

```
json('[1, 2, 3]')
```

I zwraca ten wynik:`[1, 2, 3]`

*Przykład 2*

W tym przykładzie konwertuje ten ciąg na JSON:

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

W tym przykładzie konwertuje ten kod XML na JSON:

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

### <a name="intersection"></a>Przecięcia

Zwraca kolekcję, która ma *tylko* wspólne elementy w określonych kolekcjach.
Aby pojawić się w wyniku, element musi pojawić się we wszystkich kolekcjach przekazanych do tej funkcji.
Jeśli jeden lub więcej elementów ma taką samą nazwę, ostatni element o tej nazwie pojawia się w wyniku.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*kolekcja1*>, *kolekcja <2*>, ... | Tak | Tablica lub obiekt, ale nie oba | Kolekcje, z których *chcesz, aby tylko* wspólne elementy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*przedmioty wspólne*> | Tablica lub obiekt, odpowiednio | Kolekcja, która ma tylko wspólne elementy w określonych kolekcjach |
||||

*Przykład*

W tym przykładzie znajduje wspólne elementy w tych tablicach:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

I zwraca tablicę z *tylko* tymi elementami:`[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Zwraca ciąg, który ma wszystkie elementy z tablicy i ma każdy znak oddzielony *ogranicznikiem*.

```
join([<collection>], '<delimiter>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Tablica | Tablica, która ma elementy do sprzężenia |
| <*Ogranicznik*> | Tak | Ciąg | Separator, który pojawia się między każdym znakiem w wynikowym ciągu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*char1*><*ogranicznik*><*char2*><*>...* | Ciąg | Wynikowy ciąg utworzony ze wszystkich elementów w określonej tablicy |
||||

*Przykład*

W tym przykładzie tworzy ciąg ze wszystkich elementów w tej tablicy z określonym znakiem jako ogranicznik:

```
join(createArray('a', 'b', 'c'), '.')
```

I zwraca ten wynik:`"a.b.c"`

<a name="last"></a>

### <a name="last"></a>ostatni

Zwróć ostatni element z kolekcji.

```
last('<collection>')
last([<collection>])
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Ciąg lub tablica | Kolekcja, w której można znaleźć ostatni element |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ostatni element kolekcji*> | Ciąg lub tablica, odpowiednio | Ostatni element w kolekcji |
||||

*Przykład*

Te przykłady znaleźć ostatni element w tych kolekcjach:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

I zwraca te wyniki:

* Pierwszy przykład:`"d"`
* Drugi przykład:`3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>Lastindexof

Zwraca pozycję początkową lub wartość indeksu dla ostatniego wystąpienia podciągów.
W tej funkcji nie jest rozróżniana wielkość liter, a indeksy zaczynają się od liczby 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg, który ma podciąg do znalezienia |
| <*tekst wyszukiwania*> | Tak | Ciąg | Podciąg, aby znaleźć |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*końcowa wartość indeksu*> | Liczba całkowita | Pozycja początkowa lub wartość indeksu dla ostatniego wystąpienia określonego podciągu. <p>Jeśli ciąg nie zostanie znaleziony, zwróć liczbę -1. |
||||

*Przykład*

W tym przykładzie znajduje wartość indeksu początkowego dla ostatniego wystąpienia podciągu "świat" w ciągu "hello world":

```
lastIndexOf('hello world', 'world')
```

I zwraca ten wynik:`6`

<a name="length"></a>

### <a name="length"></a>length

Zwraca liczbę elementów w kolekcji.

```
length('<collection>')
length([<collection>])
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Ciąg lub tablica | Kolekcja z elementami do zliczenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*długość lub liczba*> | Liczba całkowita | Liczba elementów w kolekcji |
||||

*Przykład*

W tych przykładach zlicza się liczbę elementów w tych kolekcjach:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

I zwrócić ten wynik:`4`

<a name="less"></a>

### <a name="less"></a>less

Sprawdź, czy pierwsza wartość jest mniejsza niż druga wartość.
Zwraca wartość true, gdy pierwsza wartość jest mniejsza lub zwraca wartość false, gdy pierwsza wartość jest większa.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Całkowita, Float lub Ciąg | Pierwsza wartość, aby sprawdzić, czy mniejsza niż druga wartość |
| <*Compareto*> | Tak | Odpowiednio całkowita, float lub string | Pozycja porównawcza |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy pierwsza wartość jest mniejsza niż druga wartość. Zwraca wartość false, gdy pierwsza wartość jest równa lub większa niż druga wartość. |
||||

*Przykład*

Te przykłady sprawdzić, czy pierwsza wartość jest mniejsza niż druga wartość.

```
less(5, 10)
less('banana', 'apple')
```

I zwrócić te wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Sprawdź, czy pierwsza wartość jest mniejsza lub równa drugiej wartości.
Zwraca wartość true, gdy pierwsza wartość jest mniejsza lub równa, lub zwraca wartość false, gdy pierwsza wartość jest większa.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Całkowita, Float lub Ciąg | Pierwsza wartość, aby sprawdzić, czy jest mniejsza lub równa drugiej wartości |
| <*Compareto*> | Tak | Odpowiednio całkowita, float lub string | Pozycja porównawcza |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false  | Wartość logiczna | Zwraca wartość true, gdy pierwsza wartość jest mniejsza lub równa drugiej wartości. Zwraca wartość false, gdy pierwsza wartość jest większa niż druga wartość. |
||||

*Przykład*

Te przykłady sprawdzić, czy pierwsza wartość jest mniejsza lub równa niż druga wartość.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

I zwrócić te wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="max"></a>

### <a name="max"></a>max

Zwraca najwyższą wartość z listy lub tablicy z liczbami, które są włącznie na obu końcach.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*numer1*>, <*numer2*>, ... | Tak | Całkowita, Float lub obie te te elementy | Zestaw liczb, z których ma być najwyższa wartość |
| [<numer *1*>, <numer *2*>,...] | Tak | Tablica — całkowita, float lub obie te 2 | Tablica liczb, z których ma być najwyższa wartość |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość maksymalna*> | Całkowita lub float | Najwyższa wartość w określonej tablicy lub zestawie liczb |
||||

*Przykład*

Te przykłady uzyskać najwyższą wartość z zestawu liczb i tablicy:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

I zwrócić ten wynik:`3`

<a name="min"></a>

### <a name="min"></a>min

Zwraca najniższą wartość z zestawu liczb lub tablicy.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*numer1*>, <*numer2*>, ... | Tak | Całkowita, Float lub obie te te elementy | Zestaw liczb, z których ma być najniższa wartość |
| [<numer *1*>, <numer *2*>,...] | Tak | Tablica — całkowita, float lub obie te 2 | Tablica liczb, z których ma być najniższa wartość |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość min.*> | Całkowita lub float | Najniższa wartość w określonym zestawie liczb lub określonej tablicy |
||||

*Przykład*

Te przykłady uzyskać najniższą wartość w zestawie liczb i tablicy:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

I zwrócić ten wynik:`1`

<a name="mod"></a>

### <a name="mod"></a>Mod

Zwróć pozostałą część z podzielenia dwóch liczb.
Aby uzyskać wynik liczby całkowitej, zobacz [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Dywidendy*> | Tak | Całkowita lub float | Liczba do podzielenia przez *dzielnik* |
| <*Dzielnik*> | Tak | Całkowita lub float | Liczba, która dzieli *dywidendę*, ale nie może być 0. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*modulo-wynik*> | Całkowita lub float | Pozostała część z podzielenia pierwszej liczby przez drugą liczbę |
||||

*Przykład*

W tym przykładzie dzieli pierwszą liczbę przez drugą liczbę:

```
mod(3, 2)
```

I zwrócić ten wynik:`1`

<a name="mul"></a>

### <a name="mul"></a>Mul

Zwróć produkt z pomnożenia dwóch liczb.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wieloplikowy1*> | Tak | Całkowita lub float | Liczba do pomnożenia przez *multiplicand2* |
| <*multiplicand2*> | Tak | Całkowita lub float | Liczba wielokrotności *multiplicand1* |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wynik produktu*> | Całkowita lub float | Produkt z pomnożenia pierwszej liczby przez drugą liczbę |
||||

*Przykład*

W tych przykładach wielokrotność pierwszej liczby przez drugą liczbę:

```
mul(1, 2)
mul(1.5, 2)
```

I zwrócić te wyniki:

* Pierwszy przykład:`2`
* Drugi przykład`3`

<a name="not"></a>

### <a name="not"></a>not

Sprawdź, czy wyrażenie jest fałszywe.
Zwraca wartość true, gdy wyrażenie jest false lub zwraca false, gdy true.

```json
not(<expression>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wyrażenie*> | Tak | Wartość logiczna | Wyrażenie do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy wyrażenie jest false. Zwraca wartość false, gdy wyrażenie jest prawdziwe. |
||||

*Przykład 1*

Te przykłady sprawdzić, czy określone wyrażenia są fałszywe:

```json
not(false)
not(true)
```

I zwrócić te wyniki:

* Pierwszy przykład: Wyrażenie jest fałszywe, `true`więc funkcja zwraca .
* Drugi przykład: Wyrażenie jest prawdziwe, `false`więc funkcja zwraca .

*Przykład 2*

Te przykłady sprawdzić, czy określone wyrażenia są fałszywe:

```json
not(equals(1, 2))
not(equals(1, 1))
```

I zwrócić te wyniki:

* Pierwszy przykład: Wyrażenie jest fałszywe, `true`więc funkcja zwraca .
* Drugi przykład: Wyrażenie jest prawdziwe, `false`więc funkcja zwraca .

<a name="or"></a>

### <a name="or"></a>lub

Sprawdź, czy co najmniej jedno wyrażenie jest prawdziwe.
Zwraca wartość true, gdy co najmniej jedno wyrażenie jest prawdziwe, lub zwraca false, gdy wszystkie są false.

```
or(<expression1>, <expression2>, ...)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Tak | Wartość logiczna | Wyrażenia do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy co najmniej jedno wyrażenie jest prawdziwe. Zwraca wartość false, gdy wszystkie wyrażenia są fałszywe. |
||||

*Przykład 1*

Te przykłady sprawdzić, czy co najmniej jedno wyrażenie jest prawdziwe:

```json
or(true, false)
or(false, false)
```

I zwrócić te wyniki:

* Pierwszy przykład: Co najmniej jedno wyrażenie jest `true`prawdziwe, więc funkcja zwraca .
* Drugi przykład: Oba wyrażenia są fałszywe, `false`więc funkcja zwraca .

*Przykład 2*

Te przykłady sprawdzić, czy co najmniej jedno wyrażenie jest prawdziwe:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

I zwrócić te wyniki:

* Pierwszy przykład: Co najmniej jedno wyrażenie jest `true`prawdziwe, więc funkcja zwraca .
* Drugi przykład: Oba wyrażenia są fałszywe, `false`więc funkcja zwraca .

<a name="rand"></a>

### <a name="rand"></a>rand

Zwraca losową liczę całkowitą z określonego zakresu, która jest włącznie tylko na końcu początkowym.

```
rand(<minValue>, <maxValue>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Minvalue*> | Tak | Liczba całkowita | Najniższa liczba całkowita w zakresie |
| <*Maxvalue*> | Tak | Liczba całkowita | Liczba całkowita, która następuje po najwyższej liczby całkowitej w zakresie, który funkcja może zwrócić |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wynik losowy*> | Liczba całkowita | Losowa liczba całkowita zwrócona z określonego zakresu |
||||

*Przykład*

W tym przykładzie pobiera losową liczbę całkowitą z określonego zakresu, z wyłączeniem wartości maksymalnej:

```
rand(1, 5)
```

I zwraca jedną z tych liczb `1` `2`w `3`wyniku: , , , lub`4`

<a name="range"></a>

### <a name="range"></a>range

Zwraca tablicę całkowitą, która rozpoczyna się od określonej liczby całkowitej.

```
range(<startIndex>, <count>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Startindex*> | Tak | Liczba całkowita | Wartość całkowita uruchamiana jako pierwszy element |
| <*Liczba*> | Tak | Liczba całkowita | Liczba liczb całkowitych w tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<> *wynik zakresu]* | Tablica | Tablica z liczbami całkowitymi zaczyna się od określonego indeksu |
||||

*Przykład*

W tym przykładzie tworzy tablicę liczb całkowitych, która rozpoczyna się od określonego indeksu i ma określoną liczbę liczb całkowitych:

```
range(1, 4)
```

I zwraca ten wynik:`[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>Zastąp

Zamień podciąg na określony ciąg i zwróć ciąg wynikowy. W tej funkcji rozróżniana jest wielkość liter.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg, który ma podciąg do zastąpienia |
| <*staryTekst*> | Tak | Ciąg | Podciąg do wymiany |
| <*nowy Tekst*> | Tak | Ciąg | Ciąg zastępczy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany tekst*> | Ciąg | Zaktualizowany ciąg po wymianie podciągów <p>Jeśli podciąg nie zostanie znaleziony, zwróć oryginalny ciąg. |
||||

*Przykład*

W tym przykładzie znajduje się podciąg "stary" w "starym ciągu" i zastępuje "stary" "nowy":

```
replace('the old string', 'old', 'new')
```

I zwraca ten wynik:`"the new string"`

<a name="skip"></a>

### <a name="skip"></a>Pomiń

Usuń elementy z przodu kolekcji i zwróć *wszystkie pozostałe* elementy.

```
skip([<collection>], <count>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Tablica | Kolekcja, której elementy chcesz usunąć |
| <*Liczba*> | Tak | Liczba całkowita | Dodatnia liczba całkowita dla liczby elementów do usunięcia z przodu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<> *zbierania aktualizacji]* | Tablica | Zaktualizowana kolekcja po usunięciu określonych elementów |
||||

*Przykład*

W tym przykładzie usuwa jeden element, numer 0, z przodu określonej tablicy:

```
skip(createArray(0, 1, 2, 3), 1)
```

I zwraca tę tablicę z pozostałymi elementami:`[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Zwraca tablicę zawierającą podciągi, oddzielone przecinkami, na podstawie określonego znaku ogranicznika w oryginalnym ciągu.

```
split('<text>', '<delimiter>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg do oddzielenia na podciągi na podstawie określonego ogranicznika w oryginalnym ciągu |
| <*Ogranicznik*> | Tak | Ciąg | Znak w oryginalnym ciągu do użycia jako ogranicznik |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*podciąg1*>,<*podciąg2*>,...] | Tablica | Tablica zawierająca podciągi z oryginalnego ciągu, oddzielona przecinkami |
||||

*Przykład*

W tym przykładzie tworzy tablicę z podciągami z określonego ciągu na podstawie określonego znaku jako ogranicznik:

```
split('a_b_c', '_')
```

I zwraca tę tablicę jako wynik:`["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Zwraca początek dnia dla sygnatury czasowej.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Określony sygnatura czasowa, ale zaczynający się od znaku zero godziny dla dnia |
||||

*Przykład*

W tym przykładzie znajduje początek dnia dla tej sygnatury czasowej:

```
startOfDay('2018-03-15T13:30:30Z')
```

I zwraca ten wynik:`"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Zwraca początek godziny dla sygnatury czasowej.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Określony sygnatura czasowa, ale zaczynający się od znaku zero-minute dla godziny |
||||

*Przykład*

W tym przykładzie znajduje początek godziny dla tej sygnatury czasowej:

```
startOfHour('2018-03-15T13:30:30Z')
```

I zwraca ten wynik:`"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Zwraca początek miesiąca dla sygnatury czasowej.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Określony sygnatura czasowa, ale rozpoczynający się pierwszego dnia miesiąca w znaku zero godziny |
||||

*Przykład*

W tym przykładzie zwraca początek miesiąca dla tej sygnatury czasowej:

```
startOfMonth('2018-03-15T13:30:30Z')
```

I zwraca ten wynik:`"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Sprawdź, czy ciąg zaczyna się od określonego podciągu.
Zwraca wartość true po znalezieniu podciągów lub zwraca wartość false, gdy nie zostanie znaleziony.
W tej funkcji nie jest rozróżniana wielkość liter.

```
startsWith('<text>', '<searchText>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg do sprawdzenia |
| <*tekst wyszukiwania*> | Tak | Ciąg | Ciąg początkowy do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false  | Wartość logiczna | Zwraca wartość true po znalezieniu podciągów początkowych. Zwraca wartość false, gdy nie znaleziono. |
||||

*Przykład 1*

W tym przykładzie sprawdza, czy ciąg "hello world" zaczyna się od podciągu "hello":

```
startsWith('hello world', 'hello')
```

I zwraca ten wynik:`true`

*Przykład 2*

W tym przykładzie sprawdza, czy ciąg "hello world" zaczyna się od podciągu "pozdrowienia":

```
startsWith('hello world', 'greetings')
```

I zwraca ten wynik:`false`

<a name="string"></a>

### <a name="string"></a>ciąg

Zwraca wersję ciągu dla wartości.

```
string(<value>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Dowolne | Wartość do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość ciągu*> | Ciąg | Wersja ciągu dla określonej wartości |
||||

*Przykład 1*

W tym przykładzie tworzy wersję ciągu dla tej liczby:

```
string(10)
```

I zwraca ten wynik:`"10"`

*Przykład 2*

W tym przykładzie tworzy ciąg dla określonego obiektu JSON\\i używa znaku ukośnika odwrotnego ( ) jako znaku ucieczki dla znaku podwójnego cudzysłowu (").

```
string( { "name": "Sophie Owen" } )
```

I zwraca ten wynik:`"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>Sub

Zwraca wynik z odejmowania drugiej liczby od pierwszej liczby.

```
sub(<minuend>, <subtrahend>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Tak | Całkowita lub float | Liczba, od której można odjąć *odchylić odchylić odchylić* |
| <*Odjemnik*> | Tak | Całkowita lub float | Liczba do odjęcie od *minuend* |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Wynik*> | Całkowita lub float | Wynik odejmowania drugiego numeru od pierwszej liczby |
||||

*Przykład*

W tym przykładzie odejmuje drugą liczbę od pierwszej liczby:

```
sub(10.3, .3)
```

I zwraca ten wynik:`10`

<a name="substring"></a>

### <a name="substring"></a>Podciąg

Zwraca znaki z ciągu, zaczynając od określonej pozycji lub indeksu.
Wartości indeksu zaczynają się od liczby 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg, którego znaki chcesz |
| <*Startindex*> | Tak | Liczba całkowita | Liczba dodatnia równa lub większa niż 0, której chcesz użyć jako pozycji wyjściowej lub wartości indeksu |
| <*Długość*> | Tak | Liczba całkowita | Dodatnia liczba znaków, które mają być w podciąg |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wynik podciągowy*> | Ciąg | Podciąg z określoną liczbą znaków, zaczynając od określonej pozycji indeksu w ciągu źródłowym |
||||

*Przykład*

W tym przykładzie tworzy podciąg pięcioznakowy z określonego ciągu, zaczynając od wartości indeksu 6:

```
substring('hello world', 6, 5)
```

I zwraca ten wynik:`"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>odejmowanieOdczasu

Odejmij liczbę jednostek czasu od sygnatury czasowej.
Zobacz też [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Interwał*> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do odjęciu |
| <*jednostka czasowa*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem:*"Drugi", "Minuta", "Godzina", "Dzień", "Tydzień", "Miesiąc", "Rok" |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Sygnatura czasowa pomniejszona o określoną liczbę jednostek czasu |
||||

*Przykład 1*

W tym przykładzie odejmuje jeden dzień od tego sygnatury czasowej:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

I zwraca ten wynik:`"2018-01-01T00:00:00:0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje jeden dzień od tego sygnatury czasowej:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

I zwraca ten wynik przy użyciu opcjonalnego formatu "D":`"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>wziąć

Zwracaj przedmioty z przodu kolekcji.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Ciąg lub tablica | Kolekcja, której elementy chcesz |
| <*Liczba*> | Tak | Liczba całkowita | Dodatnia liczba całkowita dla liczby elementów, które mają od przodu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*> podzbioru* lub [<*podzbiór*>] | Ciąg lub tablica, odpowiednio | Ciąg lub tablica, która ma określoną liczbę elementów pobranych z przodu oryginalnej kolekcji |
||||

*Przykład*

Te przykłady uzyskać określoną liczbę elementów z przodu tych kolekcji:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

I zwrócić te wyniki:

* Pierwszy przykład:`"abc"`
* Drugi przykład:`[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>Kleszcze

Zwraca `ticks` wartość właściwości dla określonej sygnatury czasowej.
*Znacznik* to 100-nanosekundowy interwał.

```
ticks('<timestamp>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg sygnatury czasowej |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*liczba znaczników*> | Liczba całkowita | Liczba znaczników od określonej sygnatury czasowej |
||||

<a name="toLower"></a>

### <a name="tolower"></a>Tolower

Zwraca ciąg w formacie małych liter. Jeśli znak w ciągu nie ma wersji małych liter, ten znak pozostaje niezmieniony w zwracanym ciągu.

```
toLower('<text>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg do zwrócenia w formacie małych liter |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*tekst przy małych literach*> | Ciąg | Oryginalny ciąg w formacie małych liter |
||||

*Przykład*

W tym przykładzie konwertuje ten ciąg na małe litery:

```
toLower('Hello World')
```

I zwraca ten wynik:`"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>Toupper

Zwraca ciąg wielkimi literami. Jeśli znak w ciągu nie ma wersji wielkich liter, ten znak pozostaje niezmieniony w zwróconym ciągu.

```
toUpper('<text>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg do zwrócenia w formacie wielkich liter |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*tekst wielkich liter*> | Ciąg | Oryginalny ciąg w formacie wielkich liter |
||||

*Przykład*

W tym przykładzie konwertuje ten ciąg na wielkie litery:

```
toUpper('Hello World')
```

I zwraca ten wynik:`"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>Przycinanie

Usuń interweniujący i końcowy biały znak z ciągu i zwraca zaktualizowany ciąg.

```
trim('<text>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg, który ma interweniujący i końcowy biały, aby usunąć |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updatedText*> | Ciąg | Zaktualizowana wersja oryginalnego ciągu bez interlinia lub końcowego odstępu |
||||

*Przykład*

W tym przykładzie usuwa interweniujący i końcowy biały z ciągu " Hello World ":

```
trim(' Hello World  ')
```

I zwraca ten wynik:`"Hello World"`

<a name="union"></a>

### <a name="union"></a>unia

Zwraca kolekcję, która ma *wszystkie* elementy z określonych kolekcji.
Aby pojawić się w wyniku, element może pojawić się w dowolnej kolekcji przekazane do tej funkcji. Jeśli jeden lub więcej elementów ma taką samą nazwę, ostatni element o tej nazwie pojawia się w wyniku.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*kolekcja1*>, *kolekcja <2*>, ...  | Tak | Tablica lub obiekt, ale nie oba | Kolekcje, z których mają być *wszystkie* elementy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*aktualizacjaCollection*> | Tablica lub obiekt, odpowiednio | Kolekcja ze wszystkimi elementami z określonych kolekcji — bez duplikatów |
||||

*Przykład*

W tym przykładzie pobiera *wszystkie* elementy z tych kolekcji:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

I zwraca ten wynik:`[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Zwraca wersję zakodowaną w jednolitym identyfikatorze zasobu (URI) dla ciągu, zastępując znaki niebezpieczne dla adresów URL znakami ucieczki.
Użyj tej funkcji, a nie [encodeUriComponent()](#encodeUriComponent).
Chociaż obie funkcje działają `uriComponent()` w ten sam sposób, jest preferowany.

```
uriComponent('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg do konwersji do formatu zakodowanego na URI |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zakodowane-uri*> | Ciąg | Ciąg zakodowany w kodach URI ze znakami ucieczki |
||||

*Przykład*

W tym przykładzie tworzy wersję zakodowaną w identyfikatorze URI dla tego ciągu:

```
uriComponent('https://contoso.com')
```

I zwraca ten wynik:`"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Zwraca wersję binarną dla składnika jednolitego identyfikatora zasobu (URI).

```
uriComponentToBinary('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg zakodowany w kodowaniu URI do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*bin-for-encoded-uri*> | Ciąg | Wersja binarna dla ciągu zakodowanego zgodnie z identyfikatorem URI. Zawartość binarna jest zakodowana w formacie `$content`base64 i reprezentowana przez program . |
||||

*Przykład*

W tym przykładzie tworzy wersję binarną dla tego ciągu zakodowanego na identyfikatorze URI:

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

Zwraca wersję ciągu dla jednolitego identyfikatora zasobu (URI) zakodowanego ciągu, skutecznie dekodując ciąg zakodowany w identyfikatorze URI.

```
uriComponentToString('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg zakodowany w kodowanie identyfikatorów URI do dekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dekodowane-uri*> | Ciąg | Zdekodowana wersja dla ciągu zakodowanego zgodnie z identyfikatorem URI |
||||

*Przykład*

W tym przykładzie tworzy zdekodowaną wersję ciągu dla tego ciągu zakodowanego w uisięcowym:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik:`"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>Utcnow

Zwraca bieżącą sygnaturę czasną.

```
utcNow('<format>')
```

Opcjonalnie można określić inny format za pomocą parametru> *formatu* <.

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*sygnatura bieżącego czasu*> | Ciąg | Bieżąca data i godzina |
||||

*Przykład 1*

Załóżmy, że dzisiaj jest 15 kwietnia 2018 o 13:00:00.
W tym przykładzie pobiera bieżącą sygnaturę czasą:

```
utcNow()
```

I zwraca ten wynik:`"2018-04-15T13:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że dzisiaj jest 15 kwietnia 2018 o 13:00:00.
W tym przykładzie pobiera bieżący znacznik czasu przy użyciu opcjonalnego formatu "D":

```
utcNow('D')
```

I zwraca ten wynik:`"Sunday, April 15, 2018"`

<a name="xml"></a>

### <a name="xml"></a>xml

Zwraca wersję XML dla ciągu zawierającego obiekt JSON.

```
xml('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg z obiektem JSON do konwersji <p>Obiekt JSON musi mieć tylko jedną właściwość główną, która nie może być tablicą. <br>Użyj znaku ukośnika odwrotnego (\\) jako znaku ucieczki dla znaku podwójnego cudzysłowu ("). |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wersja xml*> | Obiekt | Zakodowany kod XML dla określonego ciągu lub obiektu JSON |
||||

*Przykład 1*

W tym przykładzie tworzy wersję XML dla tego ciągu, który zawiera obiekt JSON:

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

W tym przykładzie tworzy XML dla ciągu, który zawiera ten obiekt JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

I zwraca ten wynik XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>Xpath

Sprawdź xml dla węzłów lub wartości, które pasują do wyrażenia XPath (XML Path Language) i zwraca pasujące węzły lub wartości. Wyrażenie XPath lub po prostu "XPath" ułatwia poruszanie się po strukturze dokumentu XML, dzięki czemu można wybrać węzły lub wartości obliczeniowe w zawartości XML.

```
xpath('<xml>', '<xpath>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Xml*> | Tak | Dowolne | Ciąg XML do wyszukiwania węzłów lub wartości zgodnych z wartością wyrażenia XPath |
| <*Xpath*> | Tak | Dowolne | Wyrażenie XPath używane do znajdowania pasujących węzłów lub wartości XML |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*xml-węzeł*> | XML | Węzeł XML, gdy tylko jeden węzeł pasuje do określonego wyrażenia XPath |
| <*Wartość*> | Dowolne | Wartość z węzła XML, gdy tylko pojedyncza wartość jest zgodna z określonym wyrażeniem XPath |
| [<> *xml-node1,* <> *xml-node2,* ...] </br>— lub — </br>[<*wartość1*>, <*wartość2*>, ...] | Tablica | Tablica z węzłami lub wartościami XML zgodnymi z określonym wyrażeniem XPath |
||||

*Przykład 1*

W poniższym przykładzie 1 w tym `<count></count>` przykładzie znajduje się `sum()` węzły, które pasują do węzła i dodaje te wartości węzła z funkcją:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

I zwraca ten wynik:`30`

*Przykład 2*

W tym przykładzie oba wyrażenia znajdują `<location></location>` węzły, które pasują do węzła, w określonych argumentach, które obejmują XML z obszarem nazw. Wyrażenia używają znaku ukośnika\\odwrotnego ( ) jako znaku ucieczki dla podwójnego cudzysłowu (").

* *Wyrażenie 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Wyrażenie 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Oto argumenty:

* Ten kod XML, który obejmuje obszar `xmlns="http://contoso.com"`nazw dokumentu XML, :

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Albo wyrażenie XPath w tym miejscu:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Oto węzeł wynikowy, `<location></location>` który pasuje do węzła:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Przykład 3*

Poniżej przykładu 3 w tym `<location></location>` przykładzie znajduje wartość w węźle:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

I zwraca ten wynik:`"Paris"`

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę zmiennych systemowych, których można używać w wyrażeniach, zobacz [Zmienne systemowe](control-flow-system-variables.md).
