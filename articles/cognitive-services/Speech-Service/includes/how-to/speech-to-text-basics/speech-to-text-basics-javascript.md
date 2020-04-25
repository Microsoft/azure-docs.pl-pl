---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: 2d6e53f8a69a3e214d7d4621e899fd2e5394c7f2
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82132146"
---
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Przed wykonaniem jakichkolwiek czynności należy zainstalować <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">zestaw SDK <span class="docon docon-navigate-external x-hidden-focus"> </span>mowy JavaScript </a>. W zależności od platformy należy wykonać następujące instrukcje:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node. js<span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Przeglądarka sieci Web<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Ponadto, w zależności od środowiska docelowego, użyj jednego z następujących elementów:

# <a name="import"></a>[zaimportować](#tab/import)

```javascript
import {
    AudioConfig,
    CancellationDetails,
    CancellationReason,
    PhraseListGrammar,
    ResultReason,
    SpeechConfig,
    SpeechRecognizer
} from "microsoft-cognitiveservices-speech-sdk";
```

Aby uzyskać więcej informacji `import`na temat, zobacz <a href="https://javascript.info/import-export" target="_blank">eksport <span class="docon docon-navigate-external x-hidden-focus"> </span>i import </a>.

# <a name="require"></a>[wymagane](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Aby uzyskać więcej informacji `require`na temat, zobacz <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">co jest wymagane? <span class="docon docon-navigate-external x-hidden-focus"></span></a>.


# <a name="script"></a>[napisy](#tab/script)

Pobierz i wyodrębnij plik <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript Speech <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> *Microsoft. cognitiveservices. Speech. pakiet. js* i umieść go w folderze dostępnym dla pliku HTML.

```html
<script src="microsoft.cognitiveservices.speech.bundle.js"></script>;
```

> [!TIP]
> Jeśli jesteś celem przeglądarki sieci Web i używasz `<script>` znacznika; `sdk` prefiks nie jest wymagany. `sdk` Prefiks jest aliasem używanym do nazwy `require` modułu.

---

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu zestawu Speech SDK, należy utworzyć [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest). Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, Host lub Token autoryzacji.

> [!NOTE]
> Bez względu na to, czy wykonujesz rozpoznawanie mowy, synteza mowy, tłumaczenie czy rozpoznawanie intencji, zawsze utworzysz konfigurację.

Istnieje kilka sposobów na zainicjowanie [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest):

* Z subskrypcją: Przekaż klucz i skojarzony region.
* Z punktem końcowym: Pass w punkcie końcowym usługi mowy. Klucz lub Token autoryzacji jest opcjonalny.
* Z hostem: Przekaż adres hosta. Klucz lub Token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: Przekaż Token autoryzacji i skojarzony region.

Przyjrzyjmy się w jaki sposób [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) jest tworzony przy użyciu klucza i regionu. Aby znaleźć identyfikator regionu, zobacz stronę [Obsługa regionów](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) .

```javascript
const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Inicjowanie aparatu rozpoznawania

Po utworzeniu [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest), następnym krokiem jest zainicjowanie [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest). Po zainicjowaniu [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)elementu należy przekazać go `speechConfig`. Zapewnia to poświadczenia wymagane przez usługę mowy do zweryfikowania Twojego żądania.

Jeśli rozpoznajesz mowę przy użyciu domyślnego mikrofonu urządzenia, Oto jak [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) powinien wyglądać:

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

Jeśli chcesz określić urządzenie wejściowe audio, należy utworzyć [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) i podać `audioConfig` parametr podczas inicjowania. [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)

> [!TIP]
> [Dowiedz się, jak uzyskać identyfikator urządzenia dla wejściowego urządzenia audio](../../../how-to-select-audio-input-devices.md).

Odwołuje `AudioConfig` się do obiektu w następujący sposób:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

Jeśli chcesz podać plik audio zamiast używać mikrofonu, nadal musisz podać `audioConfig`. Można to zrobić tylko wtedy, gdy obiektem [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest)docelowym jest **Node. js** , a podczas tworzenia, zamiast wywoływania `fromDefaultMicrophoneInput`, należy wywołać `fromWavFileOutput` i przekazać `filename` parametr.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>Rozpoznawanie mowy

[Klasa aparatu rozpoznawania](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) dla zestawu Speech SDK dla języka C# udostępnia kilka metod, których można użyć do rozpoznawania mowy.

* Rozpoznawanie pojedynczego zrzutu (Async) — wykonuje rozpoznawanie w trybie nieblokującym (asynchronicznym). Spowoduje to rozpoznanie pojedynczego wypowiedź. Koniec pojedynczej wypowiedź jest określany przez nasłuchiwanie na końcu lub do czasu przetworzenia maksymalnie 15 sekund.
* Stałe rozpoznawanie (asynchroniczne) — asynchronicznie Inicjuje operację ciągłego rozpoznawania. Użytkownik rejestruje się w zdarzeniach i obsługuje różne stany aplikacji. Aby zatrzymać asynchroniczne rozpoznawanie ciągłe, wywołaj [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync).

> [!NOTE]
> Dowiedz się więcej na temat [wybierania trybu rozpoznawania mowy](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Rozpoznawanie pojedynczego zrzutu

Oto przykład asynchronicznego rozpoznawania pojedynczego zrzutu przy użyciu [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync):

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Musisz napisać kod, aby obsłużyć wynik. Ten przykład szacuje [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?view=azure-node-latest#reason):

* Drukuje wynik rozpoznawania:`ResultReason.RecognizedSpeech`
* Jeśli nie ma dopasowania do rozpoznawania, należy poinformować użytkownika:`ResultReason.NoMatch`
* Jeśli wystąpi błąd, Wydrukuj komunikat o błędzie:`ResultReason.Canceled`

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
}
```

