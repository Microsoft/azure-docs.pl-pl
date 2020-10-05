---
title: Co to jest interfejs API wyszukiwania obrazów Bing?
titleSuffix: Azure Cognitive Services
description: Interfejs API wyszukiwania obrazów Bing umożliwia używanie poznawczego wyszukiwania obrazów Bing w Twojej aplikacji. Wysyłając zapytania wyszukiwania użytkownika za pomocą interfejsu API, możesz pobierać i wyświetlać odpowiednie obrazy wysokiej jakości podobne do obrazów Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: eb657c16f6f3ff67f4379134f3aa478f10d8ef94
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85603540"
---
# <a name="what-is-the-bing-image-search-api"></a>Co to jest interfejs API wyszukiwania obrazów Bing?

Interfejs API wyszukiwania obrazów Bing umożliwia używanie możliwości wyszukiwania obrazów Bing w aplikacji. Wysyłając zapytania wyszukiwania użytkownika za pomocą interfejsu API, możesz pobierać obrazy wysokiej jakości, podobnie jak w witrynie [bing.com/images](https://www.bing.com/images).

Interfejs API wyszukiwania obrazów Bing oferuje wyniki wyszukiwania tylko w postaci obrazów, ale możesz połączyć go z innymi [interfejsami API wyszukiwania Bing](../bing-web-search/bing-api-comparison.md) lub używać ich w celu znalezienia wielu typów zawartości w Internecie.

## <a name="bing-image-search-features"></a>Funkcje wyszukiwania obrazów Bing

| Cecha                                                                                                                                                                                 | Opis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerowanie terminów wyszukiwania w czasie rzeczywistym](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | Ulepsz działanie aplikacji przy użyciu [interfejsu API automatycznego sugerowania Bing](../bing-autosuggest/get-suggested-search-terms.md), aby wyświetlać sugerowane terminy wyszukiwania w miarę ich wpisywania. |
| [Filtrowanie i ograniczanie wyników obrazu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)                       | Filtruj obrazy, które zwraca usługa Bing, edytując parametry zapytania.                                                                                                       |
| [Przycinanie, zmienianie rozmiaru i wyświetlania miniatur](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/resize-and-crop-thumbnails)                                                | Edytuj i wyświetlaj podgląd miniatur obrazów zwróconych przez wyszukiwanie obrazów Bing.                                                                                      |
| [Przestawianie i rozwijanie zapytań wyszukiwania użytkowników](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)               | Zwiększ możliwości wyszukiwania, dołączając i wyświetlając sugerowane przez usługę Bing terminy wyszukiwania do zapytania.                                                                    |
| [Pobieranie popularnych obrazów](trending-images.md)                                                                     | Dostosuj wyszukiwanie popularnych obrazów z całego świata.                                                                                                          |

## <a name="workflow"></a>Przepływ pracy

Interfejs API wyszukiwania obrazów Bing jest usługą internetową RESTful, łatwą do wywołania z dowolnego języka programowania, który może wysyłać żądania HTTP i analizować format JSON. Możesz użyć usługi przy użyciu [interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?)lub [zestawu SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

1. Utwórz [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z dostępem do interfejsów API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć konto](https://azure.microsoft.com/free/cognitive-services/) bezpłatnie.
2. Wyślij żądanie do interfejsu API z prawidłowym [zapytaniem wyszukiwania](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries).
3. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

## <a name="next-steps"></a>Następne kroki

Najpierw wypróbuj [interaktywną demonstrację](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) interfejsu API wyszukiwania obrazów Bing.
Ta demonstracja pokazuje, jak można szybko dostosować zapytanie wyszukiwania i wyszukać obrazy w Internecie.

Aby szybko rozpocząć pracę z pierwszym żądaniem interfejsu API, możesz nauczyć się:

* [Wysyłać zapytania wyszukiwania do usługi Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) przy użyciu interfejsu API REST lub
* [Żądać i filtrować](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) obrazy zwracane przez usługę Bing przy użyciu zestawu SDK.

## <a name="see-also"></a>Zobacz też

* [Szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) interfejsów API wyszukiwania Bing. 

* Sekcja dokumentacji dotyczącej [interfejsu API wyszukiwania obrazów Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) zawiera informacje na temat punktów końcowych interfejsu API, nagłówków, odpowiedzi interfejsu API i parametrów zapytania.

* [Wymagania dotyczące użycia i wyświetlania Bing](./useanddisplayrequirements.md) określają dopuszczalne zastosowania zawartości i informacji uzyskanych za pośrednictwem interfejsów API wyszukiwania Bing.

* Artykuł [Pobieranie obrazów z Internetu za pomocą interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) opisuje sposób wyszukiwania i pobierania obrazów z Internetu.

* Artykuł [Wysyłanie i praca z zapytaniami wyszukiwania](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) opisuje sposób tworzenia, dostosowywania i przestawiania zapytań wyszukiwania.

* Odwiedź [stronę centrum interfejsu API wyszukiwanie Bing](../bing-web-search/search-the-web.md) , aby poznać inne dostępne interfejsy API.
