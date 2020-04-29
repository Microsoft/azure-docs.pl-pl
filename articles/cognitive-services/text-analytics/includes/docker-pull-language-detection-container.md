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
ms.openlocfilehash: 3444d6267c09056697abf8339cc688a606b6dc17
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877105"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Wypychanie platformy Docker dla kontenera wykrywanie języka

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z programu Microsoft Container Registry.

Pełny opis znaczników dostępnych dla kontenerów analiza tekstu można znaleźć w kontenerze [wykrywanie języka](https://go.microsoft.com/fwlink/?linkid=2018759) w usłudze Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
