---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 54ccaebd84c6af308ddcfa956add7f84b6e55832
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89321049"
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

do tego wywołania przy użyciu [hosta](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)kontenera:

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

---
