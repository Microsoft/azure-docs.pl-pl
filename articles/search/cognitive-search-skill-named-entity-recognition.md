---
title: Umiejętność rozpoznawania nazwanych jednostek
titleSuffix: Azure Cognitive Search
description: Wyodrębnij nazwane jednostki dla osoby, lokalizacji i organizacji z tekstu w potoku wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e598f16c6b441cf986c7ac82d67c037f75be8982
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547445"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Umiejętność rozpoznawania nazwanych jednostek

Umiejętność **rozpoznawania jednostek nazwanych** wyodrębnia nazwane jednostki z tekstu. Dostępne jednostki obejmują typy `person` `location` i `organization` .

> [!IMPORTANT]
> Umiejętność rozpoznawania jednostek nazwanych została już wycofana w wyniku zamiany przez [Microsoft. umiejętności. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). Wsparcie zostało zatrzymane 15 lutego 2019, a interfejs API został usunięty z produktu na 2 maja 2019. Postępuj zgodnie z zaleceniami z [przestarzałych umiejętności wyszukiwania poznawczego](cognitive-search-skill-deprecated.md) , aby przeprowadzić migrację do obsługiwanej umiejętności.

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w usłudze Azure Wyszukiwanie poznawcze. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. umiejętności. Text. NamedEntityRecognitionSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien składać się z 50 000 znaków mierzonych przez [`String.Length`](/dotnet/api/system.string.length) . Jeśli konieczne jest rozbicie danych przed wysłaniem ich do wyodrębniania kluczowych fraz, rozważ użycie [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| categories    | Tablica kategorii, które mają zostać wyodrębnione.  Możliwe typy kategorii: `"Person"` , `"Location"` , `"Organization"` . Jeśli nie podano żadnej kategorii, zwracane są wszystkie typy.|
|defaultLanguageCode |  Kod języka tekstu wejściowego. Obsługiwane są następujące języki: `de, en, es, fr, it`|
| minimumPrecision  | Liczba z zakresu od 0 do 1. Jeśli dokładność jest mniejsza niż ta wartość, jednostka nie jest zwracana. Wartość domyślna to 0.|

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

| Nazwa wejściowa      | Opis                   |
|---------------|-------------------------------|
| languageCode  | Opcjonalny. Wartość domyślna to `"en"`.  |
| tekst          | Tekst do analizy.          |

## <a name="skill-outputs"></a>Wyniki umiejętności

| Nazwa wyjściowa     | Opis                   |
|---------------|-------------------------------|
| podatnicy      | Tablica ciągów, w których każdy ciąg reprezentuje nazwę osoby. |
| locations  | Tablica ciągów, w których każdy ciąg reprezentuje lokalizację. |
| organizations  | Tablica ciągów, w których każdy ciąg reprezentuje organizację. |
| obiekty | Tablica typów złożonych. Każdy typ złożony zawiera następujące pola: <ul><li>Kategoria ( `"person"` , `"organization"` lub `"location"` )</li> <li>wartość (rzeczywista nazwa jednostki)</li><li>Przesunięcie (lokalizacja, w której została znaleziona).</li><li>zaufanie (wartość z zakresu od 0 do 1, która reprezentuje, że wartość jest rzeczywistą jednostką)</li></ul> |

##  <a name="sample-definition"></a>Definicja Przykładowa

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
    "defaultLanguageCode": "en",
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
      }
    ]
  }
```
##  <a name="sample-input"></a>Przykładowe dane wejściowe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="warning-cases"></a>Przypadki ostrzegawcze
Jeśli kod języka dla dokumentu nie jest obsługiwany, zwracane jest ostrzeżenie i nie są wyodrębniane żadne jednostki.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Umiejętność rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md)