---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 26afd53c1ff4c6f13b6f3a237ccf12aa0d46ffc6
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509495"
---
Jedną z podstawowych funkcji usługi mowy jest możliwość rozpoznawania i transkrypcja mowy (często nazywanej zamianą mowy na tekst). W tym przewodniku szybki start dowiesz się, jak używać zestawu Speech SDK w aplikacjach i produktach do wykonywania konwersji wysokiej jakości zamiany mowy na tekst.

## <a name="skip-to-samples-on-github"></a>Przejdź do przykładów w witrynie GitHub

Jeśli chcesz pominąć prosty kod przykładowy, zobacz [przykłady przewodnika Szybki Start dla języka JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Przed wykonaniem jakichkolwiek czynności konieczne będzie zainstalowanie <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">zestawu Speech SDK dla języka JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. W zależności od platformy należy wykonać następujące instrukcje:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Przeglądarka sieci Web <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Ponadto, w zależności od środowiska docelowego, użyj jednego z następujących elementów:

# <a name="script"></a>[napisy](#tab/script)

Pobierz i Wyodrębnij <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">zestaw Speech SDK dla <span class="docon docon-navigate-external x-hidden-focus"></span></a> pliku *microsoft.cognitiveservices.speech.sdk.bundle.js* JavaScript i umieść go w folderze dostępnym dla pliku HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Jeśli masz dostęp do przeglądarki sieci Web i używasz `<script>` znacznika; `sdk` prefiks nie jest wymagany podczas odwoływania się do klas. `sdk`Prefiks jest aliasem używanym do nazwy `require` modułu.

# <a name="require"></a>[wymagane](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Aby uzyskać więcej informacji na temat `require` , zobacz <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">co to <span class="docon docon-navigate-external x-hidden-focus"></span> jest wymagane? </a>.

---

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu zestawu Speech SDK, należy utworzyć [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, Host lub Token autoryzacji. Utwórz [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) klucz przy użyciu klucza i regionu. Aby znaleźć parę klucz-region, zobacz stronę [Znajdowanie kluczy i regionów](../../../overview.md#find-keys-and-region) .

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Istnieje kilka innych sposobów na zainicjowanie [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) :

* Z punktem końcowym: Pass w punkcie końcowym usługi mowy. Klucz lub Token autoryzacji jest opcjonalny.
* Z hostem: Przekaż adres hosta. Klucz lub Token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: Przekaż Token autoryzacji i skojarzony region.

> [!NOTE]
> Bez względu na to, czy wykonujesz rozpoznawanie mowy, synteza mowy, tłumaczenie czy rozpoznawanie intencji, zawsze utworzysz konfigurację.

## <a name="recognize-from-microphone-browser-only"></a>Rozpoznawanie z mikrofonu (tylko przeglądarka)

Aby rozpoznać mowę przy użyciu mikrofonu urządzenia, Utwórz `AudioConfig` za pomocą `fromDefaultMicrophoneInput()` . Następnie zainicjuj [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest) , przekazując `speechConfig` i `audioConfig` .

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromMic() {
    let audioConfig = sdk.AudioConfig.fromDefaultMicrophoneInput();
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    console.log('Speak into your microphone.');
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromMic();
```

Jeśli chcesz użyć *określonego* urządzenia wejściowego audio, musisz określić identyfikator urządzenia w `AudioConfig` . Dowiedz się [, jak uzyskać identyfikator urządzenia](../../../how-to-select-audio-input-devices.md) dla wejściowego urządzenia audio.

## <a name="recognize-from-file"></a>Rozpoznaj z pliku 

# <a name="browser"></a>[Przeglądarka](#tab/browser)

Aby rozpoznać mowę z pliku audio w środowisku JavaScript opartym na przeglądarce, należy użyć `fromWavFileInput()` funkcji do utworzenia [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest) . Funkcja `fromWavFileInput()` oczekuje [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) obiektu JavaScript jako parametru.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    // wavByteContent should be a byte array of the raw wav content
    let file = new File([wavByteContent]);
    let audioConfig = sdk.AudioConfig.fromWavFileInput(file);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromFile();
```

# <a name="nodejs"></a>[Node.js](#tab/node)

Aby rozpoznać mowę z pliku audio w Node.js, należy użyć alternatywnego wzorca projektowego używającego strumienia wypychania, ponieważ obiektu JavaScript [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) nie można używać w środowisku uruchomieniowym Node.js. Następujący kod:

* Tworzy strumień wypychania przy użyciu `createPushStream()`
* Otwiera `.wav` plik przez utworzenie strumienia odczytu i zapisanie go w strumieniu wypychania
* Tworzy konfigurację audio przy użyciu strumienia wypychania

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

Użycie strumienia wypychania jako danych wejściowych zakłada, że dane audio to nieprzetworzony moduł PCM, np. pomijanie wszystkich nagłówków.
Interfejs API będzie nadal działał w niektórych przypadkach, jeśli nagłówek nie został pominięty, ale w celu uzyskania najlepszych wyników Rozważ zaimplementowanie logiki w celu odczytania nagłówków, aby `fs` *rozpocząć od początku danych audio*.

---

## <a name="error-handling"></a>Obsługa błędów

W poprzednich przykładach wystarczy uzyskać rozpoznany tekst z `result.text` , ale aby obsłużyć błędy i inne odpowiedzi, należy napisać kod, aby obsłużyć wynik. Poniższy kod szacuje [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?preserve-view=true&view=azure-node-latest#reason) Właściwość i:

* Drukuje wynik rozpoznawania: `ResultReason.RecognizedSpeech`
* Jeśli nie ma dopasowania do rozpoznawania, należy poinformować użytkownika: `ResultReason.NoMatch`
* Jeśli wystąpi błąd, Wydrukuj komunikat o błędzie: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>Ciągłe rozpoznawanie

Poprzednie przykłady używają rozpoznawania pojedynczego zrzutu, które rozpoznaje pojedynczy wypowiedź. Koniec pojedynczej wypowiedź jest określany przez nasłuchiwanie na końcu lub do czasu przetworzenia maksymalnie 15 sekund.

Z drugiej strony rozpoznawanie ciągłe jest używane, gdy chcesz **kontrolować** czas zatrzymania rozpoznawania. Wymaga to subskrybowania `Recognizing` `Recognized` zdarzeń, i `Canceled` w celu uzyskania wyników rozpoznawania. Aby zatrzymać rozpoznawanie, należy wywołać metodę [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#stopcontinuousrecognitionasync) . Oto przykład sposobu ciągłego rozpoznawania w pliku wejściowym audio.

Zacznij od zdefiniowania danych wejściowych i zainicjowania [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest) :

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig);
```

Następnie Zasubskrybuj zdarzenia wysyłane z usługi [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest) .

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#recognizing): Sygnał dla zdarzeń zawierających pośrednie wyniki rozpoznawania.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#recognized): Sygnał dla zdarzeń zawierających końcowe wyniki rozpoznawania (wskazujący na pomyślną próbę rozpoznania).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#sessionstopped): Sygnał dla zdarzeń wskazujących koniec sesji rozpoznawania (operacji).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#canceled): Sygnał dla zdarzeń zawierających anulowane wyniki rozpoznawania (wskazujący próbę rozpoznania, która została anulowana w wyniku lub bezpośrednie żądanie anulowania lub, Alternatywnie, błąd transportu lub protokołu).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Gdy wszystko jest skonfigurowane, wywołaj metodę [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#startcontinuousrecognitionasync) rozpoczęcia rozpoznawania.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Tryb dyktowania

W przypadku korzystania z ciągłego rozpoznawania można włączyć przetwarzanie dyktowania przy użyciu odpowiedniej funkcji "Włącz dyktowanie". Ten tryb spowoduje, że wystąpienie konfiguracji mowy interpretuje opisy wyrazów struktur zdań, takich jak interpunkcja. Na przykład "wypowiedź" czy "czy" na żywo "jest interpretowany jako tekst" czy jesteś w mieście? ".

Aby włączyć tryb dyktowania, użyj [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest#enabledictation--) metody w [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) .

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Zmień język źródłowy

Typowym zadaniem rozpoznawania mowy jest określenie języka danych wejściowych (lub źródłowych). Przyjrzyjmy się sposobom zmiany języka wejściowego na włoski. Znajdź swój kod, [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) a następnie Dodaj ten wiersz bezpośrednio poniżej.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest#speechrecognitionlanguage)Właściwość oczekuje ciągu formatu ustawień regionalnych. Na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md)można podać dowolną wartość w kolumnie **Ustawienia regionalne** .

## <a name="improve-recognition-accuracy"></a>Popraw dokładność rozpoznawania

Istnieje kilka sposobów na poprawienie dokładności rozpoznawania przy użyciu mowy poszukaj list fraz. Listy fraz są używane do identyfikowania znanych fraz w danych audio, takich jak nazwa osoby lub określonej lokalizacji. Pojedyncze słowa lub kompletne wyrażenia można dodać do listy fraz. Podczas rozpoznawania jest używany wpis na liście frazy, jeśli dokładne dopasowanie dla całej frazy jest zawarte w dźwięku. Jeśli nie znaleziono dokładnego dopasowania do frazy, rozpoznawanie nie jest wspierane.

> [!IMPORTANT]
> Funkcja listy fraz jest dostępna tylko w języku angielskim.

Aby użyć listy fraz, najpierw Utwórz [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest) obiekt, a następnie Dodaj określone słowa i frazy za pomocą [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest#addphrase-string-) .

Wszelkie zmiany [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest) zaczną obowiązywać przy następnym rozpoznaniu lub po ponownym połączeniu z usługą mowy.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Jeśli musisz wyczyścić listę fraz:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Inne opcje poprawiające dokładność rozpoznawania

Listy fraz są tylko jedną opcją w celu zwiększenia dokładności rozpoznawania. Możesz również wykonać następujące czynności: 

* [Zwiększanie dokładności za pomocą mowy niestandardowej](../../../custom-speech-overview.md)
* [Zwiększanie dokładności za pomocą modeli dzierżaw](../../../tutorial-tenant-model.md)