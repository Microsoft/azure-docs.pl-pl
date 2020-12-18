---
title: Przeprowadź analizę tonacji i opinię na temat wyszukiwania za pomocą interfejsu API REST analiza tekstu
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono sposób wykrywania tonacji i podawania opinii na temat interfejs API analizy tekstu Cognitive Services platformy Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 12/04/2020
ms.author: aahi
ms.openlocfilehash: 6ea7b992a682537471ce0e78385b37674199d687
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673057"
---
# <a name="how-to-sentiment-analysis-and-opinion-mining"></a>Instrukcje: Analiza Tonacjia i wyszukiwanie opinii

Funkcja analiza tonacji interfejs API analizy tekstu zapewnia dwa sposoby wykrywania dodatnich i ujemnych tonacji. W przypadku wysłania żądania analiza tonacji interfejs API zwróci etykiety tonacji (na przykład "negatywna", "neutralna" i "pozytywne") oraz wyniki zaufania na poziomie zdania i dokumentu. Możesz również wysyłać opinie o żądaniach wyszukiwania przy użyciu punktu końcowego analiza tonacji, który zawiera szczegółowe informacje o opiniach związanych z aspektami (takimi jak atrybuty produktów lub usług) w tekście. 

Modele AI używane przez interfejs API są udostępniane przez usługę. Wystarczy przesłać zawartość do analizy.

## <a name="sentiment-analysis-versions-and-features"></a>analiza tonacji wersje i funkcje

| Cecha                                   | analiza tonacji v3 | Analiza tonacji v 3.1 (wersja zapoznawcza) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Metody dla żądań pojedynczych i wsadowych    | X                     | X                                 |
| analiza tonacji oceny i etykietowania             | X                     | X                                 |
| [Kontener platformy Docker](text-analytics-how-to-install-containers.md) oparty na systemie Linux | X  |  |
| Wyszukiwanie opinii                            |                       | X                                 |

## <a name="sentiment-analysis"></a>Analiza tonacji

Analiza tonacji w wersji 3. x stosuje etykiety tonacji do tekstu, które są zwracane na poziomie zdania i dokumentu, z wynikiem pewności dla każdej z nich. 

Etykiety są *dodatnie*, *ujemne* i *neutralne*. Na poziomie dokumentu może być również zwracana etykieta *mieszany* tonacji. Tonacji dokumentu jest określana poniżej:

| Tonacji zdania                                                                            | Etykieta zwracanego dokumentu |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Co najmniej jedno `positive` zdanie znajduje się w dokumencie. Pozostałe zdania są `neutral` . | `positive`              |
| Co najmniej jedno `negative` zdanie znajduje się w dokumencie. Pozostałe zdania są `neutral` . | `negative`              |
| Co najmniej jedno `negative` zdanie i co najmniej jedno `positive` zdanie znajduje się w dokumencie.    | `mixed`                 |
| Wszystkie zdania w dokumencie są `neutral` .                                                  | `neutral`               |

Wyniki zaufania mieszczą się w zakresie od 1 do 0. Wyniki zbliżone do 1 oznaczają wyższy poziom ufności w klasyfikacji etykiety, a niższe wyniki wskazują na zmniejszenie zaufania. Dla każdego dokumentu lub każdego zdania przewidywane wyniki skojarzone z etykietami (pozytywna, ujemna i neutralna) są dodawane do 1. Aby uzyskać więcej informacji, zobacz [Analiza tekstu przezroczystość](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context). 

## <a name="opinion-mining"></a>Wyszukiwanie opinii

Wyszukiwanie opinii to funkcja analiza tonacji, rozpoczynająca się w wersji zapoznawczej 3,1. Funkcja ta oferuje również bardziej szczegółowe informacje o opiniach związanych z aspektami (np. atrybutami produktów lub usług), nazywanymi analiza tonacji opartymi na aspektach.

