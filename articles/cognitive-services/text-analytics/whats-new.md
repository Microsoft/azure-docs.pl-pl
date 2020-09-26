---
title: Co nowego w interfejs API analizy tekstu
titleSuffix: Text Analytics - Azure Cognitive Services
description: Ten artykuł zawiera informacje o nowych wersjach i funkcjach usługi Azure Cognitive Services analiza tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: aahi
ms.openlocfilehash: 436d64583184ca2cd59b4ddf33056922c746fb34
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91271175"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Co nowego w interfejsie API analizy tekstu?

Interfejs API analizy tekstu jest regularnie aktualizowana. Aby zachować aktualność dzięki najnowszym zmianom, ten artykuł zawiera informacje o nowych wersjach i funkcjach.

## <a name="september-2020"></a>Wrzesień 2020

### <a name="general-api-updates"></a>Ogólne aktualizacje interfejsu API

* Wydanie nowego adresu URL dla publicznej wersji zapoznawczej analiza tekstu v 3.1 do obsługi aktualizacji następujących nazwanych punktów końcowych rozpoznawania jednostek: 
    * `/pii` punkt końcowy zawiera teraz nową `redactedText` Właściwość w formacie JSON odpowiedzi, w której wykryte jednostki danych wejściowych są zastępowane przez `*` dla każdego znaku tych jednostek.
    * `/linking` punkt końcowy zawiera teraz `bingID` Właściwość w formacie JSON odpowiedzi dla połączonych jednostek.
* Analiza tekstu następujące punkty końcowe interfejsu API w wersji zapoznawczej zostały wycofane 4 września 2020:
    * v 2.1 — wersja zapoznawcza
    * Wersja 3.0 (wersja zapoznawcza)
    * v 3.0 — wersja zapoznawcza 1
    
> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat interfejs API analizy tekstu v 3.1 — wersja zapoznawcza. 2](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Languages)

### <a name="text-analytics-for-health-container-updates"></a>analiza tekstu aktualizacji kontenera kondycji

