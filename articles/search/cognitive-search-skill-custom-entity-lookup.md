---
title: Umiejętność wyszukiwania w wyszukiwaniu jednostek niestandardowych
titleSuffix: Azure Cognitive Search
description: Wyodrębnij różne jednostki niestandardowe z tekstu w potoku wyszukiwania poznawczego platformy Azure Wyszukiwanie poznawcze. Ta umiejętność jest obecnie dostępna w publicznej wersji zapoznawczej.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 704763e8e6e7c5336d0ed3e1c28791fb96c77aba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97844938"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Umiejętność wyszukiwania jednostek niestandardowych (wersja zapoznawcza)

> [!IMPORTANT] 
> Ta umiejętność jest obecnie dostępna w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Obecnie nie ma obsługi portalu lub zestawu SDK platformy .NET.

Umiejętność **wyszukiwania jednostek niestandardowych** szuka tekstu na podstawie niestandardowej, zdefiniowanej przez użytkownika listy słów i fraz. Korzystając z tej listy, etykieta wszystkie dokumenty z dowolnymi zgodnymi jednostkami. Umiejętność obsługuje również stopień dopasowywania rozmytego, który można zastosować, aby znaleźć dopasowania, które są podobne, ale nie są bardzo dokładne.  

Ta umiejętność nie jest powiązana z interfejsem API Cognitive Services i może być używana bezpłatnie w okresie zapoznawczym. Mimo to należy [dołączyć zasób Cognitive Services](./cognitive-search-attach-cognitive-services.md), aby zastąpić dzienny limit wzbogacania. Dzienny limit ma zastosowanie do bezpłatnego dostępu do Cognitive Services, gdy dostęp odbywa się za pomocą usługi Azure Wyszukiwanie poznawcze.

## <a name="odatatype"></a>@odata.type  
Microsoft. umiejętności. Text. CustomEntityLookupSkill 

