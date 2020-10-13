---
title: Wypychanie platformy Docker dla kontenera wykrywanie języka
titleSuffix: Azure Cognitive Services
description: Polecenie Docker pull dla kontenera wykrywanie języka
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 95bcb4b424010f63ac1ee4eb02f9e4793647051a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906024"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Wypychanie platformy Docker dla kontenera wykrywanie języka

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz kontenera z programu Microsoft Container Registry.

Pełny opis znaczników dostępnych dla kontenerów analiza tekstu można znaleźć w kontenerze [wykrywanie języka](https://go.microsoft.com/fwlink/?linkid=2018759) w usłudze Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest
```
