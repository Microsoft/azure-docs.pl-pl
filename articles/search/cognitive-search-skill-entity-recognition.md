---
title: Umiejętność rozpoznawania jednostek
titleSuffix: Azure Cognitive Search
description: Wyodrębnij różne typy jednostek z tekstu w potoku wzbogacania na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: ddfca965ea32ca726df39f894ba45f9580225d9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542465"
---
#    <a name="entity-recognition-cognitive-skill"></a>Umiejętność rozpoznawania jednostek

Umiejętność **rozpoznawania jednostek** wyodrębnia jednostki różnych typów z tekstu. Ta umiejętność używa modeli uczenia maszynowego zapewnianych przez [Analiza tekstu](../cognitive-services/text-analytics/overview.md) w Cognitive Services.

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w usłudze Azure Wyszukiwanie poznawcze. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. umiejętności. Text. EntityRecognitionSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien składać się z 50 000 znaków mierzonych przez [`String.Length`](/dotnet/api/system.string.length) . Jeśli konieczne jest rozbicie danych przed wysłaniem ich do wyodrębniania kluczowych fraz, rozważ użycie [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry umiejętności

W parametrach jest rozróżniana wielkość liter i są one opcjonalne.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| `categories`    | Tablica kategorii, które mają zostać wyodrębnione.  Możliwe typy kategorii: `"Person"` ,,,,, `"Location"` `"Organization"` `"Quantity"` `"Datetime"` `"URL"` , `"Email"` . Jeśli nie podano żadnej kategorii, zwracane są wszystkie typy.|
| `defaultLanguageCode` |    Kod języka tekstu wejściowego. Obsługiwane są następujące języki: `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans` . Nie wszystkie kategorie jednostek są obsługiwane we wszystkich językach; Zobacz uwagę poniżej.|
| `minimumPrecision` | Wartość z zakresu od 0 do 1. Jeśli wynik pewności (w `namedEntities` danych wyjściowych) jest mniejszy niż ta wartość, jednostka nie jest zwracana. Wartość domyślna to 0. |
| `includeTypelessEntities` | Ustaw na, `true` Jeśli chcesz rozpoznać dobrze znane jednostki, które nie pasują do bieżących kategorii. Rozpoznane jednostki są zwracane w `entities` polu złożone dane wyjściowe. Na przykład "system Windows 10" jest dobrze znaną jednostką (produkt), ale ponieważ "produkty" nie jest obsługiwaną kategorią, ta jednostka zostanie uwzględniona w polu dane wyjściowe jednostek. Wartość domyślna to `false` |


## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

| Nazwa wejściowa      | Opis                   |
|---------------|-------------------------------|
| `languageCode`    | Opcjonalny. Wartość domyślna to `"en"`.  |
| `text`          | Tekst do analizy.          |

## <a name="skill-outputs"></a>Wyniki umiejętności

> [!NOTE]
> Nie wszystkie kategorie jednostek są obsługiwane we wszystkich językach. `"Person"` `"Location"` `"Organization"` Typy kategorii jednostek, i są obsługiwane w przypadku pełnej listy języków powyżej. Tylko _de_, _EN_, _es_, _fr_i _zh-Hans_ obsługuje wyodrębnianie `"Quantity"` typów, `"Datetime"` , `"URL"` i `"Email"` . Aby uzyskać więcej informacji, zobacz [Obsługa języka i regionu dla interfejs API analizy tekstu](../cognitive-services/text-analytics/language-support.md).  

| Nazwa wyjściowa      | Opis                   |
|---------------|-------------------------------|
| `persons`       | Tablica ciągów, w których każdy ciąg reprezentuje nazwę osoby. |
| `locations`  | Tablica ciągów, w których każdy ciąg reprezentuje lokalizację. |
| `organizations`  | Tablica ciągów, w których każdy ciąg reprezentuje organizację. |
| `quantities`  | Tablica ciągów, w których każdy ciąg reprezentuje liczbę. |
| `dateTimes`  | Tablica ciągów, w których każdy ciąg reprezentuje datę i godzinę (jak pojawia się w tekście). |
| `urls` | Tablica ciągów, w których każdy ciąg reprezentuje adres URL |
| `emails` | Tablica ciągów, w których każdy ciąg reprezentuje wiadomość e-mail |
| `namedEntities` | Tablica typów złożonych, które zawierają następujące pola: <ul><li>category</li> <li>wartość (rzeczywista nazwa jednostki)</li><li>Przesunięcie (lokalizacja, w której została znaleziona).</li><li>zaufanie (wyższa wartość oznacza, że jest to prawdziwa jednostka)</li></ul> |
| `entities` | Tablica typów złożonych, która zawiera bogate informacje o jednostkach wyodrębnionych z tekstu, z następującymi polami <ul><li> Nazwa (rzeczywista nazwa jednostki). Reprezentuje to "znormalizowany" formularz</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (łącze do strony Wikipedia dla jednostki)</li><li>bingId</li><li>Typ (kategoria rozpoznanej jednostki)</li><li>Podtyp (dostępny tylko dla niektórych kategorii, zapewnia bardziej szczegółowy widok typu jednostki)</li><li> dopasowania (złożona Kolekcja zawierająca)<ul><li>tekst (nieprzetworzony tekst dla jednostki)</li><li>Przesunięcie (lokalizacja, w której została znaleziona)</li><li>Długość (długość nieprzetworzonego tekstu jednostki)</li></ul></li></ul> |

##    <a name="sample-definition"></a>Definicja Przykładowa

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
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
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```

Należy zauważyć, że przesunięcia zwrócone dla jednostek w danych wyjściowych tej umiejętności są zwracane bezpośrednio z [interfejs API analizy tekstu](../cognitive-services/text-analytics/overview.md), co oznacza, że są one używane do indeksowania w oryginalnym ciągu, należy użyć klasy [StringInfo](/dotnet/api/system.globalization.stringinfo) w programie .NET w celu wyodrębnienia poprawnej zawartości.  [Więcej szczegółów można znaleźć tutaj.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="error-cases"></a>Przypadki błędów
Jeśli kod języka dla dokumentu nie jest obsługiwany, zwracany jest błąd i nie są wyodrębniane żadne jednostki.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)