Na przykład, jeśli klient opuści opinię na temat hotelu, na przykład "Pokój był świetny, ale personel był nieznajomy" ", Opinia dotycząca wyszukiwania będzie lokalizować aspekty w tekście oraz ich powiązane Opinie i mową. Analiza tonacji może zgłosić tylko ujemną tonacji.

:::image type="content" source="../media/how-tos/opinion-mining.png" alt-text="Diagram przedstawiający przykład wyszukiwania opinii" lightbox="../media/how-tos/opinion-mining.png":::

Aby uzyskać opinię na temat wyników wyszukiwania, musisz dołączyć `opinionMining=true` flagę do żądania analizy tonacji. Wyniki wyszukiwania opinii zostaną uwzględnione w odpowiedzi na analizę tonacji. Wyszukiwanie opinii to rozszerzenie analiza tonacji i jest zawarte w bieżącej [warstwie cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).


## <a name="sending-a-rest-api-request"></a>Wysyłanie żądania interfejsu API REST 

### <a name="preparation"></a>Przygotowanie

Analiza tonacji daje wynik wyższej jakości, gdy zostanie nadana mniejsza ilość tekstu do pracy. Jest to przeciwieństwo wyodrębniania kluczowych fraz, które działa lepiej na większych blokach tekstu. Aby uzyskać najlepsze wyniki obydwu operacji, warto rozważyć odpowiednią zmianę struktury danych wejściowych.

Musisz mieć dokumenty JSON w tym formacie: ID, text i Language. Analiza tonacji obsługuje szeroką gamę języków z więcej w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz listę [obsługiwanych języków](../language-support.md).

Rozmiar dokumentu musi zawierać 5 120 znaków na dokument. Aby uzyskać maksymalną dozwoloną liczbę dokumentów w kolekcji, zobacz artykuł dotyczący [limitów danych](../concepts/data-limits.md?tabs=version-3) w obszarze pojęcia. Kolekcja jest przesyłana w treści żądania.

## <a name="structure-the-request"></a>Określenie struktury żądania

Utwórz żądanie POST. Możesz [użyć programu Poster](text-analytics-how-to-call-api.md) lub **konsoli testowania interfejsu API** w poniższych linkach referencyjnych, aby szybko ją i ją wysłać. 

#### <a name="version-31-preview3"></a>[Wersja 3,1-Preview. 3](#tab/version-3-1)

