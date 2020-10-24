---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: afe6562357f1d5558fdb2ac8cb0eb53cd3422d48
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92499195"
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

# <a name="script"></a>[skrypt](#tab/script)

Pobierz i Wyodrębnij <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">zestaw Speech SDK dla <span class="docon docon-navigate-external x-hidden-focus"></span> </a> pliku *microsoft.cognitiveservices.speech.sdk.bundle.js* JavaScript i umieść go w folderze dostępnym dla pliku HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Jeśli masz dostęp do przeglądarki sieci Web i używasz `<script>` znacznika; `sdk` prefiks nie jest wymagany. `sdk`Prefiks jest aliasem używanym do nazwy `require` modułu.

# <a name="import"></a>[zaimportować](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Aby uzyskać więcej informacji na temat `import` , zobacz <a href="https://javascript.info/import-export" target="_blank">eksport <span class="docon docon-navigate-external x-hidden-focus"></span> i import </a>.

# <a name="require"></a>[wymagane](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Aby uzyskać więcej informacji na temat `require` , zobacz <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">co to <span class="docon docon-navigate-external x-hidden-focus"></span> jest wymagane? </a>.

---

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu zestawu Speech SDK, należy utworzyć [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, Host lub Token autoryzacji. Utwórz [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) klucz przy użyciu klucza i regionu. Aby znaleźć identyfikator regionu, zobacz stronę [Obsługa regionów](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) .

```javascript
const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Istnieje kilka innych sposobów na zainicjowanie [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) :

* Z punktem końcowym: Pass w punkcie końcowym usługi mowy. Klucz lub Token autoryzacji jest opcjonalny.
* Z hostem: Przekaż adres hosta. Klucz lub Token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: Przekaż Token autoryzacji i skojarzony region.

> [!NOTE]
> Bez względu na to, czy wykonujesz rozpoznawanie mowy, synteza mowy, tłumaczenie czy rozpoznawanie intencji, zawsze utworzysz konfigurację.

## <a name="recognize-from-microphone"></a>Rozpoznawanie z mikrofonu

Aby rozpoznać mowę przy użyciu mikrofonu urządzenia, Utwórz `AudioConfig` za pomocą `fromDefaultMicrophoneInput()` . Następnie zainicjuj [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true) , przekazując `speechConfig` i `audioConfig` .

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new SpeechRecognizer(speechConfig, audioConfig);

console.log('Speak into your microphone.');
recognizer.recognizeOnceAsync(result => {
    console.log(`RECOGNIZED: Text=${result.text}`);
});
```

Jeśli chcesz użyć *określonego* urządzenia wejściowego audio, musisz określić identyfikator urządzenia w `AudioConfig` . Dowiedz się [, jak uzyskać identyfikator urządzenia](../../../how-to-select-audio-input-devices.md) dla wejściowego urządzenia audio.

## <a name="recognize-from-file-nodejs-only"></a>Rozpoznawanie z pliku (tylko Node.js)

Jeśli chcesz rozpoznać mowę z pliku audio zamiast korzystać z mikrofonu, nadal musisz podać `AudioConfig` . Jednak podczas tworzenia [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true) , zamiast wywoływania `fromDefaultMicrophoneInput()` , należy wywołać `fromWavFileInput()` i przekazać ścieżkę pliku.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new SpeechRecognizer(speechConfig, audioConfig);

recognizer.recognizeOnceAsync(result => {
    console.log(`RECOGNIZED: Text=${result.text}`);
});
```

## <a name="recognize-speech"></a>Rozpoznawanie mowy

[Klasa aparatu rozpoznawania](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true) dla zestawu Speech SDK dla języka JavaScript udostępnia kilka metod, których można użyć do rozpoznawania mowy.

### <a name="single-shot-recognition"></a>Rozpoznawanie pojedynczego zrzutu

Rozpoznawanie pojedynczego zrzutu asynchronicznie rozpoznaje pojedynczy wypowiedź. Koniec pojedynczej wypowiedź jest określany przez nasłuchiwanie na końcu lub do czasu przetworzenia maksymalnie 15 sekund. Oto przykład asynchronicznego rozpoznawania pojedynczego zrzutu przy użyciu [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync) :

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Musisz napisać kod, aby obsłużyć wynik. Ten przykład szacuje [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?view=azure-node-latest&preserve-view=true#reason) :

* Drukuje wynik rozpoznawania: `ResultReason.RecognizedSpeech`
* Jeśli nie ma dopasowania do rozpoznawania, należy poinformować użytkownika: `ResultReason.NoMatch`
* Jeśli wystąpi błąd, Wydrukuj komunikat o błędzie: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        console.log("    Intent not recognized.");
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

### <a name="continuous-recognition"></a>Ciągłe rozpoznawanie

Ciągłe rozpoznawanie jest nieco większe niż w przypadku rozpoznawania pojedynczego zrzutu. Wymaga to subskrybowania `Recognizing` `Recognized` zdarzeń, i `Canceled` w celu uzyskania wyników rozpoznawania. Aby zatrzymać rozpoznawanie, należy wywołać metodę [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#stopcontinuousrecognitionasync) . Oto przykład sposobu ciągłego rozpoznawania w pliku wejściowym audio.

Zacznijmy od definiowania danych wejściowych i inicjowania [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true) :

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

Zasubskrybujemy zdarzenia wysyłane z usługi [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true) .

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizing): Sygnał dla zdarzeń zawierających pośrednie wyniki rozpoznawania.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognized): Sygnał dla zdarzeń zawierających końcowe wyniki rozpoznawania (wskazujący na pomyślną próbę rozpoznania).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#sessionstopped): Sygnał dla zdarzeń wskazujących koniec sesji rozpoznawania (operacji).
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#canceled): Sygnał dla zdarzeń zawierających anulowane wyniki rozpoznawania (wskazujący próbę rozpoznania, która została anulowana w wyniku lub bezpośrednie żądanie anulowania lub, Alternatywnie, błąd transportu lub protokołu).

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

Wszystko jest skonfigurowane, możemy wywoływać [`startContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#startcontinuousrecognitionasync) .

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();

// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Tryb dyktowania

W przypadku korzystania z ciągłego rozpoznawania można włączyć przetwarzanie dyktowania przy użyciu odpowiedniej funkcji "Włącz dyktowanie". Ten tryb spowoduje, że wystąpienie konfiguracji mowy interpretuje opisy wyrazów struktur zdań, takich jak interpunkcja. Na przykład "wypowiedź" czy "czy" na żywo "jest interpretowany jako tekst" czy jesteś w mieście? ".

Aby włączyć tryb dyktowania, użyj [`enableDictation`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#enabledictation--) metody w [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) .

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Zmień język źródłowy

Typowym zadaniem rozpoznawania mowy jest określenie języka danych wejściowych (lub źródłowych). Przyjrzyjmy się sposobom zmiany języka wejściowego na włoski. Znajdź swój kod, [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) a następnie Dodaj ten wiersz bezpośrednio poniżej.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#speechrecognitionlanguage)Właściwość oczekuje ciągu formatu ustawień regionalnych. Na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md)można podać dowolną wartość w kolumnie **Ustawienia regionalne** .

## <a name="improve-recognition-accuracy"></a>Popraw dokładność rozpoznawania

Istnieje kilka sposobów na poprawienie dokładności rozpoznawania przy użyciu mowy poszukaj list fraz. Listy fraz są używane do identyfikowania znanych fraz w danych audio, takich jak nazwa osoby lub określonej lokalizacji. Pojedyncze słowa lub kompletne wyrażenia można dodać do listy fraz. Podczas rozpoznawania jest używany wpis na liście frazy, jeśli dokładne dopasowanie dla całej frazy jest zawarte w dźwięku. Jeśli nie znaleziono dokładnego dopasowania do frazy, rozpoznawanie nie jest wspierane.

> [!IMPORTANT]
> Funkcja listy fraz jest dostępna tylko w języku angielskim.

Aby użyć listy fraz, najpierw Utwórz [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true) obiekt, a następnie Dodaj określone słowa i frazy za pomocą [`addPhrase`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true#addphrase-string-) .

Wszelkie zmiany [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true) zaczną obowiązywać przy następnym rozpoznaniu lub po ponownym połączeniu z usługą mowy.

```javascript
const phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Jeśli musisz wyczyścić listę fraz:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Inne opcje poprawiające dokładność rozpoznawania

Listy fraz są tylko jedną opcją w celu zwiększenia dokładności rozpoznawania. Możesz również wykonać następujące czynności: 

* [Zwiększanie dokładności za pomocą mowy niestandardowej](../../../how-to-custom-speech.md)
* [Zwiększanie dokładności za pomocą modeli dzierżaw](../../../tutorial-tenant-model.md)
