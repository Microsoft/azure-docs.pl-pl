---
title: Pobierz obrazy z widoku niestandardowego — wyszukiwanie niestandardowe Bing
titleSuffix: Azure Cognitive Services
description: Ogólne omówienie używania wyszukiwanie niestandardowe Bing do pobierania obrazów z widoku niestandardowego sieci Web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5fa71c62969e0f1ff7e98f374eca63efa051c041
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "66390328"
---
# <a name="get-images-from-your-custom-view"></a>Pobierz obrazy z widoku niestandardowego

Wyszukiwanie obrazów niestandardowych Bing umożliwia wzbogacanie funkcji wyszukiwania niestandardowego z obrazami. Podobnie jak w przypadku wyników z Internetu interfejs API wyszukiwania niestandardowego obsługuje wyszukiwanie obrazów w witrynach internetowych uwzględnionych na liście w Twoim wystąpieniu. Obrazy można pobrać przy użyciu interfejsu API wyszukiwania obrazów niestandardowych w usłudze Bing lub przy użyciu funkcji hostowanego interfejsu użytkownika. Korzystanie z funkcji hostowanego interfejsu użytkownika jest proste i zalecane w przypadku uruchamiania wyszukiwania w krótkiej kolejności.  Aby uzyskać informacje o konfigurowaniu hostowanego interfejsu użytkownika w celu dołączania obrazów, zobacz [Konfigurowanie środowiska interfejsu użytkownika hostowanego](hosted-ui.md).

Jeśli chcesz mieć większą kontrolę nad wyświetlaniem wyników wyszukiwania, możesz użyć interfejsu API wyszukiwania obrazów niestandardowych w usłudze Bing. Ponieważ wywołanie interfejsu API jest podobne do wywoływania interfejs API wyszukiwania obrazów Bing, wyewidencjonowanie [Wyszukiwanie obrazów Bing](../Bing-Image-Search/overview.md) na potrzeby przykładów wywoływania interfejsu API. Jednak wcześniej zapoznaj się z zawartością [interfejsu API wyszukiwania obrazów niestandardowych](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) . Główne różnice są obsługiwanymi parametrami zapytania (należy uwzględnić parametr zapytania customConfig) i punkt końcowy, do którego wysyłane są żądania.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
