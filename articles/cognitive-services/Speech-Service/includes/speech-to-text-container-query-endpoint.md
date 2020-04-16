---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1773e22a54cc86e7736c91e4be757caa0250cbf7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422063"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Zamiana mowy na tekst lub niestandardowa zamiana mowy na tekst

Kontener udostępnia interfejsy API punktu końcowego programu query oparte na websocket, które są dostępne za pośrednictwem [SDK mowy.](../index.yml) Domyślnie SDK mowy używa usług mowy online. Aby użyć kontenera, należy zmienić metodę inicjowania.

> [!TIP]
> Podczas korzystania z zestawu SDK mowy z kontenerami nie trzeba podawać klucza subskrypcji zasobu usługi Azure Speech [ani tokenu nośnego uwierzytelniania.](../rest-speech-to-text.md#authentication)

Zobacz poniższe przykłady.

# <a name="c"></a>[C #](#tab/csharp)

Zmień z przy użyciu tego wywołania inicjowania chmury platformy Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

do tego wywołania przy użyciu [hosta kontenera:](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="python"></a>[Python](#tab/python)

Zmień z przy użyciu tego wywołania inicjowania chmury platformy Azure:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

do tego wywołania przy użyciu [hosta kontenera:](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
