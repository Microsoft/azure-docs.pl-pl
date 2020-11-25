---
title: Wymagania i zalecenia dotyczące kontenera
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: 4697be519eee96778eecdf37f7b358a88ad886c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006915"
---
> [!NOTE]
> Wymagania i zalecenia są oparte na testach porównawczych z pojedynczym żądaniem na sekundę przy użyciu obrazu 8 MB z zeskanowaną listą biznesową, która zawiera 29 wierszy i łącznie 803 znaków.

W poniższej tabeli opisano minimalne i zalecane alokacje zasobów dla każdego kontenera odczytu.

| Kontener | Minimum | Zalecane |
|-----------|---------|-------------|
| Przeczytaj 2,0 — wersja zapoznawcza | 1 rdzeń, 8 GB pamięci |  8 rdzeni, 16 GB pamięci |
| Przeczytaj 3,2 — wersja zapoznawcza | 8 rdzeni, 16 GB pamięci | 8 rdzeni, 24 GB pamięci |

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.

Rdzeń i pamięć odpowiadają `--cpus` `--memory` ustawieniom i, które są używane jako część `docker run` polecenia.
