---
title: Extact obiektów dopasowania tekstu — LUIS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak dodać jednostkę listy ułatwiającą LUIS odmian wyrazu lub frazy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73499478"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Użyj jednostki listy, aby zwiększyć wykrywanie jednostek 
W tym artykule przedstawiono sposób użycia [jednostki listy](luis-concept-entity-types.md) do zwiększenia wykrywania jednostek. Jednostki listy nie muszą mieć etykiet, ponieważ są dokładnie zgodne z postanowieniami.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie jednostki listy 
> * Dodawanie znormalizowanych wartości i synonimów
> * Weryfikacja ulepszonej identyfikacji jednostek

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Najnowsza wersja środowiska [Node. js](https://nodejs.org)
> * [Aplikacja HOMEAUTOMATION Luis](luis-get-started-create-app.md). Jeśli nie masz utworzonej aplikacji Automation Home, Utwórz nową aplikację i Dodaj prezbudowaną domenę **HomeAutomation**. Przeszkol i opublikuj aplikację. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (w przypadku wykonywania zapytań wiele razy), identyfikatora aplikacji, identyfikatora wersji i [regionu](luis-reference-regions.md) dla aplikacji Luis.

> [!Tip]
> Jeśli jeszcze nie masz subskrypcji, możesz zarejestrować się w celu uzyskania [bezpłatnego konta](https://azure.microsoft.com/free/).

Cały kod w tym artykule jest dostępny w [repozytorium Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity)w witrynie GitHub. 

## <a name="use-homeautomation-app"></a>Korzystanie z aplikacji HomeAutomation
Aplikacja HomeAutomation zapewnia kontrolę nad urządzeniami, takimi jak światła, systemy rozrywki i mechanizmy kontroli środowiska, takie jak ogrzewanie i chłodzenie. Te systemy mają kilka różnych nazw, które mogą zawierać nazwy producentów, pseudonimy, akronimy i żargonu. 

Jeden system, który ma wiele nazw w różnych kulturach i demograficznych jest termostatem. Termostat może kontrolować systemy chłodzenia i ogrzewania dla domu lub budynku.

W idealnym przypadku należy rozwiązać następujący wyrażenia długości do wstępnie skompilowanej jednostki **HomeAutomation. Device**:

|#|wypowiedź|zidentyfikowana jednostka|wynik|
|--|--|--|--|
|1|Włącz AC|HomeAutomation. Device-"AC"|0,8748562|
|2|Włącz ciepło|HomeAutomation. Device — "ciepło"|0,784990132|
|3|Uczyń go zimnem|||

Dwie pierwsze mapy wyrażenia długości na różnych urządzeniach. Trzeci wypowiedź "uczyń go zimnem", nie jest mapowany na urządzenie, ale zamiast tego żąda wyniku. LUIS nie wie, że termin "chłodner" oznacza, że wymagane urządzenie jest termostatem. W idealnym przypadku LUIS powinna rozpoznać wszystkie te wyrażenia długości na tym samym urządzeniu. 

## <a name="use-a-list-entity"></a>Korzystanie z jednostki listy
Jednostka HomeAutomation. Device jest doskonałym rozwiązaniem dla niewielkiej liczby urządzeń lub z kilkoma odmianami nazw. W przypadku tworzenia lub szkoły biurowej nazwy urządzeń rosną poza użyteczność jednostki HomeAutomation. urządzenia. 

**Jednostka listy** jest dobrym wyborem w tym scenariuszu, ponieważ zestaw warunków dla urządzenia w budynku lub kampus jest znanym zestawem, nawet jeśli jest to duży zestaw. Za pomocą jednostki listy LUIS może otrzymać dowolną wartość z zestawu dla termostatu i rozpoznać ją tylko do jednego urządzenia "termostat". 

W tym artykule opisano tworzenie listy jednostek z termostatem. Alternatywne nazwy dla termostatu w tym artykule są następujące: 

|alternatywne nazwy dla termostatu|
|--|
| Napięcie |
| a/c|
| a-c|
|wydajności|
|klawisz|
|hotter|
|chłodn|
|zimner|

Jeśli LUIS musi często określić nową alternatywę, wówczas [Lista fraz](luis-concept-feature.md#how-to-use-phrase-lists) jest lepszą odpowiedzią.

## <a name="create-a-list-entity"></a>Tworzenie jednostki listy
Utwórz plik Node. js i skopiuj do niego następujący kod. Zmień wartości authoringKey, appId, versionId i region.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Użyj poniższego polecenia, aby zainstalować zależności NPM i uruchomić kod w celu utworzenia jednostki listy:

```console
npm install && node add-entity-list.js
```

Dane wyjściowe przebiegu są IDENTYFIKATORem jednostki listy:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Uczenie modelu
Pouczenie LUIS, aby nowa lista miała wpływ na wyniki zapytania. Szkolenie to dwuczęściowy proces szkolenia, a następnie sprawdzanie stanu w przypadku jego wykonania. Nauczenie aplikacji z wieloma modelami może potrwać kilka minut. Poniższy kod pociąga za siebie, że aplikacja czeka na pomyślne szkolenie. Kod używa strategii oczekiwania i ponawiania próby, aby uniknąć błędu 429 "zbyt wiele żądań". 

Utwórz plik Node. js i skopiuj do niego następujący kod. Zmień wartości authoringKey, appId, versionId i region.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Użyj następującego polecenia, aby uruchomić kod w celu uczenia aplikacji:

```console
node train.js
```

Wyjście przebiegu jest stanem każdej iteracji szkolenia modeli LUIS. Następujące wykonanie wymaga tylko jednego sprawdzenia szkolenia:

```console
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>Publikowanie modelu
Opublikuj, aby jednostka listy była dostępna w punkcie końcowym.

Utwórz plik Node. js i skopiuj do niego następujący kod. Zmień wartości endpointKey, appId i region. Możesz użyć authoringKey, jeśli nie planujesz wywołać tego pliku poza limitem przydziału.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Użyj następującego polecenia, aby uruchomić kod w celu wykonania zapytania dotyczącego aplikacji:

```console
node publish.js
```

Poniższe dane wyjściowe zawierają adres URL punktu końcowego dla dowolnych zapytań. Rzeczywiste wyniki JSON będą zawierać rzeczywisty identyfikator appID. 

```json
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>Wysyłanie zapytań do aplikacji 
Wykonaj zapytanie dotyczące aplikacji z punktu końcowego, aby upewnić się, że jednostka listy pomaga LUIS określić typ urządzenia.

Utwórz plik Node. js i skopiuj do niego następujący kod. Zmień wartości endpointKey, appId i region. Możesz użyć authoringKey, jeśli nie planujesz wywołać tego pliku poza limitem przydziału.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Użyj następującego polecenia, aby uruchomić kod i wykonać zapytanie dotyczące aplikacji:

```console
node train.js
```

Dane wyjściowe są wynikami zapytania. Ponieważ kod dodał para **pełna** nazwa/wartość do ciągu zapytania, dane wyjściowe zawierają wszystkie intencje i ich wyniki:

```json
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

Określone urządzenie **termostatu** jest identyfikowane za pomocą zapytania zorientowanego na wynik "wyłączania ciepła". Ponieważ oryginalna jednostka HomeAutomation. Device jest nadal w aplikacji, można także zobaczyć jej wyniki. 

Wypróbuj dwa inne wyrażenia długościy, aby zobaczyć, że są one również zwracane jako termostat. 

|#|wypowiedź|jednostka|type|value|
|--|--|--|--|--|
|1|Włącz AC| Napięcie | DevicesList | Sterownika|
|2|Włącz ciepło|odczytu| DevicesList |Sterownika|
|3|Uczyń go zimnem|zimner|DevicesList|Sterownika|

## <a name="next-steps"></a>Następne kroki

Można utworzyć kolejną jednostkę listy, aby rozwinąć lokalizacje urządzeń do pokojów, podłóg lub budynków. 
