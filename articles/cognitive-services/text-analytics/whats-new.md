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
ms.date: 03/25/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 0ed3a11381285a9422380eb14ff301a2b9ea816a
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093558"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Co nowego w interfejsie API analizy tekstu?

Interfejs API analizy tekstu jest regularnie aktualizowana. Aby zachować aktualność dzięki najnowszym zmianom, ten artykuł zawiera informacje o nowych wersjach i funkcjach.

## <a name="march-2021"></a>Marzec 2021

### <a name="general-api-updates"></a>Ogólne aktualizacje interfejsu API
* Wydanie nowego interfejsu API v 3.1 — wersja zapoznawcza. 4, które obejmuje 
   * Zmiany w treści odpowiedzi JSON wyszukiwania opinii: 
      * `aspects` jest teraz `targets` i `opinions` teraz `assessments` . 
   * Zmiany w treści odpowiedzi JSON hostowanego internetowego interfejsu API analiza tekstu dla kondycji: 
      * `isNegated`Nazwa logiczna wykrytego obiektu jednostki dla negacji jest przestarzała i zastąpiona przez wykrywanie potwierdzenia.
      * Nowa właściwość o nazwie `role` jest teraz częścią wyodrębnionej relacji między atrybutem i jednostką, a także relacją między jednostkami.  Spowoduje to dodanie specyficznych do wykrytych typów relacji.
   * Łączenie jednostek jest teraz dostępne jako zadanie asynchroniczne w `/analyze` punkcie końcowym.
   * Nowy `pii-categories` parametr jest teraz dostępny w `/pii` punkcie końcowym.
      * Ten parametr umożliwia określenie wybranych jednostek danych osobowych, a także tych, które nie są obsługiwane domyślnie dla języka wejściowego.
* Zaktualizowano biblioteki klienckie, które obejmują analizę asynchroniczną i analiza tekstu dla operacji związanych z kondycją. Przykłady można znaleźć w witrynie GitHub:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/javascript)
    
> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat interfejs API analizy tekstu v 3.1 — wersja zapoznawcza. 4](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-4/operations/Languages)

### <a name="text-analytics-for-health-updates"></a>analiza tekstu aktualizacji kondycji

* Nowa wersja modelu `2021-03-01` dla `/health` punktu końcowego i kontenera lokalnego, który zapewnia
    * Zmiana nazwy `Gene` typu jednostki na `GeneOrProtein` .
    * Nowy `Date` Typ jednostki.
    * Wykrywanie potwierdzenia, które zastępuje wykrywanie negacji (dostępne tylko w interfejsie API v 3.1 — wersja zapoznawcza. 4).
    * Nowa preferowana `name` Właściwość dla połączonych jednostek, które są znormalizowane z różnych systemów ontologie i kodowania (dostępne tylko w interfejsie API v 3.1 — wersja zapoznawcza. 4). 
* Nowy obraz kontenera z tagiem `3.0.015490002-onprem-amd64` i nową wersją modelu `2021-03-01` został opublikowany do repozytorium w wersji zapoznawczej kontenera. 
    * Ten obraz kontenera nie będzie już dostępny do pobrania z dniem `containerpreview.azurecr.io` 26 kwietnia 2021.
* Nowy analiza tekstu obrazu kontenera kondycji z tą samą wersją modelu jest teraz dostępny pod adresem `mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare` . Od 26 kwietnia będziesz mieć możliwość pobrania kontenera z tego repozytorium.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat analiza tekstu na potrzeby kondycji](how-tos/text-analytics-for-health.md)

### <a name="text-analytics-resource-portal-update"></a>Aktualizacja portalu zasobów analiza tekstu
* **Przetworzone rekordy tekstu** są teraz dostępne jako Metryka w sekcji **monitorowanie** dla zasobu analiza tekstu w Azure Portal.  

## <a name="february-2021"></a>Luty 2021 r.

* `2021-01-15`Wersja modelu dla WIELOosobowego punktu końcowego w [nazwanym rozpoznaniu jednostki](how-tos/text-analytics-how-to-entity-linking.md) v 3.1-Preview. x, która zapewnia 
  * Rozszerzona obsługa 9 nowych języków
  * Ulepszona jakość AI kategorii nazwanych jednostek dla obsługiwanych języków.
