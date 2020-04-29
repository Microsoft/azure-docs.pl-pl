---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: a1de313245e826511a7e9c864165ac44bcb48530
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80234187"
---
### <a name="standard-and-neural-voices"></a>Głosy standardowe i neuronowych

Użyj tej tabeli, aby określić dostępność standardowych i neuronowych głosów według regionu/punktu końcowego:

| Region | Endpoint | Głosy standardowe | Głosy neuronowych |
|--------|----------|-----------------|---------------|
| Australia Wschodnia | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Brazylia Południowa | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Kanada Środkowa | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Środkowe stany USA | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Azja Wschodnia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Wschodnie stany USA | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Wschodnie stany USA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Francja Środkowa | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Indie Środkowe | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Japonia Wschodnia | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Japonia Zachodnia | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Korea Środkowa | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Północno-środkowe stany USA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Europa Północna | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Południowo-środkowe stany USA | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Azja Południowo-Wschodnia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Południowe Zjednoczone Królestwo | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Europa Zachodnia | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Zachodnie stany USA | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Zachodnie stany USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |

### <a name="custom-voices"></a>Głosy niestandardowe

Jeśli utworzono niestandardową czcionkę głosową, użyj utworzonego przez siebie punktu końcowego. Możesz również użyć punktów końcowych wymienionych poniżej, zastępując `{deploymentId}` identyfikator wdrożenia dla modelu głosu.

| Region | Endpoint |
|--------|----------|
| Australia Wschodnia | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Brazylia Południowa | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kanada Środkowa | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Środkowe stany USA | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Azja Wschodnia | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Wschodnie stany USA | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Wschodnie stany USA 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Francja Środkowa | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Indie Środkowe | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japonia Wschodnia | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japonia Zachodnia | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Korea Środkowa | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Północno-środkowe stany USA | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa Północna | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Południowo-środkowe stany USA | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Azja Południowo-Wschodnia | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Południowe Zjednoczone Królestwo | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa Zachodnia | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Zachodnie stany USA | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Zachodnie stany USA 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
