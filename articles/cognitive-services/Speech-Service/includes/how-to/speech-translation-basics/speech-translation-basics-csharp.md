---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 798db8e438a78fb818bd0af18f84c87c1f9ceb84
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105743"
---
Jedną z podstawowych funkcji usługi mowy jest możliwość rozpoznawania mowy przez człowieka i przetłumaczenia jej na inne języki. W tym przewodniku szybki start dowiesz się, jak używać zestawu Speech SDK w aplikacjach i produktach do wykonywania translacji mowy o wysokiej jakości. Ten przewodnik Szybki Start obejmuje następujące tematy:

* Tłumaczenie zamiany mowy na tekst
* Tłumaczenie mowy na wiele języków docelowych
* Wykonywanie bezpośredniego tłumaczenia mowy na mowę

## <a name="skip-to-samples-on-github"></a>Przejdź do przykładów w witrynie GitHub

Jeśli chcesz pominąć prosty kod przykładowy, zobacz [przykłady przewodnika Szybki Start w języku C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/translate-speech-to-text) w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Przed wykonaniem jakichkolwiek czynności należy zainstalować zestaw Speech SDK. W zależności od używanej platformy postępuj zgodnie z instrukcjami w sekcji <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">pobieranie zestawu Speech SDK</a> w artykule _Informacje o zestawie mowy SDK_ .

## <a name="import-dependencies"></a>Importowanie zależności

Aby uruchomić przykłady z tego artykułu, należy uwzględnić następujące `using` instrukcje w górnej części pliku *program. cs* .

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

## <a name="sensitive-data-and-environment-variables"></a>Dane poufne i zmienne środowiskowe

