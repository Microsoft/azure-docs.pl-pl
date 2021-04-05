---
title: Zmiana danych — LUIS
description: Dowiedz się, jak można zmienić dane przed przewidywaniami w Language Understanding (LUIS)
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 621a41f743b751a8c24bf6f6ad8497fb5c79775c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026014"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Zmień wypowiedź danych przed lub podczas przewidywania
LUIS zapewnia sposoby manipulowania wypowiedź przed lub podczas przewidywania. Obejmują one [naprawianie błędów](luis-tutorial-bing-spellcheck.md)i rozwiązywanie problemów ze strefą czasową dla prekompilowanego [datetimeV2](luis-reference-prebuilt-datetimev2.md).

## <a name="correct-spelling-errors-in-utterance"></a>Poprawianie błędów pisowni w wypowiedź


### <a name="v3-runtime"></a>Środowisko uruchomieniowe v3

Przed wysłaniem wypowiedź do LUIS należy wstępnie przetworzyć tekst w celu wprowadzania poprawek pisowni. Użyj przykładu wyrażenia długości z poprawną pisownią, aby upewnić się, że pobrano poprawne przewidywania.

Użyj [Sprawdzanie pisowni Bing](../bing-spell-check/overview.md) , aby poprawić tekst przed wysłaniem go do Luis.

### <a name="prior-to-v3-runtime"></a>Przed wprowadzeniem do wersji 3 środowiska uruchomieniowego

LUIS [Sprawdzanie pisowni Bing używa interfejsu API wersji 7](../Bing-Spell-Check/overview.md) do poprawiania błędów pisowni w wypowiedź. LUIS wymaga klucza skojarzonego z tą usługą. Utwórz klucz, a następnie Dodaj klucz jako parametr QueryString w [punkcie końcowym](https://go.microsoft.com/fwlink/?linkid=2092356).

Punkt końcowy wymaga dwóch parametrów do działania poprawek pisowni:

|Param|Wartość|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|Klucz punktu końcowego [wersji 7 interfejsu API sprawdzanie pisowni Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Gdy [Sprawdzanie pisowni Bing API wersji 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) wykrywa błąd, oryginalny wypowiedź i poprawione wypowiedź są zwracane wraz z przewidywaniami z punktu końcowego.

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * *

### <a name="list-of-allowed-words"></a>Lista dozwolonych wyrazów
Interfejs API sprawdzania pisowni Bing używany w programie LUIS nie obsługuje listy wyrazów ignorowanych podczas sprawdzania pisowni. Jeśli musisz zezwolić na listę wyrazów lub akronimów, przetwórz wypowiedź w aplikacji klienckiej przed wysłaniem wypowiedź do LUIS w celu przewidywania założeń.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Zmień strefę czasową wstępnie skompilowanej jednostki datetimeV2
Gdy aplikacja LUIS używa wstępnie skompilowanej jednostki [datetimeV2](luis-reference-prebuilt-datetimev2.md) , w odpowiedzi predykcyjnej można zwrócić wartość typu DateTime. Strefa czasowa żądania służy do określenia poprawnej wartości DateTime do zwrócenia. Jeśli żądanie pochodzi z bot lub innej scentralizowanej aplikacji przed uzyskaniem LUIS, należy poprawić użycie LUIS strefy czasowej.

### <a name="v3-prediction-api-to-alter-timezone"></a>Interfejs API przewidywania v3 dotyczący zmiany strefy czasowej

W wersji 3 `datetimeReference` Określa przesunięcie strefy czasowej. Dowiedz się więcej na temat [prognoz v3](luis-migration-api-v3.md#v3-post-body).

### <a name="v2-prediction-api-to-alter-timezone"></a>Interfejs API przewidywania wersji 2 w celu zmiany strefy czasowej
Strefa czasowa jest korygowana przez dodanie strefy czasowej użytkownika do punktu końcowego przy użyciu `timezoneOffset` parametru w oparciu o wersję interfejsu API. Wartość parametru powinna być liczbą dodatnią lub ujemną w minutach, aby zmienić czas.

#### <a name="v2-prediction-daylight-savings-example"></a>Przykład 2. przewidywanie oszczędności czasu
Jeśli potrzebujesz dostosowanej wstępnie wbudowanej datetimeV2 do dostosowywania czasu letniego, należy użyć parametru QueryString z wartością +/-w minutach dla kwerendy [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) .

Dodaj 60 minut:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Usuń 60 minut:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v2-prediction-c-code-determines-correct-value-of-parameter"></a>Kod w języku C# przewidywania w wersji 2 określa poprawną wartość parametru

Poniższy kod w języku C# używa metody [FindSystemTimeZoneById](/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) klasy [TimeZoneInfo](/dotnet/api/system.timezoneinfo) w celu określenia poprawnej wartości przesunięcia na podstawie czasu systemowego:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset/datetimeReference
int offset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Popraw błędy pisowni w tym samouczku](luis-tutorial-bing-spellcheck.md)