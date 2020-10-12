---
title: Wykonywanie zapytania dotyczącego punktu końcowego kontenera zamiany tekstu na mowę
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 7e5ea8dcddce31a414d983d14fba483eb388d5d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334683"
---
Kontener zapewnia [interfejsy API punktu końcowego oparte na protokole REST](../rest-text-to-speech.md). Istnieje wiele [przykładowych projektów kodu źródłowego](https://github.com/Azure-Samples/Cognitive-Speech-TTS) dla dostępnych wariantów platformy, architektury i języka.

Korzystając ze standardowych lub neuronowych kontenerów zamiany tekstu na mowę, należy zależeć od ustawień regionalnych i głosu pobranego tagu obrazu. Na przykład jeśli pobrano `latest` znacznik, domyślne ustawienia regionalne to `en-US` i `AriaRUS` głos. `{VOICE_NAME}`Następnie argument będzie [`en-US-AriaRUS`](../language-support.md#standard-voices) . Zobacz przykład SSML poniżej:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Jednak w przypadku *niestandardowej zamiany tekstu na mowę* należy uzyskać **dźwięk/model** z [niestandardowego portalu głosowego](https://aka.ms/custom-voice-portal). Niestandardowa nazwa modelu jest równoznaczna z nazwą głosu. Przejdź do strony **szkoleń** i skopiuj **dźwięk/model** , który ma być używany jako `{VOICE_NAME}` argument.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Niestandardowy model głosowy — nazwa głosu":::

Zobacz przykład SSML poniżej:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Utwórzmy żądanie HTTP POST, dostarczając kilka nagłówków i ładunek danych. Zastąp `{VOICE_NAME}` symbol zastępczy własną wartością.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>' > output.wav
```


To polecenie:

* Tworzy żądanie HTTP POST dla `speech/synthesize/cognitiveservices/v1` punktu końcowego.
* Określa `Accept` nagłówek `audio/*`
* Określa `Content-Type` nagłówek `application/ssml+xml` , aby uzyskać więcej informacji, zobacz [treść żądania](../rest-text-to-speech.md#request-body).
* Określa `X-Microsoft-OutputFormat` nagłówek `riff-16khz-16bit-mono-pcm` , aby uzyskać więcej opcji, zobacz [dane wyjściowe audio](../rest-text-to-speech.md#audio-outputs).
* Wysyła żądanie [SSML (Speech synteza Markup Language)](../speech-synthesis-markup.md) przyznany `{VOICE_NAME}` punktowi końcowemu.