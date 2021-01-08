---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: e9b980dcbe42694137c7b29ee3bddaa7802db3da
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97978892"
---
Aby skonfigurować zestaw Speech SDK do akceptowania danych wejściowych skompresowanego dźwięku, Utwórz `PullAudioInputStream` lub `PushAudioInputStream` . Następnie Utwórz obiekt `AudioConfig` z wystąpienia klasy strumienia, określając format kompresji strumienia.

Załóżmy, że masz klasę strumienia wejściowego o nazwie `pullStream` i używają Opus/OGG. Twój kod może wyglądać następująco:

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

// ... omitted for brevity

SpeechConfig speechConfig =
    SpeechConfig.fromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");

// Create an audio config specifying the compressed
// audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = 
    AudioStreamFormat.getCompressedFormat(
        AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig =
    AudioConfig.fromStreamInput(
        pullStream,
        audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();

String text = result.getText();
```
