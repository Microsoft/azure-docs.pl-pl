---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: bac2ed447c9055f095e604725591c487378f5091
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399635"
---
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [wypróbuj bezpłatną usługę Mowy.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Zanim będzie można coś zrobić, musisz zainstalować pakiet SDK mowy. W zależności od platformy postępuj zgodnie z instrukcjami w sekcji <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Pobierz zestaw <span class="docon docon-navigate-external x-hidden-focus"></span> SDK mowy</a> w artykule Zestaw SDK mowy.

## <a name="import-dependencies"></a>Importowanie zależności

Aby uruchomić przykłady w tym artykule, należy dołączyć następujące `using` instrukcje w górnej części pliku *Program.cs.*

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Dane wrażliwe i zmienne środowiskowe

Przykładowy kod źródłowy w tym artykule zależy od zmiennych środowiskowych do przechowywania poufnych danych, takich jak klucz subskrypcji zasobów mowy i regionu. Klasa `Program` zawiera `static readonly string` dwie wartości, które są przypisane z maszyn `SPEECH__SUBSCRIPTION__KEY` `SPEECH__SERVICE__REGION`hosta zmiennych środowiskowych, a mianowicie i . Oba te pola znajdują się w zakresie klasy, dzięki czemu są dostępne w obrębie treści metody klasy. Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zobacz [zmienne środowiskowe i konfiguracja aplikacji](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
}
```

## <a name="create-a-speech-translation-configuration"></a>Tworzenie konfiguracji tłumaczenia mowy

Aby wywołać usługę mowy przy użyciu SDK [`SpeechTranslationConfig`][config]mowy, należy utworzyć plik . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, host lub token autoryzacji.

> [!TIP]
> Niezależnie od tego, czy wykonujesz rozpoznawanie mowy, syntezę mowy, tłumaczenie czy rozpoznawanie intencji, zawsze tworzysz konfigurację.

Istnieje kilka sposobów, które można [`SpeechTranslationConfig`][config]zainicjować:

* Z subskrypcją: przekaż klucz i skojarzony region.
* Z punktem końcowym: przekazać w punkcie końcowym usługi mowy. Klucz lub token autoryzacji jest opcjonalny.
* Z hostem: przekaż adres hosta. Klucz lub token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: przekaż w tokenie autoryzacji i skojarzonym regionie.

Przyjrzyjmy się, [`SpeechTranslationConfig`][config] jak jest tworzony przy użyciu klucza i regionu. Zobacz stronę [pomocy technicznej regionu,](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) aby znaleźć identyfikator regionu.

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Zmienianie języka źródłowego

Jednym z typowych zadań tłumaczenia mowy jest określenie języka wejściowego (lub źródłowego). Przyjrzyjmy się, jak można zmienić język wprowadzania na włoski. W kodzie interakcji [`SpeechTranslationConfig`][config] z wystąpieniem, `SpeechRecognitionLanguage` przypisując do właściwości.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

Właściwość [`SpeechRecognitionLanguage`][recognitionlang] oczekuje ciąg formatu ustawień regionalnych języka. Dowolną wartość można podać w kolumnie **Ustawienia regionalne** na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md).

## <a name="add-translation-language"></a>Dodawanie języka tłumaczenia

Innym typowym zadaniem tłumaczenia mowy jest określenie docelowych języków tłumaczenia, co najmniej jeden jest wymagany, ale obsługiwane są wielokrotności. We wliczeniu kodu: docelowych języka tłumaczeniowego zarówno w języku francuskim, jak i niemieckim.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

Przy każdym [`AddTargetLanguage`][addlang]wywołaniu określono nowy docelowy język tłumaczenia. Innymi słowy, gdy mowa jest rozpoznawana z języka źródłowego, każde tłumaczenie docelowe jest dostępne jako część wynikowej operacji tłumaczenia.

## <a name="initialize-a-translation-recognizer"></a>Inicjowanie aparatu tłumaczenia

Po utworzeniu przycisku [`SpeechTranslationConfig`][config], następnym krokiem [`TranslationRecognizer`][recognizer]jest zainicjowanie pliku . Podczas inicjowania [`TranslationRecognizer`][recognizer], musisz przekazać go `translationConfig`. Obiekt konfiguracji zawiera poświadczenia, które usługa mowy wymaga, aby sprawdzić poprawność żądania.

Jeśli rozpoznajesz mowę przy użyciu domyślnego mikrofonu urządzenia, oto jak [`TranslationRecognizer`][recognizer] powinno wyglądać:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

Jeśli chcesz określić urządzenie wejściowe audio, musisz utworzyć [`AudioConfig`][audioconfig] i `audioConfig` podać parametr podczas [`TranslationRecognizer`][recognizer]inicjowania pliku .

> [!TIP]
> [Dowiedz się, jak uzyskać identyfikator urządzenia dla urządzenia wejściowego audio](../../../how-to-select-audio-input-devices.md).

Najpierw odwołujesz się `AudioConfig` do obiektu w następujący sposób:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

Jeśli chcesz podać plik audio zamiast mikrofonu, nadal musisz podać plik `audioConfig`. Jednak podczas tworzenia [`AudioConfig`][audioconfig], zamiast wywoływania, `FromDefaultMicrophoneInput`wywołasz `FromWavFileInput` i `filename` przekażesz parametr.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Tłumaczenie mowy

Aby przetłumaczyć mowę, zestaw SDK mowy opiera się na wejściu mikrofonu lub pliku audio. Rozpoznawanie mowy występuje przed tłumaczeniem mowy. Po zainicjowaniu wszystkich obiektów wywołaj funkcję rozpoznawania i uzyskaj wynik.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

Aby uzyskać więcej informacji na temat zamiany mowy na tekst, zobacz [podstawy rozpoznawania mowy](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Syntetyzowanie tłumaczeń

Po pomyślnym rozpoznawaniu mowy i tłumaczeniu wynik zawiera wszystkie tłumaczenia w słowniku. Klucz [`Translations`][translations] słownika jest językiem tłumaczenia docelowego, a wartością jest przetłumaczony tekst. Rozpoznaną mowę można przetłumaczyć, a następnie zsyntetyzować w innym języku (mowa na mowę).

### <a name="event-based-synthesis"></a>Synteza oparta na zdarzeniach

Obiekt `TranslationRecognizer` udostępnia `Synthesizing` zdarzenie. Zdarzenie jest uruchamiane kilka razy i zapewnia mechanizm pobierania syntetyzowanego dźwięku z wyniku rozpoznawania tłumaczenia. Jeśli tłumaczysz na wiele języków, zobacz [ręczna synteza](#manual-synthesis). Określ głos syntezy, przypisując [`VoiceName`][voicename] i `Synthesizing` zapewnij program obsługi zdarzeń dla zdarzenia, pobierz dźwięk. Poniższy przykład zapisuje przetłumaczony dźwięk jako plik *wav.*

> [!IMPORTANT]
> Synteza oparta na zdarzeniach działa tylko z jednym tłumaczeniem, **nie** należy dodawać wielu języków tłumaczenia docelowego. Ponadto [`VoiceName`][voicename] powinien być tym samym językiem co język tłumaczenia docelowego, na przykład; `"de"` może być `"de-DE-Hedda"`mapowana na .

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>Ręczna synteza

Słownik [`Translations`][translations] może służyć do syntezy dźwięku z tekstu tłumaczenia. Iteracja każdego tłumaczenia i syntetyzowanie tłumaczenia. Podczas tworzenia `SpeechSynthesizer` wystąpienia `SpeechConfig` obiekt musi mieć [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] jego właściwość ustawioną na żądany głos. Poniższy przykład tłumaczy na pięć języków, a każde tłumaczenie jest następnie syntetyzowane do pliku audio w odpowiednim języku neuronowym.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

Aby uzyskać więcej informacji na temat syntezy mowy, zobacz [podstawy syntezy mowy](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig?view=azure-dotnet
[audioconfig]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet
[recognizer]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer?view=azure-dotnet
[recognitionlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet
[addlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage?view=azure-dotnet
[translations]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations?view=azure-dotnet
[voicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename?view=azure-dotnet
[speechsynthesisvoicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename?view=azure-dotnet
