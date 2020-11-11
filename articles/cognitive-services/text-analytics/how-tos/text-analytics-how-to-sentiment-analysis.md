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
ms.date: 11/11/2020
ms.author: aahi
ms.openlocfilehash: 87e6ad488438ae28467f6e904fbb57f7ca5448ff
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518179"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Instrukcje: wykrywanie tonacji przy użyciu interfejs API analizy tekstu

Funkcja analiza tonacji interfejs API analizy tekstu ocenia tekst i zwraca wyniki tonacji oraz etykiety dla każdego zdania. Jest to przydatne do wykrywania pozytywnych i negatywnych tonacji w mediach społecznościowych, przeglądów klientów, forów dyskusyjnych i nie tylko. Modele AI używane przez interfejs API są udostępniane przez usługę. Wystarczy przesłać zawartość do analizy.

Po wysłaniu żądania analizy tonacji interfejs API zwraca etykiety tonacji (na przykład "negatyw", "neutralny" i "pozytywna") oraz wyniki zaufania na poziomie zdania i dokumentu.

Analiza tonacji obsługuje szeroką gamę języków z więcej w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz listę [obsługiwanych języków](../language-support.md).

## <a name="sentiment-analysis-versions-and-features"></a>analiza tonacji wersje i funkcje

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Obiekt feature                                   | analiza tonacji v3 | Analiza tonacji v 3.1 (wersja zapoznawcza) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Metody dla żądań pojedynczych i wsadowych    | X                     | X                                 |
| Tonacji oceny i etykietowania             | X                     | X                                 |
| [Kontener platformy Docker](text-analytics-how-to-install-containers.md) oparty na systemie Linux | X  |  |
| Wyszukiwanie opinii                            |                       | X                                 |

## <a name="sentiment-scoring-and-labeling"></a>Tonacji ocenianie i etykietowanie

Analiza tonacji w wersji 3 stosuje etykiety tonacji do tekstu, które są zwracane na poziomie zdania i dokumentu, z wynikiem pewności dla każdej z nich. 

Etykiety są *dodatnie* , *ujemne* i *neutralne*. Na poziomie dokumentu może być również zwracana etykieta *mieszany* tonacji. Tonacji dokumentu jest określana poniżej:

| Tonacji zdania                                                                            | Etykieta zwracanego dokumentu |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Co najmniej jedno `positive` zdanie znajduje się w dokumencie. Pozostałe zdania są `neutral` . | `positive`              |
| Co najmniej jedno `negative` zdanie znajduje się w dokumencie. Pozostałe zdania są `neutral` . | `negative`              |
| Co najmniej jedno `negative` zdanie i co najmniej jedno `positive` zdanie znajduje się w dokumencie.    | `mixed`                 |
| Wszystkie zdania w dokumencie są `neutral` .                                                  | `neutral`               |

Wyniki zaufania mieszczą się w zakresie od 1 do 0. Wyniki zbliżone do 1 oznaczają wyższy poziom ufności w klasyfikacji etykiety, a niższe wyniki wskazują na zmniejszenie zaufania. Dla każdego dokumentu lub każdego zdania przewidywane wyniki skojarzone z etykietami (pozytywna, ujemna i neutralna) są dodawane do 1.

## <a name="opinion-mining"></a>Wyszukiwanie opinii

Wyszukiwanie opinii to funkcja analiza tonacji, rozpoczynająca się w wersji 3,1-Preview. 1. Funkcja ta oferuje również bardziej szczegółowe informacje o opiniach związanych z aspektami (np. atrybutami produktów lub usług), nazywanymi analiza tonacji opartymi na aspektach.

Na przykład jeśli klient opuści opinię na temat hotelu, na przykład "Pokój był świetny, ale personel był nieznajomy", Opinia dotycząca wyszukiwania będzie lokalizować treści tekstu oraz ich powiązane Opinie i mową:

| Aspekt | Opinie    | Opinia |
|--------|------------|-----------|
| pokój   | Znakomity      | positive  |
| kadr  | nieprzyjazne | negative  |

Aby uzyskać opinię na temat wyników wyszukiwania, musisz dołączyć `opinionMining=true` flagę do żądania analizy tonacji. Wyniki wyszukiwania opinii zostaną uwzględnione w odpowiedzi na analizę tonacji.

## <a name="sending-a-rest-api-request"></a>Wysyłanie żądania interfejsu API REST 

### <a name="preparation"></a>Przygotowanie

Analiza tonacji daje wynik wyższej jakości, gdy zostanie nadana mniejsza ilość tekstu do pracy. Jest to przeciwieństwo wyodrębniania kluczowych fraz, które działa lepiej na większych blokach tekstu. Aby uzyskać najlepsze wyniki obydwu operacji, warto rozważyć odpowiednią zmianę struktury danych wejściowych.

Musisz mieć dokumenty JSON w tym formacie: ID, text i Language.

Rozmiar dokumentu musi zawierać 5 120 znaków na dokument. Możesz mieć do 1 000 elementów (identyfikatorów) na kolekcję. Kolekcja jest przesyłana w treści żądania.

## <a name="structure-the-request"></a>Określenie struktury żądania

Utwórz żądanie POST. Możesz [użyć programu Poster](text-analytics-how-to-call-api.md) lub **konsoli testowania interfejsu API** w poniższych linkach referencyjnych, aby szybko ją i ją wysłać. 

