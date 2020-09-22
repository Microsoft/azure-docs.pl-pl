---
title: Wymagania dotyczące zapytań
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 23da214f4ffc9e589523f7ada00a77494183eb15
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940142"
---
W zapytaniu Użyj `@StartTime` parametru, aby uzyskać dane metryk dla konkretnej sygnatury czasowej. Ta wartość zostanie zastąpiona `yyyy-MM-ddTHH:mm:ss` ciągiem formatu. 

> [!IMPORTANT]
> Upewnij się, że tylko dane metryk z **pojedynczej sygnatury czasowej** zostaną zwrócone przez zapytanie. Doradca metryk uruchomi zapytanie względem każdej sygnatury czasowej w celu uzyskania odpowiednich danych metryki. Na przykład zapytanie dla metryki z *dzienną* szczegółowością powinno zawierać tylko jedną sygnaturę czasową, taką jak `2020-06-21T00:00:00Z` podczas uruchamiania zapytania jeden raz. 
