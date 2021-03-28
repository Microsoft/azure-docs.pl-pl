---
title: Jak uzyskać zdarzenia ułożenia twarzy dla programu lip-sync
titleSuffix: Azure Cognitive Services
description: Zestaw Speech SDK obsługuje zdarzenie viseme w syntezie mowy, które są używane do reprezentowania kluczowych zaobserwowanych mowy, takich jak pozycja pakietów lip, szczęki i języka podczas tworzenia konkretnego fonemu.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: e97c48d4e42627d0fc2caaa4f66e81b9a0cafa86
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643892"
---
# <a name="get-facial-pose-events"></a>Pobierz zdarzenia ułożenia twarzy

> [!NOTE]
> Viseme działa tylko dla `en-US-AriaNeural` głosu.

Viseme to wizualny opis elementu fonem w języku mówionym.
Definiuje położenie czołowej i jamy ustnej podczas wymawiania wyrazu.
Każdy viseme przedstawia kluczowe ułożenie twarzy dla określonego zestawu fonemów.
Nie ma żadnej relacji jeden-do-jednego między visemes i fonemów.
Często kilka fonemów odpowiada pojedynczej viseme, tak jak kilka fonemów wyglądać tak samo na twarz, na przykład `s` i `z` .
Zobacz [tabelę mapowania między visemes i fonemów](#map-phonemes-to-visemes).

Za pomocą visemes można utworzyć bardziej naturalny i inteligentny asystenta rozgłaszania wiadomości, bardziej interaktywne gry i kreskówki oraz bardziej intuicyjne wideo nauczania. Osoby z upośledzeniem słuchu mogą również wybierać dźwięki wizualnie i zawartość mowy "lip-Read", która przedstawia visemes na animowanej platformie.

## <a name="get-viseme-events-with-the-speech-sdk"></a>Pobieranie zdarzeń viseme za pomocą zestawu Speech SDK

Aby utworzyć zdarzenia viseme, Konwertujemy tekst wejściowy na zestaw sekwencji fonem i odpowiadających im sekwencji viseme. Oceniamy czas rozpoczęcia każdego visemeu w audio mowy. Zdarzenia viseme zawierają sekwencję identyfikatorów viseme, z których każdy jest przesunięty do audio, gdzie pojawia się ten viseme. Zdarzenia te mogą prowadzić do animacji, które symulują osobę mówiącą, że tekst wejściowy.

| Parametr | Opis |
|-----------|-------------|
| Identyfikator viseme | Liczba całkowita określająca viseme. W języku angielskim (Stany Zjednoczone) Oferujemy 22 różne visemes, które przedstawiają kształty dla określonego zestawu fonemów. Zobacz [tabelę mapowania między identyfikatorami viseme i fonemów](#map-phonemes-to-visemes).  |
| Przesunięcie audio | Godzina rozpoczęcia każdego visemeu w taktach (100 nanosekund). |

Aby uzyskać viseme zdarzenia, zasubskrybuj `VisemeReceived` zdarzenie w zestawie mowy SDK.
Poniższe fragmenty kodu pokazują, jak subskrybować zdarzenie viseme.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

## <a name="map-phonemes-to-visemes"></a>Mapowanie fonemów do visemes

Visemes różni się w zależności od języka. Każdy język ma zestaw visemes, który odpowiada jego konkretnemu fonemów. W poniższej tabeli przedstawiono zgodność międzynarodowych alfabetów fonetycznych (IPA) fonemów i identyfikatorów viseme dla języka angielskiego (Stany Zjednoczone).

| IPA | Przykład | Identyfikator viseme |
|-----|---------|-----------|
| mogę   | **EA** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **te** | 4 |
| ɛ | **e** bardzo | 4 |
|æ  |   **ktywny**        |1|
|ɑ  |   **o** bstinate     |2|
|ɔ  |   c —**au** SE         |3|
|ʊ  |   b **oo** k          |4|
|oʊ |   **o** LD           |8|
|u  |   **U**          |7|
|ʌ  |   **u** ncle         |1|
|aɪ |   **i** CE           |11|
|aʊ |   **jednostka organizacyjna** t           |9|
|ɔɪ |   **Oi** l           |10|
|ju |   **Yu** ma          |[6, 7]|
|ə  |   **a** go           |1|
|ɪɹ |   **Przeczyść** s          |[6, 13]|
|ɛɹ |   Płaszczyzna **powietrzna**      |[4, 13]|
|ʊɹ |   c          |[4, 13]|
|aɪ(ə)ɹ |   **Wygasnąć** ziemi   |[11, 13]|
|aʊ(ə)ɹ |   **godz**. s     |[9, 13]|
|ɔɹ |   **lub** akresu        |[3, 13]|
|ɑɹ |   **AR** TIST        |[2, 13]|
|ɝ  |   **odgięty**         |[5, 13]|
|ɚ  |   wszystkie Gy **er**       |[1, 13]|
|w  |   **w** itd **. de**   |7|
|j  |   **t** ARD, f **e** w     |6|
|p  |   **p** UT           |21|
|b  |   **b** IG           |21|
|t  |   ALK **t**          |19|
|d  |   **d** IG           |19|
|k  |   **c** UT           |20|
|g  |   **g** o            |20|
|m  |   **m** w, s **-** Popioły    |21|
|n  |   **n** o, s **n**      |19|
|ŋ  |   li **n** k          |20|
|f  |   eć **f**          |18|
|v  |   **v** RG         |18|
|θ  |   **th** w          |17|
|ð  |   **th**          |17|
|s  |   **s**           |15|
|z  |   **z** AP           |15|
|ʃ  |   **SH** e           |16|
|ʒ  |   Acques **J**       |16|
|h  |   ELP **h**          |12|
|tʃ |   **ch** w          |16|
|dʒ |   Oy- **j**           |16|
|l  |   Identyfikator **l****, g g AD**     |14|
|ɹ  |   **r** Ed, b **r** owanie    |13|


## <a name="next-steps"></a>Następne kroki

* [Dokumentacja referencyjna zestawu Speech SDK](speech-sdk.md)
