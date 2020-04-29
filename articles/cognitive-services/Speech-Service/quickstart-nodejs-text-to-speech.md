---
title: 'Szybki Start: konwertowanie tekstu na mowę, Node. js-Speech Service'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak konwertować zamianę tekstu na mowę przy użyciu środowiska Node. js i interfejsu API REST zamiany tekstu na mowę. Przykładowy tekst zawarty w tym przewodniku jest uporządkowany jako SSML (Speech Syntezing Language). Pozwala to na wybranie głosu i języka odpowiedzi na mowę.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 8cf9641a1b7a5d1aada13522d612458d5032f883
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81258697"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Szybki Start: konwertowanie tekstu na mowę przy użyciu środowiska Node. js

W tym przewodniku szybki start dowiesz się, jak konwertować zamianę tekstu na mowę przy użyciu środowiska Node. js i interfejsu API REST zamiany tekstu na mowę. Treść żądania w tym przewodniku jest uporządkowana jako [SSML (Speech syntezing Language)](speech-synthesis-markup.md), która umożliwia wybranie głosu i języka odpowiedzi.

Ten przewodnik Szybki Start wymaga [konta Cognitive Services platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](get-started.md), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* <a href="https://nodejs.org/en/" target="_blank">Node 8.12. x lub nowszy<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>lub ulubiony Edytor tekstu
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Tworzenie projektu i wymaganie zależności

Utwórz nowy projekt node. js przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `tts.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Jeśli nie korzystano z tych modułów, konieczne będzie ich zainstalowanie przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `npm install request request-promise xmlbuilder readline-sync`.

## <a name="get-an-access-token"></a>Pobranie tokenu dostępu

Interfejs API REST zamiany tekstu na mowę wymaga tokenu dostępu do uwierzytelniania. Aby uzyskać token dostępu, wymagany jest program Exchange. Ta funkcja wymienia klucz subskrypcji usługi mowy dla tokenu dostępu przy użyciu `issueToken` punktu końcowego.

W tym przykładzie przyjęto założenie, że subskrypcja usługi mowy znajduje się w regionie zachodnie stany USA. Jeśli używasz innego regionu, zaktualizuj wartość parametru `uri`. Aby uzyskać pełną listę, zobacz [regiony](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Skopiuj ten kod do projektu:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz [uwierzytelnianie przy użyciu tokenu dostępu](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

W następnej sekcji utworzymy funkcję, która wywoła interfejs API zamiany tekstu na mowę i zapisze odpowiedź na mowę.

## <a name="make-a-request-and-save-the-response"></a>Utwórz żądanie i Zapisz odpowiedź

Tutaj chcesz skompilować żądanie do interfejsu API zamiany tekstu na mowę i zapisać odpowiedź mowy. W tym przykładzie przyjęto założenie, że używasz regionu zachodnie stany USA. Jeśli zasób jest zarejestrowany w innym regionie, upewnij się, że Zaktualizowano `uri`. Aby uzyskać więcej informacji, zobacz [regiony usługi mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Następnie musisz dodać wymagane nagłówki dla żądania. Upewnij się, że Zaktualizowano `User-Agent` o nazwę zasobu (znajdującą się w Azure Portal), i ustaw `X-Microsoft-OutputFormat` na preferowane dane wyjściowe dźwięku. Aby zapoznać się z pełną listą formatów danych wyjściowych, zobacz [wyjście audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Następnie Skonstruuj treść żądania przy użyciu języka SSML (Speech Syntezing Language). Ten przykład definiuje strukturę i używa utworzonych wcześniej `text` danych wejściowych.

>[!NOTE]
> Ten przykład używa czcionki `JessaRUS` głosowej. Aby zapoznać się z pełną listą podanych głosów/języków firmy Microsoft, zobacz temat [Obsługa języków](language-support.md).
> Jeśli interesuje Cię tworzenie unikatowego, rozpoznawalnego głosu dla marki, zobacz [Tworzenie niestandardowych czcionek głosowych](how-to-customize-voice-font.md).

Na koniec zostanie wysłane żądanie do usługi. Jeśli żądanie zakończy się pomyślnie i zostanie zwrócony kod stanu 200, odpowiedź mowy jest zapisywana jako `TTSOutput.wav`.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To już prawie koniec. Ostatnim krokiem jest utworzenie funkcji asynchronicznej. Ta funkcja odczyta swój klucz subskrypcji ze zmiennej środowiskowej, wyświetli monit o tekst, Pobierz token, poczekaj na zakończenie żądania, a następnie przekonwertuj tekst na mowę i Zapisz dźwięk jako. wav.

Jeśli nie znasz zmiennych środowiskowych ani wolisz testować przy użyciu klucza subskrypcji stałe jako ciąg, Zamień `process.env.SPEECH_SERVICE_KEY` na klucz subskrypcji jako ciąg.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko, co jest gotowe do uruchamiania przykładowej aplikacji zamiany tekstu na mowę. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
node tts.js
```

Po wyświetleniu monitu wpisz w dowolny sposób, który ma zostać przekonwertowany z tekstu na mowę. Jeśli to się powiedzie, plik mowy znajduje się w folderze projektu. Odtwórz go przy użyciu ulubionego odtwarzacza multimedialnego.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla platformy Node.js w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Zobacz także

* [Dokumentacja interfejsu API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Tworzenie niestandardowych czcionek głosowych](how-to-customize-voice-font.md)
* [Rejestruj przykłady głosu, aby utworzyć niestandardowy głos](record-custom-voice-samples.md)
