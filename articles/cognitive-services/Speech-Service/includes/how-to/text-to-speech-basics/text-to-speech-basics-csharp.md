---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: fa1571721653b9a3829a91bb5659f0b8216e0037
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470973"
---
W tym przewodniku szybki start przedstawiono typowe wzorce projektowania służące do wykonywania syntezy zamiany tekstu na mowę przy użyciu zestawu Speech SDK. Najpierw należy wykonać podstawowe czynności konfiguracyjne i synteza, a następnie przejść do bardziej zaawansowanych przykładów tworzenia aplikacji niestandardowych, takich jak:

* Uzyskiwanie odpowiedzi jako strumieni w pamięci
* Dostosowywanie szybkości próbkowania danych wyjściowych i szybkości transmisji bitów
* Przesyłanie żądań syntezy przy użyciu SSML (język oznaczeń syntezy mowy)
* Korzystanie z głosów neuronowych

## <a name="skip-to-samples-on-github"></a>Przejdź do przykładów w witrynie GitHub

Jeśli chcesz pominąć prosty kod przykładowy, zobacz [przykłady przewodnika Szybki Start w języku C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/text-to-speech) w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Przed wykonaniem jakichkolwiek czynności należy zainstalować zestaw Speech SDK. W zależności od platformy należy wykonać następujące instrukcje:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">PLATFORMY UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importowanie zależności

Aby uruchomić przykłady z tego artykułu, należy uwzględnić następujące `using` instrukcje w górnej części skryptu.

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu zestawu Speech SDK, należy utworzyć [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true) . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, Host lub Token autoryzacji.

> [!NOTE]
> Bez względu na to, czy wykonujesz rozpoznawanie mowy, synteza mowy, tłumaczenie czy rozpoznawanie intencji, zawsze utworzysz konfigurację.

Istnieje kilka sposobów na zainicjowanie [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true) :

* Z subskrypcją: Przekaż klucz i skojarzony region.
* Z punktem końcowym: Pass w punkcie końcowym usługi mowy. Klucz lub Token autoryzacji jest opcjonalny.
* Z hostem: Przekaż adres hosta. Klucz lub Token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: Przekaż Token autoryzacji i skojarzony region.

