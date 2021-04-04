---
title: Wypychanie platformy Docker dla kontenera kondycji
titleSuffix: Azure Cognitive Services
description: Polecenie Docker pull dla analiza tekstu dla kontenera kondycji
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: a0b2c9548f9c1289ae0abd61a72d7146a3bbca29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94965157"
---
Wypełnij i prześlij [formularz żądania Cognitive Services](https://aka.ms/csgate) , aby zażądać dostępu do analiza tekstu dla publicznej wersji zapoznawczej kondycji.  Ta aplikacja dotyczy zarówno kontenera, jak i hostowanej publicznej wersji zapoznawczej interfejsu API sieci Web.
Formularz żąda informacji o użytkowniku, firmie i scenariuszu użytkownika, dla którego będziesz używać kontenera. Po przesłaniu formularza zespół Cognitive Services platformy Azure przegląda go, aby upewnić się, że spełnia on kryteria dostępu do prywatnego rejestru kontenerów.

> [!IMPORTANT]
> * W formularzu musisz użyć adresu e-mail skojarzonego z IDENTYFIKATORem subskrypcji platformy Azure.
> * Zasób platformy Azure, którego używasz do uruchamiania kontenera, musi zostać utworzony przy użyciu zatwierdzonego identyfikatora subskrypcji platformy Azure. 
> * Sprawdź pocztę e-mail (Skrzynka odbiorcza i foldery śmieci), aby uzyskać aktualizacje stanu aplikacji firmy Microsoft.

Po zatwierdzeniu zostanie wysłana wiadomość e-mail z poświadczeniami, aby uzyskać dostęp do prywatnego rejestru kontenerów.  Użyj polecenia Docker login z poświadczeniami podanymi w wiadomości e-mail z dołączaniem, aby połączyć się z naszym prywatnym rejestrem kontenerów dla kontenerów Cognitive Services.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać ten obraz kontenera z naszego prywatnego rejestru kontenerów.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
