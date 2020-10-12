---
title: Przykład uruchomienia kontenera dla polecenia Docker Run
titleSuffix: Azure Cognitive Services
description: Polecenie Docker Run dla kontenera kondycji
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 72a877ab8cfda126016376017c0a7eb42b1186d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86108949"
---
Aby uruchomić kontener, najpierw Znajdź jego identyfikator obrazu:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Wykonaj następujące `docker run` polecenie. Zastąp Poniższe symbole zastępcze własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz dla zasobu analiza tekstu. Można je znaleźć na stronie **klucz zasobu i punkt końcowy** na Azure Portal. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Punkt końcowy do uzyskiwania dostępu do interfejs API analizy tekstu. Można je znaleźć na stronie **klucz zasobu i punkt końcowy** na Azure Portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | Identyfikator obrazu dla kontenera. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | Katalog wejściowy dla kontenera. | Systemy `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

To polecenie:

- Przyjęto, że katalog wejściowy istnieje na komputerze hosta
- Uruchamia analiza tekstu dla kontenera kondycji z obrazu kontenera
- Przydziela 6 rdzeni procesora CPU i 12 gigabajtów (GB) pamięci
- Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera
- Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.
