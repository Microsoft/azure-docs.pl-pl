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
ms.date: 06/25/2020
ms.author: aahi
ms.openlocfilehash: 12c09ad8e1db3914263fcc864c9c2d09069d63a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85412587"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Przeprowadź migrację do wersji 3. x interfejs API analizy tekstu

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

Jeśli używasz wersji 2,1 interfejs API analizy tekstu, ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 3. x. Wersja 3,0 jest ogólnie dostępna i wprowadza nowe funkcje, takie jak rozszerzone [rozpoznawanie jednostek nazwanych (ner)](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) i [przechowywanie wersji modelu](concepts/model-versioning.md). Dostępna jest również wersja zapoznawcza v 3.1 (v 3.1-Preview. x), która dodaje funkcje, takie jak [Wyszukiwanie opinii](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features). Modele używane w wersji 2 nie będą otrzymywać przyszłych aktualizacji. 

#### <a name="sentiment-analysis"></a>[Analiza tonacji](#tab/sentiment-analysis)

## <a name="feature-changes"></a>Zmiany funkcji 

Analiza tonacji w wersji 2,1 zwraca wyniki tonacji z zakresu od 0 do 1 dla każdego dokumentu wysłanego do interfejsu API, z wynikami zbliżonymi do 1 wskazującą więcej pozytywnych tonacji. Zamiast tego zwraca etykiety tonacji (takie jak "pozytyw" lub "ujemna") zarówno dla zdania i dokumentu jako całości, jak i skojarzonych z nimi ocen zaufania. 

## <a name="steps-to-migrate"></a>Kroki migracji

### <a name="rest-api"></a>Interfejs API REST

Jeśli aplikacja używa interfejsu API REST, Zaktualizuj swój punkt końcowy żądania do punktu końcowego v3 dla analizy tonacji. Na przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment` . Należy również zaktualizować aplikację tak, aby korzystała z etykiet tonacji zwracanych w [odpowiedzi JSON](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results). 

### <a name="client-libraries"></a>Biblioteki klienta

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="ner-and-entity-linking"></a>[NER i łączenie jednostek](#tab/named-entity-recognition)

## <a name="feature-changes"></a>Zmiany funkcji

> [!NOTE] 
> Obecnie [Kategorie jednostek v3](named-entity-types.md) są zwracane tylko w języku angielskim i tekście hiszpańskim. Interfejs API zwraca wersję 2,1 wyników dla żądań w innych językach, pod warunkiem, że są one obsługiwane w wersji 2,1.

W wersji 2,1 interfejs API analizy tekstu używa jednego punktu końcowego dla nazwanego rozpoznawania jednostek (NER) i konsolidacji jednostek. Wersja 3 zapewnia rozszerzone wykrywanie nazwanych jednostek i używa oddzielnych punktów końcowych dla żądań NER i konsolidacji jednostek. Począwszy od wersji v 3.1 — wersja zapoznawcza. 1, NER może dodatkowo wykrywać `pii` informacje osobiste i kondycję `phi` . 

## <a name="steps-to-migrate"></a>Kroki migracji

### <a name="rest-api"></a>Interfejs API REST

Jeśli aplikacja używa interfejsu API REST, Zaktualizuj swój punkt końcowy żądania do punktów końcowych v3 dla konsolidacji NER i/lub obiektu.

Łączenie jednostek
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

Należy również zaktualizować aplikację tak, aby korzystała z [kategorii jednostek](named-entity-types.md) zwracanych w [odpowiedzi JSON](how-tos/text-analytics-how-to-entity-linking.md#view-results).

### <a name="client-libraries"></a>Biblioteki klienta

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="language-detection"></a>[Wykrywanie języka](#tab/language-detection)

## <a name="feature-changes"></a>Zmiany funkcji 

Funkcja wykrywania języka nie została zmieniona w wersji 3 poza wersją punktu końcowego, ale odpowiedź JSON będzie zawierać `ConfidenceScore` zamiast `score` . V3 zwraca również tylko jeden język w danych wyjściowych. 

## <a name="steps-to-migrate"></a>Kroki migracji

### <a name="rest-api"></a>Interfejs API REST

Jeśli aplikacja używa interfejsu API REST, Zaktualizuj swój punkt końcowy żądania do punktu końcowego v3 na potrzeby wykrywania języka. Na przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` . Należy również zaktualizować aplikację do użycia `ConfidenceScore` zamiast `score` w [odpowiedzi JSON](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results). 

### <a name="client-libraries"></a>Biblioteki klienta

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="key-phrase-extraction"></a>[Wyodrębnianie kluczowych fraz](#tab/key-phrase-extraction)

## <a name="feature-changes"></a>Zmiany funkcji 

Funkcja wyodrębniania frazy klucza nie została zmieniona w wersji 3 poza wersją punktu końcowego.

## <a name="steps-to-migrate"></a>Kroki migracji

### <a name="rest-api"></a>Interfejs API REST

Jeśli aplikacja używa interfejsu API REST, Zaktualizuj swój punkt końcowy żądania do punktu końcowego V3 w celu wyodrębnienia frazy kluczowej. Na przykład: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

### <a name="client-libraries"></a>Biblioteki klienta

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---


## <a name="see-also"></a>Zobacz też

* [Dokumentacja interfejs API analizy tekstu v2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/)
* [Co to jest interfejs API analizy tekstu](overview.md)
* [Obsługa języków](language-support.md)
* [Wersje modelu danych](concepts/model-versioning.md)