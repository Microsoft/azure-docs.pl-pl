---
title: Czym jest interfejs API sprawdzania pisowni Bing?
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej i interfejsie API sprawdzania pisowni Bing, który wykorzystuje uczenie maszynowe oraz statystyczne tłumaczenie maszynowe do kontekstowego sprawdzania pisowni.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 5586704e098fc568c714e779c2eed44aa3d1df9c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75383003"
---
# <a name="what-is-the-bing-spell-check-api"></a>Czym jest interfejs API sprawdzania pisowni Bing?

Interfejs API sprawdzania pisowni Bing umożliwia kontekstowe sprawdzanie pisowni i gramatyki tekstu. Podczas gdy większość narzędzi do sprawdzania pisowni polega na zestawach reguł utworzonych na podstawie słowników, narzędzie do sprawdzania pisowni Bing zapewnia dokładne i kontekstowe poprawki przy użyciu uczenia maszynowego i statystycznego tłumaczenia maszynowego. 

## <a name="features"></a>Funkcje


|  |  |
|---------|---------|
|Wiele trybów sprawdzania pisowni     | Wiele trybów sprawdzania pisowni zapewnia poprawki skupiające się na gramatyce i/lub pisowni. |
|Rozpoznawanie żargonu i języka potocznego     | Rozpoznawanie wyrażeń potocznych i nieformalnych użytych w tekście.         |
|Rozróżnianie podobnych słów     | Znajdowanie i poprawianie użycia słów, które brzmią podobnie, ale różnią się znaczeniem (np. „może” i „morze”).        |
|Obsługa nazw marek, tytułów i popularnych słów     | Rozpoznawanie nazw marek, tytułów i innych popularnych wyrażeń, gdy się pojawiają. |

## <a name="workflow"></a>Przepływ pracy

Interfejs API sprawdzania pisowni Bing można łatwo wywołać z dowolnego języka programowania, który może wysyłać żądania HTTP i analizować odpowiedzi w formacie JSON. Usługa jest dostępna za pomocą interfejsu API REST lub zestawów SDK sprawdzania pisowni Bing. 

1. Utwórz [konto interfejsu API usług Cognitive Services](../cognitive-services-apis-create-account.md) z dostępem do interfejsów API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć bezpłatne konto. 
2. Wyślij żądanie do interfejsu API wyszukiwania w Internecie Bing.
3. Analizowanie odpowiedzi w formacie JSON

## <a name="next-steps"></a>Następne kroki

Najpierw wypróbuj [interaktywną demonstrację](https://azure.microsoft.com/services/cognitive-services/spell-check/) interfejsu API sprawdzania pisowni Bing, aby przekonać się, jak szybko możesz sprawdzać różne teksty.

Gdy wszystko jest gotowe do wywołania interfejsu API, utwórz [konto interfejsu API usług Cognitive Services](../../cognitive-services/cognitive-services-apis-create-account.md). Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) bezpłatnie.

Możesz również odwiedzić [stronę centrum interfejsu API wyszukiwanie Bing](../bing-web-search/search-the-web.md) , aby poznać inne dostępne interfejsy API.