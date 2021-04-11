---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 832cc1d4f9ec3cec4ada6e964e3ab2f6f023dd41
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554286"
---
### <a name="neural-and-standard-voices"></a>Neuronowych i głosy standardowe

Użyj tej tabeli, aby określić **dostępność neuronowych i odgłosów standardowych** według regionu/punktu końcowego:

| Region | Punkt końcowy |
|--------|----------|
| Australia Wschodnia | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Brazylia Południowa | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Kanada Środkowa | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Central US | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Azja Wschodnia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Wschodnie stany USA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` |
| Francja Środkowa | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Indie Środkowe | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Japonia Wschodnia | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Japonia Zachodnia | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` |
| Korea Środkowa | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Północno-środkowe stany USA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Europa Północna | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| South Central US | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Southeast Asia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Południowe Zjednoczone Królestwo | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Zachodnio-środkowe stany USA | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| West Europe | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| Zachodnie stany USA | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Zachodnie stany USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` |

> [!TIP]
> [Głosy w wersji zapoznawczej](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) są dostępne tylko w tych 3 regionach: Wschodnie stany USA, Europa Zachodnia i Azja Południowo-Wschodnia.

### <a name="custom-voices"></a>Głosy niestandardowe

Jeśli utworzono niestandardową czcionkę głosową, użyj utworzonego przez siebie punktu końcowego. Możesz również użyć punktów końcowych wymienionych poniżej, zastępując `{deploymentId}` Identyfikator wdrożenia dla modelu głosu.

| Region | Punkt końcowy |
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

| Region | Punkt końcowy |
|--------|----------|
| East US | `https://eastus.customvoice.api.speech.microsoft.com` |
| Indie Środkowe | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Southeast Asia | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Południowe Zjednoczone Królestwo | `https://uksouth.customvoice.api.speech.microsoft.com` |
| West Europe | `https://westeurope.customvoice.api.speech.microsoft.com` |
