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
ms.openlocfilehash: b28049f1854494b61d63824334b986d814a641cd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91309318"
---
Wypełnij i prześlij [formularz żądania Cognitive Services kontenerów](https://aka.ms/csgate) , aby zażądać dostępu do kontenera.
Formularz żąda informacji o użytkowniku, firmie i scenariuszu użytkownika, dla którego będziesz używać kontenera. Po przesłaniu formularza zespół Cognitive Services platformy Azure przegląda go, aby upewnić się, że spełnia on kryteria dostępu do prywatnego rejestru kontenerów.

> [!IMPORTANT]
> * W formularzu musisz użyć adresu e-mail skojarzonego z IDENTYFIKATORem subskrypcji platformy Azure.
> * Zasób platformy Azure, którego używasz do uruchamiania kontenera, musi zostać utworzony przy użyciu zatwierdzonego identyfikatora subskrypcji platformy Azure. 
> * Sprawdź pocztę e-mail (Skrzynka odbiorcza i foldery śmieci), aby uzyskać aktualizacje stanu aplikacji firmy Microsoft.

Użyj polecenia Docker login z poświadczeniami podanymi w wiadomości e-mail z dołączaniem, aby połączyć się z naszym prywatnym rejestrem kontenerów dla kontenerów Cognitive Services.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać ten obraz kontenera z naszego prywatnego rejestru kontenerów.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
