---
title: Wykonywanie zapytania dotyczącego punktu końcowego kontenera zamiany tekstu na mowę
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b2a621a23a81e4fb4f47e7c99d780211973e30a0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81275233"
---
Kontener zapewnia [interfejsy API punktu końcowego oparte na protokole REST](../rest-text-to-speech.md). Istnieje wiele [przykładowych projektów kodu źródłowego](https://github.com/Azure-Samples/Cognitive-Speech-TTS) dla dostępnych wariantów platformy, architektury i języka.

Przy użyciu *standardowego kontenera zamiany tekstu na mowę* należy zależeć od ustawień regionalnych i głosu pobranego tagu obrazu. Na przykład jeśli pobrano `latest` znacznik, domyślne ustawienia regionalne to `en-US` i `JessaRUS` głos. Następnie `{VOICE_NAME}` argument będzie [`en-US-JessaRUS`](../language-support.md#standard-voices). Zobacz przykład SSML poniżej:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
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
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

To polecenie:

* Tworzy żądanie HTTP POST dla `speech/synthesize/cognitiveservices/v1` punktu końcowego.
* Określa `Accept` nagłówek`audio/*`
* Określa `Content-Type` nagłówek `application/ssml+xml`, aby uzyskać więcej informacji, zobacz [treść żądania](../rest-text-to-speech.md#request-body).
* Określa `X-Microsoft-OutputFormat` nagłówek `riff-16khz-16bit-mono-pcm`, aby uzyskać więcej opcji, zobacz [dane wyjściowe audio](../rest-text-to-speech.md#audio-outputs).
* Wysyła żądanie [SSML (Speech synteza Markup Language)](../speech-synthesis-markup.md) przyznany `{VOICE_NAME}` punktowi końcowemu.