---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2021
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 3fa47935721ccfccdfe18d60a66d5cc480582e7d
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428227"
---
W tym przewodniku szybki start przedstawiono typowe wzorce projektowania służące do wykonywania syntezy zamiany tekstu na mowę przy użyciu zestawu Speech SDK. Najpierw należy wykonać podstawowe czynności konfiguracyjne i synteza, a następnie przejść do bardziej zaawansowanych przykładów tworzenia aplikacji niestandardowych, takich jak:

* Uzyskiwanie odpowiedzi jako strumieni w pamięci
* Dostosowywanie szybkości próbkowania danych wyjściowych i szybkości transmisji bitów
* Przesyłanie żądań syntezy przy użyciu SSML (język oznaczeń syntezy mowy)
* Korzystanie z głosów neuronowych

## <a name="skip-to-samples-on-github"></a>Przejdź do przykładów w witrynie GitHub

Jeśli chcesz pominąć prosty kod przykładowy, zobacz [przykłady przewodnika Szybki Start dla języka JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i zasób usługi mowy. Jeśli nie masz konta i zasobu, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Przed wykonaniem jakichkolwiek czynności konieczne będzie zainstalowanie <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">zestawu Speech SDK dla języka JavaScript </a>. W zależności od platformy należy wykonać następujące instrukcje:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Przeglądarka sieci Web </a>

Ponadto, w zależności od środowiska docelowego, użyj jednego z następujących elementów:

# <a name="script"></a>[napisy](#tab/script)

Pobierz i Wyodrębnij <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">zestaw Speech SDK dla</a> pliku *microsoft.cognitiveservices.speech.sdk.bundle.js* JavaScript i umieść go w folderze dostępnym dla pliku HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Jeśli masz dostęp do przeglądarki sieci Web i używasz `<script>` znacznika; `sdk` prefiks nie jest wymagany. `sdk`Prefiks jest aliasem używanym do nazwy `require` modułu.

# <a name="import"></a>[zaimportować](#tab/import)

```javascript
import * as sdk from "microsoft-cognitiveservices-speech-sdk";
```

Aby uzyskać więcej informacji na temat `import` , zobacz <a href="https://javascript.info/import-export" target="_blank">eksport i import </a>.

# <a name="require"></a>[wymagane](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Aby uzyskać więcej informacji na temat `require` , zobacz <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">co to jest wymagane? </a>.

---


## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu zestawu Speech SDK, należy utworzyć [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) . Ta klasa zawiera informacje o Twoim zasobie, takie jak klucz i skojarzony region, punkt końcowy, Host lub Token autoryzacji.

> [!NOTE]
> Bez względu na to, czy wykonujesz rozpoznawanie mowy, synteza mowy, tłumaczenie czy rozpoznawanie intencji, zawsze utworzysz konfigurację.

Istnieje kilka sposobów na zainicjowanie [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) :

* Z zasobem: Przekaż klucz i skojarzony region.
* Z punktem końcowym: Pass w punkcie końcowym usługi mowy. Klucz lub Token autoryzacji jest opcjonalny.
* Z hostem: Przekaż adres hosta. Klucz lub Token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: Przekaż Token autoryzacji i skojarzony region.

W tym przykładzie utworzysz [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) przy użyciu klucza zasobu i regionu. Pobierz te poświadczenia, wykonując czynności opisane w sekcji [Wypróbuj bezpłatnie usługę Speech](../../../overview.md#try-the-speech-service-for-free). Utworzysz również podstawowy kod standardowy do użycia w pozostałej części tego artykułu, który można modyfikować w celu dostosowania.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}

synthesizeSpeech();
```

## <a name="synthesize-speech-to-a-file"></a>Wyrównać mowę do pliku

Następnie utworzysz [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer) obiekt, który wykonuje konwersje zamiany tekstu na mowę i wyjście na głośniki, pliki lub inne strumienie wyjściowe. [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer)Akceptuje jako params [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) obiekt utworzony w poprzednim kroku oraz [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) obiekt, który określa sposób obsługi wyników.

Aby rozpocząć, Utwórz element, `AudioConfig` Aby automatycznie zapisywać dane wyjściowe do `.wav` pliku przy użyciu `fromAudioFileOutput()` funkcji statycznej.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Następnie Utwórz wystąpienie `SpeechSynthesizer` przekazanie `speechConfig` obiektu i `audioConfig` obiektu jako parametry. Następnie wykonanie syntezy mowy i zapis w pliku jest tak proste jak uruchamianie `speakTextAsync()` z ciągiem tekstu. Wywołanie zwrotne wynik jest doskonałym miejscem do wywołania `synthesizer.close()` , w rzeczywistości — to wywołanie jest konieczne, aby synteza działała prawidłowo.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            synthesizer.close();
            if (result) {
                // return result as stream
                return fs.createReadStream("path-to-file.wav");
            }
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Uruchom program, a plik z syntezą `.wav` jest zapisywana w określonej lokalizacji. Jest to dobry przykład typowego użycia, ale następnym zapoznaj się z tematem dostosowywania danych wyjściowych i obsługi odpowiedzi wyjściowej jako strumienia znajdującego się w pamięci na potrzeby pracy z niestandardowymi scenariuszami.

## <a name="synthesize-to-speaker-output"></a>Synteza danych wyjściowych prezentera

W niektórych przypadkach można chcieć bezpośrednio wyprowadzać dane z głosu do osoby mówiącej. W tym celu Utwórz wystąpienie `AudioConfig` przy użyciu `fromDefaultSpeakerOutput()` funkcji statycznej. To wyjście do bieżącego aktywnego urządzenia wyjściowego.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                synthesizer.close();
                return result.audioData;
            }
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Pobierz wynik jako strumień w pamięci

W przypadku wielu scenariuszy tworzenia aplikacji mowy potrzebne są wyniki danych audio jako strumień w pamięci zamiast bezpośredniego zapisywania do pliku. Pozwoli to na tworzenie zachowań niestandardowych, w tym:

* Abstrakcyjna w efekcie tablica bajtów jako strumień umożliwiający wyszukiwanie niestandardowych usług podrzędnych.
* Zintegruj wynik z innymi interfejsami API lub usługami.
* Modyfikowanie danych audio, zapisywanie niestandardowych `.wav` nagłówków itp.

Jest to proste, aby wprowadzić tę zmianę z poprzedniego przykładu. Najpierw usuń `AudioConfig` blok, ponieważ będzie można ręcznie zarządzać zachowaniem danych wyjściowych od tego momentu w celu zwiększenia kontroli. Następnie Przekaż `undefined` `AudioConfig` w `SpeechSynthesizer` konstruktorze. 

> [!NOTE]
> W `undefined` przypadku `AudioConfig` , gdy nie zostanie pominięty w powyższym przykładzie danych wyjściowych prezentera, nie będzie odtwarzany dźwięk domyślnie na bieżącym aktywnym urządzeniu wyjściowym.

Tym razem można zapisać wynik w [`SpeechSynthesisResult`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult) zmiennej. `SpeechSynthesisResult.audioData`Właściwość zwraca `ArrayBuffer` dane wyjściowe, domyślny typ strumienia przeglądarki. W przypadku kodu serwerowego Konwertuj arrayBuffer na strumień bufora. 

Poniższy kod działa dla kodu po stronie klienta. 

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            synthesizer.close();
            return result.audioData;
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

W tym miejscu można zaimplementować dowolne zachowanie niestandardowe przy użyciu `ArrayBuffer` obiektu wyniku. ArrayBuffer jest wspólnym typem, który ma być odbierany w przeglądarce i odtwarzany w tym formacie. 

W przypadku dowolnego kodu opartego na serwerze, jeśli potrzebujesz pracy z danymi jako strumień zamiast ArrayBuffer, musisz przekonwertować obiekt na strumień. 

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            const { audioData } = result;

            synthesizer.close();

            // convert arrayBuffer to stream
            // return stream
            const bufferStream = new PassThrough();
            bufferStream.end(Buffer.from(audioData));
            return bufferStream;
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="customize-audio-format"></a>Dostosuj format audio

W poniższej sekcji pokazano, jak dostosować atrybuty wyjściowe audio, w tym:

* Typ pliku audio
* Szybkość próbkowania
* Bit — Głębokość

Aby zmienić format dźwięku, należy użyć `speechSynthesisOutputFormat` właściwości `SpeechConfig` obiektu. Ta właściwość oczekuje `enum` typu [`SpeechSynthesisOutputFormat`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat) , którego można użyć do wybrania formatu danych wyjściowych. [Listę dostępnych formatów audio](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat) można znaleźć w dokumentacji referencyjnej.

Istnieją różne opcje dla różnych typów plików, w zależności od wymagań. Należy pamiętać, że zgodnie z definicją, formaty nieprzetworzone, takie jak `Raw24Khz16BitMonoPcm` nie obejmują nagłówków audio. Używaj formatów nieprzetworzonych tylko wtedy, gdy wiesz, że wdrożenie podrzędne może zdekodować surową Bitstream lub jeśli planujesz ręczne tworzenie nagłówków na podstawie głębi bitowej, szybkości próbkowania, liczby kanałów itd.

> [!NOTE]
> Głosy **en-us-AriaRUS** i **en-us-GuyRUS** są tworzone na podstawie przykładów zakodowanych w `Riff24Khz16BitMonoPcm` współczynniku próbkowania.

W tym przykładzie należy określić format RIFF o wysokiej wierności, `Riff24Khz16BitMonoPcm` ustawiając `speechSynthesisOutputFormat` dla `SpeechConfig` obiektu. Podobnie jak w przypadku przykładu w poprzedniej sekcji, Pobierz `ArrayBuffer` dane audio i pracuj z nimi.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Ponowne uruchomienie programu spowoduje zapisanie `.wav` pliku w określonej ścieżce.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Użyj SSML, aby dostosować charakterystykę mowy

Język znaczników syntezy mowy (SSML) umożliwia precyzyjne dostosowanie wielkości liter, wymowy, liczby głosu i większej liczby danych wyjściowych zamiany tekstu na mowę przez przesłanie żądań ze schematu XML. W tej sekcji przedstawiono kilka praktycznych przykładów użycia, ale w celu uzyskania bardziej szczegółowego przewodnika zapoznaj się z [artykułem How to SSML](../../../speech-synthesis-markup.md).

Aby rozpocząć korzystanie z SSML do dostosowywania, należy wprowadzić prostą zmianę, która przełącza głos.
Najpierw utwórz nowy plik XML dla konfiguracji SSML w katalogu głównym projektu, w tym przykładzie `ssml.xml` . Element główny jest zawsze `<speak>` , a Zawijanie tekstu w `<voice>` elemencie pozwala na zmianę głosu przy użyciu `name` parametru. Ten przykład zmienia głos na styk brytyjski (Zjednoczone Królestwo). Należy zauważyć, że ten głos jest **standardowym** głosem, który ma inne ceny i dostępność niż **neuronowych** głosów. Zapoznaj się z [pełną listą](../../../language-support.md#standard-voices) obsługiwanych głosów **standardowych** .

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Następnie musisz zmienić żądanie syntezy mowy, aby odwołać się do pliku XML. Żądanie jest w większości takie samo, ale zamiast przy użyciu `speakTextAsync()` funkcji, używasz `speakSsmlAsync()` . Ta funkcja oczekuje ciągu XML, więc najpierw należy utworzyć funkcję do załadowania pliku XML i zwrócić ją jako ciąg.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Aby uzyskać więcej informacji na temat `readFileSync` , zobacz <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js File System</a>. W tym miejscu obiekt wynik jest dokładnie taki sam jak w poprzednich przykładach.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Dane wyjściowe działają, ale wprowadzono kilka prostych dodatkowych zmian, które ułatwiają bardziej naturalny dźwięk. Ogólna szybkość mówienia jest nieco zbyt szybka, dlatego dodamy `<prosody>` znacznik i obniży szybkość do **90%** częstotliwości domyślnej. Ponadto wstrzymanie po przecinku w zdaniu jest nieco zbyt krótkie i nienaturalne. Aby rozwiązać ten problem, Dodaj `<break>` znacznik, aby opóźnić mowę i ustawić parametry czasu na **200ms**. Uruchom ponowną syntezę, aby zobaczyć, jak te dostosowania wpłynęły na dane wyjściowe.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Głosy neuronowych

Głosy neuronowych są algorytmami syntezy mowy obsługiwanymi przez głębokie sieci neuronowych. W przypadku korzystania z głosu neuronowych, synteza mowy jest niemal nieczytelna w odróżnieniu od nagrań ludzkich. Podobnie jak naturalna prosodya i wyraźny zbiór wyrazów, głosy neuronowych znacząco zmniejszają zmęczenie nasłuchiwania, gdy użytkownicy współpracują z systemami AI.

Aby przełączyć się na głos neuronowych, Zmień na `name` jedną z [opcji głosu neuronowych](../../../language-support.md#neural-voices). Następnie Dodaj przestrzeń nazw XML dla `mstts` i zawiń tekst w `<mstts:express-as>` tagu. Użyj `style` parametru, aby dostosować styl mówiący. Ten przykład używa `cheerful` , ale spróbuje ustawić `customerservice` lub, `chat` Aby zobaczyć różnicę w stylu mówiącym.

> [!IMPORTANT]
> Głosy neuronowych są obsługiwane **tylko** w przypadku zasobów mowy utworzonych w regionach *Wschodnie stany usa*, *Południowe Azja Wschodnia* i *Europa Zachodnia* .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
