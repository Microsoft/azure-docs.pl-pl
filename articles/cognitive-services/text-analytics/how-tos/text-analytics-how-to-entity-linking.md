---
title: Korzystanie z rozpoznawania jednostek z interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak identyfikować i odróżnić tożsamość jednostki znalezionej w tekście za pomocą interfejsu API REST analiza tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: 0876dd00933203c943417d87978567cf555a3e4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599004"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Jak używać rozpoznawania jednostek nazwanych w analiza tekstu

Interfejs API analizy tekstu umożliwia przyjęcie tekstu bez struktury i zwrócenie listy niejednoznacznych jednostek z linkami do dodatkowych informacji w sieci Web. Interfejs API obsługuje jednocześnie funkcję rozpoznawania jednostek nazwanych (NER) dla kilku kategorii jednostek oraz łączenie jednostek.

## <a name="entity-linking"></a>Łączenie jednostek

Łączenie jednostek to możliwość identyfikacji i odróżnienia tożsamości jednostki znalezionej w tekście (na przykład określenie, czy wystąpienie słowa "Mars" odwołuje się do globalnej, czy do rzymskie akty Boże wojny). Ten proces wymaga obecności bazy wiedzy w odpowiednim języku do łączenia rozpoznanych jednostek w tekście. Łączenie jednostek używa [witryny Wikipedia](https://www.wikipedia.org/) jako tej bazy wiedzy.

## <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych (NER)

Funkcja rozpoznawania jednostek nazwanych (NER) to możliwość identyfikowania różnych jednostek w tekście i kategoryzowania ich do wstępnie zdefiniowanych klas lub typów, takich jak: osoba, lokalizacja, wydarzenie, produkt i organizacja.  

## <a name="personally-identifiable-information-pii"></a>Identyfikowalne dane osobowe

Funkcja dane OSOBowe jest częścią NER i może identyfikować i wygrupować poufne jednostki w tekście, które są skojarzone z poszczególnymi osobami, takimi jak: numer telefonu, adres e-mail, adres wysyłkowy, numer paszportu.

## <a name="named-entity-recognition-features-and-versions"></a>Funkcje i wersje rozpoznawania jednostek nazwanych

| Cecha                                                         | NER v 3.0 | NER v 3.1 — wersja zapoznawcza. 4 |
|-----------------------------------------------------------------|--------|----------|
| Metody dla żądań pojedynczych i wsadowych                          | X      | X        |
| Rozbudowane rozpoznawanie jednostek w kilku kategoriach           | X      | X        |
| Oddziel punkty końcowe do wysyłania połączeń jednostek i żądań NER. | X      | X        |
| Rozpoznawanie `PII` jednostek informacji osobistych () i kondycji ( `PHI` )        |        | X        |
| Redakcja `PII`        |        | X        |

Aby uzyskać informacje, zobacz temat [Obsługa języków](../language-support.md) .

Rozpoznawanie jednostek nazwanych v3 zapewnia rozszerzone wykrywanie w wielu typach. Obecnie NER v 3.0 może rozpoznawać jednostki w [kategorii jednostki ogólne](../named-entity-types.md).

Nazwanego rozpoznawania jednostek v 3.1 — wersja zapoznawcza. 4 obejmuje możliwości wykrywania wersji 3.0 i: 
* Możliwość wykrywania informacji osobistych ( `PII` ) przy użyciu `v3.1-preview.4/entities/recognition/pii` punktu końcowego. 
* Opcjonalny `domain=phi` parametr do wykrywania poufnych informacji o kondycji ( `PHI` ).
* [Operacja asynchroniczna](text-analytics-how-to-call-api.md) przy użyciu `/analyze` punktu końcowego.

Aby uzyskać więcej informacji, zobacz sekcję [Kategorie jednostek](../named-entity-types.md) i [punkty końcowe żądania](#request-endpoints) poniżej. Aby uzyskać więcej informacji na temat wyników pewności, zobacz [Analiza tekstu przezroczystość](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context). 

## <a name="sending-a-rest-api-request"></a>Wysyłanie żądania interfejsu API REST

### <a name="preparation"></a>Przygotowanie

Musisz mieć dokumenty JSON w tym formacie: ID, text, language.

Każdy dokument musi mieć 5 120 znaków i może mieć do 1 000 elementów (identyfikatorów) na kolekcję. Kolekcja jest przesyłana w treści żądania.

### <a name="structure-the-request"></a>Określenie struktury żądania

Utwórz żądanie POST. Można [użyć programu Poster](text-analytics-how-to-call-api.md) lub **konsoli testowania interfejsu API** w poniższych linkach, aby szybko przeprowadzić strukturę i wysłać ją. 

> [!NOTE]
> Klucz i punkt końcowy dla zasobu analiza tekstu można znaleźć w witrynie Azure Portal. Zostaną one umieszczone na stronie **szybkiego startu** zasobu w obszarze **Zarządzanie zasobami**. 


### <a name="request-endpoints"></a>Punkty końcowe żądania

#### <a name="version-31-preview"></a>[Wersja 3,1-Preview](#tab/version-3-preview)

Rozpoznawanie jednostek nazwanych `v3.1-preview.4` używa oddzielnych punktów końcowych dla żądań ner, dane osobowe i powiązania jednostek. Użyj poniższego formatu adresu URL na podstawie Twojego żądania.

**Łączenie jednostek**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/linking`

[Rozpoznawanie jednostek nazwanych wersja 3,1 — Dokumentacja dotycząca wersji zapoznawczej dla `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-4/operations/EntitiesLinking)

**Rozpoznawanie jednostek nazwanych**
* Jednostki ogólne — `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/recognition/general`

[Rozpoznawanie jednostek nazwanych wersja 3,1 — Dokumentacja dotycząca wersji zapoznawczej dla `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-4/operations/EntitiesRecognitionGeneral)

**Identyfikowalne dane osobowe**
* Personal ( `PII` ) — informacje — `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/recognition/pii`

Można również użyć opcjonalnego `domain=phi` parametru do wykrywania informacji o kondycji ( `PHI` ) w tekście. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/recognition/pii?domain=phi`

W systemie `v3.1-preview.4` odpowiedź JSON zawiera `redactedText` Właściwość, która zawiera zmodyfikowany tekst wejściowy, w którym wykryte jednostki danych osobowych są zastępowane przez `*` dla każdego znaku w jednostkach.

[Rozpoznawanie jednostek nazwanych wersja 3,1 — Dokumentacja dotycząca wersji zapoznawczej dla `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-4/operations/EntitiesRecognitionPii)

Interfejs API podejmie próbę wykrycia [kategorii jednostek na liście](../named-entity-types.md?tabs=personal) dla danego języka dokumentu. Jeśli chcesz określić, które jednostki będą wykrywane i zwracane, użyj opcjonalnego parametru "dane osobowe" z odpowiednimi kategoriami jednostek. Ten parametr umożliwia również wykrycie jednostek, które nie są domyślnie włączone dla języka dokumentu. Na przykład numer licencji sterownika francuskiego, który może wystąpić w tekocie w języku angielskim.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/recognition/pii?piiCategories=[FRDriversLicenseNumber]`

**Operacja asynchroniczna**

Począwszy od programu `v3.1-preview.4` , można wysyłać żądania ner i powiązania jednostek asynchronicznie przy użyciu `/analyze` punktu końcowego.

* Operacja asynchroniczna — `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/analyze`

Zobacz [, jak wywoływać interfejs API analizy tekstu,](text-analytics-how-to-call-api.md) Aby uzyskać informacje dotyczące wysyłania żądań asynchronicznych.

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Nazwanego rozpoznawania jednostek v3 używa oddzielnych punktów końcowych dla żądań NER i konsolidacji jednostek. Użyj poniższego formatu adresu URL na podstawie Twojego żądania:

**Łączenie jednostek**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[Odwołanie do nazwanego rozpoznawania jednostek w wersji 3,0 dla `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

**Rozpoznawanie jednostek nazwanych**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[Odwołanie do nazwanego rozpoznawania jednostek w wersji 3,0 dla `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

Ustaw nagłówek żądania w taki sposób, aby zawierał klucz interfejs API analizy tekstu. W treści żądania Podaj przygotowane dokumenty JSON.

## <a name="example-requests"></a>Przykładowe żądania

#### <a name="version-31-preview"></a>[Wersja 3,1-Preview](#tab/version-3-preview)

### <a name="example-synchronous-ner-request"></a>Przykład synchronicznego żądania NER 

Poniższy kod JSON jest przykładem zawartości, którą można wysłać do interfejsu API. Format żądania jest taki sam dla obu wersji interfejsu API.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

### <a name="example-asynchronous-ner-request"></a>Przykładowe asynchroniczne żądanie NER

Jeśli używasz `/analyze` punktu końcowego dla [operacji asynchronicznej](text-analytics-how-to-call-api.md), otrzymasz odpowiedź zawierającą zadania wysłane do interfejsu API.

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}
```

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

### <a name="example-synchronous-ner-request"></a>Przykład synchronicznego żądania NER 

Wersja 3,0 zawiera tylko operację synchroniczną. Poniższy kod JSON jest przykładem zawartości, którą można wysłać do interfejsu API. Format żądania jest taki sam dla obu wersji interfejsu API.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

---

## <a name="post-the-request"></a>Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Zapoznaj się z sekcją [limity danych](../overview.md#data-limits) w temacie Omówienie dotyczącej rozmiaru i liczby żądań wysyłanych na minutę i sekundę.

Interfejs API analizy tekstu jest bezstanowy. Na Twoim koncie nie są przechowywane żadne dane, a wyniki są zwracane natychmiast w odpowiedzi.

## <a name="view-results"></a>Wyświetlanie wyników

Wszystkie żądania POST zwracają sformatowaną w formacie JSON odpowiedź z identyfikatorami i wykrytymi właściwościami jednostki.

Dane wyjściowe są zwracane natychmiast. Wyniki można przesłać strumieniowo do aplikacji, która akceptuje kod JSON, lub zapisać do pliku w systemie lokalnym, a następnie zaimportować do aplikacji, która umożliwia sortowanie i wyszukiwanie danych oraz manipulowanie nimi. Ze względu na obsługę wielojęzycznych i emoji, odpowiedź może zawierać przesunięcia tekstu. Aby uzyskać więcej informacji, zobacz [Jak przetwarzać przesunięcia tekstu](../concepts/text-offsets.md).

### <a name="example-responses"></a>Przykładowe odpowiedzi

Wersja 3 zawiera oddzielne punkty końcowe dla ogólnych NER, dane OSOBowe i powiązania jednostek. Wersja 3,1-pareview obejmuje tryb analizy asynchronicznej. Odpowiedzi na te operacje są poniżej. 

#### <a name="version-31-preview"></a>[Wersja 3,1-Preview](#tab/version-3-preview)

### <a name="synchronous-example-results"></a>Synchroniczne wyniki przykładu

Przykład ogólnej odpowiedzi NER:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

Przykład odpowiedzi na dane OSOBowe:

```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

Przykład powiązania jednostki z odpowiedzią:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```

### <a name="example-asynchronous-result"></a>Przykładowy wynik asynchroniczny

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Przykład ogólnej odpowiedzi NER:
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

---


## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy dotyczące łączenia jednostek przy użyciu analiza tekstu w Cognitive Services. Podsumowanie:

* Dokumenty JSON w treści żądania obejmują identyfikator, tekst i kod języka.
* Żądania POST są wysyłane do jednego lub większej liczby punktów końcowych, przy użyciu spersonalizowanego [klucza dostępu i punktu końcowego](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , który jest prawidłowy dla Twojej subskrypcji.
* Dane wyjściowe odpowiedzi, które obejmują połączone jednostki (w tym wyniki pewności, przesunięcia i linki sieci Web dla każdego identyfikatora dokumentu) mogą być używane w dowolnej aplikacji

## <a name="next-steps"></a>Następne kroki

* [Przegląd analizy tekstu](../overview.md)
* [Korzystanie z biblioteki klienta analiza tekstu](../quickstarts/client-libraries-rest-api.md)
* [Co nowego](../whats-new.md)
