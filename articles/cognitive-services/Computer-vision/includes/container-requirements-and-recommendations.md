---
title: Wymagania i zalecenia dotyczące kontenera
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: bffdb7b33fc7da38b6985edc3948b848f417a497
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982070"
---
> [!NOTE]
> Wymagania i zalecenia są oparte na testach porównawczych z pojedynczym żądaniem na sekundę przy użyciu obrazu 8 MB z zeskanowaną listą biznesową, która zawiera 29 wierszy i łącznie 803 znaków.

W poniższej tabeli opisano minimalne i zalecane alokacje zasobów dla każdego kontenera odczytu.

| Kontener | Minimum | Zalecane |
|-----------|---------|-------------|
| Przeczytaj 3,0 — wersja zapoznawcza | 8 rdzeni, 16 GB pamięci | 8 rdzeni, 24 GB pamięci |
| Przeczytaj 3,1 — wersja zapoznawcza | 8 rdzeni, 16 GB pamięci | 8 rdzeni, 24 GB pamięci |

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.

Rdzeń i pamięć odpowiadają `--cpus` `--memory` ustawieniom i, które są używane jako część `docker run` polecenia.
