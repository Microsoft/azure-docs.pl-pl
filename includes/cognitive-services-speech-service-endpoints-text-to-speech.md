---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 6b16dea3c4f9241133b91b092c90c9056da57de0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515064"
---
### <a name="standard-and-neural-voices"></a>Głosy standardowe i neuronowych

Użyj tej tabeli, aby określić dostępność standardowych i neuronowych głosów według regionu/punktu końcowego:

| Region (Region) | Punkt końcowy | Głosy neuronowych | Głosy standardowe |
|--------|----------|-----------------|---------------|
| Australia Wschodnia | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Brazylia Południowa | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Nie | Tak |
| Kanada Środkowa | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Central US | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Nie | Tak |
| Azja Wschodnia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Nie | Tak |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Wschodnie stany USA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Nie | Tak |
| Francja Środkowa | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Nie | Tak |
| Indie Środkowe | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Japonia Wschodnia | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Nie | Tak |
| Japonia Zachodnia | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Nie | Tak |
| Korea Środkowa | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Nie | Tak |
| Północno-środkowe stany USA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Nie | Tak |
| Europa Północna | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Nie | Tak |
| South Central US | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Southeast Asia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Południowe Zjednoczone Królestwo | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| West Europe | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Zachodnie stany USA | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Nie | Tak |
| Zachodnie stany USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |

> [!TIP]
> [Głosy w wersji zapoznawczej](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) są dostępne tylko w tych 3 regionach: Wschodnie stany USA, Europa Zachodnia i Azja Południowo-Wschodnia.

### <a name="custom-voices"></a>Głosy niestandardowe

Jeśli utworzono niestandardową czcionkę głosową, użyj utworzonego przez siebie punktu końcowego. Możesz również użyć punktów końcowych wymienionych poniżej, zastępując `{deploymentId}` Identyfikator wdrożenia dla modelu głosu.

| Region (Region) | Punkt końcowy |
|--------|----------|
| Australia Wschodnia | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Brazylia Południowa | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kanada Środkowa | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Central US | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Azja Wschodnia | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| East US | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Wschodnie stany USA 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Francja Środkowa | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Indie Środkowe | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japonia Wschodnia | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japonia Zachodnia | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Korea Środkowa | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Północno-środkowe stany USA | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa Północna | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| South Central US | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Southeast Asia | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Południowe Zjednoczone Królestwo | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| West Europe | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Zachodnie stany USA | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Zachodnie stany USA 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |

### <a name="custom-neural-voice"></a>Niestandardowy głos neuronowych

W poniższej tabeli przedstawiono obsługę regionalną niestandardowych funkcji głosu neuronowych.

| Cecha | Obsługiwane regiony |
|---|---|
| Hosting modelu głosowego | Wschodnie stany USA, zachodnie stany USA 2, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Południowe Zjednoczone Królestwo, Europa Zachodnia, Australia Wschodnia |
| Znaki w czasie rzeczywistym | Wschodnie stany USA, zachodnie stany USA 2, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Południowe Zjednoczone Królestwo, Europa Zachodnia, Australia Wschodnia |
| Długie znaki audio | Wschodnie stany USA, Europa Zachodnia, Południowe Zjednoczone Królestwo, Azja Południowo-Wschodnia, Indie Środkowe |
| Niestandardowe szkolenie neuronowych | Wschodnie stany USA, Południowe Zjednoczone Królestwo |

### <a name="long-audio-api"></a>Długi interfejs API audio

Długi interfejs API audio jest dostępny w wielu regionach z unikatowymi punktami końcowymi.

| Region (Region) | Punkt końcowy |
|--------|----------|
| East US | `https://eastus.customvoice.api.speech.microsoft.com` |
| Indie Środkowe | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Southeast Asia | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Południowe Zjednoczone Królestwo | `https://uksouth.customvoice.api.speech.microsoft.com` |
| West Europe | `https://westeurope.customvoice.api.speech.microsoft.com` |
