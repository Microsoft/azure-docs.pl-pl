---
title: Niestandardowa umiejętność wyszukiwania funkcji wyszukiwania funkcji wyszukiwania funkcji
titleSuffix: Azure Cognitive Search
description: Wyodrębnij różne encje niestandardowe z tekstu w potoku wyszukiwania kognitywnego usługi Azure Cognitive Search. Ta umiejętność jest obecnie w publicznej wersji zapoznawczej.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 3659070d4ffd4346a8827d2748e67db436fc15b3
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085743"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Umiejętność poznawcza wyszukiwania encji niestandardowej (wersja zapoznawcza)

> [!IMPORTANT] 
> Ta umiejętność jest obecnie w publicznej wersji zapoznawczej. Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Obecnie nie ma obsługi portalu ani SDK .NET.

Umiejętność **Wyszukiwanie encji niestandardowej** wyszukuje tekst z niestandardowej, zdefiniowanej przez użytkownika listy słów i fraz. Korzystając z tej listy, etykietuje wszystkie dokumenty z dowolnymi pasującymi encjami. Umiejętność obsługuje również stopień rozmytego dopasowania, które można zastosować, aby znaleźć dopasowania, które są podobne, ale nie do końca dokładne.  

Ta umiejętność nie jest powiązana z interfejsem API usług Cognitive Services i może być używana bezpłatnie w okresie podglądu. Należy jednak [dołączyć zasób usług Cognitive Services,](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services)aby zastąpić dzienny limit wzbogacenia. Dzienny limit ma zastosowanie do bezpłatnego dostępu do usług Cognitive Services, gdy jest dostępny za pośrednictwem usługi Azure Cognitive Search.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.CustomEntityLookupSkill 

