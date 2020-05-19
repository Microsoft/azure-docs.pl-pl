---
title: Wypychanie platformy Docker dla kontenera analiza tonacji
titleSuffix: Azure Cognitive Services
description: Polecenie Docker pull dla kontenera analiza tonacji
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a3db0e2ffdd4a75f02634ca2227c3c41416d4f65
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588408"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Wypychanie platformy Docker dla kontenera analiza tonacji v3

Kontener tonacji Analysis Container V3 jest dostępny w kilku językach. Aby pobrać kontener dla kontenera angielskiego, użyj poniższego polecenia. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en
```

Aby pobrać kontener dla innego języka, Zamień `en` na jeden z poniższych kodów języka. 

| Kontener analiza tekstu | Kod języka |
|--|--|
| Angielski | `en` |
| Hiszpański | `es` |
| Francuski | `fr` |
| Włoski | `it` |
| Niemiecki | `de` |
| Chiński (uproszczony) | `zh` |
| Chiński (tradycyjny) | `zht` |
| Japoński | `ja` |
| Portugalski | `pt` |
| Niderlandzki | `nl` |

Pełny opis znaczników dostępnych dla kontenerów analiza tekstu można znaleźć w temacie [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).