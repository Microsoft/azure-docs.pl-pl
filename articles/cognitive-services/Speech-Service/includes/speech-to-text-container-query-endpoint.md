---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1552dda28291112a2412bdf956bc49a0b541d7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95996452"
---
Kontener udostępnia interfejsy API punktu końcowego zapytania opartego na protokole WebSocket, do których można uzyskać dostęp za pośrednictwem [zestawu Speech SDK](../index.yml). Domyślnie zestaw Speech SDK używa usług online Speech Services. Aby użyć kontenera, należy zmienić metodę inicjacji.

> [!TIP]
> W przypadku korzystania z zestawu Speech SDK z kontenerami nie trzeba podawać klucza subskrypcji zasobów usługi Azure Speech [ani tokenu okaziciela uwierzytelniania](../rest-speech-to-text.md#authentication).

Zobacz poniższe przykłady.

# <a name="c"></a>[C#](#tab/csharp)

Zmień użycie tego wywołania inicjalizacji Azure-Cloud:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Aby użyć tego wywołania z [hostem](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?preserve-view=true&view=azure-dotnet)kontenera:

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# <a name="python"></a>[Python](#tab/python)

Zmień użycie tego wywołania inicjalizacji Azure-Cloud:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

Aby użyć tego wywołania z [punktem końcowym](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python)kontenera:

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---