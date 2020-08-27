---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: debd1eda050e75ea2b9baed45e6e5af0f5a95c46
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944439"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Zamiana mowy na tekst lub Custom Speech na tekst

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
