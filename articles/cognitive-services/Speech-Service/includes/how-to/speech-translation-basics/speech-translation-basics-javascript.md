---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.custom: devx-track-js
ms.openlocfilehash: c63b55905ea16c0597e1f8f1179153229018ed32
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92471037"
---
Jedną z podstawowych funkcji usługi mowy jest możliwość rozpoznawania mowy przez człowieka i przetłumaczenia jej na inne języki. W tym przewodniku szybki start dowiesz się, jak używać zestawu Speech SDK w aplikacjach i produktach do wykonywania translacji mowy o wysokiej jakości. Ten przewodnik Szybki Start obejmuje następujące tematy:

* Tłumaczenie zamiany mowy na tekst
* Tłumaczenie mowy na wiele języków docelowych
* Wykonywanie bezpośredniego tłumaczenia mowy na mowę

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

## <a name="create-a-translation-configuration"></a>Utwórz konfigurację tłumaczenia

Aby wywołać usługę tłumaczenia przy użyciu zestawu Speech SDK, należy utworzyć [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true) . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, Host lub Token autoryzacji.

> [!NOTE]
> Bez względu na to, czy wykonujesz rozpoznawanie mowy, synteza mowy, tłumaczenie czy rozpoznawanie intencji, zawsze utworzysz konfigurację.
Istnieje kilka sposobów na zainicjowanie [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true) :

* Z subskrypcją: Przekaż klucz i skojarzony region.
* Z punktem końcowym: Pass w punkcie końcowym usługi mowy. Klucz lub Token autoryzacji jest opcjonalny.
* Z hostem: Przekaż adres hosta. Klucz lub Token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: Przekaż Token autoryzacji i skojarzony region.