#### <a name="version-31-preview2"></a>[Wersja 3,1-Preview. 2](#tab/version-3-1)

[Dokumentacja analiza tonacji v 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-2/operations/Sentiment)

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

[Informacje dotyczące analiza tonacji v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>Punkty końcowe żądania

Ustaw punkt końcowy HTTPS na potrzeby analizy tonacji przy użyciu zasobu analiza tekstu na platformie Azure lub [kontenera analiza tekstu](text-analytics-how-to-install-containers.md)wystąpienia. Musisz podać prawidłowy adres URL używanej wersji. Przykład:

> [!NOTE]
> Klucz i punkt końcowy dla zasobu analiza tekstu można znaleźć w witrynie Azure Portal. Zostaną one umieszczone na stronie **szybkiego startu** zasobu w obszarze **Zarządzanie zasobami**. 

#### <a name="version-31-preview2"></a>[Wersja 3,1-Preview. 2](#tab/version-3-1)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/sentiment`

Aby uzyskać opinię na temat wyników wyszukiwania, należy uwzględnić `opinionMining=true` parametr. Przykład:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/sentiment?opinionMining=true`

Ten parametr jest domyślnie ustawiony na wartość `false` . 

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

Ustaw nagłówek żądania w taki sposób, aby zawierał klucz interfejs API analizy tekstu. W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

### <a name="example-sentiment-analysis-request"></a>Przykładowe żądanie analiza tonacji 

Oto przykład zawartości, dla której można analizować tonację. Format żądania jest taki sam dla obu wersji.
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Aby uzyskać informacje na temat rozmiaru i liczby żądań wysyłanych na minutę i sekundę, zobacz sekcję [limity danych](../overview.md#data-limits) w przeglądzie.

Interfejs API analizy tekstu jest bezstanowy. Na Twoim koncie nie są przechowywane żadne dane, a wyniki są zwracane natychmiast w odpowiedzi.


### <a name="view-the-results"></a>Wyświetlanie wyników

Analiza tonacji zwraca etykietę tonacji i wynik pewności dla całego dokumentu oraz każde zdanie w nim. Wyniki zbliżone do 1 oznaczają wyższy poziom ufności w klasyfikacji etykiety, a niższe wyniki wskazują na zmniejszenie zaufania. Dokument może zawierać wiele zdań, a wyniki pewności w ramach każdego dokumentu lub zdania są dodawane do 1.

Dane wyjściowe są zwracane natychmiast. Można przesyłać strumieniowo wyniki do aplikacji, która akceptuje kod JSON lub zapisuje dane wyjściowe do pliku w systemie lokalnym. Następnie zaimportuj dane wyjściowe do aplikacji, która może być używana do sortowania, wyszukiwania i manipulowania danymi. Ze względu na obsługę wielojęzycznych i emoji, odpowiedź może zawierać przesunięcia tekstu. Aby uzyskać więcej informacji [, zobacz Jak przetwarzać przesunięcia](../concepts/text-offsets.md) .

#### <a name="version-31-preview2"></a>[Wersja 3,1-Preview. 2](#tab/version-3-1)

### <a name="sentiment-analysis-v31-example-response"></a>Przykładowa odpowiedź na analiza tonacji v 3.1

Analiza tonacji v 3.1 oferuje do wyszukiwania opinii oprócz obiektu Response na karcie w **wersji 3,0** . W poniższej odpowiedzi zdanie *w restauracji miało doskonałą żywność i nasz oczekiwał* ma dwa aspekty: *żywność* i *zaczekaj*. Każda właściwość aspektu `relations` zawiera `ref` wartość z odwołaniem identyfikatora URI do skojarzonych `documents` , `sentences` i `opinions` obiektów.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly.",
                    "aspects": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 25,
                            "length": 4,
                            "text": "food",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/0"
                                }
                            ]
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 38,
                            "length": 6,
                            "text": "waiter",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/1"
                                }
                            ]
                        }
                    ],
                    "opinions": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 19,
                            "length": 5,
                            "text": "great",
                            "isNegated": false
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 49,
                            "length": 8,
                            "text": "friendly",
                            "isNegated": false
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

### <a name="sentiment-analysis-v30-example-response"></a>Przykładowa odpowiedź na analiza tonacji v 3.0

Odpowiedzi z analiza tonacji v3 zawierają etykiety i oceny tonacji dla każdego analizowanego zdania i dokumentu.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
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

W tym artykule przedstawiono koncepcje i przepływ pracy analizy tonacji przy użyciu interfejs API analizy tekstu. Podsumowanie:

+ Analiza tonacji jest dostępny dla wybranych języków.
+ Dokumenty JSON w treści żądania obejmują identyfikator, tekst i kod języka.
+ Żądanie POST jest `/sentiment` punktem końcowym przy użyciu spersonalizowanego [klucza dostępu i punktu końcowego](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , który jest prawidłowy dla Twojej subskrypcji.
+ Dane wyjściowe odpowiedzi, które składają się z wyniku tonacji dla każdego identyfikatora dokumentu, mogą być przesyłane strumieniowo do dowolnej aplikacji, która akceptuje kod JSON. Na przykład program Excel i Power BI.

## <a name="see-also"></a>Zobacz także

* [Przegląd analizy tekstu](../overview.md)
* [Korzystanie z biblioteki klienta analiza tekstu](../quickstarts/text-analytics-sdk.md)
* [Co nowego](../whats-new.md)