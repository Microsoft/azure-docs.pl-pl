---
title: Wykonywanie analizy tonacji przy użyciu interfejsu API REST analiza tekstu
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono sposób wykrywania tonacji w tekście przy użyciu interfejsu API REST usługi Azure Cognitive Services analiza tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 04/27/2020
ms.author: aahi
ms.openlocfilehash: 99a62daf6dced88efd9bda591a0ca44a8b259a75
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195642"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Instrukcje: wykrywanie tonacji przy użyciu interfejs API analizy tekstu

Funkcja analiza tonacji interfejs API analizy tekstu ocenia tekst i zwraca wyniki tonacji oraz etykiety dla każdego zdania. Jest to przydatne do wykrywania pozytywnych i negatywnych tonacji w mediach społecznościowych, przeglądów klientów, forów dyskusyjnych i nie tylko. Modele AI używane przez interfejs API są udostępniane przez usługę. Wystarczy przesłać zawartość do analizy.

> [!TIP]
> Analiza tekstu udostępnia również obraz kontenera platformy Docker oparty na systemie Linux na potrzeby wykrywania języka, można więc [zainstalować i uruchomić kontener analizy tekstu](text-analytics-how-to-install-containers.md) blisko danych.

Analiza tonacji obsługuje szeroką gamę języków z więcej w wersji zapoznawczej. Więcej informacji, zobacz [Obsługiwane języki](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Pojęcia

Interfejs API analizy tekstu używa algorytmu klasyfikacji uczenia maszynowego w celu wygenerowania wyniku tonacji z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 wskazują na pozytywną opinię, a wyniki zbliżone do wartości 0 wskazują na negatywną opinię. Analiza tonacji jest wykonywana w całym dokumencie, a nie w poszczególnych jednostkach tekstu. Oznacza to, że wyniki tonacji są zwracane na poziomie dokumentu lub zdania. 

Używany model jest wstępnie szkolony z obszerną korpus skojarzeń tekstu i tonacji. Wykorzystuje kombinację technik analizy, w tym przetwarzanie tekstu, analizę części mowy, umieszczenie słowa i skojarzenia słów. Aby uzyskać więcej informacji na temat algorytmu, zobacz [Introducing Text Analytics (Wprowadzenie do analizy tekstu)](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/). Obecnie nie jest możliwe udostępnianie własnych danych szkoleniowych. 

Istnieje tendencja do zwiększenia dokładności oceniania, gdy dokumenty zawierają mniej zdań, a nie duże bloki tekstu. W fazie oceny obiektywizmu model określa, czy dokumentu jako całość jest obiektywny, czy też zawiera opinię. Dokument, który jest przede wszystkim nie przechodzi do fazy wykrywania tonacji, co skutkuje wynikami 0,50, bez dalszej obróbki. W przypadku dokumentów, które kontynuują się w potoku, następna faza generuje wynik powyżej lub poniżej 0,50. Wynik zależy od stopnia wykrycia elementu tonacji w dokumencie.

## <a name="sentiment-analysis-versions-and-features"></a>analiza tonacji wersje i funkcje

Interfejs API analizy tekstu oferuje dwie wersje analiza tonacji-v2 i v3. Analiza tonacji v3 (publiczna wersja zapoznawcza) zapewnia znaczące ulepszenia dokładności i szczegółowości dotyczące kategoryzacji i oceny tekstu interfejsu API.

> [!NOTE]
> * Format żądania analiza tonacji v3 i [limity danych](../overview.md#data-limits) są takie same jak w poprzedniej wersji.
> * Analiza tonacji `Australia East`V3 jest dostępny w następujących regionach:, `Central Canada` `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South`,,,,,,,, `West Europe`, i. `West US 2`

| Funkcja                                   | analiza tonacji v2 | analiza tonacji v3 |
|-------------------------------------------|-----------------------|-----------------------|
| Metody dla żądań pojedynczych i wsadowych    | X                     | X                     |
| Tonacji wyniki dla całego dokumentu  | X                     | X                     |
| Tonacji wyniki dla pojedynczych zdań |                       | X                     |
| Etykietowanie tonacji                        |                       | X                     |
| Przechowywanie wersji modelu                   |                       | X                     |

#### <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

### <a name="sentiment-scoring"></a>Ocenianie tonacji

Analiza tonacji v3 klasyfikuje tekst za pomocą etykiet tonacji (opisanych poniżej). Zwracane wyniki reprezentują zaufanie modelu, że tekst jest dodatni, ujemny lub neutralny. Wyższe wartości oznacza wyższego poziomu zaufania. 

### <a name="sentiment-labeling"></a>Etykietowanie tonacji

Analiza tonacji v3 zwraca etykiety tonacji na poziomie zdania i dokumentu (`positive`, `negative`i `neutral`) wraz z wynikami pewności. Etykietę `mixed` tonacji można także zwrócić na poziomie dokumentu. 

Tonacji dokumentu jest określana poniżej:

| Tonacji zdania                                                                            | Etykieta zwracanego dokumentu |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Co najmniej jedno `positive` zdanie znajduje się w dokumencie. Pozostałe zdania są `neutral`. | `positive`              |
| Co najmniej jedno `negative` zdanie znajduje się w dokumencie. Pozostałe zdania są `neutral`. | `negative`              |
| Co najmniej jedno `negative` zdanie i co najmniej jedno `positive` zdanie znajduje się w dokumencie.    | `mixed`                 |
| Wszystkie zdania w dokumencie są `neutral`.                                                  | `neutral`               |

### <a name="model-versioning"></a>Przechowywanie wersji modelu

> [!NOTE]
> Wersja Modeling for tonacji Analysis jest dostępna od wersji `v3.0-preview.1`.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Przykładowy kod w języku C#

Możesz znaleźć przykładową aplikację w języku C#, która wywołuje tę wersję analiza tonacji w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs).


#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

### <a name="sentiment-scoring"></a>Ocenianie tonacji

Analizator tonacji klasyfikuje tekst jako "dodatnie" lub ujemne. Przypisuje wynik z zakresu od 0 do 1. Wartości zbliżone do 0,5 oznaczają opinię neutralną lub brak opinii. Wynik 0,5 oznacza opinię neutralną. Gdy nie można przeanalizować ciągu dla tonacji lub nie ma tonacji, wynik jest zawsze 0,5 dokładnie. Na przykład jeśli przekażesz ciąg w języku hiszpańskim z kodem języka angielskiego, wynik będzie wynosić 0,5.

---

## <a name="sending-a-rest-api-request"></a>Wysyłanie żądania interfejsu API REST 

### <a name="preparation"></a>Przygotowywanie

Analiza tonacji daje wynik wyższej jakości, gdy zostanie nadana mniejsza ilość tekstu do pracy. Jest to przeciwieństwo wyodrębniania kluczowych fraz, które działa lepiej na większych blokach tekstu. Aby uzyskać najlepsze wyniki dla obu operacji, rozważ odpowiednią zmianę struktury danych wejściowych.

Musisz mieć dokumenty JSON w tym formacie: ID, text i Language.

Rozmiar dokumentu musi zawierać 5 120 znaków na dokument. Możesz mieć do 1 000 elementów (identyfikatorów) na kolekcję. Kolekcja jest przesyłana w treści żądania.

## <a name="structure-the-request"></a>Określenie struktury żądania

Utwórz żądanie POST. Możesz [użyć programu Poster](text-analytics-how-to-call-api.md) lub **konsoli testowania interfejsu API** w poniższych linkach referencyjnych, aby szybko ją i ją wysłać. 

#### <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

[Informacje dotyczące analiza tonacji v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

[Dokumentacja analiza tonacji v2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

Ustaw punkt końcowy HTTPS na potrzeby analizy tonacji przy użyciu zasobu analiza tekstu na platformie Azure lub [kontenera analiza tekstu](text-analytics-how-to-install-containers.md)wystąpienia. Musisz podać prawidłowy adres URL używanej wersji. Przykład:

> [!NOTE]
> Klucz i punkt końcowy dla zasobu analiza tekstu można znaleźć w witrynie Azure Portal. Zostaną one umieszczone na stronie **szybkiego startu** zasobu w obszarze **Zarządzanie zasobami**. 

#### <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

Ustaw nagłówek żądania w taki sposób, aby zawierał klucz interfejs API analizy tekstu. W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

### <a name="example-sentiment-analysis-request"></a>Przykładowe żądanie analiza tonacji 

Oto przykład zawartości, dla której można analizować tonację. Format żądania jest taki sam dla obu wersji interfejsu API.
    
```json
{
    "documents": [
    {
        "language": "en",
        "id": "1",
        "text": "Hello world. This is some input text that I love."
    },
    {
        "language": "en",
        "id": "2",
        "text": "It's incredibly sunny outside! I'm so happy."
    }
    ],
}
```

### <a name="post-the-request"></a>Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Aby uzyskać informacje na temat rozmiaru i liczby żądań wysyłanych na minutę i sekundę, zobacz sekcję [limity danych](../overview.md#data-limits) w przeglądzie.

Interfejs API analizy tekstu jest bezstanowy. Na Twoim koncie nie są przechowywane żadne dane, a wyniki są zwracane natychmiast w odpowiedzi.


### <a name="view-the-results"></a>Wyświetlanie wyników

Analizator tonacji klasyfikuje tekst jako "dodatnie" lub ujemne. Przypisuje wynik z zakresu od 0 do 1. Wartości zbliżone do 0,5 oznaczają opinię neutralną lub brak opinii. Wynik 0,5 oznacza opinię neutralną. Gdy nie można przeanalizować ciągu dla tonacji lub nie ma tonacji, wynik jest zawsze 0,5 dokładnie. Na przykład jeśli przekażesz ciąg w języku hiszpańskim z kodem języka angielskiego, wynik będzie wynosić 0,5.

Dane wyjściowe są zwracane natychmiast. Można przesyłać strumieniowo wyniki do aplikacji, która akceptuje kod JSON lub zapisuje dane wyjściowe do pliku w systemie lokalnym. Następnie zaimportuj dane wyjściowe do aplikacji, która może być używana do sortowania, wyszukiwania i manipulowania danymi. Ze względu na obsługę wielojęzycznych i emoji, odpowiedź może zawierać przesunięcia tekstu. Aby uzyskać więcej informacji [, zobacz Jak przetwarzać przesunięcia](../concepts/text-offsets.md) .

#### <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Przykładowa odpowiedź analiza tonacji v3

Odpowiedzi z analiza tonacji v3 zawierają etykiety i oceny tonacji dla każdego analizowanego zdania i dokumentu. `documentScores`nie jest zwracany, jeśli etykieta dokumentu tonacji ma `mixed`wartość.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>Przykładowa odpowiedź analiza tonacji v2

Odpowiedzi z analiza tonacji v2 zawierają wyniki tonacji dla każdego wysłanego dokumentu.

```json
{
  "documents": [{
    "id": "1",
    "score": 0.98690706491470337
  }, {
    "id": "2",
    "score": 0.95202046632766724
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy analizy tonacji przy użyciu interfejs API analizy tekstu. Podsumowanie:

+ Analiza tonacji jest dostępny dla wybranych języków w dwóch wersjach.
+ Dokumenty JSON w treści żądania obejmują identyfikator, tekst i kod języka.
+ Żądanie POST jest `/sentiment` punktem końcowym przy użyciu spersonalizowanego [klucza dostępu i punktu końcowego](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , który jest prawidłowy dla Twojej subskrypcji.
+ Dane wyjściowe odpowiedzi, które składają się z wyniku tonacji dla każdego identyfikatora dokumentu, mogą być przesyłane strumieniowo do dowolnej aplikacji, która akceptuje kod JSON. Na przykład program Excel i Power BI.

## <a name="see-also"></a>Zobacz także

* [Przegląd analiza tekstu](../overview.md)
* [Korzystanie z biblioteki klienta analiza tekstu](../quickstarts/text-analytics-sdk.md)
* [Co nowego](../whats-new.md)
