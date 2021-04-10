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
ms.date: 03/01/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 3c6fb1ca23bcc9c57e73bcaf960e0387611fcff3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599218"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Jak wywołać interfejs API REST analiza tekstu

W tym artykule używamy interfejsu API REST analiza tekstu i programu [Poster](https://www.postman.com/downloads/) w celu zademonstrowania kluczowych pojęć. Interfejs API udostępnia kilka synchronicznych i asynchronicznych punktów końcowych do korzystania z funkcji usługi. 

## <a name="create-a-text-analytics-resource"></a>Tworzenie zasobu analiza tekstu

> [!NOTE]
> * Jeśli chcesz użyć punktów końcowych lub, będziesz potrzebować zasobu analiza tekstu przy użyciu [warstwy cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) standardowa `/analyze` `/health` . `/analyze`Punkt końcowy jest uwzględniony w [warstwie cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

Przed użyciem interfejs API analizy tekstu należy utworzyć zasób platformy Azure z kluczem i punktem końcowym aplikacji. 

1.  Najpierw przejdź do [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) i Utwórz nowy zasób analiza tekstu, jeśli jeszcze go nie masz. Wybierz [warstwę cenową](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

2.  Wybierz region, który ma być używany dla punktu końcowego.  Należy zwrócić uwagę, że `/analyze` `/health` punkty końcowe i są dostępne tylko w następujących regionach: zachodnie stany USA 2, Wschodnie stany USA 2, środkowe stany USA, Europa Północna i Europa Zachodnia.

3.  Utwórz zasób analiza tekstu i przejdź do bloku "klucze i punkt końcowy" w lewej części strony. Skopiuj klucz, który będzie używany później podczas wywoływania interfejsów API. Ten element zostanie później dodany jako wartość `Ocp-Apim-Subscription-Key` nagłówka.

## <a name="change-your-pricing-tier"></a>Zmień warstwę cenową 

Jeśli masz istniejący zasób analiza tekstu za pomocą warstwy cenowej S0 za pośrednictwem S4, należy ją zaktualizować do korzystania z [warstwy cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)standardowa. Warstwy cenowe S0 za pomocą S4 zostaną wycofane. Aby zaktualizować Cennik zasobu:

1. Przejdź do zasobu analiza tekstu w [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **warstwa cenowa** w menu nawigacji po lewej stronie. Będzie on niższy niż **Zarządzanie zasobami**. 
3. Wybierz warstwę cenową Standard (S). Następnie kliknij pozycję **Wybierz**.

Nowy zasób analiza tekstu można również utworzyć za pomocą warstwy cenowej Standard (S) i przeprowadzić migrację aplikacji w celu użycia poświadczeń dla nowego zasobu. 

## <a name="using-the-api-synchronously"></a>Korzystanie z interfejsu API synchronicznie

Możesz wywoływać analiza tekstu synchronicznie (dla scenariuszy o małych opóźnieniach). Każdy interfejs API (funkcję) należy wywoływać osobno podczas korzystania z synchronicznego interfejsu API. Jeśli musisz wywołać wiele funkcji, zapoznaj się z sekcją poniżej, jak wywoływać analiza tekstu asynchronicznie. 

## <a name="using-the-api-asynchronously"></a>Korzystanie z interfejsu API asynchronicznie

Począwszy od wersji v 3.1 — wersja zapoznawcza. 3, interfejs API analizy tekstu zawiera dwa asynchroniczne punkty końcowe: 

* `/analyze`Punkt końcowy dla analiza tekstu umożliwia analizowanie tego samego zestawu dokumentów tekstowych z wieloma funkcjami analizy tekstu w jednym wywołaniu interfejsu API. Wcześniej, aby korzystać z wielu funkcji, należy wykonać oddzielne wywołania interfejsu API dla każdej operacji. Tę funkcję należy wziąć pod uwagę, gdy trzeba analizować duże zestawy dokumentów za pomocą więcej niż jednej funkcji analiza tekstu.

* `/health`Punkt końcowy dla analiza tekstu kondycji, który może wyodrębnić i oznaczyć odpowiednie informacje medyczne z dokumentów klinicznych.  

Zapoznaj się z poniższą tabelą, aby zobaczyć, które funkcje mogą być używane asynchronicznie. Należy zauważyć, że tylko kilka funkcji można wywołać z `/analyze` punktu końcowego. 

| Cecha | Synchronous | Asynchroniczny |
|--|--|--|
| Wykrywanie języka | ✔ |  |
| Analiza tonacji | ✔ |  |
| Wyszukiwanie opinii | ✔ |  |
| Wyodrębnianie kluczowych fraz | ✔ | ✔* |
| Rozpoznawanie jednostek nazwanych (w tym dane OSOBowe i PHI) | ✔ | ✔* |
| Łączenie jednostek | ✔ | ✔* |
| Analiza tekstu dla kondycji (kontener) | ✔ |  |
| Analiza tekstu dla kondycji (API) |  | ✔  |

`*` -Wywoływana asynchronicznie za pomocą `/analyze` punktu końcowego.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

<a name="json-schema"></a>

## <a name="api-request-formats"></a>Formaty żądań interfejsu API

Można wysyłać synchroniczne i asynchroniczne wywołania do interfejs API analizy tekstu.

#### <a name="synchronous"></a>[Synchronous](#tab/synchronous)

### <a name="synchronous-requests"></a>Żądania synchroniczne

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

#### <a name="asynchronous"></a>[Asynchroniczny](#tab/asynchronous)

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>Żądania asynchroniczne do `/analyze` punktu końcowego

> [!NOTE]
> Najnowsza wersja wstępna biblioteki klienta analiza tekstu umożliwia wywoływanie asynchronicznych operacji analizowania przy użyciu obiektu klienta. Przykłady można znaleźć w witrynie GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

`/analyze`Punkt końcowy umożliwia wybranie spośród obsługiwanych analiza tekstu funkcji, które mają być używane w jednym wywołaniu interfejsu API. Ten punkt końcowy obsługuje obecnie:

* Wyodrębnianie kluczowych fraz 
* Rozpoznawanie jednostek nazwanych (w tym dane OSOBowe i PHI)
* Łączenie jednostek

| Element | Prawidłowe wartości | Wymagane? | Użycie |
|---------|--------------|-----------|-------|
|`displayName` | Ciąg | Opcjonalne | Używany jako nazwa wyświetlana dla unikatowego identyfikatora zadania.|
|`analysisInput` | Zawiera `documents` pole poniżej | Wymagane | Zawiera informacje o dokumentach, które chcesz wysłać. |
|`documents` | Zawiera `id` pola i `text` poniżej | Wymagane | Zawiera informacje dotyczące każdego wysyłanego dokumentu oraz nieprzetworzony tekst dokumentu. |
|`id` | Ciąg | Wymagane | Wprowadzone identyfikatory są używane do struktury danych wyjściowych. |
|`text` | Nieprzetworzony tekst nieprzebudowany, do 125 000 znaków. | Wymagane | Musi być w języku angielskim, który jest obecnie obsługiwanym językiem. |
|`tasks` | Program zawiera następujące funkcje analiza tekstu: `entityRecognitionTasks` , `entityLinkingTasks` , `keyPhraseExtractionTasks` lub `entityRecognitionPiiTasks` . | Wymagane | Co najmniej jedna z funkcji analiza tekstu, które mają być używane. Należy pamiętać, że `entityRecognitionPiiTasks` ma opcjonalny `domain` parametr, który może być ustawiony na `pii` lub `phi` i na `pii-categories` potrzeby wykrywania wybranych typów jednostek. Jeśli `domain` parametr jest nieokreślony, system ma wartość domyślną `pii` . |
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
        "entityLinkingTasks": [
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
                "model-version": "latest",
                "stringIndexType": "TextElements_v8",
                "domain": "phi",
                "pii-categories":"default"
            }
        }]
    }
}