W tym przykładzie utworzysz [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true) przy użyciu klucza subskrypcji i regionu. Pobierz te poświadczenia, wykonując czynności opisane w sekcji [Wypróbuj bezpłatnie usługę Speech](../../../overview.md#try-the-speech-service-for-free). Utworzysz również podstawowy kod standardowy do użycia w pozostałej części tego artykułu, który można modyfikować w celu dostosowania.

```csharp
public class Program 
{
    static async Task Main()
    {
        await SynthesizeAudioAsync();
    }

    static async Task SynthesizeAudioAsync() 
    {
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Wyrównać mowę do pliku

Następnie utworzysz [`SpeechSynthesizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet&preserve-view=true) obiekt, który wykonuje konwersje zamiany tekstu na mowę i wyjście na głośniki, pliki lub inne strumienie wyjściowe. [`SpeechSynthesizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet&preserve-view=true)Akceptuje jako params [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true) obiekt utworzony w poprzednim kroku oraz [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet&preserve-view=true) obiekt, który określa sposób obsługi wyników.

Aby rozpocząć, Utwórz element, `AudioConfig` Aby automatycznie zapisywać dane wyjściowe do `.wav` pliku przy użyciu `FromWavFileOutput()` funkcji i utworzyć jej wystąpienie z `using` instrukcją. `using`Instrukcja w tym kontekście automatycznie usuwa niezarządzane zasoby i powoduje, że obiekt wykracza poza zakres po usunięciu.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
}
```

Następnie Utwórz wystąpienie `SpeechSynthesizer` z inną `using` instrukcją. Przekaż `config` obiekt i `audioConfig` obiekt jako parametry. Następnie wykonanie syntezy mowy i zapis w pliku jest tak proste jak uruchamianie `SpeakTextAsync()` z ciągiem tekstu.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
    using var synthesizer = new SpeechSynthesizer(config, audioConfig);
    await synthesizer.SpeakTextAsync("A simple test to write to a file.");
}
```

Uruchom program, a plik z syntezą `.wav` jest zapisywana w określonej lokalizacji. Jest to dobry przykład typowego użycia, ale następnym zapoznaj się z tematem dostosowywania danych wyjściowych i obsługi odpowiedzi wyjściowej jako strumienia znajdującego się w pamięci na potrzeby pracy z niestandardowymi scenariuszami.

## <a name="synthesize-to-speaker-output"></a>Synteza danych wyjściowych prezentera

W niektórych przypadkach można chcieć bezpośrednio wyprowadzać dane z głosu do osoby mówiącej. W tym celu wystarczy pominąć `AudioConfig` parametr podczas tworzenia `SpeechSynthesizer` w powyższym przykładzie. To wyjście do bieżącego aktywnego urządzenia wyjściowego.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config);
    await synthesizer.SpeakTextAsync("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Pobierz wynik jako strumień w pamięci

W przypadku wielu scenariuszy tworzenia aplikacji mowy potrzebne są wyniki danych audio jako strumień w pamięci zamiast bezpośredniego zapisywania do pliku. Pozwoli to na tworzenie zachowań niestandardowych, w tym:

* Abstrakcyjna w efekcie tablica bajtów jako strumień umożliwiający wyszukiwanie niestandardowych usług podrzędnych.
* Zintegruj wynik z innymi interfejsami API lub usługami.
* Modyfikowanie danych audio, zapisywanie niestandardowych `.wav` nagłówków itp.

Jest to proste, aby wprowadzić tę zmianę z poprzedniego przykładu. Najpierw usuń `AudioConfig` blok, ponieważ będzie można ręcznie zarządzać zachowaniem danych wyjściowych od tego momentu w celu zwiększenia kontroli. Następnie Przekaż `null` `AudioConfig` w `SpeechSynthesizer` konstruktorze. 

> [!NOTE]
> W `null` przypadku `AudioConfig` , gdy nie zostanie pominięty w powyższym przykładzie danych wyjściowych prezentera, nie będzie odtwarzany dźwięk domyślnie na bieżącym aktywnym urządzeniu wyjściowym.

Tym razem można zapisać wynik w [`SpeechSynthesisResult`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult?view=azure-dotnet&preserve-view=true) zmiennej. `AudioData`Właściwość zawiera `byte []` dane wyjściowe. Możesz korzystać z tego `byte []` ręcznie lub użyć [`AudioDataStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet&preserve-view=true) klasy do zarządzania strumieniem znajdującym się w pamięci. W tym przykładzie użyto `AudioDataStream.FromResult()` funkcji statycznej w celu uzyskania strumienia z wyniku.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var result = await synthesizer.SpeakTextAsync("Getting the response as an in-memory stream.");
    using var stream = AudioDataStream.FromResult(result);
}
```

W tym miejscu można zaimplementować dowolne zachowanie niestandardowe przy użyciu `stream` obiektu wyniku.

## <a name="customize-audio-format"></a>Dostosuj format audio

W poniższej sekcji pokazano, jak dostosować atrybuty wyjściowe audio, w tym:

* Typ pliku audio
* Szybkość próbkowania
* Bit — Głębokość

Aby zmienić format dźwięku, należy użyć `SetSpeechSynthesisOutputFormat()` funkcji dla `SpeechConfig` obiektu. Ta funkcja oczekuje `enum` typu [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet&preserve-view=true) , którego można użyć do wybrania formatu danych wyjściowych. [Listę dostępnych formatów audio](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet&preserve-view=true) można znaleźć w dokumentacji referencyjnej.

Istnieją różne opcje dla różnych typów plików, w zależności od wymagań. Należy pamiętać, że zgodnie z definicją, formaty nieprzetworzone, takie jak `Raw24Khz16BitMonoPcm` nie obejmują nagłówków audio. Używaj formatów nieprzetworzonych tylko wtedy, gdy wiesz, że wdrożenie podrzędne może zdekodować surową Bitstream lub jeśli planujesz ręczne tworzenie nagłówków na podstawie głębi bitowej, szybkości próbkowania, liczby kanałów itd.

W tym przykładzie należy określić format RIFF o wysokiej wierności, `Riff24Khz16BitMonoPcm` ustawiając `SpeechSynthesisOutputFormat` dla `SpeechConfig` obiektu. Podobnie jak w przypadku przykładu w poprzedniej sekcji, należy użyć [`AudioDataStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet&preserve-view=true) , aby uzyskać strumień w pamięci wyniku, a następnie zapisać go do pliku.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config.SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    using var synthesizer = new SpeechSynthesizer(config, null);
    var result = await synthesizer.SpeakTextAsync("Customizing audio output format.");

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

