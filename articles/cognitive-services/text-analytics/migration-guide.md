---
title: Przewodnik migracji dla wersji 2 interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przenieść aplikacje do korzystania z wersji 3 interfejs API analizy tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/22/2021
ms.author: aahi
ms.openlocfilehash: 416ef4ceddbb43e9f1606d44a66ffd5295cee4e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699899"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Przeprowadź migrację do wersji 3. x interfejs API analizy tekstu

Jeśli używasz wersji 2,1 interfejs API analizy tekstu, ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 3. x. Wersja 3,0 jest ogólnie dostępna i wprowadza nowe funkcje, takie jak rozszerzone [rozpoznawanie jednostek nazwanych (ner)](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-features-and-versions) i [przechowywanie wersji modelu](concepts/model-versioning.md). Dostępna jest również wersja zapoznawcza v 3.1 (v 3.1-Preview. x), która dodaje funkcje, takie jak [Wyszukiwanie opinii](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features). Modele używane w wersji 2 nie będą otrzymywać przyszłych aktualizacji. 

## <a name="sentiment-analysis"></a>[Analiza tonacji](#tab/sentiment-analysis)

### <a name="feature-changes"></a>Zmiany funkcji 

Analiza tonacji w wersji 2,1 zwraca wyniki tonacji z zakresu od 0 do 1 dla każdego dokumentu wysłanego do interfejsu API, z wynikami zbliżonymi do 1 wskazującą więcej pozytywnych tonacji. Zamiast tego zwraca etykiety tonacji (takie jak "pozytyw" lub "ujemna") zarówno dla zdania i dokumentu jako całości, jak i skojarzonych z nimi ocen zaufania. 

### <a name="steps-to-migrate"></a>Kroki migracji

#### <a name="rest-api"></a>Interfejs API REST

Jeśli aplikacja używa interfejsu API REST, Zaktualizuj swój punkt końcowy żądania do punktu końcowego v3 dla analizy tonacji. Na przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment` . Należy również zaktualizować aplikację tak, aby korzystała z etykiet tonacji zwracanych w [odpowiedzi interfejsu API](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results). 

Przykłady odpowiedzi JSON można znaleźć w dokumentacji referencyjnej.
* [Wersja 2,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [Wersja 3,0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [Wersja 3,1-Preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="client-libraries"></a>Biblioteki klienta

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="ner-and-entity-linking"></a>[NER i łączenie jednostek](#tab/named-entity-recognition)

### <a name="feature-changes"></a>Zmiany funkcji

W wersji 2,1 interfejs API analizy tekstu używa jednego punktu końcowego dla nazwanego rozpoznawania jednostek (NER) i konsolidacji jednostek. Wersja 3 zapewnia rozszerzone wykrywanie nazwanych jednostek i używa oddzielnych punktów końcowych dla żądań NER i konsolidacji jednostek. Począwszy od wersji v 3.1 — wersja zapoznawcza. 1, NER może dodatkowo wykrywać `pii` informacje osobiste i kondycję `phi` . 

### <a name="steps-to-migrate"></a>Kroki migracji

#### <a name="rest-api"></a>Interfejs API REST

Jeśli aplikacja używa interfejsu API REST, Zaktualizuj swój punkt końcowy żądania do punktów końcowych v3 dla konsolidacji NER i/lub obiektu.

Łączenie jednostek
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

Należy również zaktualizować aplikację tak, aby korzystała z [kategorii jednostek](named-entity-types.md) zwracanych w [odpowiedzi interfejsu API](how-tos/text-analytics-how-to-entity-linking.md#view-results).

Przykłady odpowiedzi JSON można znaleźć w dokumentacji referencyjnej.
* [Wersja 2,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [Wersja 3,0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [Wersja 3,1-Preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/EntitiesRecognitionGeneral)

#### <a name="client-libraries"></a>Biblioteki klienta

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="version-21-entity-categories"></a>Kategorie jednostek w wersji 2,1

Poniższa tabela zawiera listę kategorii jednostek zwróconych dla NER v 2.1.

| Kategoria   | Opis                          |
|------------|--------------------------------------|
| Person (Osoba)   |   Nazwy osób.  |
|Lokalizacja    | Punkty orientacyjne, struktury, funkcje geograficzne i geopolityczne, w naturalny i ludzki sposób |
|Organizacja | Firmy, grupy polityczne, zespoły muzyczne, trefle sportowe, instytucje rządowe i organizacje publiczne. Narodowe i religijne nie są uwzględnione w tym typie jednostki. |
| PhoneNumber | Numery telefonów (tylko numery telefonów USA i UE). |
| E-mail | Adresy e-mail. |
| Adres URL | Adresy URL do witryn sieci Web. |
| Adres IP | Adresy IP sieci. |
| DateTime | Daty i godziny dnia.| 
| Date (Data) | Daty kalendarzowe. |
| Godzina | Razy dziennie |
| Zakres dat | Zakresy dat. |
| Zakres czasu | Zakresy czasu. |
| Czas trwania | Czasów trwania. |
| Set | Ustawianie, powtarzające się czasy. |
| Liczba | Liczby i ilości liczbowe. |
| Liczba | Numery. |
| Procent | Wartości procentowe.|
| Liczba porządkowa | Liczby porządkowe. |
| Wiek | Ważności. |
| Waluta | Waluty. |
| Wymiar | Wymiary i pomiary. |
| Temperatura | Nosząc. |

## <a name="language-detection"></a>[Wykrywanie języka](#tab/language-detection)

### <a name="feature-changes"></a>Zmiany funkcji 

Dane wyjściowe funkcji wykrywania języka zostały zmienione w wersji 3. Odpowiedź JSON będzie zawierać `ConfidenceScore` zamiast `score` . V3 zwraca również tylko jeden język w  `detectedLanguage` atrybucie dla każdego dokumentu.

### <a name="steps-to-migrate"></a>Kroki migracji

#### <a name="rest-api"></a>Interfejs API REST

Jeśli aplikacja używa interfejsu API REST, Zaktualizuj swój punkt końcowy żądania do punktu końcowego v3 na potrzeby wykrywania języka. Na przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` . Należy również zaktualizować aplikację do użycia `ConfidenceScore` zamiast `score` w [odpowiedzi interfejsu API](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results). 

Przykłady odpowiedzi JSON można znaleźć w dokumentacji referencyjnej.
* [Wersja 2,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [Wersja 3,0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [Wersja 3,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

#### <a name="client-libraries"></a>Biblioteki klienta

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="key-phrase-extraction"></a>[Wyodrębnianie kluczowych fraz](#tab/key-phrase-extraction)

### <a name="feature-changes"></a>Zmiany funkcji 

Funkcja wyodrębniania frazy klucza nie została zmieniona w wersji 3 poza wersją punktu końcowego.

### <a name="steps-to-migrate"></a>Kroki migracji

#### <a name="rest-api"></a>Interfejs API REST

Jeśli aplikacja używa interfejsu API REST, Zaktualizuj swój punkt końcowy żądania do punktu końcowego V3 w celu wyodrębnienia frazy kluczowej. Na przykład: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

Przykłady odpowiedzi JSON można znaleźć w dokumentacji referencyjnej.
* [Wersja 2,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [Wersja 3,0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [Wersja 3,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/KeyPhrases)

#### <a name="client-libraries"></a>Biblioteki klienta

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---

## <a name="see-also"></a>Zobacz też

* [Co to jest interfejs API analizy tekstu](overview.md)
* [Obsługa języków](language-support.md)
* [Wersje modelu danych](concepts/model-versioning.md)
