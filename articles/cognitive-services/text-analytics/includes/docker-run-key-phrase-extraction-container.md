---
title: Przykład uruchomienia kontenera dla polecenia Docker Run
titleSuffix: Azure Cognitive Services
description: Polecenie Docker Run dla kontenera wyodrębnianie kluczowych fraz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: d4ae9d793168a41930a51047caca6e0fb7e923b2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906111"
---
Aby uruchomić kontener *wyodrębnianie kluczowych fraz* , wykonaj następujące `docker run` polecenie. Zastąp Poniższe symbole zastępcze własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz dla zasobu analiza tekstu. Można je znaleźć na stronie **klucz zasobu i punkt końcowy** na Azure Portal. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Punkt końcowy do uzyskiwania dostępu do interfejs API analizy tekstu. Można je znaleźć na stronie **klucz zasobu i punkt końcowy** na Azure Portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia *wyodrębnianie kluczowych fraz* kontener z obrazu kontenera
* Przypisuje jedne rdzeń procesora CPU i 4 gigabajty (GB) pamięci
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.