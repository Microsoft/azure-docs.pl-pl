---
title: 'Samouczek: Language Understanding bot Node. js v4'
description: Korzystając ze środowiska Node. js, skompiluj bot z integracją języka (LUIS) w tym samouczku. Ten czatbot używa aplikacji Human Resources, aby szybko wdrożyć rozwiązanie bota. Bot jest tworzony za pomocą platformy Bot Framework w wersji 4 i bota aplikacji internetowej platformy Azure.
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 3ce12176957412a5599ced8b043f553969194efb
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76987848"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Samouczek: używanie bot aplikacji sieci Web w programie Node. js przy użyciu Language Understanding

Użyj środowiska Node. js, aby skompilować bot z integracją z funkcją interpretacji języka (LUIS). Bot jest tworzona przy użyciu [aplikacji sieci Web](https://docs.microsoft.com/azure/bot-service/) platformy Azure bot Resource i [bot Framework w wersji](https://github.com/Microsoft/botbuilder-dotnet) v4.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie bota aplikacji internetowej. Ten proces tworzy nową aplikację usługi LUIS.
> * Pobierz projekt bot utworzony przez usługę sieci Web bot
> * Uruchamianie bota i emulatora lokalnie na komputerze
> * Wyświetlanie wyników wypowiedzi w bocie

## <a name="prerequisites"></a>Wymagania wstępne

* [Emulator bota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Tworzenie zasobu bot aplikacji sieci Web

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz polecenie **Utwórz nowy zasób**.

1. W polu wyszukiwania wyszukaj i wybierz pozycję **Web App Bot** (Bot aplikacji internetowej). Wybierz przycisk **Utwórz**.

1. W polu **Bot Service** (Usługa bota) podaj wymagane informacje:

    |Ustawienie|Przeznaczenie|Zalecane ustawienia|
    |--|--|--|
    |Nazwa bota|Nazwa zasobu|`luis-nodejs-bot-` + `<your-name>`, na przykład `luis-nodejs-bot-johnsmith`|
    |Subskrypcja|Subskrypcja miejsca utworzenia bota.|Subskrypcja podstawowa.
    |Grupa zasobów|Logiczna grupa zasobów platformy Azure|Utwórz nową grupę do przechowywania wszystkich zasobów używanych z tym botem, nazwij grupę `luis-nodejs-bot-resource-group`.|
    |Lokalizacja|Region platformy Azure — nie musi być taki sam jak region tworzenia lub publikowania usługi LUIS.|`westus`|
    |Warstwa cenowa|Służy do określania limitów żądań usługi i rozliczeń.|`F0` to warstwa bezpłatna.
    |Nazwa aplikacji|Nazwa jest używana jako domena podrzędna, gdy bot jest wdrażany w chmurze (na przykład humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, na przykład `luis-nodejs-bot-johnsmith`|
    |Szablon bota|Ustawienia struktury bota — zobacz następną tabelę|
    |Lokalizacja aplikacji usługi LUIS|Musi być taka sama jak region zasobu usługi LUIS|`westus`|
    |Plan/Lokalizacja usługi App Service|Nie zmieniaj podanej wartości domyślnej.|
    |Application Insights|Nie zmieniaj podanej wartości domyślnej.|
    |Identyfikator i hasło aplikacji firmy Microsoft|Nie zmieniaj podanej wartości domyślnej.|

1. W **szablonie bot**wybierz poniższe opcje, a następnie wybierz przycisk **Wybierz** w obszarze te ustawienia:

    |Ustawienie|Przeznaczenie|Wybór|
    |--|--|--|
    |Wersja zestawu SDK|Wersja struktury bota|**Zestaw SDK w wersji 4**|
    |Język zestawu SDK|Język programowania bota|**Node.js**|
    |Bot|Typ bota|**Bot podstawowy**|

1. Wybierz przycisk **Utwórz**. To powoduje utworzenie i wdrożenie usługi bota na platformie Azure. W ramach tego procesu jest tworzona nowa aplikacja usługi LUIS o nazwie `luis-nodejs-bot-XXXX`. Ta nazwa jest oparta na nazwie aplikacji usługi/Azure bot.

    > [!div class="mx-imgBorder"]
    > [![Tworzenie bot aplikacji sieci Web](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Przed kontynuowaniem poczekaj na utworzenie usługi bot.

## <a name="the-bot-has-a-language-understanding-model"></a>Bot ma model Language Understanding

Proces tworzenia usługi bot tworzy również nową aplikację LUIS z intencjami i przykładem wyrażenia długości. Bot zapewnia mapowanie intencji do nowej aplikacji LUIS dla następujących intencji:

|Intencje usługi LUIS bota podstawowego|przykładowa wypowiedź|
|--|--|
|Lot z książki|`Travel to Paris`|
|Cancel|`bye`|
|Getpogoda|`what's the weather like?`|
|Brak|Cokolwiek spoza domeny aplikacji.|

## <a name="test-the-bot-in-web-chat"></a>Testowanie bot w rozmowie w sieci Web

1. Mimo że w Azure Portal dla nowego bot, wybierz pozycję **Testuj w rozmowie w sieci Web**.
1. W polu tekstowym **wpisz wiadomość** wpisz tekst `Book a flight from Seattle to Berlin tomorrow`. Bot reaguje na weryfikację, aby zaksięgować lot.

    ![Zrzut ekranu przedstawiający Azure Portal, wprowadź tekst "Hello".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Możesz użyć funkcji testu, aby szybko przetestować bot. Aby uzyskać pełniejsze testowanie, w tym debugowanie, Pobierz kod bot i użyj programu Visual Studio.

## <a name="download-the-web-app-bot-source-code"></a>Pobierz kod źródłowy bot aplikacji sieci Web
Aby tworzyć kod bota aplikacji internetowej, pobierz kod i użyj go na komputerze lokalnym.

1. W witrynie Azure Portal wybierz pozycję **Build** (Kompilacja) z sekcji **Bot management** (Zarządzanie botem).

1. Wybierz przycisk **Download Bot source code** (Pobierz kod źródłowy bota).

    [![Pobierz kod źródłowy bot aplikacji sieci Web dla podstawowego bot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Po wyświetleniu okna dialogowego z monitem o **uwzględnienie ustawień aplikacji w pobranym pliku zip**wybierz pozycję **tak**. Zapewnia to ustawienia LUIS.

1. Po spakowaniu kodu źródłowego w komunikacie zostanie podany hiperlink umożliwiający pobranie kodu. Wybierz hiperlink.

1. Zapisz plik zip na komputerze lokalnym i wyodrębnij pliki. Otwórz projekt za pomocą programu Visual Studio.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Przejrzyj kod, aby wysłać wypowiedź do LUIS i uzyskać odpowiedź

1. Aby wysłać użytkownika wypowiedź do punktu końcowego przewidywania LUIS, Otwórz **okno dialogowe > flightBookingRecognizer. js** . Jest to miejsce, gdzie wypowiedź użytkownika wprowadzana do bota jest wysyłania do usługi LUIS. Odpowiedź z LUIS jest zwracana z metody **executeLuisQuery** .

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. **Okna dialogowe-> mainDialog** przechwytuje wypowiedź i wysyła je do executeLuisQuery w metodzie actStep.


    ````javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                this.recognizer = new LuisRecognizer(config, {}, true);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Testowanie bot przy użyciu emulatora bot

Zażądaj bot pytania dotyczącego zamiaru lotów w ramach książki.

1. Rozpocznij emulator bot i wybierz pozycję **Otwórz bot**.
1. W wyskakującym okienku Otwórz okno dialogowe **bot** wprowadź adres URL bot, taki jak `http://localhost:3978/api/messages`. `/api/messages` Trasa jest adresem sieci Web dla bot.
1. Wprowadź **Identyfikator aplikacji firmy Microsoft** i **hasło aplikacji firmy**Microsoft, które znajdują się w pliku **ENV** w katalogu głównym pobranego kodu bot.

1. W emulatorze bot wprowadź `Book a flight from Seattle to Berlin tomorrow` i uzyskaj taką samą odpowiedź dla podstawowego bot, jak w przypadku **testu w rozmowie w sieci Web**.

    [![Podstawowa odpowiedź bot w emulatorze](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Wybierz pozycję **tak**. Bot reaguje z podsumowaniem jego akcji.
1. Z dziennika emulatora bot wybierz wiersz, który zawiera `Luis Trace`. Spowoduje to wyświetlenie odpowiedzi JSON z LUIS dla zamiar i jednostek wypowiedź.

    [![Podstawowa odpowiedź bot w emulatorze](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Następne kroki

Zobacz więcej [przykładów](https://github.com/microsoft/botframework-solutions) z botami konwersacyjnymi.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji Language Understanding z niestandardową domeną podmiotu](luis-quickstart-intents-only.md)