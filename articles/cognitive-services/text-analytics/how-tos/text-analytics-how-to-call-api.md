---
title: Wywoływanie interfejsu API usługi Text Analytics
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak można wywołać usługę Azure Cognitive Services analiza tekstu interfejsu API REST i programu Poster.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: 2977946b2e1f37aa356ee075d2caac237170df0f
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "95993346"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Jak wywołać interfejs API REST analiza tekstu

W tym artykule używamy interfejsu API REST analiza tekstu i programu [Poster](https://www.postman.com/downloads/) w celu zademonstrowania kluczowych pojęć. Interfejs API udostępnia kilka synchronicznych i asynchronicznych punktów końcowych do korzystania z funkcji usługi. 

## <a name="using-the-api-asynchronously"></a>Korzystanie z interfejsu API asynchronicznie

Począwszy od wersji v 3.1 — wersja zapoznawcza. 3, interfejs API analizy tekstu zawiera dwa asynchroniczne punkty końcowe: 

* `/analyze`Punkt końcowy dla analiza tekstu umożliwia analizowanie tego samego zestawu dokumentów tekstowych z wieloma funkcjami analizy tekstu w jednym wywołaniu interfejsu API. Wcześniej, aby korzystać z wielu funkcji, należy wykonać oddzielne wywołania interfejsu API dla każdej operacji. Tę funkcję należy wziąć pod uwagę, gdy trzeba analizować duże zestawy dokumentów za pomocą więcej niż jednej funkcji analiza tekstu.

* `/health`Punkt końcowy dla analiza tekstu kondycji, który może wyodrębnić i oznaczyć odpowiednie informacje medyczne z dokumentów klinicznych.  

Zapoznaj się z poniższą tabelą, aby zobaczyć, które funkcje mogą być używane asynchronicznie. Należy zauważyć, że tylko kilka funkcji można wywołać z `/analyze` punktu końcowego. 

| Cechy | Synchronous | Asynchroniczny |
|--|--|--|
| Wykrywanie języka | ✔ |  |
| Analiza tonacji | ✔ |  |
| Wyszukiwanie opinii | ✔ |  |
| Wyodrębnianie kluczowych fraz | ✔ | ✔* |
| Rozpoznawanie jednostek nazwanych (w tym dane OSOBowe i PHI) | ✔ | ✔* |
| Analiza tekstu dla kondycji (kontener) | ✔ |  |
| Analiza tekstu dla kondycji (API) |  | ✔  |

`*` -Wywoływana asynchronicznie za pomocą `/analyze` punktu końcowego.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## <a name="prerequisites"></a>Wymagania wstępne


> [!NOTE]
> Jeśli chcesz użyć punktów końcowych lub, będziesz potrzebować zasobu analiza tekstu przy użyciu [warstwy cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) standardowa `/analyze` `/health` .

1.  Najpierw przejdź do [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) i Utwórz nowy zasób analiza tekstu, jeśli jeszcze go nie masz. Wybierz warstwę cenową Standard (S), jeśli chcesz użyć `/analyze` `/health` punktów końcowych lub.

2.  Wybierz region, który ma być używany przez punkt końcowy.

3.  Utwórz zasób analiza tekstu i przejdź do bloku "klucze i punkt końcowy" w lewej części strony. Skopiuj klucz, który będzie używany później podczas wywoływania interfejsów API. Ten element zostanie później dodany jako wartość `Ocp-Apim-Subscription-Key` nagłówka.


<a name="json-schema"></a>

## <a name="api-request-format"></a>Format żądania interfejsu API

#### <a name="synchronous"></a>[Synchronous](#tab/synchronous)

Format żądań interfejsu API jest taki sam dla wszystkich operacji synchronicznych. Dokumenty są przesyłane w obiekcie JSON jako nieprzetworzony tekst bez struktury. KOD XML nie jest obsługiwany. Schemat JSON składa się z elementów opisanych poniżej.

| Element | Prawidłowe wartości | Wymagane? | Użycie |
|---------|--------------|-----------|-------|
|`id` |Typ danych to ciąg, ale w temacie identyfikatory dokumentów w programie mają być liczbami całkowitymi. | Wymagane | System używa identyfikatorów dostarczanych do struktury danych wyjściowych. Kody języka, kluczowe frazy i oceny tonacji są generowane dla każdego identyfikatora w żądaniu.|
|`text` | Nieprzetworzony tekst nieprzebudowany, do 5 120 znaków. | Wymagane | W przypadku wykrywania języka tekst może być wyrażony w dowolnym języku. W przypadku analizy tonacji, wyodrębniania kluczowych fraz i identyfikacji jednostek tekst musi być w [obsługiwanym języku](../language-support.md). |
|`language` | 2-znakowy kod [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) dla [obsługiwanego języka](../language-support.md) | Różnie | Wymagane do analizy tonacji, wyodrębniania kluczowych fraz i konsolidacji jednostek; opcjonalne do wykrywania języka. Jeśli wyłączysz go, nie ma żadnego błędu, ale analiza zostanie nieprzerwana. Kod języka powinien odpowiadać podanemu podaniu `text` . |

Poniżej znajduje się przykład żądania interfejsu API dla synchronicznych punktów końcowych analiza tekstu. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="analyze"></a>[Analiza](#tab/analyze)

> [!NOTE]
> Najnowsza wersja wstępna biblioteki klienta analiza tekstu umożliwia wywoływanie asynchronicznych operacji analizowania przy użyciu obiektu klienta. Przykłady można znaleźć w witrynie GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

`/analyze`Punkt końcowy umożliwia wybranie spośród obsługiwanych analiza tekstu funkcji, które mają być używane w jednym wywołaniu interfejsu API. Ten punkt końcowy obsługuje obecnie:

* Wyodrębnianie kluczowych fraz 
* Rozpoznawanie jednostek nazwanych (w tym dane OSOBowe i PHI)

| Element | Prawidłowe wartości | Wymagane? | Użycie |
|---------|--------------|-----------|-------|
|`displayName` | Ciąg | Opcjonalne | Używany jako nazwa wyświetlana dla unikatowego identyfikatora zadania.|
|`analysisInput` | Zawiera `documents` pole poniżej | Wymagane | Zawiera informacje o dokumentach, które chcesz wysłać. |
|`documents` | Zawiera `id` pola i `text` poniżej | Wymagane | Zawiera informacje dotyczące każdego wysyłanego dokumentu oraz nieprzetworzony tekst dokumentu. |
|`id` | Ciąg | Wymagane | Wprowadzone identyfikatory są używane do struktury danych wyjściowych. |
|`text` | Nieprzetworzony tekst nieprzebudowany, do 125 000 znaków. | Wymagane | Musi być w języku angielskim, który jest obecnie obsługiwanym językiem. |
|`tasks` | Program zawiera następujące funkcje analiza tekstu: `entityRecognitionTasks` , `keyPhraseExtractionTasks` lub `entityRecognitionPiiTasks` . | Wymagane | Co najmniej jedna z funkcji analiza tekstu, które mają być używane. Należy pamiętać, że `entityRecognitionPiiTasks` ma opcjonalny `domain` parametr, który można ustawić na `pii` lub `phi` . Jeśli nie zostanie określony, system ma wartość domyślną `pii` . |
|`parameters` | Zawiera `model-version` pola i `stringIndexType` poniżej | Wymagane | To pole jest zawarte w wybranych zadaniach funkcji. Zawierają one informacje o wersji modelu, która ma być używana, oraz typu indeksu. |
|`model-version` | Ciąg | Wymagane | Określ, która wersja modelu jest wywoływana, która ma być używana.  |
|`stringIndexType` | Ciąg | Wymagane | Określ dekoder tekstu, który jest zgodny ze środowiskiem programowania.  Obsługiwane typy to `textElement_v8` (domyślnie), `unicodeCodePoint` , `utf16CodeUnit` . Więcej informacji można znaleźć w [artykule przesunięcia tekstu](../concepts/text-offsets.md#offsets-in-api-version-31-preview) .  |
|`domain` | Ciąg | Opcjonalne | Stosuje się tylko jako parametr do `entityRecognitionPiiTasks` zadania i może być ustawiony na `pii` lub `phi` . Wartość domyślna to `pii` Jeśli nie została określona.  |

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
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}

```

#### <a name="text-analytics-for-health"></a>[Analiza tekstu dla opieki zdrowotnej](#tab/health)

Format żądań interfejsu API do analiza tekstu dla obsługiwanego interfejsu API usługi kondycji jest taki sam jak w przypadku jego kontenera. Dokumenty są przesyłane w obiekcie JSON jako nieprzetworzony tekst bez struktury. KOD XML nie jest obsługiwany. Schemat JSON składa się z elementów opisanych poniżej.  Wypełnij i prześlij [formularz żądania Cognitive Services](https://aka.ms/csgate) , aby zażądać dostępu do analiza tekstu dla publicznej wersji zapoznawczej kondycji. Za użycie kondycji nie będą naliczane opłaty za analiza tekstu. 

| Element | Prawidłowe wartości | Wymagane? | Użycie |
|---------|--------------|-----------|-------|
|`id` |Typ danych to ciąg, ale w temacie identyfikatory dokumentów w programie mają być liczbami całkowitymi. | Wymagane | System używa identyfikatorów dostarczanych do struktury danych wyjściowych. |
|`text` | Nieprzetworzony tekst nieprzebudowany, do 5 120 znaków. | Wymagane | Należy pamiętać, że obecnie jest obsługiwany tylko tekst w języku angielskim. |
|`language` | 2-znakowy kod [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) dla [obsługiwanego języka](../language-support.md) | Wymagane | Tylko `en` obecnie obsługiwane. |

Poniżej znajduje się przykład żądania interfejsu API dla analiza tekstu dla punktów końcowych kondycji. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Zapoznaj się z artykułem [ograniczenia dotyczące danych i szybkości](../concepts/data-limits.md) , aby uzyskać informacje na temat stawek i limitów rozmiaru na potrzeby wysyłania danych do interfejs API analizy tekstu.


## <a name="set-up-a-request"></a>Skonfiguruj żądanie 

W programie Poster (lub innym narzędziu testowym interfejsu API sieci Web) Dodaj punkt końcowy dla funkcji, której chcesz użyć. Skorzystaj z poniższej tabeli, aby znaleźć odpowiedni format punktu końcowego, i Zastąp `<your-text-analytics-resource>` go punktem końcowym zasobu. Na przykład:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Synchronous](#tab/synchronous)

| Cechy | Typ żądania | Punkty końcowe zasobów |
|--|--|--|
| Wykrywanie języka | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Analiza tonacji | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Wyszukiwanie opinii | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Wyodrębnianie kluczowych fraz | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Rozpoznawanie jednostek nazwanych — ogólne | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Rozpoznawanie jednostek nazwanych — dane OSOBowe | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Rozpoznawanie jednostek nazwanych — Fi | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="analyze"></a>[Analiza](#tab/analyze)

| Cechy | Typ żądania | Punkty końcowe zasobów |
|--|--|--|
| Prześlij zadanie analizy | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| Pobieranie stanu i wyników analizy | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

#### <a name="text-analytics-for-health"></a>[Analiza tekstu dla opieki zdrowotnej](#tab/health)

| Cechy | Typ żądania | Punkty końcowe zasobów |
|--|--|--|
| Prześlij analiza tekstu do zadania kondycji  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs` |
| Pobieranie stanu i wyników zadania | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |
| Anuluj zadanie | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |

--- 

Po utworzeniu punktu końcowego w programie Poster (lub innym narzędziu do testowania internetowego interfejsu API):

1. Wybierz typ żądania dla funkcji, której chcesz użyć.
2. Wklej w punkcie końcowym odpowiedniej operacji z powyższej tabeli.
3. Ustaw trzy nagłówki żądania:

   + `Ocp-Apim-Subscription-Key`: Twój klucz dostępu uzyskany z Azure Portal
   + `Content-Type`: Application/JSON
   + `Accept`: Application/JSON

    Jeśli używasz programu Poster, żądanie powinno wyglądać podobnie do poniższego zrzutu ekranu, przy założeniu `/keyPhrases` punktu końcowego.
    
    ![Zażądaj zrzutu ekranu z punktem końcowym i nagłówkami](../media/postman-request-keyphrase-1.png)
    
4. Wybierz **nieprzetworzony** format **treści**
    
    ![Żądaj zrzutu ekranu z ustawieniami treści](../media/postman-request-body-raw.png)

5. Wklej w prawidłowym formacie w niektórych dokumentach JSON. Skorzystaj z przykładów w sekcji **format żądania interfejsu API** powyżej, a aby uzyskać więcej informacji i przykładów, zobacz poniższe tematy:

      + [Wykrywanie języka](text-analytics-how-to-language-detection.md)
      + [Wyodrębnianie kluczowych fraz](text-analytics-how-to-keyword-extraction.md)
      + [Analiza tonacji](text-analytics-how-to-sentiment-analysis.md)
      + [Rozpoznawanie jednostek](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>Wysyłanie żądania

Prześlij żądanie interfejsu API. Jeśli wykonano wywołanie synchronicznego punktu końcowego, odpowiedź zostanie wyświetlona natychmiast, jako pojedynczy dokument JSON, z elementem dla każdego identyfikatora dokumentu dostarczonego w żądaniu.

Jeśli wywołanie asynchroniczne `/analyze` lub końcowe zostało wykonane `/health` , sprawdź, czy został otrzymany kod odpowiedzi 202. musisz uzyskać odpowiedź, aby wyświetlić wyniki:

1. W odpowiedzi interfejsu API Znajdź `Operation-Location` z nagłówka, który identyfikuje zadanie wysłane do interfejsu API. 
2. Utwórz żądanie GET dla używanego punktu końcowego. Zapoznaj się z [powyższą tabelą](#set-up-a-request) w polu Format punktu końcowego i zapoznaj się z [dokumentacją interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus). Na przykład:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. Dodaj `Operation-Location` do żądania.

4. Odpowiedź będzie jednym dokumentem JSON z elementem dla każdego identyfikatora dokumentu dostarczonego w żądaniu.

## <a name="example-api-responses"></a>Przykładowe odpowiedzi interfejsu API
 
# <a name="synchronous"></a>[Synchronous](#tab/synchronous)

Synchroniczne odpowiedzi punktów końcowych różnią się w zależności od używanego punktu końcowego. Zobacz następujące artykuły, aby uzyskać odpowiedzi na przykład.

+ [Wykrywanie języka](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Wyodrębnianie kluczowych fraz](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Analiza tonacji](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Rozpoznawanie jednostek](text-analytics-how-to-entity-linking.md#view-results)

# <a name="analyze"></a>[Analiza](#tab/analyze)

Jeśli to się powiedzie, żądanie GET do `/analyze` punktu końcowego zwróci obiekt zawierający przypisane zadania. Na przykład: `keyPhraseExtractionTasks`. Te zadania zawierają obiekt Response z odpowiedniej funkcji analiza tekstu. Zobacz następujące artykuły, aby uzyskać więcej informacji.

+ [Wyodrębnianie kluczowych fraz](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Rozpoznawanie jednostek](text-analytics-how-to-entity-linking.md#view-results)


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

# <a name="text-analytics-for-health"></a>[Analiza tekstu dla opieki zdrowotnej](#tab/health)

Aby uzyskać więcej informacji na temat analiza tekstu asynchronicznej odpowiedzi interfejsu API kondycji, zobacz następujący artykuł:

+ [Analiza tekstu dla opieki zdrowotnej](text-analytics-for-health.md#hosted-asynchronous-web-api-response)


--- 

## <a name="see-also"></a>Zobacz też

* [Przegląd analizy tekstu](../overview.md)
* [Często zadawane pytania](../text-analytics-resource-faq.md)</br>
* [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712)
* [Korzystanie z biblioteki klienta analiza tekstu](../quickstarts/text-analytics-sdk.md)
* [Co nowego](../whats-new.md)
