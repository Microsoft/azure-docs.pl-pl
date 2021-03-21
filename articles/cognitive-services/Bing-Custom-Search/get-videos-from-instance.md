---
title: Pobierz wideo z widoku niestandardowego — wyszukiwanie niestandardowe Bing
titleSuffix: Azure Cognitive Services
description: Ogólne omówienie używania wyszukiwanie niestandardowe Bing do pobierania filmów wideo z widoku niestandardowego sieci Web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 4e2bb14519f8ed4e3e4a0e9ac8800957b30229a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96338422"
---
# <a name="get-videos-from-your-custom-view"></a>Pobierz wideo z widoku niestandardowego

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Wyszukiwanie niestandardowych filmów wideo Bing pozwala wzbogacać niestandardowe środowisko wyszukiwania za pomocą filmów wideo. Podobnie jak w przypadku wyników z Internetu interfejs API wyszukiwania niestandardowego obsługuje wyszukiwanie wideo w witrynach internetowych uwzględnionych na liście w Twoim wystąpieniu. Filmy wideo można pobrać przy użyciu niestandardowego interfejsu API wyszukiwania wideo w usłudze Bing lub przy użyciu funkcji hostowanego interfejsu użytkownika. Korzystanie z funkcji hostowanego interfejsu użytkownika jest proste i zalecane w przypadku uruchamiania wyszukiwania w krótkiej kolejności. Aby uzyskać informacje o konfigurowaniu hostowanego interfejsu użytkownika do dołączania wideo, zobacz [Konfigurowanie środowiska interfejsu użytkownika hostowanego](hosted-ui.md).

Jeśli chcesz mieć większą kontrolę nad wyświetlaniem wyników wyszukiwania, możesz użyć niestandardowego interfejsu API wyszukiwania wideo w usłudze Bing. Ponieważ wywołanie interfejsu API jest podobne do wywoływania interfejs API wyszukiwania wideo Bing, wyewidencjonowanie [Wyszukiwanie wideo Bing](../bing-video-search/overview.md) na potrzeby przykładów wywoływania interfejsu API. Jednak wcześniej zapoznaj się z zawartością [interfejsu API wyszukiwania niestandardowych filmów wideo](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) . Główne różnice są obsługiwanymi parametrami zapytania (należy uwzględnić parametr zapytania customConfig) i punkt końcowy, do którego wysyłane są żądania.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->