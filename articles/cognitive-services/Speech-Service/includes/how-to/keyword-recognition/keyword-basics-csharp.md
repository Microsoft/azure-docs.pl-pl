---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: c770ca3d619ea443fd5a320f91f3bfae56732bac
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792402"
---
Najpierw załaduj plik modelu słowa kluczowego przy użyciu `FromFile()` funkcji statycznej, która zwraca `KeywordRecognitionModel` . Użyj ścieżki do `.table` pliku pobranego z programu Speech Studio. Ponadto tworzysz `AudioConfig` przy użyciu domyślnego mikrofonu, a następnie tworzymy nowe wystąpienie `KeywordRecognizer` przy użyciu konfiguracji audio.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Następnie uruchamianie rozpoznawania słów kluczowych odbywa się przy użyciu jednego wywołania do `RecognizeOnceAsync()` przez przekazanie obiektu modelu. Spowoduje to uruchomienie sesji rozpoznawania słowa kluczowego, która obowiązuje do momentu rozpoznania słowa kluczowego. Z tego względu Wzorzec projektowy jest używany w aplikacjach wielowątkowych lub w przypadkach użycia, w których można oczekiwać na zakończenie wyciągania.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> W poniższym przykładzie zastosowano funkcję lokalnego rozpoznawania słów kluczowych, ponieważ nie wymaga ona `SpeechConfig` obiektu dla kontekstu uwierzytelniania i nie kontaktuje się z zapleczem. Można jednak uruchomić rozpoznawanie słów kluczowych i weryfikację przy [użyciu bezpośredniego połączenia zaplecza](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword).

### <a name="continuous-recognition"></a>Ciągłe rozpoznawanie

Inne klasy w zestawie mowy SDK obsługują stałe rozpoznawanie (zarówno w przypadku rozpoznawania mowy, jak i przeznaczenie) przy użyciu rozpoznawania słowa kluczowego. Pozwala to na użycie tego samego kodu, który zwykle jest używany do ciągłego rozpoznawania, z możliwością odwoływania się do `.table` pliku dla modelu słowa kluczowego.

W przypadku zamiany mowy na tekst postępuj zgodnie z tym samym wzorcem projektu przedstawionym w [przewodniku szybki start](../../../get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=script%2cbrowser%2cwindowsinstall#continuous-recognition) , aby skonfigurować ciągłe rozpoznawanie. Następnie zastąp wywołanie do `recognizer.StartContinuousRecognitionAsync()` `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` i przekaż `KeywordRecognitionModel` obiekt. Aby zatrzymać ciągłe rozpoznawanie przy użyciu słowa kluczowego wykrywania trendów, użyj `recognizer.StopKeywordRecognitionAsync()` zamiast `recognizer.StopContinuousRecognitionAsync()` .

Funkcja rozpoznawania intencji używa tego samego wzorca [`StartKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) z [`StopKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) funkcjami i.