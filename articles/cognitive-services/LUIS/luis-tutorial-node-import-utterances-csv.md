---
title: Importowanie wyrażenia długości przy użyciu polecenia Node.js-LUIS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć aplikację LUIS programowo z istniejących danych w formacie CSV przy użyciu interfejsu API tworzenia LUIS.
services: cognitive-services
manager: nitinme
ms.custom: seodec18, devx-track-js
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/05/2019
ms.openlocfilehash: 58eb92f4d0bc3de4671ca2ece14a178a876e4a6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541049"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Programistyczne Kompilowanie aplikacji LUIS przy użyciu Node.js

Usługa LUIS udostępnia programistyczny interfejs API, który robi wszystko, co witryna sieci Web [Luis](luis-reference-regions.md) . Pozwala to zaoszczędzić czas, gdy masz wstępnie istniejące dane i szybciej utworzyć aplikację LUIS programowo niż przez wprowadzanie informacji.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Zaloguj się do witryny sieci Web [Luis](luis-reference-regions.md) i Znajdź swój [klucz tworzenia](luis-how-to-azure-subscription.md#authoring-key) w ustawieniach konta. Ten klucz służy do wywoływania interfejsów API tworzenia.
* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).
* Ten artykuł rozpoczyna się od pliku CSV dla hipotetycznych plików dziennika żądań użytkowników. Pobierz je [tutaj](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Zainstaluj najnowszą Node.js z NPM. Pobierz go [stąd](https://nodejs.org/en/download/).
* **[Zalecane]** Visual Studio Code do IntelliSense i debugowania, pobierz ją z tego [miejsca](https://code.visualstudio.com/) bezpłatnie.

Cały kod w tym artykule jest dostępny w [repozytorium Azure-samples Language Understanding GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv).

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mapowanie istniejących danych na intencje i jednostki
Nawet jeśli masz system, który nie został utworzony z LUIS, jeśli zawiera on dane tekstowe, które są mapowane na różne rzeczy, które użytkownicy chcą wykonać, można utworzyć mapowanie z istniejących kategorii danych wejściowych użytkownika na intencje w LUIS. Jeśli możliwe jest zidentyfikowanie ważnych wyrazów lub fraz w tym, co użytkownicy poinformowały, te słowa mogą być mapowane na jednostki.

Otwórz [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) plik. Zawiera dziennik zapytań użytkowników do hipotetycznej usługi automatyzacji domowej, w tym sposobu, w jaki zostały skategoryzowane, co użytkownik poinformował, oraz niektórych kolumn z przydatnymi informacjami.

![Plik CSV wstępnie istniejących danych](./media/luis-tutorial-node-import-utterances-csv/csv.png)

Zobaczysz, że kolumna **RequestType** może być intencjami, a kolumna **żądania** zawiera przykład wypowiedź. Pozostałe pola mogą być jednostkami, jeśli występują w wypowiedź. Ponieważ istnieją intencje, jednostki i przykład wyrażenia długości, masz wymagania dotyczące prostej, przykładowej aplikacji.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Procedura generowania aplikacji LUIS z danych innych niż LUIS
Aby wygenerować nową aplikację LUIS z pliku CSV:

* Przeanalizuj dane z pliku CSV:
    * Konwertuj na format, który można przekazać do LUIS przy użyciu interfejsu API tworzenia.
    * Z danych przeanalizowanych Zbierz informacje o intencjach i jednostkach.
* Utwórz wywołania interfejsu API tworzenia dla:
    * Utwórz aplikację.
    * Dodawanie intencji i jednostek, które zostały zebrane z przeanalizowanych danych.
    * Po utworzeniu aplikacji LUIS można dodać przykładową wyrażenia długości z przeanalizowanych danych.

Ten przepływ programu można zobaczyć w ostatniej części `index.js` pliku. Skopiuj lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) ten kod i Zapisz go w `index.js` .

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Analizowanie woluminu CSV

Wpisy kolumn zawierające wyrażenia długości w woluminie CSV muszą być analizowane w formacie JSON, który LUIS może zrozumieć. Ten format JSON musi zawierać `intentName` pole, które identyfikuje zamiar wypowiedź. Musi także zawierać `entityLabels` pole, które może być puste, jeśli nie ma żadnych jednostek w wypowiedź.

Na przykład wpis "Włącz lampki" odwzorowuje w ten kod JSON:

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

W tym przykładzie `intentName` pochodzi od żądania użytkownika w nagłówku kolumny **Request** w pliku CSV i `entityName` pochodzi z innych kolumn z kluczowymi informacjami. Na przykład, jeśli istnieje wpis do **operacji** lub **urządzenia**, a ten ciąg występuje również w rzeczywistym żądaniu, można go oznaczyć jako jednostkę. Poniższy kod ilustruje ten proces analizy. Można je skopiować lub [pobrać](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) i zapisać do programu `_parse.js` .

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Tworzenie aplikacji LUIS
Gdy dane zostaną przeanalizowane w formacie JSON, Dodaj je do aplikacji LUIS. Poniższy kod tworzy aplikację LUIS. Skopiuj go lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) , a następnie zapisz go w programie `_create.js` .

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Dodawanie intencji
Gdy masz aplikację, musisz być jej intencjami. Poniższy kod tworzy aplikację LUIS. Skopiuj go lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) , a następnie zapisz go w programie `_intents.js` .

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Dodawanie jednostek
Poniższy kod dodaje jednostki do aplikacji LUIS. Skopiuj go lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) , a następnie zapisz go w programie `_entities.js` .

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]



## <a name="add-utterances"></a>Dodawanie wypowiedzi
Po zdefiniowaniu jednostek i intencji w aplikacji LUIS można dodać wyrażenia długości. Poniższy kod używa interfejsu API [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) , który umożliwia dodanie do 100 wyrażenia długości jednocześnie.  Skopiuj go lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) , a następnie zapisz go w programie `_upload.js` .

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Uruchamianie kodu


### <a name="install-nodejs-dependencies"></a>Zainstaluj zależności Node.js
Zainstaluj zależności Node.js z NPM w terminalu/wierszu polecenia.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Zmień ustawienia konfiguracji
Aby można było korzystać z tej aplikacji, należy zmienić wartości w pliku index.js na własny klucz punktu końcowego i podać nazwę, która ma mieć aplikacja. Możesz również ustawić kulturę aplikacji lub zmienić numer wersji.

Otwórz plik index.js i Zmień te wartości w górnej części pliku.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us";
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Uruchamianie skryptu
Uruchom skrypt z terminalu/wiersza polecenia z Node.js.

```console
> node index.js
```

lub

```console
> npm start
```

### <a name="application-progress"></a>Postęp aplikacji
Gdy aplikacja jest uruchomiona, wiersz polecenia wyświetla postęp. Dane wyjściowe wiersza polecenia zawierają format odpowiedzi z LUIS.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>Otwieranie aplikacji LUIS
Po zakończeniu działania skryptu możesz zalogować się do [Luis](luis-reference-regions.md) i zobaczyć aplikację Luis utworzoną w obszarze **Moje aplikacje**. Powinien być widoczny wyrażenia długości dodany w ramach intencji **wlaczanie** **, wykluczanie i** **Brak** .

![Zamiar wlaczania](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Testowanie i uczenie aplikacji w witrynie LUIS](luis-interactive-test.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

Ta przykładowa aplikacja używa następujących interfejsów API LUIS:
- [Tworzenie aplikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [Dodawanie intencji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [Dodawanie jednostek](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e)
- [Dodaj wyrażenia długości](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