### <a name="continuous-recognition"></a>Ciągłe rozpoznawanie

Ciągłe rozpoznawanie jest nieco większe niż w przypadku rozpoznawania pojedynczego zrzutu. Wymaga to subskrybowania zdarzeń `Recognizing`, `Recognized`i `Canceled` w celu uzyskania wyników rozpoznawania. Aby zatrzymać rozpoznawanie, należy wywołać metodę [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync). Oto przykład sposobu ciągłego rozpoznawania w pliku wejściowym audio.

Zacznijmy od definiowania danych wejściowych i inicjowania [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest):

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

Zasubskrybujemy zdarzenia wysyłane z usługi [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest).

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizing): Sygnał dla zdarzeń zawierających pośrednie wyniki rozpoznawania.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognized): Sygnał dla zdarzeń zawierających końcowe wyniki rozpoznawania (wskazujący na pomyślną próbę rozpoznania).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#sessionstopped): Sygnał dla zdarzeń wskazujących koniec sesji rozpoznawania (operacji).
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#canceled): Sygnał dla zdarzeń zawierających anulowane wyniki rozpoznawania (wskazujący próbę rozpoznania, która została anulowana w wyniku lub bezpośrednie żądanie anulowania lub, Alternatywnie, błąd transportu lub protokołu).

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

Wszystko jest skonfigurowane, możemy wywoływać [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync).

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();

// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Tryb dyktowania

W przypadku korzystania z ciągłego rozpoznawania można włączyć przetwarzanie dyktowania przy użyciu odpowiedniej funkcji "Włącz dyktowanie". Ten tryb spowoduje, że wystąpienie konfiguracji mowy interpretuje opisy wyrazów struktur zdań, takich jak interpunkcja. Na przykład "wypowiedź" czy "czy" na żywo "jest interpretowany jako tekst" czy jesteś w mieście? ".

Aby włączyć tryb dyktowania, użyj [`enableDictation`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--) metody w. [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Zmień język źródłowy

Typowym zadaniem rozpoznawania mowy jest określenie języka danych wejściowych (lub źródłowych). Przyjrzyjmy się sposobom zmiany języka wejściowego na włoski. Znajdź swój [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)kod, a następnie Dodaj ten wiersz bezpośrednio poniżej.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#speechrecognitionlanguage) Właściwość oczekuje ciągu formatu ustawień regionalnych. Na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md)można podać dowolną wartość w kolumnie **Ustawienia regionalne** .

## <a name="improve-recognition-accuracy"></a>Popraw dokładność rozpoznawania

Istnieje kilka sposobów na poprawienie dokładności rozpoznawania przy użyciu mowy poszukaj list fraz. Listy fraz są używane do identyfikowania znanych fraz w danych audio, takich jak nazwa osoby lub określonej lokalizacji. Pojedyncze słowa lub kompletne wyrażenia można dodać do listy fraz. Podczas rozpoznawania jest używany wpis na liście frazy, jeśli dokładne dopasowanie dla całej frazy jest zawarte w dźwięku. Jeśli nie znaleziono dokładnego dopasowania do frazy, rozpoznawanie nie jest wspierane.

> [!IMPORTANT]
> Funkcja listy fraz jest dostępna tylko w języku angielskim.

Aby użyć listy fraz, najpierw Utwórz [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest) obiekt, a następnie Dodaj określone słowa i frazy za [`addPhrase`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest#addphrase-string-)pomocą.

Wszelkie zmiany [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest) zaczną obowiązywać przy następnym rozpoznaniu lub po ponownym połączeniu z usługą mowy.

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
