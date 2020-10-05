---
title: Co to jest interfejs API wyszukiwania wiadomości Bing?
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejs API wyszukiwania wiadomości Bing, aby wyszukać w Internecie bieżące nagłówki w różnych kategoriach, w tym o nagłówkach i tematach trendów.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: d44fe58eb17e7f11dc64ee1426df7f356cb91aef
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85602758"
---
# <a name="what-is-the-bing-news-search-api"></a>Co to jest interfejs API wyszukiwania wiadomości Bing?

Interfejs API wyszukiwania wiadomości Bing umożliwia łatwą integrację możliwości poznawczego wyszukiwania wiadomości w usłudze Bing z aplikacjami. Interfejs API udostępnia podobne środowisko jak witryna [Wiadomości Bing](https://www.bing.com/news), umożliwiając wysyłanie zapytań wyszukiwania i odbieranie odpowiadających artykułów z wiadomościami.

Należy pamiętać, że interfejs API wyszukiwania wiadomości Bing udostępnia tylko wyniki wyszukiwania wiadomości. Użyj [interfejsu API wyszukiwania w Internecie Bing](../bing-web-search/search-the-web.md), [interfejsu API wyszukiwania wideo](../bing-video-search/search-the-web.md) i [interfejsu API wyszukiwania obrazów](../bing-image-search/overview.md) dla innych typów zawartości internetowej.

## <a name="bing-news-search-api-features"></a>Funkcje interfejsu API wyszukiwania wiadomości Bing

O ile interfejs API wyszukiwania wiadomości Bing umożliwia przede wszystkim wyszukiwanie artykułów z wiadomościami i zwraca je, to udostępnia także kilka funkcji inteligentnego i ukierunkowanego pobierania wiadomości w Internecie.

|Cecha  |Opis  |
|---------|---------|
|[Sugerowanie i używanie terminów wyszukiwania](concepts/search-for-news.md#suggest-and-use-search-terms)     | Ulepsz środowisko wyszukiwania przy użyciu [interfejsu API automatycznego sugerowania Bing](../bing-autosuggest/get-suggested-search-terms.md), aby wyświetlać sugerowane terminy wyszukiwania w miarę ich wpisywania.         |
|[Uzyskiwanie wiadomości ogólnych](concepts/search-for-news.md#get-general-news)     | Wyszukiwanie wiadomości przez wysłanie zapytania wyszukiwania do interfejsu API wyszukiwania wiadomości Bing i pobieranie w odpowiedzi listy odpowiadających artykułów z wiadomościami.           |
|[Dzisiejsze najważniejsze wiadomości](concepts/search-for-news.md#get-todays-top-news)      | Uzyskiwanie najważniejszych wiadomości z wszystkich kategorii dla bieżącego dnia.       |
|[Wiadomości według kategorii](concepts/search-for-news.md)     | Wyszukiwanie wiadomości w określonych kategoriach.        | 
|[Nagłówki](concepts/search-for-news.md)     | Wyszukiwanie najważniejszych nagłówków we wszystkich kategoriach.         |

## <a name="workflow"></a>Przepływ pracy

Interfejs API wyszukiwania wiadomości Bing jest usługą internetową RESTful łatwą do wywołania z dowolnego języka programowania, który może wysyłać żądania HTTP i analizować kod JSON. Możesz użyć tej usługi za pomocą interfejsu API REST lub zestawu SDK.

1. Utwórz [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z dostępem do interfejsów API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć konto](https://azure.microsoft.com/free/cognitive-services/) bezpłatnie.
2. Wyślij żądanie do interfejsu API przy użyciu prawidłowego zapytania wyszukiwania.
3. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

## <a name="next-steps"></a>Następne kroki

Najpierw wypróbuj [interaktywną demonstrację](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) interfejsu API wyszukiwania wiadomości Bing. Ta demonstracja pokazuje, jak można szybko dostosować zapytanie wyszukiwania i wyszukać wiadomości w Internecie.

Aby szybko rozpocząć pracę z pierwszym żądaniem interfejsu API, wypróbuj przewodnik Szybki start dla [interfejsu API REST](quickstart.md) lub jednego z [zestawów SDK](sdk.md).

## <a name="see-also"></a>Zobacz też

* Sekcja dokumentacji [interfejsu API wyszukiwania wiadomości Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) zawiera definicje i informacje dotyczące punktów końcowych, nagłówków, odpowiedzi interfejsu API i parametrów zapytania, których możesz użyć do żądania wyników wyszukiwania na podstawie obrazu.
* [Wymagania dotyczące użycia i wyświetlania Bing](./useanddisplayrequirements.md) określają dopuszczalne zastosowania zawartości i informacji uzyskanych za pośrednictwem interfejsów API wyszukiwania Bing.
* Odwiedź [stronę centrum interfejsu API wyszukiwanie Bing](../bing-web-search/search-the-web.md) , aby poznać inne dostępne interfejsy API.