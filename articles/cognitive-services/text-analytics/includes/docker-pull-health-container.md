---
title: Wypychanie platformy Docker dla kontenera kondycji
titleSuffix: Azure Cognitive Services
description: Polecenie Docker pull dla analiza tekstu dla kontenera kondycji
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4c33952ed56399b94a7db1088cb60919a4f88137
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108987"
---
Wypełnij i prześlij [formularz żądania Cognitive Services kontenerów](https://aka.ms/cognitivegate) , aby zażądać dostępu do kontenera.

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

Użyj polecenia Docker login z poświadczeniami podanymi w wiadomości e-mail z dołączaniem, aby połączyć się z naszym prywatnym rejestrem kontenerów dla kontenerów Cognitive Services.

```bash
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać ten obraz kontenera z naszego prywatnego rejestru kontenerów.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