## <a name="data-limits"></a>Limity danych
+ Maksymalny obsługiwany rozmiar rekordu wejściowego to 256 MB. Jeśli konieczne jest rozbicie danych przed wysłaniem ich do umiejętności wyszukiwania jednostki niestandardowej, rozważ użycie [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).
+ Maksymalna obsługiwana tabela definicji jednostek to 10 MB, jeśli jest ona podawana przy użyciu parametru *entitiesDefinitionUri* . 
+ Jeśli jednostki są zdefiniowane w tekście, przy użyciu parametru *inlineEntitiesDefinition* maksymalny obsługiwany rozmiar wynosi 10 KB.

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| `entitiesDefinitionUri`    | Ścieżka do pliku JSON lub CSV zawierającego cały tekst docelowy do dopasowania. Ta definicja jednostki jest odczytywana na początku przebiegu indeksatora; wszystkie aktualizacje tego pliku nie zostaną zrealizowane do momentu kolejnego uruchomienia. Ta konfiguracja musi być dostępna za pośrednictwem protokołu HTTPS. Zobacz Format [definicji jednostki niestandardowej](#custom-entity-definition-format) "poniżej dla oczekiwanego schematu CSV lub JSON.|
|`inlineEntitiesDefinition` | Definicje jednostek śródwierszowego elementu JSON. Ten parametr zastępuje parametr entitiesDefinitionUri, jeśli jest obecny. Nie więcej niż 10 KB konfiguracji może być dostarczonych wewnętrznie. Poniżej przedstawiono [definicję jednostki niestandardowej](#custom-entity-definition-format) dla oczekiwanego schematu JSON. |
|`defaultLanguageCode` |    Obowiązkowe Kod języka tekstu wejściowego służącego do tokenize i odróżnić tekstu wejściowego. Obsługiwane są następujące języki: `da, de, en, es, fi, fr, it, ko, pt` . Wartość domyślna to angielski ( `en` ). Jeśli przekażesz format languagecode-CountryCode, zostanie użyta tylko część languagecode formatu.  |
|`globalDefaultCaseSensitive` | Obowiązkowe Domyślna wartość wielkości liter dla umiejętności. Jeśli `defaultCaseSensitive` wartość jednostki nie zostanie określona, ta wartość stanie się `defaultCaseSensitive` wartością dla tej jednostki. |
|`globalDefaultAccentSensitive` | Obowiązkowe Domyślna wartość akcentu dla umiejętności. Jeśli `defaultAccentSensitive` wartość jednostki nie zostanie określona, ta wartość stanie się `defaultAccentSensitive` wartością dla tej jednostki. |
|`globalDefaultFuzzyEditDistance` | Obowiązkowe Domyślna wartość w postaci rozmytej edycji dla umiejętności. Jeśli `defaultFuzzyEditDistance` wartość jednostki nie zostanie określona, ta wartość stanie się `defaultFuzzyEditDistance` wartością dla tej jednostki. |

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

| Nazwa wejściowa      | Opis                   |
|---------------|-------------------------------|
| `text`          | Tekst do analizy.          |
| `languageCode`    | Opcjonalny. Wartość domyślna to `"en"`.  |


## <a name="skill-outputs"></a>Wyniki umiejętności


| Nazwa wyjściowa      | Opis                   |
|---------------|-------------------------------|
| `entities` | Tablica obiektów, która zawiera informacje o znalezionych dopasowaniach i powiązanych metadanych. Każda identyfikowana jednostka może zawierać następujące pola:  <ul> <li> *Nazwa*: zidentyfikowana jednostka najwyższego poziomu. Jednostka reprezentuje formę "znormalizowana". </li> <li> *Identyfikator*: unikatowy identyfikator jednostki zdefiniowanej przez użytkownika w "niestandardowym formacie definicji jednostki".</li> <li> *Opis*: Opis jednostki zdefiniowany przez użytkownika w "niestandardowym formacie definicji jednostki". </li> <li> *Typ:* Typ jednostki zdefiniowany przez użytkownika w "niestandardowym formacie definicji jednostki".</li> <li> *Podtyp:* Podtyp jednostki zdefiniowany przez użytkownika w "niestandardowym formacie definicji jednostki".</li>  <li> *dopasowuje*: Kolekcja opisująca wszystkie dopasowania dla tej jednostki na źródłowym tekście. Każde dopasowanie będzie miało następujące elementy członkowskie: </li> <ul> <li> *tekst*: pierwotny tekst jest zgodny z dokumentem źródłowym. </li> <li> *przesunięcie*: lokalizacja, w której znaleziono dopasowanie w tekście. </li> <li> *Długość*: długość dopasowanego tekstu. </li> <li> *matchDistance*: liczba znaków, które różnią się od nazwy lub aliasu oryginalnej jednostki.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Niestandardowy format definicji jednostki

Istnieją trzy różne sposoby udostępniania listy jednostek niestandardowych dla umiejętności wyszukiwania jednostek niestandardowych. Możesz podać listę w. Plik CSV, a. Plik JSON lub jako wbudowana Definicja w ramach definicji umiejętności.  

Jeśli plik definicji jest. Wolumin CSV lub. Plik JSON, ścieżka pliku musi być podana jako część parametru *entitiesDefinitionUri* . W takim przypadku plik jest pobierany raz na początku każdego uruchomienia indeksatora. Plik musi być dostępny, o ile indeksator ma być uruchomiony. Ponadto plik musi być zakodowany w formacie UTF-8.

Jeśli definicja została podana w tekście, powinna być określona jako wbudowana jako zawartość parametru umiejętności *inlineEntitiesDefinition* . 

### <a name="csv-format"></a>Format CSV

Możesz podać Definicje jednostek niestandardowych do wyszukania w pliku wartości Comma-Separated (CSV), podając ścieżkę do pliku i ustawiając go w parametrze umiejętności *entitiesDefinitionUri*  . Ścieżka powinna znajdować się w lokalizacji HTTPS. Plik definicji może mieć rozmiar do 10 MB.

Format CSV jest prosty. Każdy wiersz reprezentuje unikatową jednostkę, jak pokazano poniżej:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

W takim przypadku istnieją trzy jednostki, które można zwrócić jako jednostki znalezione (bramy rozliczeniowe, Satya Nadella, Microsoft), ale zostaną zidentyfikowane, jeśli którykolwiek z warunków w wierszu (aliasów) zostanie dopasowany do tekstu. Na przykład jeśli w dokumencie zostanie znaleziony ciąg "William H. Brama", zostanie zwrócona zgodność z jednostką "bramy rozliczeniowe".

### <a name="json-format"></a>Format JSON

Można również podać definicję jednostek niestandardowych, które mają być wyszukiwane w pliku JSON. Format JSON zapewnia nieco większą elastyczność, ponieważ umożliwia definiowanie zgodnych reguł na okres. Na przykład można określić odległość dopasowywania rozmytego (Damerau-Levenshtein) dla każdego okresu lub czy w dopasowaniu powinna być rozróżniana wielkość liter. 

 Podobnie jak w przypadku plików CSV, należy podać ścieżkę do pliku JSON i ustawić go w parametrze umiejętności *entitiesDefinitionUri* . Ścieżka powinna znajdować się w lokalizacji HTTPS. Plik definicji może mieć rozmiar do 10 MB.

Najbardziej podstawową definicją niestandardowej listy jednostek JSON może być lista jednostek do dopasowania:

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

Bardziej skomplikowany przykład definicji JSON może opcjonalnie podać identyfikator, opis, typ i podtyp każdej jednostki, a także inne *aliasy*. W przypadku dopasowania terminu aliasu jednostka zostanie zwrócona także:

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

Poniższe tabele zawierają więcej szczegółów na temat różnych parametrów konfiguracyjnych, które można ustawić podczas definiowania jednostek do dopasowania:

|  Nazwa pola  |        Opis  |
|--------------|----------------------|
| `name` | Deskryptor jednostki najwyższego poziomu. Dopasowania w danych wyjściowych o umiejętnościach zostaną pogrupowane według tej nazwy i powinny reprezentować "znormalizowaną" formę znalezionego tekstu.  |
| `description`  | Obowiązkowe To pole może służyć jako przekazywanie niestandardowych metadanych dotyczących dopasowanego tekstu. Wartość tego pola będzie wyświetlana wraz z każdym dopasowaniem jednostki w danych wyjściowych umiejętności. |
| `type` | Obowiązkowe To pole może służyć jako przekazywanie niestandardowych metadanych dotyczących dopasowanego tekstu. Wartość tego pola będzie wyświetlana wraz z każdym dopasowaniem jednostki w danych wyjściowych umiejętności. |
| `subtype` | Obowiązkowe To pole może służyć jako przekazywanie niestandardowych metadanych dotyczących dopasowanego tekstu. Wartość tego pola będzie wyświetlana wraz z każdym dopasowaniem jednostki w danych wyjściowych umiejętności. |
| `id` | Obowiązkowe To pole może służyć jako przekazywanie niestandardowych metadanych dotyczących dopasowanego tekstu. Wartość tego pola będzie wyświetlana wraz z każdym dopasowaniem jednostki w danych wyjściowych umiejętności. |
| `caseSensitive` | Obowiązkowe Wartością domyślną jest false. Wartość logiczna określająca, czy porównania z nazwą jednostki powinny być poufne dla wielkości liter. Przykładowe dopasowania bez uwzględniania wielkości liter "Microsoft" mogą być następujące: Microsoft, microSoft, MICROSOFT |
| `accentSensitive` | Obowiązkowe Wartością domyślną jest false. Wartość logiczna określająca, czy litery akcentowane i nieakcentowane, takie jak "é" i "e", powinny być identyczne. |
| `fuzzyEditDistance` | Obowiązkowe Wartość domyślna to 0. Maksymalna wartość 5. Oznacza akceptowalną liczbę rozbieżnych znaków, które w dalszym ciągu stanowią dopasowanie do nazwy jednostki. Zostanie zwrócona najmniejsza możliwa rozmycia dla danego dopasowania.  Na przykład jeśli dla opcji Edytuj odległość wybrano wartość 3, w systemie Windows 10 nadal będzie pasować "Windows", "Windows10" i "Windows 7". <br/> Gdy ustawienie czułości przypadku ma wartość FAŁSZ, różnice wielkości liter nie są wliczane do tolerancji rozmycia, ale w przeciwnym razie. |
| `defaultCaseSensitive` | Obowiązkowe Zmienia domyślną wartość wielkości liter dla tej jednostki. Może służyć do zmiany wartości domyślnej wszystkich aliasów caseSensitive wartości. |
| `defaultAccentSensitive` | Obowiązkowe Zmienia domyślną wartość wyróżniającą akcent dla tej jednostki. Może służyć do zmiany wartości domyślnej wszystkich aliasów accentSensitive wartości.|
| `defaultFuzzyEditDistance` | Obowiązkowe Zmienia domyślną wartość w postaci rozmytej edycji dla tej jednostki. Może służyć do zmiany wartości domyślnej wszystkich aliasów fuzzyEditDistance wartości. |
| `aliases` | Obowiązkowe Tablica obiektów złożonych, która może służyć do określania alternatywnych pisowni lub synonimów nazwy jednostki głównej. |

| Właściwości aliasu | Opis |
|------------------|-------------|
| `text`  | Alternatywna pisownia lub reprezentacja nazwy jednostki docelowej.  |
| `caseSensitive` | Obowiązkowe Działa tak samo jak parametr "caseSensitive" jednostki głównej powyżej, ale ma zastosowanie tylko do tego jednego aliasu. |
| `accentSensitive` | Obowiązkowe Działa tak samo jak parametr "accentSensitive" jednostki głównej powyżej, ale ma zastosowanie tylko do tego jednego aliasu. |
| `fuzzyEditDistance` | Obowiązkowe Działa tak samo jak parametr "fuzzyEditDistance" jednostki głównej powyżej, ale ma zastosowanie tylko do tego jednego aliasu. |


### <a name="inline-format"></a>Format wbudowany

W niektórych przypadkach może być wygodniejsze udostępnienie listy jednostek niestandardowych do dopasowania bezpośrednio do definicji umiejętności. W takim przypadku można użyć podobnego formatu JSON do opisanego powyżej, ale jest on zawarty w definicji umiejętności.
W tekście można zdefiniować tylko konfiguracje o rozmiarze mniejszym niż 10 KB (rozmiar serializowany). 

##    <a name="sample-definition"></a>Definicja Przykładowa

Poniżej przedstawiono przykładową definicję umiejętności korzystającą z formatu śródwierszowego:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Hardware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
Alternatywnie, jeśli zdecydujesz się dostarczyć wskaźnik do pliku definicji jednostek, poniżej przedstawiono przykładową definicję umiejętności przy użyciu `entitiesDefinitionUri` formatu:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##    <a name="sample-input"></a>Przykładowe dane wejściowe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company, Microsoft, was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>Błędy i ostrzeżenia

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Ostrzeżenie: osiągnięto maksymalną pojemność dla dopasowań, pomija wszystkie dalsze zduplikowane dopasowania.

To ostrzeżenie będzie emitowane, jeśli liczba wykrytych dopasowań jest większa niż maksymalna dozwolona wartość. W takim przypadku będziemy przestać uwzględniać zduplikowane dopasowania. Jeśli nie możesz tego zrobić, Utwórz [bilet pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) , aby mogliśmy pomóc Ci w poszczególnym przypadku użycia.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Umiejętność rozpoznawania jednostek (do wyszukiwania dobrze znanych jednostek)](cognitive-search-skill-entity-recognition.md)
