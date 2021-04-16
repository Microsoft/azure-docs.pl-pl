---
title: Ściąganie platformy Docker dla analiza tonacji kontenera
titleSuffix: Azure Cognitive Services
description: Polecenie ściągnięć platformy Docker dla analiza tonacji kontenera
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 32a550e120331a8255281d51725d2d5fc8ca1e05
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564664"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Ściąganie platformy Docker dla analiza tonacji w wersji 3

Kontener analizy tonacji w wersji 3 jest dostępny w kilku językach. Aby pobrać kontener dla kontenera w języku angielskim, użyj poniższego polecenia. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Aby pobrać kontener dla innego języka, zastąp `en` jednym z poniższych kodów języków. 

| analiza tekstu kontenera | Kod języka |
|--|--|
| Chiński uproszczony    |   `zh-hans`   |
| Chiński tradycyjny   |   `zh-hant`   |
| Niderlandzki                 |     `nl`      |
| Angielski               |     `en`      |
| Francuski                |     `fr`      |
| Niemiecki                |     `de`      |
| Hindi                 |    `hi`       |
| Włoski               |     `it`      |
| japoński              |     `ja`      |
| Koreański                |     `ko`      |
| Norweski (Bokmål)   |     `no`      |
| portugalski (Brazylia)   |    `pt-BR`    |
| Portugalski (Portugalia) |    `pt-PT`    |
| Hiszpański               |     `es`      |
| turecki               |     `tr`      |

Aby uzyskać pełny opis dostępnych tagów dla kontenerów analiza tekstu, zobacz [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).
