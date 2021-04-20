---
title: Wywoływanie interfejsu API usługi Text Analytics
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak wywołać interfejs API REST Azure Cognitive Services analiza tekstu postman.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 5790c7c62b9d97df9683773170301b6e09a47667
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728486"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Jak wywołać interfejs API REST analiza tekstu interfejsu API

W tym artykule używamy interfejsu API REST analiza tekstu [Postman,](https://www.postman.com/downloads/) aby zademonstrować kluczowe pojęcia. Interfejs API udostępnia kilka synchronicznych i asynchronicznych punktów końcowych do korzystania z funkcji usługi. 

## <a name="create-a-text-analytics-resource"></a>Tworzenie zasobu analiza tekstu zasobów

> [!NOTE]
> * Jeśli chcesz używać punktów końcowych lub , analiza tekstu [](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) zasobów sieciowych przy użyciu warstwy cenowej Standardowa `/analyze` `/health` (S). Punkt `/analyze` końcowy jest uwzględniony w [warstwie cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

Przed użyciem interfejsu API analiza tekstu należy utworzyć zasób platformy Azure z kluczem i punktem końcowym dla aplikacji. 

1.  Najpierw przejdź do strony [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) i utwórz nowy zasób analiza tekstu, jeśli jeszcze go nie masz. Wybierz warstwę [cenową](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

2.  Wybierz region, którego chcesz użyć dla punktu końcowego.  Należy pamiętać, że punkty końcowe i są dostępne tylko w następujących regionach: Zachodnie stany USA 2, Wschodnie stany `/analyze` `/health` USA 2, Środkowe stany USA, Europa Północna i Europa Zachodnia.

3.  Utwórz zasób analiza tekstu i przejdź do bloku "klucze i punkt końcowy" po lewej stronie. Skopiuj klucz, który będzie używany później podczas wywołania interfejsów API. Później dodasz go jako wartość `Ocp-Apim-Subscription-Key` nagłówka.

4. Aby sprawdzić liczbę rekordów tekstowych, które zostały wysłane przy użyciu zasobu analiza tekstu zasobów:

    1. Przejdź do zasobu analiza tekstu w Azure Portal. 
    2. Kliknij **pozycję Metryki** znajdującą się w **obszarze Monitorowanie** w menu nawigacji po lewej stronie. 
    3. Wybierz *pozycję Przetworzone rekordy tekstowe* w polu listy rozwijanej dla opcji **Metryka**.
    
Rekord tekstowy ma 1000 znaków.

## <a name="change-your-pricing-tier"></a>Zmienianie warstwy cenowej 

Jeśli masz istniejący zasób analiza tekstu korzystający z warstwy cenowej S0–S4, zaktualizuj go, aby używać warstwy cenowej Standardowa [(S).](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) Warstwy cenowe S0–S4 zostaną wycofane. Aby zaktualizować ceny zasobu:

1. Przejdź do zasobu analiza tekstu w Azure Portal [.](https://portal.azure.com/)
2. Wybierz **pozycję Warstwa cenowa** w menu nawigacji po lewej stronie. Będzie on miał grupę poniżej **wartości ZARZĄDZANIE ZASOBAMI.** 
3. Wybierz warstwę cenową Standardowa (S). Następnie kliknij pozycję **Wybierz**.

Możesz również utworzyć nowy zasób usługi analiza tekstu w warstwie cenowej Standardowa (S) i przeprowadzić migrację aplikacji w celu użycia poświadczeń dla nowego zasobu. 

## <a name="using-the-api-synchronously"></a>Korzystanie z interfejsu API synchronicznie

Możesz wywołać analiza tekstu synchronicznie (w przypadku scenariuszy o małych opóźnieniach). W przypadku korzystania z synchronicznego interfejsu API należy wywołać każdy interfejs API (funkcję) oddzielnie. Jeśli musisz wywołać wiele funkcji, zapoznaj się z sekcją poniżej, aby dowiedzieć się, jak wywołać analiza tekstu asynchronicznie. 

## <a name="using-the-api-asynchronously"></a>Asynchroniczne używanie interfejsu API

Począwszy od wersji 3.1(wersja zapoznawcza 3.3), interfejs API analiza tekstu udostępnia dwa asynchroniczne punkty końcowe: 

* Punkt końcowy dla analiza tekstu umożliwia analizowanie tego samego zestawu dokumentów tekstowych z wieloma funkcjami analizy tekstu `/analyze` w jednym wywołaniu interfejsu API. Wcześniej, aby korzystać z wielu funkcji, należy wykonać oddzielne wywołania interfejsu API dla każdej operacji. Rozważ tę możliwość, gdy musisz analizować duże zestawy dokumentów z więcej niż jedną analiza tekstu funkcją.

* Punkt końcowy dla analiza tekstu dla zdrowia, który może wyodrębniać i oznaczać odpowiednie informacje `/health` medyczne z dokumentów klinicznych.  

Należy pamiętać, że punkty końcowe /analyze i /health są dostępne tylko w następujących regionach: Zachodnie stany USA 2, Wschodnie stany USA 2, Środkowe stany USA, Europa Północna i Europa Zachodnia.

Zobacz tabelę poniżej, aby zobaczyć, które funkcje mogą być używane asynchronicznie. Należy pamiętać, że z punktu końcowego można wywołać tylko kilka `/analyze` funkcji. 

| Cecha | Synchronous | Asynchroniczny |
|--|--|--|
| Wykrywanie języka | ✔ |  |
| Analiza tonacji | ✔ |  |
| Wyszukiwanie opinii | ✔ |  |
| Wyodrębnianie kluczowych fraz | ✔ | ✔* |
| rozpoznawanie jednostek nazwanych (w tym PII i PHI) | ✔ | ✔* |
| Łączenie jednostek | ✔ | ✔* |
| analiza tekstu dla kondycji (kontener) | ✔ |  |
| analiza tekstu kondycji (API) |  | ✔  |

`*` — Wywoływane asynchronicznie za pośrednictwem punktu `/analyze` końcowego.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

<a name="json-schema"></a>

## <a name="api-request-formats"></a>Formaty żądań interfejsu API

Do interfejsu API można wysyłać zarówno wywołania synchroniczne, jak analiza tekstu i asynchroniczne.

#### <a name="synchronous"></a>[Synchronous](#tab/synchronous)

### <a name="synchronous-requests"></a>Żądania synchroniczne

Format żądań interfejsu API jest taki sam dla wszystkich operacji synchronicznych. Dokumenty są przesyłane w obiekcie JSON jako nieprzetworzony tekst bez struktury. Kod XML nie jest obsługiwany. Schemat JSON składa się z elementów opisanych poniżej.

| Element | Prawidłowe wartości | Wymagane? | Użycie |
|---------|--------------|-----------|-------|
|`id` |Typ danych to ciąg, ale w praktyce identyfikatory dokumentów są zwykle liczbami całkowitymi. | Wymagane | System używa identyfikatorów, które podaniem, do struktury danych wyjściowych. Kody języków, frazy kluczowe i wyniki tonacji są generowane dla każdego identyfikatora w żądaniu.|
|`text` | Nieprzetworzony tekst bez struktury, maksymalnie 5120 znaków. | Wymagane | W przypadku wykrywania języka tekst może być wyrażony w dowolnym języku. W przypadku analizy tonacji, wyodrębniania kluczowych fraz i identyfikacji jednostek tekst musi być w [obsługiwanym języku](../language-support.md). |
|`language` | 2-znakowy [kod ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) dla [obsługiwanego języka](../language-support.md) | Różnie | Wymagane do analizy tonacji, wyodrębniania kluczowych fraz i łączenia jednostek; jest opcjonalny w przypadku wykrywania języka. Jeśli wykluczysz ten błąd, nie wystąpi błąd, ale analiza zostanie bez niego wykluczona. Kod języka powinien odpowiadać `text` podaniem. |

Poniżej przedstawiono przykład żądania interfejsu API dla synchronicznych punktów analiza tekstu końcowych. 

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

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>Żądania asynchroniczne do punktu `/analyze` końcowego

> [!NOTE]
> Najnowsza wersja wstępna biblioteki analiza tekstu umożliwia wywołanie asynchronicznych operacji analizy przy użyciu obiektu klienta. Przykłady można znaleźć w witrynie GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

Punkt `/analyze` końcowy pozwala wybrać, które z obsługiwanych analiza tekstu, których chcesz użyć w pojedynczym wywołaniu interfejsu API. Ten punkt końcowy obsługuje obecnie:

* Wyodrębnianie kluczowych fraz 
* rozpoznawanie jednostek nazwanych (w tym piI i PHI)
* Łączenie jednostek

| Element | Prawidłowe wartości | Wymagane? | Użycie |
|---------|--------------|-----------|-------|
|`displayName` | Ciąg | Opcjonalne | Używana jako nazwa wyświetlana unikatowego identyfikatora zadania.|
|`analysisInput` | Zawiera `documents` poniższe pole | Wymagane | Zawiera informacje dotyczące dokumentów, które chcesz wysłać. |
|`documents` | Zawiera poniższe `id` `text` pola i | Wymagane | Zawiera informacje o każdym wysyłanym dokumencie i nieprzetworzonego tekście dokumentu. |
|`id` | Ciąg | Wymagane | Identyfikatory, które należy podać, są używane do struktury danych wyjściowych. |
|`text` | Nieprzetworzony tekst bez struktury, maksymalnie 125 000 znaków. | Wymagane | Musi być w języku angielskim, który jest jedynym obecnie obsługiwanym językiem. |
|`tasks` | Zawiera następujące funkcje analiza tekstu funkcji: `entityRecognitionTasks` `entityLinkingTasks` , lub `keyPhraseExtractionTasks` `entityRecognitionPiiTasks` . | Wymagane | Co najmniej jedna z analiza tekstu, których chcesz użyć. Należy `entityRecognitionPiiTasks` pamiętać, że ma opcjonalny parametr, który można ustawić na lub i dla `domain` `pii` `phi` `pii-categories` wykrywania wybranych typów jednostek. Jeśli parametr `domain` jest nieokreślony, system domyślnie ma wartość `pii` . |
|`parameters` | Zawiera poniższe `model-version` `stringIndexType` pola i | Wymagane | To pole znajduje się w ramach powyższych zadań funkcji, które wybierzesz. Zawierają one informacje o wersji modelu, której chcesz użyć, i typie indeksu. |
|`model-version` | Ciąg | Wymagane | Określ wersję wywoływanego modelu, którego chcesz użyć.  |
|`stringIndexType` | Ciąg | Wymagane | Określ dekoder tekstu, który odpowiada Twojemu środowisku programowania.  Obsługiwane typy to `textElement_v8` (domyślne), `unicodeCodePoint` , `utf16CodeUnit` . Aby uzyskać więcej [informacji,](../concepts/text-offsets.md#offsets-in-api-version-31-preview) zobacz artykuł Przesunięcie tekstu.  |
|`domain` | Ciąg | Opcjonalne | Ma zastosowanie tylko jako parametr do `entityRecognitionPiiTasks` zadania i można go ustawić na wartość lub `pii` `phi` . Wartość domyślna to `pii` , jeśli nie zostanie określone.  |

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

### <a name="asynchronous-requests-to-the-health-endpoint"></a>Żądania asynchroniczne do punktu `/health` końcowego

Format żądań interfejsu API do interfejsu API analiza tekstu dla interfejsu API hostowanej kondycji jest taki sam jak dla jego kontenera. Dokumenty są przesyłane w obiekcie JSON jako nieprzetworzony tekst bez struktury. Kod XML nie jest obsługiwany. Schemat JSON składa się z elementów opisanych poniżej.  Wypełnij i prześlij formularz żądania [Cognitive Services,](https://aka.ms/csgate) aby zażądać dostępu do aplikacji analiza tekstu publicznej wersji zapoznawczej kondycji. Nie będą naliczane opłaty za analiza tekstu za użycie kondycji. 

| Element | Prawidłowe wartości | Wymagane? | Użycie |
|---------|--------------|-----------|-------|
|`id` |Typ danych to ciąg, ale w praktyce identyfikatory dokumentów są zwykle liczbami całkowitymi. | Wymagane | System używa identyfikatorów, które należy podać, do struktury danych wyjściowych. |
|`text` | Nieprzetworzony tekst bez struktury, maksymalnie 5120 znaków. | Wymagane | Należy pamiętać, że obecnie jest obsługiwany tylko tekst w języku angielskim. |
|`language` | 2-znakowy [kod ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) dla [obsługiwanego języka](../language-support.md) | Wymagane | Obecnie `en` obsługiwane są tylko te urządzenia. |

Poniżej przedstawiono przykład żądania interfejsu API dla punktu końcowego analiza tekstu kondycji. 

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
> Zobacz artykuł [Data and rate limits (Limity danych](../concepts/data-limits.md) i szybkości), aby uzyskać informacje na temat stawek i limitów rozmiaru dla wysyłania danych do interfejsu API analiza tekstu API.


## <a name="set-up-a-request"></a>Konfigurowanie żądania 

W narzędziu Postman (lub innym narzędziu do testowania internetowego interfejsu API) dodaj punkt końcowy dla funkcji, której chcesz użyć. Skorzystaj z poniższej tabeli, aby znaleźć odpowiedni format punktu końcowego i zastąpić `<your-text-analytics-resource>` parametr punktem końcowym zasobu. Na przykład:

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
| Rozpoznawanie jednostek nazwanych — piI | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Rozpoznawanie jednostek nazwanych — PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="asynchronous"></a>[Asynchroniczny](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>Punkty końcowe do wysyłania żądań asynchronicznych do punktu `/analyze` końcowego

| Cecha | Typ żądania | Punkty końcowe zasobów |
|--|--|--|
| Przesyłanie zadania analizy | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze` |
| Uzyskiwanie stanu analizy i wyników | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>Punkty końcowe do wysyłania żądań asynchronicznych do punktu `/health` końcowego

| Cecha | Typ żądania | Punkty końcowe zasobów |
|--|--|--|
| Przesyłanie analiza tekstu zadania kondycji  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs` |
| Uzyskiwanie stanu zadania i wyników | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |
| Anulowanie zadania | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |

--- 

Po użyciu punktu końcowego w narzędziu Postman (lub innym narzędziu do testowania internetowego interfejsu API):

1. Wybierz typ żądania dla funkcji, której chcesz użyć.
2. Wklej punkt końcowy odpowiedniej operacji z powyższej tabeli.
3. Ustaw trzy nagłówki żądań:

   + `Ocp-Apim-Subscription-Key`: klucz dostępu uzyskany z Azure Portal
   + `Content-Type`: application/json
   + `Accept`: application/json

    Jeśli używasz narzędzia Postman, żądanie powinno wyglądać podobnie do poniższego zrzutu ekranu, przy założeniu punktu `/keyPhrases` końcowego.
    
    ![Zrzut ekranu żądania z punktem końcowym i nagłówkami](../media/postman-request-keyphrase-1.png)
    
4. Wybierz **nieprzetworzone** dla formatu **treści**
    
    ![Zrzut ekranu żądania z ustawieniami treści](../media/postman-request-body-raw.png)

5. Wklej niektóre dokumenty JSON w prawidłowym formacie. Skorzystaj z przykładów w **powyższej sekcji** dotyczącej formatu żądania interfejsu API, a aby uzyskać więcej informacji i przykładów, zobacz poniższe tematy:

      + [Wykrywanie języka](text-analytics-how-to-language-detection.md)
      + [Wyodrębnianie kluczowych fraz](text-analytics-how-to-keyword-extraction.md)
      + [Analiza tonacji](text-analytics-how-to-sentiment-analysis.md)
      + [Rozpoznawanie jednostek](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>Wysyłanie żądania

Prześlij żądanie interfejsu API. Jeśli wywołasz synchroniczny punkt końcowy, odpowiedź zostanie natychmiast wyświetlona jako pojedynczy dokument JSON z elementem dla każdego identyfikatora dokumentu podanego w żądaniu.

Jeśli wywołanie do asynchronicznych lub punktów końcowych jest wykonywane, sprawdź, czy odebrano kod odpowiedzi `/analyze` `/health` 202. Aby wyświetlić wyniki, musisz uzyskać odpowiedź:

1. W odpowiedzi interfejsu API znajdź w nagłówku , który identyfikuje zadanie `Operation-Location` wysłane do interfejsu API. 
2. Utwórz żądanie GET dla używanego punktu końcowego. Zapoznaj się z [tabelą powyżej,](#set-up-a-request) aby uzyskać informacje o formacie punktu końcowego, i zapoznaj się z [dokumentacją interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus). Na przykład:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>`

3. Dodaj `Operation-Location` do żądania .

4. Odpowiedź będzie pojedynczym dokumentem JSON z elementem dla każdego identyfikatora dokumentu podanego w żądaniu.

Należy pamiętać, że w przypadku operacji asynchronicznych lub operacji wyniki żądania GET w kroku 2 powyżej są dostępne przez 24 godziny od `/analyze` `/health` czasu utworzenia zadania.  Ten czas jest wskazywany `expirationDateTime` przez wartość w odpowiedzi GET.  Po tym okresie wyniki są przeczyszczane i nie są już dostępne do pobrania.    

## <a name="example-api-responses"></a>Przykładowe odpowiedzi interfejsu API
 
# <a name="synchronous"></a>[Synchronous](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>Przykładowe odpowiedzi dla operacji synchronicznej

Odpowiedzi synchronicznego punktu końcowego będą się różnić w zależności od punktu końcowego, z których korzystasz. Zobacz następujące artykuły, aby uzyskać przykładowe odpowiedzi.

+ [Wykrywanie języka](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Wyodrębnianie kluczowych fraz](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Analiza tonacji](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Rozpoznawanie jednostek](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[Asynchroniczny](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>Przykładowe odpowiedzi dla operacji asynchronicznych

W przypadku powodzenia żądanie GET do punktu końcowego `/analyze` zwróci obiekt zawierający przypisane zadania. Na przykład: `keyPhraseExtractionTasks`. Te zadania zawierają obiekt odpowiedzi z odpowiedniej analiza tekstu funkcji. Aby uzyskać więcej informacji, zobacz następujące artykuły.

+ [Wyodrębnianie kluczowych fraz](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Rozpoznawanie jednostek](text-analytics-how-to-entity-linking.md#view-results)
+ [Analiza tekstu dla opieki zdrowotnej](text-analytics-for-health.md#hosted-asynchronous-web-api-response)

--- 

## <a name="see-also"></a>Zobacz też

* [Przegląd analizy tekstu](../overview.md)
* [Często zadawane pytania](../text-analytics-resource-faq.md)</br>
* [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712)
* [Korzystanie z biblioteki analiza tekstu klienta](../quickstarts/client-libraries-rest-api.md)
* [Co nowego](../whats-new.md)
