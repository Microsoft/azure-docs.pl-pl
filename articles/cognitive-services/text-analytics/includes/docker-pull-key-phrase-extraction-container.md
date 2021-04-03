---
title: Wypychanie platformy Docker dla kontenera wyodrębnianie kluczowych fraz
titleSuffix: Azure Cognitive Services
description: Polecenie Docker pull dla kontenera wyodrębnianie kluczowych fraz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 02dde8a27b9687e58bf1a09c1a951f306937f0d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906084"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Wypychanie platformy Docker dla kontenera wyodrębnianie kluczowych fraz

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz kontenera z programu Microsoft Container Registry.

Pełny opis znaczników dostępnych dla kontenerów analiza tekstu można znaleźć w kontenerze [wyodrębnianie kluczowych fraz](https://go.microsoft.com/fwlink/?linkid=2018757) w usłudze Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```
