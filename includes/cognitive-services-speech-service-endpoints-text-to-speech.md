---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 650ce0cc9586118b30593767c6a3ddb92f494ac3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972656"
---
### <a name="standard-and-neural-voices"></a>Głosy standardowe i neuronowych

Użyj tej tabeli, aby określić dostępność standardowych i neuronowych głosów według regionu/punktu końcowego:

| Region (Region) | Punkt końcowy | Głosy standardowe | Głosy neuronowych |
|--------|----------|-----------------|---------------|
| Australia Wschodnia | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Brazil South | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Kanada Środkowa | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Central US | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Azja Wschodnia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Wschodnie stany USA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Francja Środkowa | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Indie Środkowe | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Japan East | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Japonia Zachodnia | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Korea Środkowa | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Północno-środkowe stany USA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Europa Północna | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| South Central US | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Southeast Asia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Południowe Zjednoczone Królestwo | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| West Europe | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Zachodnie stany USA | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Zachodnie stany USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |

> [!TIP]
> [Głosy w wersji zapoznawczej](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) są dostępne tylko w tych 3 regionach: Wschodnie stany USA, Europa Zachodnia i Azja Południowo-Wschodnia.

### <a name="custom-voices"></a>Głosy niestandardowe

Jeśli utworzono niestandardową czcionkę głosową, użyj utworzonego przez siebie punktu końcowego. Możesz również użyć punktów końcowych wymienionych poniżej, zastępując `{deploymentId}` Identyfikator wdrożenia dla modelu głosu.

| Region (Region) | Punkt końcowy |
|--------|----------|
| Australia Wschodnia | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Brazil South | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
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
