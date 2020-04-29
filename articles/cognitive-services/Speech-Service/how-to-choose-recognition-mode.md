---
title: Wybieranie trybu rozpoznawania mowy przy użyciu zestawu Speech SDK
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wybrać najlepszy tryb rozpoznawania przy użyciu zestawu Speech SDK.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5fdca371e9188ef69068ddbcaa416cbb2b44054c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402158"
---
# <a name="choose-a-speech-recognition-mode"></a>Wybierz tryb rozpoznawania mowy

Gdy rozważasz operacje rozpoznawania zamiany mowy na tekst, [zestaw mowy SDK](speech-sdk.md) udostępnia wiele trybów przetwarzania mowy. Koncepcyjnie, czasami nazywane *trybem rozpoznawania*. W tym artykule porównano różne tryby rozpoznawania.

## <a name="recognize-once"></a>Rozpoznaj raz

Jeśli chcesz przetwarzać każdy wypowiedź jeden "zdanie" w czasie, użyj funkcji "Rozpoznaj raz". Ta metoda wykrywa rozpoznane wypowiedź z danych wejściowych, rozpoczynając od początku wykrytej mowy do następnego wstrzymania. Zwykle pauza oznacza koniec zdania lub linii.

Po zakończeniu jednego rozpoznanego wypowiedź usługa przerywa przetwarzanie dźwięku z tego żądania. Maksymalny limit rozpoznawania to czas trwania zdania wynoszący 20 sekund.

::: zone pivot="programming-language-csharp"

Aby uzyskać więcej informacji na temat `RecognizeOnceAsync` korzystania z funkcji, zobacz dokumentację [zestawu Speech SDK dla platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Aby uzyskać więcej informacji na temat `RecognizeOnceAsync` korzystania z funkcji, zobacz [Dokumentacja zestawu SDK języka C++](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync).

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Aby uzyskać więcej informacji na temat `recognizeOnceAsync` korzystania z funkcji, zobacz dokumentację [języka Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable).

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Aby uzyskać więcej informacji na temat `recognize_once` korzystania z funkcji, zobacz dokumentację [zestawu Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult)dla języka Python.

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

Dodatkowe języki można znaleźć w [dokumentacji referencyjnej dotyczącej zestawu Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="continuous"></a>Ciągłe

Jeśli potrzebujesz długotrwałego rozpoznawania, użyj funkcji Start i odpowiednich zatrzymywania do ciągłego rozpoznawania. Funkcja Start zostanie uruchomiona i kontynuuje przetwarzanie wszystkich wyrażenia długości do momentu wywołania funkcji zatrzymania lub do czasu przekroczenia zbyt dużo czasu. W przypadku korzystania z trybu ciągłego należy zadbać o zarejestrowanie się w różnych zdarzeniach, które będą uruchamiane po wystąpieniu. Na przykład zdarzenie "rozpoznane" wyzwalane w przypadku wystąpienia funkcji rozpoznawania mowy. Musisz mieć procedurę obsługi zdarzeń w celu obsługi rozpoznawania.

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end
::: zone pivot="programming-language-more"

Dodatkowe języki można znaleźć w [dokumentacji referencyjnej dotyczącej zestawu Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="dictation"></a>Dyktowanie

W przypadku korzystania z ciągłego rozpoznawania można włączyć przetwarzanie dyktowania przy użyciu odpowiedniej funkcji "Włącz dyktowanie". Ten tryb spowoduje, że wystąpienie konfiguracji mowy interpretuje opisy wyrazów struktur zdań, takich jak interpunkcja. Na przykład "wypowiedź" czy "czy" na żywo "jest interpretowany jako tekst" czy jesteś w mieście? ".

::: zone pivot="programming-language-csharp"

Aby uzyskać więcej informacji na temat `EnableDictation` korzystania z funkcji, zobacz dokumentację [zestawu Speech SDK dla platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Aby uzyskać więcej informacji na temat `EnableDictation` korzystania z funkcji, zobacz [Dokumentacja zestawu SDK języka C++](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation).

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Aby uzyskać więcej informacji na temat `enableDictation` korzystania z funkcji, zobacz dokumentację [języka Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable).

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Aby uzyskać więcej informacji na temat `enable_dictation` korzystania z funkcji, zobacz dokumentację [zestawu Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--)dla języka Python.

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

Dodatkowe języki można znaleźć w [dokumentacji referencyjnej dotyczącej zestawu Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj dodatkowe przykłady zestawu Speech SDK w witrynie GitHub](https://aka.ms/csspeech/samples)
