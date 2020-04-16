---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: d91082ae3c0ae5e501675a06b02e2f55f20ce236
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399567"
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
import { readFileSync } from "fs";
import {
    AudioConfig,
    SpeechConfig,
    SpeechSynthesisOutputFormat,
    SpeechSynthesizer 
} from "microsoft-cognitiveservices-speech-sdk";
```

Aby uzyskać `import`więcej informacji na temat , zobacz <a href="https://javascript.info/import-export" target="_blank">eksport i importowanie <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

# <a name="require"></a>[Wymagają](#tab/require)

```javascript
const readFileSync = require("fs").readFileSync;
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Aby uzyskać `require`więcej informacji na temat , <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">zobacz, co jest wymagane? <span class="docon docon-navigate-external x-hidden-focus"></span></a>.


# <a name="script"></a>[Skrypt](#tab/script)

Pobierz i wyodrębnij plik <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> SDK javascript speech</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* i umieść go w folderze dostępnym dla pliku HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
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

W tym przykładzie [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) można utworzyć przy użyciu klucza subskrypcji i regionu. Zobacz stronę [pomocy technicznej regionu,](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) aby znaleźć identyfikator regionu. Można również utworzyć kilka podstawowych kod standardowy do użycia w pozostałej części tego artykułu, które można zmodyfikować dla różnych dostosowań.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Syntetyzowanie mowy do pliku

Następnie należy utworzyć [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) obiekt, który wykonuje konwersje tekstu na mowę i wyjścia do głośników, plików lub innych strumieni wyjściowych. Akceptuje [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) jako params [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) obiektu utworzonego w poprzednim [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) kroku i obiekt, który określa, jak wyniki wyjściowe powinny być obsługiwane.

