---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: 502729e4c90a6d6ffa424ea7f379e2b426b3e3e0
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444690"
---
## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Przed wykonaniem jakichkolwiek czynności konieczne będzie zainstalowanie <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">zestawu Speech SDK dla języka JavaScript </a>. W zależności od platformy należy wykonać następujące instrukcje:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Przeglądarka sieci Web </a>

## <a name="create-voice-signatures"></a>Tworzenie podpisów głosowych

Pierwszym krokiem jest utworzenie podpisów głosowych dla uczestników konwersacji, dzięki czemu mogą one być zidentyfikowane jako unikatowe głośniki. Wejściowy `.wav` plik audio służący do tworzenia podpisów głosowych powinien mieć 16-bitową, 16 kHz szybkość próbkowania i format pojedynczego kanału (mono). Zalecana długość dla każdego przykładu audio to 30 sekund i dwie minuty. `.wav`Plik powinien być przykładem głosu **jednej osoby** , aby można było utworzyć unikatowy profil głosowy.

Poniższy przykład pokazuje, jak utworzyć podpis głosowy przy [użyciu interfejsu API REST](https://aka.ms/cts/signaturegenservice) w języku JavaScript. Zwróć uwagę na to, że musisz zastąpić prawdziwe informacje dla `subscriptionKey` , `region` i ścieżkę do pliku przykładowego `.wav` .

```javascript
const fs = require('fs');
const axios = require('axios');
const formData = require('form-data');
 
const subscriptionKey = 'your-subscription-key';
const region = 'your-region';
 
async function createProfile() {
    let form = new formData();
    form.append('file', fs.createReadStream('path-to-voice-sample.wav'));
    let headers = form.getHeaders();
    headers['Ocp-Apim-Subscription-Key'] = subscriptionKey;
 
    let url = `https://signature.${region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData`;
    let response = await axios.post(url, form, { headers: headers });
    
    // get signature from response, serialize to json string
    return JSON.stringify(response.data.Signature);
}
 
async function main() {
    // use this voiceSignature string with conversation transcription calls below
    let voiceSignatureString = await createProfile();
    console.log(voiceSignatureString);
}
main();
```

Uruchomienie tego skryptu zwraca ciąg podpisu głosowego w zmiennej `voiceSignatureString` . Uruchom funkcję dwukrotnie, aby użyć dwóch ciągów do użycia jako dane wejściowe do zmiennych `voiceSignatureStringUser1` i `voiceSignatureStringUser2` poniżej.

> [!NOTE]
> Sygnatury głosowe mogą być tworzone **tylko** przy użyciu interfejsu API REST.

## <a name="transcribe-conversations"></a>Transkrypcja konwersacje

Poniższy przykładowy kod demonstruje, jak transkrypcja konwersacje w czasie rzeczywistym dla dwóch głośników. Przyjęto założenie, że dla każdego głośnika zostały już utworzone ciągi podpisu głosowego, jak pokazano powyżej. Podstaw rzeczywiste informacje dla `subscriptionKey` , `region` i ścieżkę audio, `filepath` którą chcesz transkrypcja.

Ten przykładowy kod wykonuje następujące czynności:

* Tworzy strumień wypychania do użycia na potrzeby transkrypcji i zapisuje w `.wav` nim przykładowy plik.
* Tworzy element `Conversation` using `createConversationAsync()` .
* Tworzy `ConversationTranscriber` Konstruktor przy użyciu.
* Dodaje uczestników do konwersacji. Ciągi `voiceSignatureStringUser1` i `voiceSignatureStringUser2` powinny być podane jako dane wyjściowe z powyższych kroków.
* Rejestruje do zdarzeń i rozpoczyna transkrypcję.

```javascript
(function() {
    "use strict";
    var sdk = require("microsoft-cognitiveservices-speech-sdk");
    var fs = require("fs");
    
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav"; // 8-channel audio
    
    // create the push stream and write file to it
    var pushStream = sdk.AudioInputStream.createPushStream();
    fs.createReadStream(filepath).on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
    
    var speechTranslationConfig = sdk.SpeechTranslationConfig.fromSubscription(subscriptionKey, region);
    var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    speechTranslationConfig.speechRecognitionLanguage = "en-US";
    
    // create conversation and transcriber
    var conversation = sdk.Conversation.createConversationAsync(speechTranslationConfig, "myConversation");
    var transcriber = new sdk.ConversationTranscriber(audioConfig);
    
    // attach the transcriber to the conversation
    transcriber.joinConversationAsync(conversation,
    function () {
        // add first participant using voiceSignature created in enrollment step
        var user1 = sdk.Participant.From("user1@example.com", "en-us", voiceSignatureStringUser1);
        conversation.addParticipantAsync(user1,
        function () {
            // add second participant using voiceSignature created in enrollment step
            var user2 = sdk.Participant.From("user2@example.com", "en-us", voiceSignatureStringUser2);
            conversation.addParticipantAsync(user2,
            function () {
                transcriber.sessionStarted = function(s, e) {
                console.log("(sessionStarted)");
                };
                transcriber.sessionStopped = function(s, e) {
                console.log("(sessionStopped)");
                };
                transcriber.canceled = function(s, e) {
                console.log("(canceled)");
                };
                transcriber.transcribed = function(s, e) {
                console.log("(transcribed) text: " + e.result.text);
                console.log("(transcribed) speakerId: " + e.result.speakerId);
                };
    
                // begin conversation transcription
                transcriber.startTranscribingAsync(
                function () { },
                function (err) {
                    console.trace("err - starting transcription: " + err);
                });
        },
        function (err) {
            console.trace("err - adding user1: " + err);
        });
    },
    function (err) {
        console.trace("err - adding user2: " + err);
    });
    },
    function (err) {
    console.trace("err - " + err);
    });
}()); 
```