---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: b11194640c4d049c90f85974022908dce6b4fd79
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399783"
---
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [wypróbuj bezpłatną usługę Mowy.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Zanim cokolwiek zrobisz, musisz zainstalować <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">pakiet JavaScript Speech <span class="docon docon-navigate-external x-hidden-focus"> </span>SDK </a>. W zależności od platformy należy korzystać z następujących instrukcji:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js<span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Przeglądarka internetowa<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Ponadto w zależności od środowiska docelowego należy użyć jednej z następujących czynności:

# <a name="import"></a>[Importu](#tab/import)

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

Aby uzyskać `import`więcej informacji na temat , zobacz <a href="https://javascript.info/import-export" target="_blank">eksport i importowanie <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

# <a name="require"></a>[Wymagają](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Aby uzyskać `require`więcej informacji na temat , <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">zobacz, co jest wymagane? <span class="docon docon-navigate-external x-hidden-focus"></span></a>.


# <a name="script"></a>[Skrypt](#tab/script)

Pobierz i wyodrębnij plik <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">SDK <span class="docon docon-navigate-external x-hidden-focus"></span> javascript speech</a> *microsoft.cognitiveservices.speech.bundle.js* i umieść go w folderze dostępnym dla pliku HTML.

```html
<script src="microsoft.cognitiveservices.speech.bundle.js"></script>;
```

> [!TIP]
> Jeśli kierujesz reklamy na przeglądarkę `<script>` internetową i używasz tagu; prefiks nie `sdk` jest potrzebny. `sdk` Prefiks jest aliasem używanym do nadawanie nazwy modułowi. `require`

---

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu SDK [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)mowy, należy utworzyć plik . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, host lub token autoryzacji.

> [!NOTE]
> Niezależnie od tego, czy wykonujesz rozpoznawanie mowy, syntezę mowy, tłumaczenie czy rozpoznawanie intencji, zawsze tworzysz konfigurację.

Istnieje kilka sposobów, które można [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)zainicjować:

* Z subskrypcją: przekaż klucz i skojarzony region.
* Z punktem końcowym: przekazać w punkcie końcowym usługi mowy. Klucz lub token autoryzacji jest opcjonalny.
* Z hostem: przekaż adres hosta. Klucz lub token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: przekaż w tokenie autoryzacji i skojarzonym regionie.

Przyjrzyjmy się, [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) jak jest tworzony przy użyciu klucza i regionu. Zobacz stronę [pomocy technicznej regionu,](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) aby znaleźć identyfikator regionu.

```javascript
const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Inicjowanie aparatu rozpoznawania

Po utworzeniu przycisku [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest), następnym krokiem [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)jest zainicjowanie pliku . Podczas inicjowania [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest), musisz przekazać go `speechConfig`. Zapewnia to poświadczenia, które usługa mowy wymaga, aby sprawdzić poprawność żądania.

Jeśli rozpoznajesz mowę przy użyciu domyślnego mikrofonu urządzenia, oto jak [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) powinno wyglądać:

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

Jeśli chcesz określić urządzenie wejściowe audio, musisz utworzyć [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) i `audioConfig` podać parametr podczas [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)inicjowania pliku .

> [!TIP]
> [Dowiedz się, jak uzyskać identyfikator urządzenia dla urządzenia wejściowego audio](../../../how-to-select-audio-input-devices.md).

Odwołaj `AudioConfig` się do obiektu w następujący sposób:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const speechConfig = SpeechConfig.fromSubscription(speechConfig, audioConfig);
```

Jeśli chcesz podać plik audio zamiast mikrofonu, nadal musisz podać plik `audioConfig`. Można to jednak zrobić tylko podczas kierowania **na node.js** i podczas tworzenia [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest), zamiast wywoływania, `fromDefaultMicrophoneInput`wywołasz `fromWavFileOutput` i przekażesz `filename` parametr.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const speechConfig = SpeechConfig.fromSubscription(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>Rozpoznawanie mowy

Klasa [aparatu rozpoznawania](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) dla SDK mowy dla języka C# udostępnia kilka metod, które można użyć do rozpoznawania mowy.

* Rozpoznawanie pojedynczego strzału (asynchowe) — wykonuje rozpoznawanie w trybie nieblokowym (asynchronimiacyjnym). Spowoduje to rozpoznanie pojedynczej wypowiedzi. Koniec pojedynczego wypowiedź jest określana przez nasłuchiwanie wyciszenia na końcu lub do maksymalnie 15 sekund audio jest przetwarzany.
* Ciągłe rozpoznawanie (asynchroniczne) — asynchronicznie inicjuje ciągłą operację rozpoznawania. Użytkownik rejestruje się do zdarzeń i obsługuje różne stany aplikacji. Aby zatrzymać ciągłe rozpoznawanie asynchroniczne, należy wywołać . [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync)

> [!NOTE]
> Dowiedz się więcej o [wyborze trybu rozpoznawania mowy](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Rozpoznawanie jednostrzałowe

Oto przykład asynchroniiowego rozpoznawania pojedynczego [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync)strzału za pomocą:

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Musisz napisać kod do obsługi wyniku. W tym przykładzie [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?view=azure-node-latest#reason)ocenia się:

* Drukuje wynik rozpoznawania:`ResultReason.RecognizedSpeech`
* Jeśli nie ma dopasowania rozpoznawania, poinformuj o tym użytkownika:`ResultReason.NoMatch`
* Jeśli wystąpi błąd, wydrukuj komunikat o błędzie:`ResultReason.Canceled`

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

Ciągłe rozpoznawanie jest nieco bardziej zaangażowane niż rozpoznawanie pojedynczych strzałów. To wymaga, aby zapisać `Recognizing`się `Recognized`do `Canceled` , i wydarzenia, aby uzyskać wyniki uznania. Aby zatrzymać rozpoznawanie, [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync)należy wywołać . Oto przykład ciągłego rozpoznawania na pliku wejściowym audio.

Zacznijmy od zdefiniowania danych wejściowych [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)i zainicjowania:

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

Zapiszemy się do wydarzeń wysłanych z . [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizing): Sygnał dla zdarzeń zawierających wyniki pośredniego rozpoznawania.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognized): Sygnał dla zdarzeń zawierających końcowe wyniki rozpoznawania (wskazujący pomyślną próbę rozpoznania).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#sessionstopped): Sygnał dla zdarzeń wskazujących koniec sesji rozpoznawania (operacja).
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#canceled): Sygnał dla zdarzeń zawierających anulowane wyniki rozpoznawania (wskazujący próbę rozpoznania, która została anulowana w wyniku lub bezpośrednie żądanie anulowania lub, alternatywnie, błąd transportu lub protokołu).

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

Z wszystko skonfigurowane, [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync)możemy zadzwonić .

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();

// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Tryb dyktowania

Korzystając z ciągłego rozpoznawania, można włączyć przetwarzanie dyktowania za pomocą odpowiedniej funkcji "włącz dyktowanie". Ten tryb spowoduje, że wystąpienie konfiguracji mowy będzie interpretować opisy wyrazów struktur zdań, takich jak znaki interpunkcyjne. Na przykład wypowiedź "Czy mieszkasz w znaku zapytania miasta" będzie interpretowana jako tekst "Czy mieszkasz w mieście?".

Aby włączyć tryb dyktowania, [`enableDictation`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--) użyj [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)tej metody w programie .

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Zmienianie języka źródłowego

Typowym zadaniem rozpoznawania mowy jest określenie języka wejściowego (lub źródłowego). Przyjrzyjmy się, jak można zmienić język wprowadzania na włoski. W kodzie znajdź [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)swój , a następnie dodaj ten wiersz bezpośrednio pod nim.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

Właściwość [`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#speechrecognitionlanguage) oczekuje ciąg formatu ustawień regionalnych języka. Dowolną wartość można podać w kolumnie **Ustawienia regionalne** na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Większa dokładność rozpoznawania

Istnieje kilka sposobów, aby poprawić dokładność rozpoznawania z mowy Ramiąć spojrzeć na listy fraz. Listy fraz służą do identyfikowania znanych fraz w danych audio, takich jak imię i nazwisko osoby lub określona lokalizacja. Pojedyncze słowa lub pełne frazy można dodać do listy fraz. Podczas rozpoznawania wpis na liście fraz jest używany, jeśli dokładne dopasowanie dla całej frazy jest zawarte w audio. Jeśli dokładne dopasowanie do frazy nie zostanie znalezione, rozpoznawanie nie jest wspomagane.

> [!IMPORTANT]
> Funkcja Lista fraz jest dostępna tylko w języku angielskim.

Aby użyć listy fraz, [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest) najpierw utwórz obiekt, a [`addPhrase`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest#addphrase-string-)następnie dodaj określone wyrazy i frazy za pomocą programu .

Wszelkie zmiany, które [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest) mają obowiązywać przy następnym rozpoznawaniu lub po ponownym połączeniu z usługą mowy.

```javascript
const phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Jeśli chcesz wyczyścić listę fraz:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Inne opcje w celu zwiększenia dokładności rozpoznawania

Listy fraz są tylko jedną z opcji, aby poprawić dokładność rozpoznawania. Możesz również wykonać następujące czynności: 

* [Zwiększanie dokładności za pomocą mowy niestandardowej](../../../how-to-custom-speech.md)
* [Zwiększanie dokładności za pomocą modeli dzierżaw](../../../tutorial-tenant-model.md)