Aby rozpocząć, `AudioConfig` utwórz, aby automatycznie `.wav` zapisać dane `fromAudioFileOutput()` wyjściowe do pliku przy użyciu funkcji statycznej.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Następnie wystąpienia `SpeechSynthesizer` przekazując obiekt `speechConfig` i `audioConfig` obiekt jako params. Następnie wykonywanie syntezy mowy i zapisywanie do `speakTextAsync()` pliku jest tak proste, jak uruchamianie z ciągiem tekstu. Wynik callback jest doskonałym miejscem `synthesizer.close()`do wywołania , w rzeczywistości - to wezwanie jest potrzebne, aby synteza działała poprawnie.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        }
    },
    error => {
        console.log(error);
        synthesizer.close();
    });
}
```

Uruchom program, a plik syntetyzowany `.wav` jest zapisywany w określonej lokalizacji. Jest to dobry przykład najbardziej podstawowego użycia, ale następnie spojrzeć na dostosowywanie danych wyjściowych i obsługi odpowiedzi wyjściowej jako strumień w pamięci do pracy z scenariuszy niestandardowych.

## <a name="synthesize-to-speaker-output"></a>Syntetyzowanie do wyjścia głośnika

W niektórych przypadkach można bezpośrednio wyprowadzić syntetyzowaną mowę bezpośrednio do głośnika. Aby to zrobić, smówić wystąpienie `AudioConfig` przy użyciu funkcji statycznej. `fromDefaultSpeakerOutput()` Spowoduje to wyjście do bieżącego aktywnego urządzenia wyjściowego.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
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

## <a name="get-result-as-an-in-memory-stream"></a>Uzyskaj wynik jako strumień w pamięci

W wielu scenariuszach w rozwoju aplikacji mowy prawdopodobnie potrzebujesz wynikowych danych audio jako strumienia w pamięci, a nie bezpośrednio do pliku. Pozwoli to na tworzenie zachowań niestandardowych, w tym:

* Abstrakcja wynikowej tablicy bajtów jako strumienia w zakresie wyszukiwania dla niestandardowych usług podrzędnych.
* Zintegruj wynik z innymi interfejsami API lub usługami.
* Modyfikuj dane `.wav` audio, zapisuj niestandardowe nagłówki itp.

To proste, aby to zmienić z poprzedniego przykładu. Najpierw usuń `AudioConfig` blok, ponieważ będzie zarządzać zachowanie danych wyjściowych ręcznie od tego punktu do zwiększenia kontroli. Następnie `undefined` przekazać `AudioConfig` w `SpeechSynthesizer` konstruktorze. 

> [!NOTE]
> Przechodząc `undefined` do `AudioConfig`, zamiast pomijając go jak w przykładzie wyjścia głośnika powyżej, nie będzie odtwarzać dźwięku domyślnie na bieżącym aktywnym urządzeniu wyjściowym.

Tym razem wynik jest zapisywany w zmiennej. [`SpeechSynthesisResult`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?view=azure-node-latest) Właściwość `SpeechSynthesisResult.audioData` zwraca `ArrayBuffer` dane wyjściowe. Można pracować z `ArrayBuffer` tym ręcznie.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
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

W tym miejscu można zaimplementować dowolne zachowanie niestandardowe przy użyciu wynikowego `ArrayBuffer` obiektu.

## <a name="customize-audio-format"></a>Dostosowywanie formatu audio

W poniższej sekcji pokazano, jak dostosować atrybuty wyjścia audio, w tym:

* Typ pliku audio
* Częstotliwość próbkowania
* Głębia bitowa

Aby zmienić format audio, `speechSynthesisOutputFormat` należy `SpeechConfig` użyć właściwości na obiekcie. Ta właściwość `enum` oczekuje [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest)typu , którego używasz do wyboru formatu wyjściowego. Zobacz dokumenty referencyjne, aby uzyskać [listę dostępnych formatów audio.](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest)

Istnieją różne opcje dla różnych typów plików w zależności od wymagań. Należy zauważyć, że z `Raw24Khz16BitMonoPcm` definicji formaty nieprzetworzone, takie jak nie zawierają nagłówków audio. Formaty nieprzetworzone należy używać tylko wtedy, gdy wiadomo, że implementacja niższego rzędu może dekodować nieprzetworzony strumień bitów lub jeśli planujesz ręczne tworzenie nagłówków na podstawie głębi bitowej, częstotliwości próbkowania, liczby kanałów itp.

W tym przykładzie należy określić format `Riff24Khz16BitMonoPcm` RIFF `speechSynthesisOutputFormat` o `SpeechConfig` wysokiej wierności, ustawiając na obiekcie. Podobnie jak w przykładzie w poprzedniej `ArrayBuffer` sekcji, pobierz dane audio i interakcji z nim.

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

Ponowne uruchomienie programu spowoduje `.wav` zapisanie pliku do określonej ścieżki.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Dostosowywanie charakterystyk mowy za pomocą protokołu SSML

Język znaczników syntezy mowy (SSML) umożliwia precyzyjne dostosowanie tonowania tonów, wymowy, szybkości mówienia, głośności i innych danych wyjściowych tekstu na mowę, przesyłając żądania ze schematu XML. W tej sekcji przedstawiono kilka praktycznych przykładów użycia, ale bardziej szczegółowy przewodnik można znaleźć w [artykule instrukcjowym SSML](../../../speech-synthesis-markup.md).

Aby rozpocząć korzystanie z SSML do dostosowywania, należy wprowadzić prostą zmianę, która przełącza głos.
Najpierw utwórz nowy plik XML dla konfiguracji SSML w katalogu projektu `ssml.xml`głównego, w tym przykładzie . Element główny jest `<speak>`zawsze , a `<voice>` zawijanie tekstu w elemencie pozwala zmienić głos za pomocą `name` param. W tym przykładzie zmienia głos na męski głos w języku angielskim (UK). Należy pamiętać, że ten głos jest **standardowy** głos, który ma różne ceny i dostępność niż głosy **neuronowe.** Zobacz [pełną listę](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices) obsługiwanych **standardowych** głosów.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Następnie należy zmienić żądanie syntezy mowy, aby odwołać się do pliku XML. Żądanie jest w większości takie samo, ale `speakTextAsync()` zamiast korzystać z funkcji, używasz `speakSsmlAsync()`. Ta funkcja oczekuje ciągu XML, więc najpierw utwórz funkcję, aby załadować plik XML i zwrócić go jako ciąg.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Aby uzyskać `readFileSync`więcej informacji na temat , zobacz <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js<span class="docon docon-navigate-external x-hidden-focus"></span>system plików</a>. W tym miejscu obiekt wynik jest dokładnie taki sam jak poprzednie przykłady.

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

Wyjście działa, ale istnieje kilka prostych dodatkowych zmian, które można wprowadzić, aby pomóc mu brzmieć bardziej naturalnie. Ogólna szybkość mówienia jest trochę za szybka, `<prosody>` więc dodamy tag i zmniejszymy prędkość do **90%** domyślnej szybkości. Dodatkowo pauza po przecinkom w zdaniu jest trochę za krótka i nienaturalna. Aby rozwiązać ten problem, dodaj `<break>` znacznik, aby opóźnić mowę, i ustaw param czasu na **200ms**. Uruchom ponownie syntezę, aby zobaczyć, jak te dostosowania wpłynęły na dane wyjściowe.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Głosy neuronowe

Głosy neuronowe są algorytmami syntezy mowy zasilanymi przez głębokie sieci neuronowe. Podczas korzystania z głosu nerwowego, syntetyzowane mowy jest prawie nie do odróżnienia od ludzkich nagrań. Dzięki naturalnej prozodyi i wyraźnej artykulacji słów, głosy nerwowe znacznie zmniejszają zmęczenie słuchowe, gdy użytkownicy wchodzą w interakcje z systemami Sztucznej Inteligencji.

Aby przełączyć się na głos `name` neuronowy, zmień jedną z [opcji głosu neuronowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices). Następnie dodaj obszar nazw XML dla `mstts`programu i `<mstts:express-as>` zawiń tekst w znaczniku. Użyj `style` param, aby dostosować styl mówienia. W tym `cheerful`przykładzie użyto `customerservice` , `chat` ale spróbuj go ustawienie lub zobaczyć różnicę w stylu mówienia.

> [!IMPORTANT]
> Głosy neuronowe są obsługiwane **tylko** dla zasobów mowy utworzonych w regionach *Wschodnich Stanów Zjednoczonych,* *Azji Południowo-Wschodniej*i *Europy Zachodniej.*

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
