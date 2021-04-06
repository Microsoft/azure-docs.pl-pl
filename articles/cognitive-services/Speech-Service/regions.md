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
ms.date: 08/20/2020
ms.author: panosper
ms.custom: seodec18,references_regions
ms.openlocfilehash: 646d29e72b91cd6afcde8e70ad8fd8715442b88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98786795"
---
# <a name="speech-service-supported-regions"></a>Obsługiwane regiony usługi Speech

Usługa mowy umożliwia aplikacji Konwertowanie dźwięku na tekst, wykonywanie tłumaczeń mowy i przekonwertować tekstu na mowę. Usługa jest dostępna w wielu regionach z unikatowymi punktami końcowymi zestawu Speech SDK i interfejsów API REST.

Portal mowy do wykonywania niestandardowych konfiguracji w środowisku mowy dla wszystkich regionów jest dostępny tutaj: https://speech.microsoft.com

Należy pamiętać o następujących kwestiach podczas rozważania regionów:

* Jeśli aplikacja korzysta z [zestawu Speech SDK](speech-sdk.md), `westus` podczas tworzenia konfiguracji mowy należy podać identyfikator regionu, taki jak.
* Jeśli aplikacja używa jednego z [interfejsów API REST](./overview.md#reference-docs)usługi mowy, region jest częścią identyfikatora URI punktu końcowego, który jest używany podczas tworzenia żądań.
* Klucze utworzone dla regionu są prawidłowe tylko w tym regionie. Próba użycia ich z innymi regionami spowoduje błędy uwierzytelniania.

## <a name="speech-sdk"></a>Zestaw SDK rozpoznawania mowy

W [zestawie mowy SDK](speech-sdk.md)regiony są określone jako ciąg (na przykład jako parametr `SpeechConfig.FromSubscription` w zestawie mowy SDK dla języka C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Zamiana mowy na tekst, zamiana tekstu na mowę i tłumaczenie

Portal dostosowania mowy jest dostępny tutaj: https://speech.microsoft.com

Usługa Speech jest dostępna w tych regionach na potrzeby **rozpoznawania mowy**, **zamiany tekstu na mowę** i **tłumaczenia**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Jeśli używasz [zestawu Speech SDK](speech-sdk.md), regiony są określane przez **Identyfikator regionu** (na przykład jako parametr do `SpeechConfig.FromSubscription` ). Upewnij się, że region jest zgodny z regionem subskrypcji.

Jeśli planujesz uczenie modelu niestandardowego z danymi audio, użyj jednego z [regionów z dedykowanym sprzętem](custom-speech-overview.md#set-up-your-azure-account) do szybszego szkolenia. Możesz użyć [interfejsu API REST](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) , aby skopiować w pełni przeszkolony model do innego regionu później.

### <a name="intent-recognition"></a>Rozpoznawanie intencji

Dostępne regiony do **rozpoznawania intencji** za pośrednictwem zestawu Speech SDK są następujące:

| Region globalny | Region (Region)           | Identyfikator regionu |
| ------------- | ---------------- | -------------------- |
| Azja          | Azja Wschodnia        | `eastasia`           |
| Azja          | Southeast Asia   | `southeastasia`      |
| Australia     | Australia Wschodnia   | `australiaeast`      |
| Europa        | Europa Północna     | `northeurope`        |
| Europa        | West Europe      | `westeurope`         |
| Ameryka Północna | East US          | `eastus`             |
| Ameryka Północna | Wschodnie stany USA 2        | `eastus2`            |
| Ameryka Północna | South Central US | `southcentralus`     |
| Ameryka Północna | Zachodnio-środkowe stany USA  | `westcentralus`      |
| Ameryka Północna | Zachodnie stany USA          | `westus`             |
| Ameryka Północna | Zachodnie stany USA 2        | `westus2`            |
| Ameryka Południowa | Brazylia Południowa     | `brazilsouth`        |

Jest to podzestaw regionów publikowania obsługiwanych przez [usługę Language Understanding (Luis)](../luis/luis-reference-regions.md).

### <a name="voice-assistants"></a>Asystenci głosowi

[Zestaw Speech SDK](speech-sdk.md) obsługuje funkcje **asystenta głosowego** za pośrednictwem [bezpośredniej linii mowy](./direct-line-speech.md) w następujących regionach:

| Region globalny | Region (Region)           | Identyfikator regionu    |
| ------------- | ---------------- | -------------------- |
| Ameryka Północna | Zachodnie stany USA          | `westus`             |
| Ameryka Północna | Zachodnie stany USA 2        | `westus2`            |
| Ameryka Północna | East US          | `eastus`             |
| Ameryka Północna | Wschodnie stany USA 2        | `eastus2`            |
| Ameryka Północna | Zachodnio-środkowe stany USA  | `westcentralus`      |
| Ameryka Północna | South Central US | `southcentralus`     |
| Europa        | West Europe      | `westeurope`         |
| Europa        | Europa Północna     | `northeurope`        |
| Azja          | Azja Wschodnia        | `eastasia`           |
| Azja          | Southeast Asia   | `southeastasia`      |
| Indie         | Indie Środkowe    | `centralindia`       |

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