[Dokumentacja analiza tonacji v 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

[Informacje dotyczące analiza tonacji v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>Punkty końcowe żądania

Ustaw punkt końcowy HTTPS na potrzeby analizy tonacji przy użyciu zasobu analiza tekstu na platformie Azure lub [kontenera analiza tekstu](text-analytics-how-to-install-containers.md)wystąpienia. Musisz podać prawidłowy adres URL używanej wersji. Na przykład:

> [!NOTE]
> Możesz znaleźć klucz i punkt końcowy dla zasobu analiza tekstu na Azure Portal. Zostaną one umieszczone na stronie **szybkiego startu** zasobu w obszarze **Zarządzanie zasobami**. 

#### <a name="version-31-preview3"></a>[Wersja 3,1-Preview. 3](#tab/version-3-1)

**Analiza tonacji**

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/sentiment`

**Wyszukiwanie opinii**

Aby uzyskać opinię na temat wyników wyszukiwania, należy uwzględnić `opinionMining=true` parametr. Na przykład:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/sentiment?opinionMining=true`

Ten parametr jest domyślnie ustawiony na wartość `false` . 

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

**Analiza tonacji**

W programie v 3.0 jedynym dostępnym punktem końcowym jest dla analiza tonacji.
 
`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

Ustaw nagłówek żądania w taki sposób, aby zawierał klucz interfejs API analizy tekstu. W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

### <a name="example-request-for-sentiment-analysis-and-opinion-mining"></a>Przykładowe żądanie dla analiza tonacji i wyszukiwania opinii  

Oto przykład zawartości, dla której można analizować tonację. Format żądania jest taki sam dla obu `v3.0` i `v3.1-preview` .
    
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

Dane wyjściowe są zwracane natychmiast. Można przesyłać strumieniowo wyniki do aplikacji, która akceptuje kod JSON lub zapisuje dane wyjściowe do pliku w systemie lokalnym. Następnie zaimportuj dane wyjściowe do aplikacji, która może być używana do sortowania, wyszukiwania i manipulowania danymi. Ze względu na obsługę wielojęzycznych i emoji, odpowiedź może zawierać przesunięcia tekstu. Aby uzyskać więcej informacji [, zobacz Jak przetwarzać przesunięcia](../concepts/text-offsets.md) .

#### <a name="version-31-preview3"></a>[Wersja 3,1-Preview. 3](#tab/version-3-1)

### <a name="sentiment-analysis-and-opinion-mining-example-response"></a>Przykład odpowiedzi wyszukiwania analiza tonacji i opinii

> [!IMPORTANT]
> Poniżej znajduje się przykład kodu JSON służący do korzystania z funkcji wyszukiwania opinii z analiza tonacjiami oferowanymi w v 3.1 interfejsu API. Jeśli nie poprosisz o wyszukiwanie opinii, odpowiedź interfejsu API będzie taka sama jak karta w **wersji 3,0** .  

Analiza tonacji v 3.1 może zwracać obiekty odpowiedzi zarówno dla analiza tonacji, jak i do wyszukiwania opinii.
  
Analiza tonacji zwraca etykietę tonacji i wynik pewności dla całego dokumentu oraz każde zdanie w nim. Wyniki zbliżone do 1 oznaczają wyższy poziom ufności w klasyfikacji etykiety, a niższe wyniki wskazują na zmniejszenie zaufania. Dokument może zawierać wiele zdań, a wyniki pewności w ramach każdego dokumentu lub zdania są dodawane do 1.

Opinia wyszukiwania będzie lokalizować aspekty w tekście oraz ich powiązane Opinie i mową. W poniższej odpowiedzi zdanie *w restauracji miało doskonałą żywność i nasz oczekiwał* ma dwa aspekty: *żywność* i *zaczekaj*. Każda właściwość aspektu `relations` zawiera `ref` wartość z odwołaniem identyfikatora URI do skojarzonych `documents` , `sentences` i `opinions` obiektów.

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

### <a name="sentiment-analysis-example-response"></a>analiza tonacji Przykładowa odpowiedź

Analiza tonacji zwraca etykietę tonacji i wynik pewności dla całego dokumentu oraz każde zdanie w nim. Wyniki zbliżone do 1 oznaczają wyższy poziom ufności w klasyfikacji etykiety, a niższe wyniki wskazują na zmniejszenie zaufania. Dokument może zawierać wiele zdań, a wyniki pewności w ramach każdego dokumentu lub zdania są dodawane do 1.

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

+ W przypadku wybrania języków analiza tonacji i dostępne są opinie.
+ Dokumenty JSON w treści żądania obejmują identyfikator, tekst i kod języka.
+ Żądanie POST jest `/sentiment` punktem końcowym przy użyciu spersonalizowanego [klucza dostępu i punktu końcowego](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , który jest prawidłowy dla Twojej subskrypcji.
+ Użyj `opinionMining=true` w żądaniach analizy Sentient, aby uzyskać wyniki wyszukiwania.
+ Dane wyjściowe odpowiedzi, które składają się z wyniku tonacji dla każdego identyfikatora dokumentu, mogą być przesyłane strumieniowo do dowolnej aplikacji, która akceptuje kod JSON. Na przykład program Excel i Power BI.

## <a name="see-also"></a>Zobacz także

* [Przegląd analizy tekstu](../overview.md)
* [Korzystanie z biblioteki klienta analiza tekstu](../quickstarts/client-libraries-rest-api.md)
* [Co nowego](../whats-new.md)