* Warstwy cenowe S0 za pomocą S4 są wycofywane z 8 marca, 2021. Jeśli masz istniejący zasób analiza tekstu za pomocą warstwy cenowej S0 za pośrednictwem S4, należy ją zaktualizować do korzystania z [warstwy cenowej](how-tos/text-analytics-how-to-call-api.md#change-your-pricing-tier)standardowa.
* [Kontener wykrywania języka](how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment) jest teraz ogólnie dostępny.
* Wersja 2.1 interfejsu API jest wycofywana. 

## <a name="january-2021"></a>Styczeń 2021 r.

* `2021-01-15`Wersja modelu dla [nazwanej jednostki rozpoznawania](how-tos/text-analytics-how-to-entity-linking.md) v3. x, która zapewnia 
  * Rozszerzona obsługa języków dla [kilku ogólnych kategorii jednostek](named-entity-types.md). 
  * Udoskonalone ogólne kategorie dla wszystkich obsługiwanych języków v3. 

* `2021-01-05`Wersja modelu do [wykrywania języka](how-tos/text-analytics-how-to-language-detection.md), która oferuje dodatkową [obsługę języka](language-support.md?tabs=language-detection).

Te wersje modeli są obecnie niedostępne w regionie Wschodnie stany USA. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej o nowym modelu NER](https://azure.microsoft.com/updates/text-analytics-ner-improved-ai-quality)

## <a name="december-2020"></a>Grudzień 2020 r.

* [Zaktualizowano szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) interfejs API analizy tekstu.

## <a name="november-2020"></a>Listopad 2020 r.

* [Nowy punkt końcowy](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Analyze) z interfejs API analizy tekstu v 3.1 — wersja zapoznawcza. 3 dla nowego [interfejsu API analizy](how-tos/text-analytics-how-to-call-api.md?tabs=analyze)asynchronicznej, który obsługuje przetwarzanie wsadowe dla operacji wyodrębniania ner, dane osobowe i frazy klucza.
* [Nowy punkt końcowy](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) z interfejs API analizy tekstu v 3.1 — wersja zapoznawcza. 3 dla nowych analiza tekstu asynchronicznych dla interfejsu API hostowanego w usłudze [Health](how-tos/text-analytics-for-health.md) z obsługą przetwarzania wsadowego.
* Obie nowe funkcje wymienione powyżej są dostępne tylko w następujących regionach: `West US 2` ,, `East US 2` `Central US` `North Europe` i `West Europe` .
* Portugalski (Brazylia) `pt-BR` jest teraz obsługiwane w [Analiza tonacji](how-tos/text-analytics-how-to-sentiment-analysis.md) v3. x, rozpoczynając od wersji modelu `2020-04-01` . Dodaje do istniejącej obsługi języka `pt-PT` portugalskiego.
* Zaktualizowano biblioteki klienckie, które obejmują analizę asynchroniczną i analiza tekstu dla operacji związanych z kondycją. Przykłady można znaleźć w witrynie GitHub:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * 
> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat interfejs API analizy tekstu v 3.1 — wersja zapoznawcza. 3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

## <a name="october-2020"></a>Październik 2020 r.

* Obsługa języka hindi dla analiza tonacji v3. x, rozpoczynając od wersji modelu `2020-04-01` . 
* Wersja modelu `2020-09-01` dla punktu końcowego/Languages v3, który dodaje ulepszone wykrywanie i dokładność języka.
* Dostępność wersji 3 w centralnej wersji Indie i Zjednoczone Emiraty Arabskie.

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
> [Dowiedz się więcej na temat interfejs API analizy tekstu v 3.1 — wersja zapoznawcza. 2](quickstarts/client-libraries-rest-api.md)

### <a name="text-analytics-for-health-container-updates"></a>analiza tekstu aktualizacji kontenera kondycji

Następujące aktualizacje są specyficzne dla wydania września analiza tekstu tylko dla kontenera kondycji.
* Nowy obraz kontenera z `1.1.013530001-amd64-preview` nowym modelem w wersji `2020-09-03` został ogłoszony w repozytorium wersji zapoznawczej kontenera. 
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

> [!div class="nextstepaction"]
> [Dowiedz się więcej o interfejs API analizy tekstu wersji 3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Interfejs API analizy tekstu v 3.1 — publiczna wersja zapoznawcza
   * Nowa funkcja analiza tonacji — [Wyszukiwanie opinii](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Nowy osobisty `PII` Filtr domeny dla chronionych informacji o zdrowiu ( `PHI` ).

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wersji zapoznawczej interfejs API analizy tekstu v 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Luty 2020 r.

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Obsługa zestawu SDK dla publicznej wersji zapoznawczej interfejs API analizy tekstu v3

W ramach [ujednoliconej wersji zestawu Azure SDK](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)zestaw interfejs API analizy tekstu v3 SDK jest teraz dostępny jako publiczna wersja zapoznawcza następujących języków programowania:
   * [C#](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript (Node.js)](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-java&tabs=version-3)
   
> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat zestawu SDK dla interfejs API analizy tekstu v3](./quickstarts/client-libraries-rest-api.md?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Niestandardowa wersja próbna rozpoznawania jednostek v3

Dodatkowe typy jednostek są teraz dostępne w publicznej wersji zapoznawczej rozpoznawania jednostek (NER) v3, ponieważ rozszerzamy wykrywanie ogólnych i osobistych jednostek informacji znalezionych w tekście. Ta aktualizacja wprowadza [wersję modelu](concepts/model-versioning.md) `2020-02-01` , która obejmuje:

* Rozpoznawanie następujących głównych typów jednostek (tylko w języku angielskim):
    * Persontype
    * Produkt
    * Zdarzenie
    * Jednostka geopolityczna (GPE) jako podtyp w lokalizacji
    * Czy

* Rozpoznawanie następujących typów jednostek informacji osobistych (tylko w języku angielskim):
    * Person (Osoba)
    * Organizacja
    * Wiek jako podtyp w ramach ilości
    * Data jako podtyp w obszarze DateTime
    * E-mail 
    * Numer telefonu (tylko Stany Zjednoczone)
    * Adres URL
    * Adres IP

### <a name="october-2019"></a>Październik 2019 r.

#### <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych (NER)

* [Nowy punkt końcowy](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii) do rozpoznawania typów jednostek informacji osobistych (tylko w języku angielskim)

* Oddziel punkty końcowe do [rozpoznawania jednostek](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) i [łączenia jednostek](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking).

* [Wersja modelu](concepts/model-versioning.md) `2019-10-01` , która obejmuje:
    * Rozwinięte wykrywanie i kategoryzacja jednostek znalezionych w tekście. 
    * Rozpoznawanie następujących typów nowych jednostek:
        * Numer telefonu
        * Adres IP

Łączenie jednostek obsługuje język angielski i hiszpański. Obsługa języka NER zależy od typu jednostki.

#### <a name="sentiment-analysis-v3-public-preview"></a>Publiczna wersja zapoznawcza analiza tonacji v3

* [Nowy punkt końcowy](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) do analizowania tonacji.
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