Następujące aktualizacje są specyficzne dla wydania września analiza tekstu tylko dla kontenera kondycji.
* Nowy obraz kontenera z `1.1.013530001-amd64-preview` nowym modelem w wersji `2020-09-03` został ogłoszony do repozytorium containerpreview. 
* Ta wersja modelu zapewnia ulepszenia rozpoznawania jednostek, wykrywania skrótów i ulepszeń opóźnienia.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat analiza tekstu na potrzeby kondycji](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>Sierpień 2020 r.

### <a name="general-api-updates"></a>Ogólne aktualizacje interfejsu API

* Wersja modelu `2020-07-01` dla `/keyphrases` `/pii` `/languages` punktów końcowych v3 i, które dodaje:
    * Dodatkowe [Kategorie jednostek](named-entity-types.md?tabs=personal) dla instytucji rządowych i poszczególnych krajów dla nazwanego rozpoznawania jednostek.
    * Wsparcie norweskie i tureckie w analiza tonacji v3.
* W przypadku żądań interfejsu API v3, które przekraczają opublikowane [limity danych](concepts/data-limits.md), zostanie teraz zwrócony błąd HTTP 400. 
* Punkty końcowe zwracające przesunięcie obsługują teraz opcjonalny `stringIndexType` parametr, który dostosowuje zwrócone `offset` i `length` wartości zgodne z obsługiwanym [schematem indeksu ciągu](concepts/text-offsets.md).

### <a name="text-analytics-for-health-container-updates"></a>analiza tekstu aktualizacji kontenera kondycji

Następujące aktualizacje są specyficzne dla wydania z sierpnia analiza tekstu tylko dla kontenera kondycji.

* Nowy model — wersja dla analiza tekstu na potrzeby kondycji: `2020-07-24`
* Nowy adres URL służący do wysyłania analiza tekstu dla żądań dotyczących kondycji: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (należy pamiętać, że czyszczenie pamięci podręcznej przeglądarki będzie konieczne, aby można było użyć demonstracyjnej aplikacji sieci Web dołączonej do tego nowego obrazu kontenera)

Zmieniono następujące właściwości odpowiedzi JSON:

* Zmieniono nazwę polecenia `type` na `category` 
* Zmieniono nazwę polecenia `score` na `confidenceScore`
* Jednostki w `category` polu danych wyjściowych JSON znajdują się teraz w języku Pascal. Zmieniono nazwy następujących jednostek:
    * `EXAMINATION_RELATION` została zmieniona na `RelationalOperator` .
    * `EXAMINATION_UNIT` została zmieniona na `MeasurementUnit` .
    * `EXAMINATION_VALUE` została zmieniona na `MeasurementValue` .
    * `ROUTE_OR_MODE` Zmieniono nazwę `MedicationRoute` .
    * Nazwa jednostki relacyjnej została `ROUTE_OR_MODE_OF_MEDICATION` zmieniona na `RouteOfMedication` .

Dodano następujące jednostki:

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* Wyodrębnianie relacji
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat analiza tekstu dla kontenera kondycji](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>Lipiec 2020 r. 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Analiza tekstu dla kontenera kondycji — publiczna wersja zapoznawcza

Analiza tekstu dla kontenera kondycji jest teraz w publicznej wersji zapoznawczej, która umożliwia wyodrębnienie informacji z tekstu języka angielskiego bez struktury w dokumentach klinicznych, takich jak: formularze przyjmowania pacjentów, notatki lekarza, dokumenty badawcze i podsumowania zrzutów. Obecnie nie są naliczane opłaty za użycie kontenera kondycji analiza tekstu.

Kontener oferuje następujące funkcje:

* Rozpoznawanie jednostek nazwanych
* Wyodrębnianie relacji
* Łączenie jednostek
* Negacja

## <a name="may-2020"></a>Maj 2020 r.

### <a name="text-analytics-api-v3-general-availability"></a>Ogólna dostępność interfejs API analizy tekstu v3

Interfejs API analizy tekstu V3 jest teraz ogólnie dostępny w przypadku następujących aktualizacji:

* Wersja modelu `2020-04-01`
* Nowe [limity danych](concepts/data-limits.md) dla każdej funkcji
* Zaktualizowano [obsługę języka](language-support.md) dla [Analiza tonacji (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md)
* Oddzielny punkt końcowy do łączenia jednostek 
* Nowa kategoria jednostki "Address" w programie [nazwanego rozpoznawania jednostek (ner) v3](how-tos/text-analytics-how-to-entity-linking.md).
* Nowe podkategorie w NER v3:
   * Lokalizacja — geograficzne
   * Lokalizacja — strukturalna
   * Wymiana w organizacji
   * Organizacja — medycyna
   * Organizacja — Sport
   * Wydarzenie — kulturowe
   * Wydarzenie — naturalny
   * Wydarzenie — Sport

Dodano następujące właściwości odpowiedzi JSON:
   * `SentenceText` w analiza tonacji
   * `Warnings` dla każdego dokumentu 

Nazwy następujących właściwości w odpowiedzi JSON zostały zmienione, o ile ma to zastosowanie:

* Zmieniono nazwę polecenia `score` na `confidenceScore`
    * `confidenceScore` ma dwie dziesiętne punkty dokładności. 
* Zmieniono nazwę polecenia `type` na `category`
* Zmieniono nazwę polecenia `subtype` na `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [Dowiedz się więcej o interfejs API analizy tekstu wersji 3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Interfejs API analizy tekstu v 3.1 — publiczna wersja zapoznawcza
   * Nowa funkcja analiza tonacji — [Wyszukiwanie opinii](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Nowy [osobisty `PII` Filtr domeny](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) dla chronionych informacji o zdrowiu ( `PHI` ).

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wersji zapoznawczej interfejs API analizy tekstu v 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Luty 2020 r.

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Obsługa zestawu SDK dla publicznej wersji zapoznawczej interfejs API analizy tekstu v3

W ramach [ujednoliconej wersji zestawu Azure SDK](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)zestaw interfejs API analizy tekstu v3 SDK jest teraz dostępny jako publiczna wersja zapoznawcza następujących języków programowania:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [Dowiedz się więcej na temat zestawu SDK dla interfejs API analizy tekstu v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Niestandardowa wersja próbna rozpoznawania jednostek v3

Dodatkowe typy jednostek są teraz dostępne w publicznej wersji zapoznawczej rozpoznawania jednostek (NER) v3, ponieważ rozszerzamy wykrywanie ogólnych i osobistych jednostek informacji znalezionych w tekście. Ta aktualizacja wprowadza [wersję modelu](concepts/model-versioning.md) `2020-02-01` , która obejmuje:

* Rozpoznawanie następujących głównych typów jednostek (tylko w języku angielskim):
    * Persontype
    * Produkt
    * Zdarzenie
    * Jednostka geopolityczna (GPE) jako podtyp w lokalizacji
    * Czy

* Rozpoznawanie następujących typów jednostek informacji osobistych (tylko w języku angielskim):
    * Osoba
    * Organizacja
    * Wiek jako podtyp w ramach ilości
    * Data jako podtyp w obszarze DateTime
    * Poczta e-mail 
    * Numer telefonu (tylko Stany Zjednoczone)
    * Adres URL
    * Adres IP

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat nazwanego rozpoznawania jednostek v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Październik 2019 r.

#### <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych (NER)

* [Nowy punkt końcowy](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) do rozpoznawania typów jednostek informacji osobistych (tylko w języku angielskim)

* Oddziel punkty końcowe do [rozpoznawania jednostek](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) i [łączenia jednostek](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Wersja modelu](concepts/model-versioning.md) `2019-10-01` , która obejmuje:
    * Rozwinięte wykrywanie i kategoryzacja jednostek znalezionych w tekście. 
    * Rozpoznawanie następujących typów nowych jednostek:
        * Numer telefonu
        * Adres IP

Łączenie jednostek obsługuje język angielski i hiszpański. Obsługa języka NER zależy od typu jednostki.

#### <a name="sentiment-analysis-v3-public-preview"></a>Publiczna wersja zapoznawcza analiza tonacji v3

* [Nowy punkt końcowy](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) do analizowania tonacji.
* [Wersja modelu](concepts/model-versioning.md) `2019-10-01` , która obejmuje:

    * Znaczące ulepszenia dokładności i szczegółowości dotyczące kategoryzacji i oceny tekstu interfejsu API.
    * Automatyczne etykietowanie dla różnych mową w tekście.
    * Tonacji analizę i dane wyjściowe na poziomie dokumentu i zdania. 

Obsługuje język angielski ( `en` ), japoński ( `ja` ), chiński uproszczony (), chiński (tradycyjny), francuski (), włoski (), hiszpański (), `zh-Hans` `zh-Hant` `fr` `it` `es` holenderski ( `nl` ), portugalski () i `pt` niemiecki ( `de` ) i jest dostępny w następujących regionach: `Australia East` , `Central Canada` `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` ,,,, i. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej o analiza tonacji wersji 3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Następne kroki

* [Czym jest interfejs API analizy tekstu?](overview.md)  
* [Przykładowe scenariusze dotyczące użytkowników](text-analytics-user-scenarios.md)
* [Analiza tonacji](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Wykrywanie języka](how-tos/text-analytics-how-to-language-detection.md)
* [Rozpoznawanie jednostek](how-tos/text-analytics-how-to-entity-linking.md)
* [Wyodrębnianie kluczowych fraz](how-tos/text-analytics-how-to-keyword-extraction.md)
