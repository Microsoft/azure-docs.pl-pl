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
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 243086ddaae47eba20eea6877fe6d7f8f9889290
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79203495"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Jak używać rozpoznawania jednostek nazwanych w analiza tekstu

Interfejs API analizy tekstu umożliwia przyjęcie tekstu bez struktury i zwrócenie listy niejednoznacznych jednostek z linkami do dodatkowych informacji w sieci Web. Interfejs API obsługuje jednocześnie rozpoznawanie jednostek nazwanych (NER) i łączenie jednostek.

### <a name="entity-linking"></a>Łączenie jednostek

Łączenie jednostek to możliwość identyfikacji i odróżnienia tożsamości jednostki znalezionej w tekście (na przykład w przypadku określenia, czy wystąpienie słowa `Mars` odwołuje się do globalnej, czy do rzymskie akty Boże wojny). Ten proces wymaga obecności bazy wiedzy w odpowiednim języku do łączenia rozpoznanych jednostek w tekście. Łączenie jednostek używa [witryny Wikipedia](https://www.wikipedia.org/) jako tej bazy wiedzy.


### <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych (NER)

Funkcja rozpoznawania jednostek nazwanych (NER) to możliwość identyfikowania różnych jednostek w tekście i kategoryzowania ich do wstępnie zdefiniowanych klas lub typów, takich jak: osoba, lokalizacja, wydarzenie, produkt i organizacja.  

Począwszy od wersji 3, ta funkcja interfejs API analizy tekstu może również identyfikować informacje osobiste i poufne, takie jak numer telefonu, numer ubezpieczenia społecznego, adres e-mail i numer konta bankowego.  Zidentyfikowanie tych jednostek może pomóc w klasyfikowaniu poufnych dokumentów i redagowanie informacji osobistych.

## <a name="named-entity-recognition-versions-and-features"></a>Wersje i funkcje rozpoznawania jednostek nazwanych

Interfejs API analizy tekstu oferuje dwie wersje nazwanego rozpoznawania jednostek — v2 i v3. Wersja 3 (Public Preview) zawiera bardziej szczegółowe informacje na temat jednostek, które mogą być wykrywane i klasyfikowane.

| Funkcja                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| Metody dla żądań pojedynczych i wsadowych                          | X      | X      |
| Podstawowe rozpoznawanie jednostek w różnych kategoriach              | X      | X      |
| Rozszerzona klasyfikacja dla rozpoznanych jednostek                 |        | X      |
| Oddziel punkty końcowe do wysyłania połączeń jednostek i żądań NER. |        | X      |
| Przechowywanie wersji modelu                                                |        | X      |

Aby uzyskać informacje, zobacz temat [Obsługa języków](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) .


#### <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

### <a name="entity-types"></a>Typy jednostek

Rozpoznawanie jednostek nazwanych v3 zapewnia rozszerzone wykrywanie w wielu typach. Obecnie NER v3 może rozpoznać następujące kategorie jednostek:

* Ogólne
* Dane osobowe 

Aby uzyskać szczegółową listę obsługiwanych jednostek i języków, zobacz artykuł [obsługiwane typy jednostek ner v3](../named-entity-types.md) .

### <a name="request-endpoints"></a>Punkty końcowe żądania

Nazwanego rozpoznawania jednostek v3 używa oddzielnych punktów końcowych dla żądań NER i konsolidacji jednostek. Użyj poniższego formatu adresu URL na podstawie Twojego żądania:

NER
* Jednostki ogólne —`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Dane osobowe —`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Łączenie jednostek
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Przechowywanie wersji modelu

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

### <a name="entity-types"></a>Typy jednostek

> [!NOTE]
> Rozpoznawanie jednostek nazwanych (NER) w wersji 2 obsługuje tylko następujące jednostki. NER V3 jest dostępna w publicznej wersji zapoznawczej i znacząco rozszerza liczbę i głębokość jednostek rozpoznawanych w tekście.   

| Typ  | SubType | Przykład |
|:-----------   |:------------- |:---------|
| Person (Osoba)        | NIE DOTYCZY\*         | "Jan", "bramy Bill"     |
| Lokalizacja      | NIE DOTYCZY\*         | "Redmond, Waszyngton", "Paryż"  |
| Organizacja  | NIE DOTYCZY\*         | Programu   |
| Liczba      | Liczba        | "6", "sześć"     |
| Liczba      | Procentowe    | "50%", "50 procent"|
| Liczba      | Liczbą       | "druga", "s"     |
| Liczba      | Wiek           | "90 dzień dawna", "30 lat starych"    |
| Liczba      | Waluta      | "$10,99"     |
| Liczba      | Wymiar     | "10 mil", "40 cm"     |
| Liczba      | Temperatura   | "32 stopni"    |
| DateTime      | NIE DOTYCZY\*         | "6:30 4 lutego 2012"      |
| DateTime      | Date          | "Maj druga, 2017", "05/02/2017"   |
| DateTime      | Time          | "8:00", "8:00"  |
| DateTime      | DateRange     | "Od 1 maja do 5 maja"    |
| DateTime      | TimeRange     | "18:00 do 7pm"     |
| DateTime      | Czas trwania      | "1 minuta i 45 s"   |
| DateTime      | Set           | "każdy wtorek"     |
| Adres URL           | NIE DOTYCZY\*         | "https:\//www.Bing.com"    |
| Poczta e-mail         | NIE DOTYCZY\*         | "support@contoso.com" |
| Numer telefonu USA  | NIE DOTYCZY\*         | (Tylko numery telefonów US) "(312) 555-0176" |
| Adres IP    | NIE DOTYCZY\*         | "10.0.0.100" |

\*W zależności od danych wejściowych i wyodrębnionych niektóre jednostki mogą pominąć `SubType`.  Wszystkie obsługiwane typy jednostek są dostępne tylko w językach angielskim, chińskim, francuskim, niemieckim i hiszpańskim.

### <a name="request-endpoints"></a>Punkty końcowe żądania

Rozpoznawanie jednostek nazwanych v2 używa jednego punktu końcowego dla żądań NER i konsolidacji jednostek:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>Wysyłanie żądania interfejsu API REST

### <a name="preparation"></a>Przygotowywanie

Musisz mieć dokumenty JSON w tym formacie: ID, text, language.

Każdy dokument musi mieć 5 120 znaków i może mieć do 1 000 elementów (identyfikatorów) na kolekcję. Kolekcja jest przesyłana w treści żądania.

### <a name="structure-the-request"></a>Określenie struktury żądania

Utwórz żądanie POST. Można [użyć programu Poster](text-analytics-how-to-call-api.md) lub **konsoli testowania interfejsu API** w poniższych linkach, aby szybko przeprowadzić strukturę i wysłać ją. 

> [!NOTE]
> Klucz i punkt końcowy dla zasobu analiza tekstu można znaleźć w witrynie Azure Portal. Zostaną one umieszczone na stronie **szybkiego startu** zasobu w obszarze **Zarządzanie zasobami**. 

#### <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

[Odwołanie do nazwanego rozpoznawania jednostek v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

W wersji 3 są używane oddzielne punkty końcowe dla żądań NER i konsolidacji jednostek. Użyj poniższego formatu adresu URL na podstawie Twojego żądania:

NER
* Jednostki ogólne —`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Jednostki danych osobowych —`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Łączenie jednostek
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

[Dokumentacja rozpoznawania jednostek nazwanych (NER) v2](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

Wersja 2 używa następującego punktu końcowego do konsolidacji obiektów i żądań NER: 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

Ustaw nagłówek żądania w taki sposób, aby zawierał klucz interfejs API analizy tekstu. W treści żądania Podaj przygotowane dokumenty JSON.

### <a name="example-ner-request"></a>Przykładowe żądanie NER 

Poniżej znajduje się przykład zawartości, którą można wysłać do interfejsu API. Format żądania jest taki sam dla obu wersji interfejsu API.

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
    }
  ]
}
```

## <a name="post-the-request"></a>Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Zapoznaj się z sekcją [limity danych](../overview.md#data-limits) w temacie Omówienie dotyczącej rozmiaru i liczby żądań wysyłanych na minutę i sekundę.

Interfejs API analizy tekstu jest bezstanowy. Na Twoim koncie nie są przechowywane żadne dane, a wyniki są zwracane natychmiast w odpowiedzi.

## <a name="view-results"></a>Wyświetlanie wyników

Wszystkie żądania POST zwracają sformatowaną w formacie JSON odpowiedź z identyfikatorami i wykrytymi właściwościami jednostki.

Dane wyjściowe są zwracane natychmiast. Wyniki można przesłać strumieniowo do aplikacji, która akceptuje kod JSON, lub zapisać do pliku w systemie lokalnym, a następnie zaimportować do aplikacji, która umożliwia sortowanie i wyszukiwanie danych oraz manipulowanie nimi. Ze względu na obsługę wielojęzycznych i emoji, odpowiedź może zawierać przesunięcia tekstu. Aby uzyskać więcej informacji [, zobacz Jak przetwarzać przesunięcia tekstu](../concepts/text-offsets.md) .

#### <a name="version-30-preview"></a>[Wersja 3,0-Preview)](#tab/version-3)

### <a name="example-v3-responses"></a>Przykładowe odpowiedzi v3

Wersja 3 zapewnia oddzielne punkty końcowe dla NER i konsolidacji jednostek. Odpowiedzi dla obu operacji są poniżej. 

#### <a name="example-ner-response"></a>Przykładowa odpowiedź NER

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>Przykład powiązania jednostki z odpowiedzią

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

### <a name="example-ner-v2-response"></a>Przykładowa odpowiedź NER v2
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy dotyczące łączenia jednostek przy użyciu analiza tekstu w Cognitive Services. Podsumowanie:

* Rozpoznawanie jednostek nazwanych jest dostępne dla wybranych języków w dwóch wersjach.
* Dokumenty JSON w treści żądania obejmują identyfikator, tekst i kod języka.
* Żądania POST są wysyłane do jednego lub większej liczby punktów końcowych, przy użyciu spersonalizowanego [klucza dostępu i punktu końcowego](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , który jest prawidłowy dla Twojej subskrypcji.
* Dane wyjściowe odpowiedzi, które obejmują połączone jednostki (w tym wyniki pewności, przesunięcia i linki sieci Web dla każdego identyfikatora dokumentu) mogą być używane w dowolnej aplikacji

## <a name="next-steps"></a>Następne kroki

* [Przegląd analiza tekstu](../overview.md)
* [Korzystanie z biblioteki klienta analiza tekstu](../quickstarts/text-analytics-sdk.md)
* [Co nowego](../whats-new.md)
