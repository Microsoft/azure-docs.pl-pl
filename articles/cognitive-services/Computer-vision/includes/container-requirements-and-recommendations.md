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
ms.openlocfilehash: 3ab5462cc2b368bd1518606b03c8ec1027bc31c3
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397196"
---
> [!NOTE]
> Wymagania i zalecenia są oparte na testach porównawczych z pojedynczym żądaniem na sekundę przy użyciu obrazu 8 MB z zeskanowaną listą biznesową, która zawiera 29 wierszy i łącznie 803 znaków.

W poniższej tabeli opisano minimalne i zalecane alokacje zasobów dla każdego kontenera odczytu.

| Kontener | Minimum | Zalecane |
|-----------|---------|-------------|
| Przeczytaj 2,0 — wersja zapoznawcza | 1 rdzeń, 8 GB pamięci |  8 rdzeni, 16 GB pamięci |
| Przeczytaj 3,0 — wersja zapoznawcza | 8 rdzeni, 16 GB pamięci | 8 rdzeni, 24 GB pamięci |
| Przeczytaj 3,1 — wersja zapoznawcza | 8 rdzeni, 16 GB pamięci | 8 rdzeni, 24 GB pamięci |

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.

Rdzeń i pamięć odpowiadają `--cpus` `--memory` ustawieniom i, które są używane jako część `docker run` polecenia.