Przykładowy kod źródłowy opisany w tym artykule zależy od zmiennych środowiskowych w celu przechowywania poufnych danych, takich jak klucz subskrypcji zasobów mowy i region. `Program`Klasa zawiera dwie `static readonly string` wartości, które są przypisane ze zmiennych środowiskowych komputerów-hostów, `SPEECH__SUBSCRIPTION__KEY` a mianowicie i `SPEECH__SERVICE__REGION` . Oba te pola znajdują się w zakresie klasy, dzięki czemu są dostępne w treści metody klasy. Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zobacz [zmienne środowiskowe i konfiguracja aplikacji](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

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

Aby wywołać usługę mowy przy użyciu zestawu Speech SDK, należy utworzyć [`SpeechTranslationConfig`][config] . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, Host lub Token autoryzacji.

> [!TIP]
> Bez względu na to, czy wykonujesz rozpoznawanie mowy, synteza mowy, tłumaczenie czy rozpoznawanie intencji, zawsze utworzysz konfigurację.

Istnieje kilka sposobów na zainicjowanie [`SpeechTranslationConfig`][config] :

* Z subskrypcją: Przekaż klucz i skojarzony region.
* Z punktem końcowym: Pass w punkcie końcowym usługi mowy. Klucz lub Token autoryzacji jest opcjonalny.
* Z hostem: Przekaż adres hosta. Klucz lub Token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: Przekaż Token autoryzacji i skojarzony region.

Przyjrzyjmy się w jaki sposób [`SpeechTranslationConfig`][config] jest tworzony przy użyciu klucza i regionu. Pobierz te poświadczenia, wykonując czynności opisane w sekcji [Wypróbuj bezpłatnie usługę Speech](../../../overview.md#try-the-speech-service-for-free).

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

## <a name="change-source-language"></a>Zmień język źródłowy

Jednym z typowych zadań tłumaczenia mowy jest określenie języka danych wejściowych (lub źródłowych). Przyjrzyjmy się sposobom zmiany języka wejściowego na włoski. W kodzie można korzystać z [`SpeechTranslationConfig`][config] wystąpienia, przypisując do `SpeechRecognitionLanguage` właściwości.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

[`SpeechRecognitionLanguage`][recognitionlang]Właściwość oczekuje ciągu formatu ustawień regionalnych. Na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md)można podać dowolną wartość w kolumnie **Ustawienia regionalne** .

## <a name="add-translation-language"></a>Dodawanie języka tłumaczenia

Innym typowym zadaniem tłumaczenia mowy jest określenie docelowych języków tłumaczenia, ale co najmniej jeden jest wymagany, ale obsługiwane są różne. Poniższy fragment kodu ustawia francuski i niemiecki jako cele języka tłumaczenia.

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

W przypadku każdego wywołania do programu [`AddTargetLanguage`][addlang] określony jest nowy docelowy język tłumaczenia. Innymi słowy, gdy rozpoznawanie mowy jest rozpoznawane w języku źródłowym, każde docelowe tłumaczenie jest dostępne jako część wyniku operacji tłumaczenia.

## <a name="initialize-a-translation-recognizer"></a>Inicjowanie aparatu rozpoznawania tłumaczenia

Po utworzeniu [`SpeechTranslationConfig`][config] , następnym krokiem jest zainicjowanie [`TranslationRecognizer`][recognizer] . Po zainicjowaniu elementu należy [`TranslationRecognizer`][recognizer] przekazać go `translationConfig` . Obiekt Configuration zawiera poświadczenia wymagane przez usługę mowy do zweryfikowania Twojego żądania.

Jeśli rozpoznajesz mowę przy użyciu domyślnego mikrofonu urządzenia, Oto jak [`TranslationRecognizer`][recognizer] powinien wyglądać:

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

Jeśli chcesz określić urządzenie wejściowe audio, należy utworzyć [`AudioConfig`][audioconfig] i podać `audioConfig` parametr podczas inicjowania [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Dowiedz się, jak uzyskać identyfikator urządzenia dla wejściowego urządzenia audio](../../../how-to-select-audio-input-devices.md).

Najpierw należy odwołać się do `AudioConfig` obiektu w następujący sposób:

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

Jeśli chcesz podać plik audio zamiast używać mikrofonu, nadal musisz podać `audioConfig` . Jednak podczas tworzenia [`AudioConfig`][audioconfig] , zamiast wywoływania `FromDefaultMicrophoneInput` , należy wywołać `FromWavFileInput` i przekazać `filename` parametr.

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

Aby tłumaczyć mowę, zestaw Speech SDK polega na użyciu mikrofonu lub wejściowego pliku audio. Rozpoznawanie mowy występuje przed tłumaczeniem mowy. Po zainicjowaniu wszystkich obiektów wywołaj funkcję rozpoznawania-raz i uzyskaj wynik.

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

Aby uzyskać więcej informacji na temat zamiany mowy na tekst, zobacz [podstawy rozpoznawania mowy](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Synteza tłumaczeń

Po pomyślnym przeprowadzeniu rozpoznawania mowy i tłumaczeniu, wynik zawiera wszystkie tłumaczenia w słowniku. [`Translations`][translations]Klucz słownika to docelowy język tłumaczenia, a wartość to przetłumaczony tekst. Rozpoznaną mowę można przetłumaczyć, a następnie wyróżnić w innym języku (zamiana mowy na mowę).

### <a name="event-based-synthesis"></a>Synteza oparta na zdarzeniach

`TranslationRecognizer`Obiekt ujawnia `Synthesizing` zdarzenie. Zdarzenie zostanie wyzwolone kilka razy i udostępnia mechanizm umożliwiający pobranie dźwięku z tłumaczenia z wyniku rozpoznawania. W przypadku tłumaczenia na wiele języków, zobacz [synteza ręczna](#manual-synthesis). Określ głos syntezy, przypisując [`VoiceName`][voicename] i podając procedurę obsługi zdarzeń dla `Synthesizing` zdarzenia, Pobierz dźwięk. Poniższy przykład zapisuje przetłumaczony dźwięk jako plik *. wav* .

> [!IMPORTANT]
> Synteza oparta na zdarzeniach działa tylko w przypadku jednego tłumaczenia **, nie należy dodawać** wielu docelowych języków tłumaczenia. Ponadto [`VoiceName`][voicename] powinien być to ten sam język, w którym znajduje się docelowy język tłumaczenia, na przykład `"de"` może być mapowany na `"de-DE-Hedda"` .

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

### <a name="manual-synthesis"></a>Synteza ręczna

[`Translations`][translations]Słownik może służyć do syntezowania dźwięku z tekstu tłumaczenia. Wykonaj iterację każdego tłumaczenia i wytłumacz tłumaczenie. Podczas tworzenia `SpeechSynthesizer` wystąpienia `SpeechConfig` obiekt musi mieć [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] ustawioną właściwość na żądany głos. W poniższym przykładzie przekładają się na pięć języków, a każde tłumaczenie jest następnie wydane do pliku audio w odpowiednim języku neuronowych.

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

Aby uzyskać więcej informacji na temat syntezy mowy, zobacz [podstawy syntezy mowy](../../../get-started-text-to-speech.md).

[config]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig
[audioconfig]: /dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig
[recognizer]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage
[addlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage
[translations]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations
[voicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename
[speechsynthesisvoicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename