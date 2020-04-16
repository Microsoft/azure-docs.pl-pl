---
title: Wybieranie trybu rozpoznawania mowy za pomocą SDK mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wybrać najlepszy tryb rozpoznawania podczas korzystania z SDK mowy.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402158"
---
# <a name="choose-a-speech-recognition-mode"></a>Wybieranie trybu rozpoznawania mowy

Rozważając operacje rozpoznawania mowy na tekst, [zestaw SDK mowy](speech-sdk.md) udostępnia wiele trybów przetwarzania mowy. Koncepcyjnie, czasami nazywany *tryb rozpoznawania*. W tym artykule porównano różne tryby rozpoznawania.

## <a name="recognize-once"></a>Rozpoznaj raz

Jeśli chcesz przetworzyć każdy wypowiedź po jednym "zdaniu" naraz, użyj funkcji "rozpoznaj raz". Ta metoda wykryje rozpoznaną wypowiedź z danych wejściowych, począwszy od początku wykrytej mowy do następnej pauzy. Zazwyczaj pauza oznacza koniec zdania lub wiersza myśli.

Na końcu jednego rozpoznanego wypowiedź usługa zatrzymuje przetwarzania dźwięku z tego żądania. Maksymalny limit rozpoznawania to czas trwania zdania wynoszący 20 sekund.

::: zone pivot="programming-language-csharp"

Aby uzyskać więcej `RecognizeOnceAsync` informacji na temat korzystania z tej funkcji, zobacz [dokumenty SDK mowy .NET](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Aby uzyskać więcej `RecognizeOnceAsync` informacji na temat korzystania z tej funkcji, zobacz [dokumenty SDK mowy języka C++.](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync)

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Aby uzyskać więcej `recognizeOnceAsync` informacji na temat korzystania z tej funkcji, zobacz [dokumenty Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable).

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Aby uzyskać więcej `recognize_once` informacji na temat korzystania z tej funkcji, zobacz [dokumenty SDK mowy języka Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult).

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

Aby uzyskać dodatkowe języki, zobacz [dokumenty odwołania do SDK mowy](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="continuous"></a>Ciągłe

Jeśli potrzebujesz długotrwałego rozpoznawania, użyj funkcji start i odpowiadające im do ciągłego rozpoznawania. Funkcja start rozpocznie się i będzie kontynuować przetwarzanie wszystkich wypowiedzi, dopóki nie wywołasz funkcji zatrzymania lub dopóki nie minie zbyt dużo czasu w ciszy. Korzystając z trybu ciągłego, należy zarejestrować się do różnych zdarzeń, które będą uruchamiane po wystąpieniu. Na przykład zdarzenie "rozpoznane" jest uruchamiane w przypadku rozpoznawania mowy. Musisz mieć program obsługi zdarzeń w miejscu do obsługi rozpoznawania.

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

Aby uzyskać dodatkowe języki, zobacz [dokumenty odwołania do SDK mowy](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="dictation"></a>Dyktowania

Korzystając z ciągłego rozpoznawania, można włączyć przetwarzanie dyktowania za pomocą odpowiedniej funkcji "włącz dyktowanie". Ten tryb spowoduje, że wystąpienie konfiguracji mowy będzie interpretować opisy wyrazów struktur zdań, takich jak znaki interpunkcyjne. Na przykład wypowiedź "Czy mieszkasz w znaku zapytania miasta" będzie interpretowana jako tekst "Czy mieszkasz w mieście?".

::: zone pivot="programming-language-csharp"

Aby uzyskać więcej `EnableDictation` informacji na temat korzystania z tej funkcji, zobacz [dokumenty SDK mowy .NET](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Aby uzyskać więcej `EnableDictation` informacji na temat korzystania z tej funkcji, zobacz [dokumenty SDK mowy języka C++.](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation)

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Aby uzyskać więcej `enableDictation` informacji na temat korzystania z tej funkcji, zobacz [dokumenty Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable).

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Aby uzyskać więcej `enable_dictation` informacji na temat korzystania z tej funkcji, zobacz [dokumenty SDK mowy języka Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--).

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

Aby uzyskać dodatkowe języki, zobacz [dokumenty odwołania do SDK mowy](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj dodatkowe przykłady zestawów SDK mowy w usłudze GitHub](https://aka.ms/csspeech/samples)
