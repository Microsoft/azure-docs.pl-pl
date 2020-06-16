---
title: Regiony — usługa mowy
titleSuffix: Azure Cognitive Services
description: Lista dostępnych regionów i punktów końcowych usługi mowy, w tym zamiany mowy na tekst, zamiany tekstu na mowę i Tłumaczenie mowy.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 27e26bb37b444b49797d46dd4e12b61f8fe11b16
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84782538"
---
# <a name="speech-service-supported-regions"></a>Obsługiwane regiony usługi Speech

Usługa mowy umożliwia aplikacji Konwertowanie dźwięku na tekst, wykonywanie tłumaczeń mowy i przekonwertować tekstu na mowę. Usługa jest dostępna w wielu regionach z unikatowymi punktami końcowymi zestawu Speech SDK i interfejsów API REST.

Portal mowy do wykonywania niestandardowych konfiguracji w środowisku mowy dla wszystkich regionów jest dostępny tutaj:https://speech.microsoft.com

W przypadku wywołań usługi mowy upewnij się, że wywołanie jest zgodne z regionem subskrypcji.

## <a name="speech-sdk"></a>Zestaw SDK rozpoznawania mowy

W [zestawie mowy SDK](speech-sdk.md)regiony są określone jako ciąg (na przykład jako parametr `SpeechConfig.FromSubscription` w zestawie mowy SDK dla języka C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Zamiana mowy na tekst, zamiana tekstu na mowę i tłumaczenie

Portal dostosowania mowy jest dostępny tutaj:https://speech.microsoft.com

Usługa Speech jest dostępna w tych regionach na potrzeby **rozpoznawania mowy**, **zamiany tekstu na mowę**i **tłumaczenia**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Jeśli używasz [zestawu Speech SDK](speech-sdk.md), regiony są określane przez **Identyfikator regionu** (na przykład jako parametr do `SpeechConfig.FromSubscription` ). Upewnij się, że region jest zgodny z regionem subskrypcji.

### <a name="intent-recognition"></a>Rozpoznawanie intencji

Dostępne regiony do **rozpoznawania intencji** za pośrednictwem zestawu Speech SDK są następujące:

| Region globalny | Region           | Identyfikator regionu |
| ------------- | ---------------- | -------------------- |
| Azja          | Azja Wschodnia        | `eastasia`           |
| Azja          | Azja Południowo-Wschodnia   | `southeastasia`      |
| Australia     | Australia Wschodnia   | `australiaeast`      |
| Europa        | Europa Północna     | `northeurope`        |
| Europa        | Europa Zachodnia      | `westeurope`         |
| Ameryka Północna | Wschodnie stany USA          | `eastus`             |
| Ameryka Północna | Wschodnie stany USA 2        | `eastus2`            |
| Ameryka Północna | Południowo-środkowe stany USA | `southcentralus`     |
| Ameryka Północna | Zachodnio-środkowe stany USA  | `westcentralus`      |
| Ameryka Północna | Zachodnie stany USA          | `westus`             |
| Ameryka Północna | Zachodnie stany USA 2        | `westus2`            |
| Ameryka Południowa | Brazylia Południowa     | `brazilsouth`        |

Jest to podzestaw regionów publikowania obsługiwanych przez [usługę Language Understanding (Luis)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Asystenci głosowi

[Zestaw Speech SDK](speech-sdk.md) obsługuje funkcje **asystenta głosowego** w następujących regionach:

| Region         | Identyfikator regionu |
| -------------- | -------------------- |
| Zachodnie stany USA        | `westus`             |
| Zachodnie stany USA 2      | `westus2`            |
| Wschodnie stany USA        | `eastus`             |
| Wschodnie stany USA 2      | `eastus2`            |
| Europa Zachodnia    | `westeurope`         |
| Europa Północna   | `northeurope`        |
| Azja Południowo-Wschodnia | `southeastasia`      |

### <a name="speaker-recognition"></a>Rozpoznawanie osoby mówiącej

Rozpoznawanie osoby mówiącej jest obecnie dostępna tylko w `westus` regionie.

## <a name="rest-apis"></a>Interfejsy API REST

Usługa mowy udostępnia również punkty końcowe REST dla żądań zamiany mowy na tekst i zamiany tekstu na mowę.

### <a name="speech-to-text"></a>Zamiana mowy na tekst

Aby zapoznać się z dokumentacją dotyczącą zamiany mowy na tekst, zobacz [interfejs API REST zamiany mowy na tekst](rest-speech-to-text.md).

Punkt końcowy interfejsu API REST ma następujący format:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Zamień na `<REGION_IDENTIFIER>` Identyfikator pasujący do regionu subskrypcji z tej tabeli:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Parametr Language musi być dołączony do adresu URL, aby uniknąć otrzymania błędu HTTP 4xx. Na przykład język ustawiony na angielski w regionie zachodnie stany USA to: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

Aby zapoznać się z dokumentacją dotyczącą zamiany tekstu na mowę, zobacz [interfejs API REST zamiany tekstu na mowę](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