```

### <a name="asynchronous-requests-to-the-health-endpoint"></a>Żądania asynchroniczne do `/health` punktu końcowego

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

### <a name="endpoints-for-sending-synchronous-requests"></a>Punkty końcowe do wysyłania żądań synchronicznych

| Cecha | Typ żądania | Punkty końcowe zasobów |
|--|--|--|
| Wykrywanie języka | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Analiza tonacji | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Wyszukiwanie opinii | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Wyodrębnianie kluczowych fraz | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Rozpoznawanie jednostek nazwanych — ogólne | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Rozpoznawanie jednostek nazwanych — dane OSOBowe | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Rozpoznawanie jednostek nazwanych — Fi | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="asynchronous"></a>[Asynchroniczny](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>Punkty końcowe do wysyłania żądań asynchronicznych do `/analyze` punktu końcowego

| Cecha | Typ żądania | Punkty końcowe zasobów |
|--|--|--|
| Prześlij zadanie analizy | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze` |
| Pobieranie stanu i wyników analizy | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>Punkty końcowe do wysyłania żądań asynchronicznych do `/health` punktu końcowego

| Cecha | Typ żądania | Punkty końcowe zasobów |
|--|--|--|
| Prześlij analiza tekstu do zadania kondycji  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs` |
| Pobieranie stanu i wyników zadania | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |
| Anuluj zadanie | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |

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

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>`

3. Dodaj `Operation-Location` do żądania.

4. Odpowiedź będzie jednym dokumentem JSON z elementem dla każdego identyfikatora dokumentu dostarczonego w żądaniu.

Należy pamiętać, że w przypadku asynchronicznych `/analyze` lub `/health` operacji wyniki żądania GET w kroku 2 powyżej są dostępne przez 24 godziny od momentu utworzenia zadania.  Ten czas jest wskazywany przez `expirationDateTime` wartość w odpowiedzi get.  Po upływie tego czasu wyniki zostaną usunięte i nie będą już dostępne do pobrania.    

## <a name="example-api-responses"></a>Przykładowe odpowiedzi interfejsu API
 
# <a name="synchronous"></a>[Synchronous](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>Przykładowe odpowiedzi dla operacji synchronicznych

Synchroniczne odpowiedzi punktów końcowych różnią się w zależności od używanego punktu końcowego. Zobacz następujące artykuły, aby uzyskać odpowiedzi na przykład.

+ [Wykrywanie języka](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Wyodrębnianie kluczowych fraz](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Analiza tonacji](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Rozpoznawanie jednostek](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[Asynchroniczny](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>Przykładowe odpowiedzi dla operacji asynchronicznych

Jeśli to się powiedzie, żądanie GET do `/analyze` punktu końcowego zwróci obiekt zawierający przypisane zadania. Na przykład: `keyPhraseExtractionTasks`. Te zadania zawierają obiekt Response z odpowiedniej funkcji analiza tekstu. Zobacz następujące artykuły, aby uzyskać więcej informacji.

+ [Wyodrębnianie kluczowych fraz](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Rozpoznawanie jednostek](text-analytics-how-to-entity-linking.md#view-results)
+ [Analiza tekstu dla opieki zdrowotnej](text-analytics-for-health.md#hosted-asynchronous-web-api-response)

--- 

## <a name="see-also"></a>Zobacz też

* [Przegląd analizy tekstu](../overview.md)
* [Często zadawane pytania](../text-analytics-resource-faq.md)</br>
* [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712)
* [Korzystanie z biblioteki klienta analiza tekstu](../quickstarts/client-libraries-rest-api.md)
* [Co nowego](../whats-new.md)