Ponowne uruchomienie programu spowoduje zapisanie `.wav` pliku w określonej ścieżce.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Użyj SSML, aby dostosować charakterystykę mowy

Język znaczników syntezy mowy (SSML) umożliwia precyzyjne dostosowanie wielkości liter, wymowy, liczby głosu i większej liczby danych wyjściowych zamiany tekstu na mowę przez przesłanie żądań ze schematu XML. W tej sekcji przedstawiono kilka praktycznych przykładów użycia, ale w celu uzyskania bardziej szczegółowego przewodnika zapoznaj się z [artykułem How to SSML](../../../speech-synthesis-markup.md).

Aby rozpocząć korzystanie z SSML do dostosowywania, należy wprowadzić prostą zmianę, która przełącza głos.
Najpierw utwórz nowy plik XML dla konfiguracji SSML w katalogu głównym projektu, w tym przykładzie `ssml.xml` . Element główny jest zawsze `<speak>` , a Zawijanie tekstu w `<voice>` elemencie pozwala na zmianę głosu przy użyciu `name` parametru. Ten przykład zmienia głos na styk brytyjski (Zjednoczone Królestwo). Należy zauważyć, że ten głos jest **standardowym** głosem, który ma inne ceny i dostępność niż **neuronowych** głosów. Zapoznaj się z [pełną listą](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices) obsługiwanych głosów **standardowych** .

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Następnie musisz zmienić żądanie syntezy mowy, aby odwołać się do pliku XML. Żądanie jest w większości takie samo, ale zamiast przy użyciu `SpeakTextAsync()` funkcji, używasz `SpeakSsmlAsync()` . Ta funkcja oczekuje ciągu XML, dlatego należy najpierw załadować konfigurację SSML jako ciąg za pomocą polecenia `File.ReadAllText()` . W tym miejscu obiekt wynik jest dokładnie taki sam jak w poprzednich przykładach.

> [!NOTE]
> Jeśli używasz programu Visual Studio, konfiguracja kompilacji prawdopodobnie nie odnajdzie pliku XML domyślnie. Aby rozwiązać ten problem, kliknij prawym przyciskiem myszy plik XML i wybierz polecenie **Właściwości**. Zmień **akcję kompilacji** na *zawartość*i zmień opcję **Kopiuj do katalogu wyjściowego** na *zawsze Kopiuj*.

```csharp
public static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var ssml = File.ReadAllText("./ssml.xml");
    var result = await synthesizer.SpeakSsmlAsync(ssml);

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
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

Aby przełączyć się na głos neuronowych, Zmień na `name` jedną z [opcji głosu neuronowych](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices). Następnie Dodaj przestrzeń nazw XML dla `mstts` i zawiń tekst w `<mstts:express-as>` tagu. Użyj `style` parametru, aby dostosować styl mówiący. Ten przykład używa `cheerful` , ale spróbuje ustawić `customerservice` lub, `chat` Aby zobaczyć różnicę w stylu mówiącym.

> [!IMPORTANT]
> Głosy neuronowych są obsługiwane **tylko** w przypadku zasobów mowy utworzonych w regionach *Wschodnie stany usa*, *Południowe Azja Wschodnia*i *Europa Zachodnia* .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