Przyjrzyjmy się w jaki sposób [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true) jest tworzony przy użyciu klucza i regionu. Pobierz te poświadczenia, wykonując czynności opisane w sekcji [Wypróbuj bezpłatnie usługę Speech](../../../overview.md#try-the-speech-service-for-free).

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>Inicjowanie translatora

Po utworzeniu [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true) , następnym krokiem jest zainicjowanie [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true) . Po zainicjowaniu elementu należy [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true) przekazać go `speechTranslationConfig` . Zapewnia to poświadczenia wymagane przez usługę tłumaczenia do zweryfikowania Twojego żądania.

W przypadku tłumaczenia mowy z domyślnego mikrofonu urządzenia [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true) należy wyglądać następująco:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Jeśli chcesz określić urządzenie wejściowe audio, należy utworzyć [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true) i podać `audioConfig` parametr podczas inicjowania [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true) .

> [!TIP]
> [Dowiedz się, jak uzyskać identyfikator urządzenia dla wejściowego urządzenia audio](../../../how-to-select-audio-input-devices.md).
Odwołuje się do `AudioConfig` obiektu w następujący sposób:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

Jeśli chcesz podać plik audio zamiast używać mikrofonu, nadal musisz podać `audioConfig` . Można to jednak zrobić tylko wtedy, gdy element docelowy **Node.js** i podczas tworzenia [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true) , zamiast wywoływania `fromDefaultMicrophoneInput` , należy wywołać `fromWavFileOutput` i przekazać `filename` parametr.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>Tłumaczenie mowy

[Klasa TranslationRecognizer](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true) dla zestawu Speech SDK dla języka JavaScript udostępnia kilka metod, których można użyć do tłumaczenia mowy.

* Tłumaczenie pojedynczego zrzutu (Async) — wykonuje translację w trybie nieblokującym (asynchronicznym). Spowoduje to przetłumaczenie jednego wypowiedźa. Koniec pojedynczej wypowiedź jest określany przez nasłuchiwanie na końcu lub do czasu przetworzenia maksymalnie 15 sekund.
* Stałe tłumaczenie (asynchroniczne) — asynchronicznie Inicjuje operację ciągłego tłumaczenia. Użytkownik rejestruje się w zdarzeniach i obsługuje różne stany aplikacji. Aby zatrzymać asynchroniczne tłumaczenie ciągłe, wywołaj [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#stopcontinuousrecognitionasync) .

> [!NOTE]
> Dowiedz się więcej na temat [wybierania trybu rozpoznawania mowy](../../../how-to-choose-recognition-mode.md).
### <a name="specify-a-target-language"></a>Określ język docelowy

Aby przetłumaczyć, należy określić język źródłowy i co najmniej jeden język docelowy.
Możesz wybrać język źródłowy przy użyciu ustawień regionalnych wymienionych w [tabeli tłumaczenia mowy](../../../language-support.md#speech-translation). Znajdź opcje przetłumaczonego języka w tym samym łączu. Opcje języków docelowych różnią się w zależności od tego, czy chcesz wyświetlić tekst, czy też chcesz usłyszeć przetłumaczone Tłumaczenie mowy. Aby dokonać translacji z języka angielskiego na język niemiecki, zmodyfikuj obiekt konfiguracji Tłumaczenia:

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>Rozpoznawanie pojedynczego zrzutu

Oto przykładowe asynchroniczne tłumaczenie jednokrotne przy użyciu [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync) :

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Musisz napisać kod, aby obsłużyć wynik. Ten przykład szacuje [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult?view=azure-node-latest&preserve-view=true) dla tłumaczenia na język niemiecki:

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

Kod może również obsługiwać aktualizacje udostępniane podczas przetwarzania tłumaczenia.
Możesz użyć tych aktualizacji, aby zapewnić wizualną opinię na temat postępu tłumaczenia.
Zapoznaj się z [tym przykładem JavaScript Node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js) przykładowy kod, który pokazuje aktualizacje podane podczas procesu tłumaczenia. Poniższy kod również Wyświetla szczegóły wygenerowane podczas procesu tłumaczenia.

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>Ciągłe tłumaczenie

Ciągłe tłumaczenie jest nieco większe niż w przypadku rozpoznawania pojedynczego zrzutu. Wymaga to subskrybowania `recognizing` `recognized` zdarzeń, i `canceled` w celu uzyskania wyników rozpoznawania. Aby zatrzymać tłumaczenie, należy wywołać metodę [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#stopcontinuousrecognitionasync) . Oto przykład sposobu ciągłego tłumaczenia na plik wejściowy audio.

Zacznijmy od definiowania danych wejściowych i inicjowania [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true) :

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Zasubskrybujemy zdarzenia wysyłane z usługi [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true) .

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#recognizing): Sygnał dla zdarzeń zawierających pośrednie wyniki tłumaczenia.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#recognized): Sygnał dla zdarzeń zawierających końcowe wyniki tłumaczenia (wskazujący, że próba przeprowadzenia translacji powiodła się).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#sessionstopped): Sygnał dla zdarzeń wskazujących koniec sesji tłumaczenia (operacji).
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#canceled): Sygnał dla zdarzeń zawierających anulowane wyniki tłumaczenia (wskazujący próbę przeprowadzenia translacji, która została anulowana jako wynik lub bezpośrednie żądanie anulowania lub, Alternatywnie, błąd transportu lub protokołu).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
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

## <a name="choose-a-source-language"></a>Wybierz język źródłowy

Typowym zadaniem do tłumaczenia mowy jest określenie języka danych wejściowych (lub źródłowych). Przyjrzyjmy się sposobom zmiany języka wejściowego na włoski. Znajdź swój kod, [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true) a następnie Dodaj poniższy wiersz bezpośrednio poniżej.

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true#speechrecognitionlanguage)Właściwość oczekuje ciągu formatu ustawień regionalnych. Na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md)można podać dowolną wartość w kolumnie **Ustawienia regionalne** .

## <a name="choose-one-or-more-target-languages"></a>Wybierz co najmniej jeden język docelowy

Zestaw Speech SDK może być tłumaczony na wiele języków docelowych równolegle. Dostępne języki są nieco inne niż na liście język źródłowy i można określić Języki docelowe przy użyciu kodu języka, a nie ustawień regionalnych.
Zapoznaj się z listą kodów języka dla celów tekstowych w [tabeli translacji mowy na stronie obsługa języka](../../../language-support.md#speech-translation). W tym miejscu możesz również znaleźć szczegółowe informacje na temat tłumaczenia.

Poniższy kod dodaje niemiecki jako język docelowy:

```javascript
translationConfig.addTargetLanguage("de");
```

Ponieważ możliwe jest wielokrotne tłumaczenie języka docelowego, kod musi określać język docelowy podczas badania wyniku. Poniższy kod pobiera wyniki tłumaczenia dla języka niemieckiego.

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```
