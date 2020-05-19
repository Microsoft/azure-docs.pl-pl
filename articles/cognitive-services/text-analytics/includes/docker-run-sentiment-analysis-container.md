---
title: Przykład uruchomienia kontenera dla polecenia Docker Run
titleSuffix: Azure Cognitive Services
description: Polecenie Docker Run dla kontenera analiza tonacji
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 253c391b222c757a367bd5cd39939052cc697b00
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588411"
---
Aby uruchomić kontener *Analiza tonacji v3* , wykonaj następujące `docker run` polecenie.

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia *Analiza tonacji* kontener z obrazu kontenera
* Przypisuje jedne rdzeń procesora CPU i 8 gigabajtów (GB) pamięci
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.