## <a name="data-limits"></a>Limity danych
+ Maksymalny obsługiwany rozmiar rekordu wejściowego wynosi 256 MB. Jeśli musisz podzielić dane przed wysłaniem ich do umiejętności wyszukiwania encji niestandardowej, rozważ użycie [umiejętności Dzielenie tekstu](cognitive-search-skill-textsplit.md).
+ Tabela definicji maksymalnej liczby jednostek jest obsługiwana jako 10 MB, jeśli jest dostarczana przy użyciu parametru *entitiesDefinitionUri.* 
+ Jeśli jednostki są zdefiniowane w linii, przy użyciu *inlineEntitiesDefinition* parametr, maksymalny obsługiwany rozmiar wynosi 10 KB.

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| jednostkiDedefitionUri    | Ścieżka do pliku JSON lub CSV zawierającego cały tekst docelowy, z który ma być dopasowywać. Ta definicja jednostki jest odczytywana na początku uruchomienia indeksatora; wszelkie aktualizacje tego pliku w połowie biegu nie zostaną zrealizowane do kolejnych przebiegów. Ten config musi być dostępny za pośrednictwem protokołu HTTPS. Zobacz [format definicji jednostki niestandardowej"](#custom-entity-definition-format) poniżej dla oczekiwanego schematu CSV lub JSON.|
|wbudowanadefinicja wyszczeżeń | Wbudowane definicje encji JSON. Ten parametr zastępuje jednostkiDefinitionUri parametr, jeśli jest obecny. Nie więcej niż 10 KB konfiguracji mogą być dostarczone w ramach. Zobacz [definicję encji niestandardowej](#custom-entity-definition-format) poniżej dla oczekiwanego schematu JSON. |
|domyślny Kod Języka |    (Opcjonalnie) Kod języka tekstu wejściowego używanego do tokenizacji i nakreślenia tekstu wejściowego. Obsługiwane są następujące języki: `da, de, en, es, fi, fr, it, ko, pt`. Wartość domyślna`en`to angielski ( ). Jeśli przejdziesz format languagecode-countrycode, używana jest tylko część kodu języka formatu.  |


## <a name="skill-inputs"></a>Wprowadzanie umiejętności

| Nazwa wejściowa      | Opis                   |
|---------------|-------------------------------|
| tekst          | Tekst do analizy.          |
| languageCode    | Element opcjonalny. Wartość domyślna to `"en"`.  |


## <a name="skill-outputs"></a>Wyniki umiejętności


| Nazwa wyjściowa      | Opis                   |
|---------------|-------------------------------|
| Podmioty | Tablica obiektów, które zawierają informacje o znalezionych dopasowaniach i powiązanych metadanych. Każda ze zidentyfikowanych jednostek może zawierać następujące pola:  <ul> <li> *nazwa*: Zidentyfikowano jednostkę najwyższego poziomu. Jednostka reprezentuje formularz "znormalizowany". </li> <li> *id*: Unikatowy identyfikator jednostki zdefiniowanej przez użytkownika w "Niestandardowym formacie definicji encji".</li> <li> *description*: Opis jednostki zdefiniowany przez użytkownika w "Formacie niestandardowej definicji encji". </li> <li> *typ:* Typ jednostki zdefiniowany przez użytkownika w "Niestandardowym formacie definicji encji".</li> <li> *podtyp:* Podtyp jednostki zdefiniowany przez użytkownika w "Niestandardowym formacie definicji encji".</li>  <li> *dopas :* Kolekcja, która opisuje każdy z dopasowań dla tej encji w tekście źródłowym. Każdy mecz będzie miał następujących członków: </li> <ul> <li> *tekst*: Nieprzetworzony tekst jest zgodny z dokumentem źródłowym. </li> <li> *przesunięcie*: Miejsce, w którym znaleziono dopasowanie w tekście. </li> <li> *długość*: Długość dopasowanego tekstu. </li> <li> *matchDistance*: Liczba znaków różnych tego dopasowania pochodziła od oryginalnej nazwy jednostki lub aliasu.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Niestandardowy format definicji encji

Istnieją 3 różne sposoby dostarczania listy encji niestandardowych do umiejętności Wyszukiwanie encji niestandardowych. Listę można podać w pliku . CSV, a . JSON lub jako wbudowana definicja jako część definicji umiejętności.  

Jeśli plik definicji jest . CSV lub . JSON, ścieżka pliku musi być podana jako część *jednostekDedefitionItionUri* parametru. W takim przypadku plik jest pobierany raz na początku każdego uruchomienia indeksatora. Plik musi być dostępny tak długo, jak indeksator jest przeznaczony do uruchomienia. Ponadto plik musi być zakodowany UTF-8.

Jeśli definicja jest dostarczana w linii, powinna być podana jako wbudowana, jak zawartość parametru umiejętności *inlineEntitiesDefinition.* 

### <a name="csv-format"></a>Format CSV

Można podać definicję encji niestandardowych, których należy szukać w pliku CSV (Comma-Separated Value), udostępniając ścieżkę do pliku i ustawiając ją w parametrze umiejętności *entitiesDefinitionUri.* Ścieżka powinna znajdować się w lokalizacji https. Rozmiar pliku definicji może mieć rozmiar do 10 MB.

Format CSV jest prosty. Każdy wiersz reprezentuje unikatowy element, jak pokazano poniżej:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

W takim przypadku istnieją trzy jednostki, które mogą być zwracane jako znalezione jednostki (Bill Gates, Satya Nadella, Microsoft), ale zostaną one zidentyfikowane, jeśli którykolwiek z warunków w wierszu (aliasy) są dopasowane do tekstu. Na przykład, jeśli ciąg "William H. Gates" zostanie znaleziony w dokumencie, zostanie zwrócone dopasowanie do jednostki "Bill Gates".

### <a name="json-format"></a>Format JSON

Można podać definicję encji niestandardowych, których należy szukać również w pliku JSON. Format JSON zapewnia nieco większą elastyczność, ponieważ umożliwia definiowanie reguł dopasowania według terminu. Na przykład można określić rozmytą odległość dopasowania (odległość Damerau-Levenshtein) dla każdego terminu lub czy dopasowanie powinno być rozróżniane, czy nie. 

 Podobnie jak w przypadku plików CSV, należy podać ścieżkę do pliku JSON i ustawić go w parametrze umiejętności *entitiesDefinitionUri.* Ścieżka powinna znajdować się w lokalizacji https. Rozmiar pliku definicji może mieć rozmiar do 10 MB.

Najbardziej podstawową definicją listy encji niestandardowych JSON może być lista encji do dopasowania:

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

Bardziej złożonym przykładem definicji JSON można opcjonalnie podać identyfikator, opis, typ i podtyp każdej encji — a także inne *aliasy.* Jeśli termin aliasu jest dopasowywał, jednostka zostanie również zwrócona:

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

W poniższych tabelach opisano bardziej szczegółowo różne parametry konfiguracji, które można ustawić podczas definiowania elementów, które mają być zgodne:

|  Nazwa pola  |        Opis  |
|--------------|----------------------|
| name | Deskryptator encji najwyższego poziomu. Dopasowania w danych wyjściowych umiejętności będą pogrupowane według tej nazwy i powinny reprezentować "znormalizowane" formy znalezionego tekstu.  |
| description  | (Opcjonalnie) To pole może służyć jako przekazywanie niestandardowych metadanych dotyczących dopasowanych tekstu. Wartość tego pola będzie wyświetlana przy każdym dopasowaniu jego encji w danych wyjściowych umiejętności. |
| type | (Opcjonalnie) To pole może służyć jako przekazywanie niestandardowych metadanych dotyczących dopasowanych tekstu. Wartość tego pola będzie wyświetlana przy każdym dopasowaniu jego encji w danych wyjściowych umiejętności. |
| Podtypu | (Opcjonalnie) To pole może służyć jako przekazywanie niestandardowych metadanych dotyczących dopasowanych tekstu. Wartość tego pola będzie wyświetlana przy każdym dopasowaniu jego encji w danych wyjściowych umiejętności. |
| id | (Opcjonalnie) To pole może służyć jako przekazywanie niestandardowych metadanych dotyczących dopasowanych tekstu. Wartość tego pola będzie wyświetlana przy każdym dopasowaniu jego encji w danych wyjściowych umiejętności. |
| Casesensitive | (Opcjonalnie) Domyślnie wartość false. Wartość logiczna oznaczająca, czy porównania z nazwą jednostki powinny być wrażliwe na wielkość liter. Przykładowe dopasowania "Microsoft" mogą być niewrażliwe na przykład: Microsoft, microSoft, MICROSOFT |
| fuzzyEditDistance | (Opcjonalnie) Wartość domyślna to 0. Maksymalna wartość 5. Oznacza dopuszczalną liczbę rozbieżnych znaków, które nadal stanowiłyby dopasowanie do nazwy jednostki. Zwraca się najmniejszą możliwą rozmytość dla danego meczu.  Na przykład, jeśli odległość edycji jest ustawiona na 3, "Windows 10" nadal będzie pasować do "Windows", "Windows10" i "Windows 7". <br/> Gdy czułość wielkości liter jest ustawiona na wartość false, różnice przypadków NIE są wliczane do tolerancji rozmyć, ale w przeciwnym razie. |
| defaultCaseSensitive | (Opcjonalnie) Zmienia domyślną wartośćróżna wielkości liter dla tej encji. Służy do zmiany domyślnej wartości wszystkich aliasów caseSensitive wartości. |
| defaultFuzzyEditDistance | (Opcjonalnie) Zmienia domyślną wartość odległości edycji rozmytej dla tej encji. Może służyć do zmiany wartości domyślnej wszystkich aliasów rozmyteedytowanie wartości. |
| Aliasy | (Opcjonalnie) Tablica złożonych obiektów, które mogą służyć do określania alternatywnych pisowni lub synonimów do nazwy jednostki głównej. |

| Właściwości aliasu | Opis |
|------------------|-------------|
| tekst  | Alternatywna pisownia lub reprezentacja nazwy niektórych encji docelowej.  |
| Casesensitive | (Opcjonalnie) Działa tak samo jak jednostka główna "caseSensitive" parametr powyżej, ale ma zastosowanie tylko do tego aliasu. |
| fuzzyEditDistance | (Opcjonalnie) Działa tak samo jak jednostka główna "fuzzyEditDistance" parametr powyżej, ale ma zastosowanie tylko do tego aliasu. |


### <a name="inline-format"></a>Format wbudowany

W niektórych przypadkach może być bardziej wygodne, aby zapewnić listę niestandardowych jednostek, aby dopasować wbudowane bezpośrednio do definicji umiejętności. W takim przypadku można użyć podobnego formatu JSON do opisanego powyżej, ale jest on inlined w definicji umiejętności.
Tylko konfiguracje o rozmiarze mniejszym niż 10 KB (rozmiar serializowany) można zdefiniować w linii. 

##    <a name="sample-definition"></a>Przykładowa definicja

Poniżej przedstawiono przykładową definicję umiejętności przy użyciu formatu wbudowanego:

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
        "type": "Harware",
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
Alternatywnie, jeśli zdecydujesz się podać wskaźnik do pliku definicji jednostek, przykładowa definicja umiejętności przy użyciu formatu entitiesDefinitionUri jest pokazana poniżej:

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
             "text": "The company microsoft was founded by Bill Gates. Microsoft's gaming console is called Xbox",
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

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Ostrzeżenie: Osiągnięto maksymalną pojemność meczów, pomijając wszystkie dalsze zduplikowane dopasowania.

To ostrzeżenie zostanie wyemitowane, jeśli liczba wykrytych dopasowań jest większa niż maksymalna dozwolona. W takim przypadku przestaniemy uwzględniać zduplikowane dopasowania. Jeśli jest to dla Ciebie nie do przyjęcia, zgłać [bilet pomocy technicznej,](https://ms.portal.azure.com/#create/Microsoft.Support) abyśmy mogli ci pomóc w indywidualnym przypadku użycia.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Umiejętność rozpoznawania encji (wyszukiwanie dobrze znanych jednostek)](cognitive-search-skill-entity-recognition.md)
