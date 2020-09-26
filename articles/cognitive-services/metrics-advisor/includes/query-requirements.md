---
title: Wymagania dotyczące zapytań
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 68b5946cb21bef1c8979a94c8780682cfe9defa6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376996"
---
W zapytaniu Użyj `@StartTime` parametru, aby uzyskać dane metryk dla konkretnej sygnatury czasowej. Ta wartość zostanie zastąpiona `yyyy-MM-ddTHH:mm:ss` ciągiem formatu. 

> [!IMPORTANT]
> Upewnij się, że tylko dane metryk z **pojedynczej sygnatury czasowej** zostaną zwrócone przez zapytanie. Doradca metryk uruchomi zapytanie względem każdej sygnatury czasowej w celu uzyskania odpowiednich danych metryki. Na przykład zapytanie dla metryki z *dzienną* szczegółowością powinno zawierać tylko jedną sygnaturę czasową, taką jak `2020-06-21T00:00:00Z` podczas uruchamiania zapytania jeden raz. 
