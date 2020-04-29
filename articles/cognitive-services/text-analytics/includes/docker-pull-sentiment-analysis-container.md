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
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877065"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Wypychanie platformy Docker dla kontenera analiza tonacji

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z programu Microsoft Container Registry.

Pełny opis znaczników dostępnych dla kontenerów analiza tekstu można znaleźć w kontenerze [Analiza tonacji](https://go.microsoft.com/fwlink/?linkid=2018654) w usłudze Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
