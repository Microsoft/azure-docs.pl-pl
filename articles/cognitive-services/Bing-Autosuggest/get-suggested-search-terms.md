---
title: Czym jest funkcja automatycznego sugerowania Bing?
titleSuffix: Azure Cognitive Services
description: Interfejs API automatycznego sugerowania Bing zwraca listę proponowanych zapytań na podstawie częściowego ciągu zapytania w polu wyszukiwania.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 014705cf628aa2d2df43d0964ff843fae09595ac
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352779"
---
# <a name="what-is-bing-autosuggest"></a>Czym jest funkcja automatycznego sugerowania Bing?

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Jeśli aplikacja wysyła zapytania do dowolnego z interfejsów API wyszukiwania Bing, możesz użyć interfejsu API automatycznego sugerowania Bing w celu ulepszenia środowiska wyszukiwania dla użytkowników. Interfejs API automatycznego sugerowania Bing zwraca listę proponowanych zapytań na podstawie częściowego ciągu zapytania w polu wyszukiwania. W miarę wprowadzania znaków w polu wyszukiwania możesz wyświetlać sugestie na liście rozwijanej.

## <a name="bing-autosuggest-api-features"></a>Funkcje interfejsu API automatycznego sugerowania Bing

| Cechy                                                                                                                                                                                 | Opis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerowanie terminów wyszukiwania w czasie rzeczywistym](concepts/get-suggestions.md) | Ulepsz działanie aplikacji przy użyciu interfejsu API automatycznego sugerowania, aby wyświetlać sugerowane terminy wyszukiwania w miarę ich wpisywania. |

## <a name="workflow"></a>Przepływ pracy

Interfejs API automatycznego sugerowania Bing jest usługą internetową RESTful łatwą do wywołania z dowolnego języka programowania, który może wysyłać żądania HTTP i analizować format JSON.

1. Utwórz [konto interfejsu API usług Cognitive Services](../cognitive-services-apis-create-account.md) z dostępem do interfejsów API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć konto](https://azure.microsoft.com/free/cognitive-services/) bezpłatnie.
2. Wyślij żądanie do tego interfejsu API za każdym razem, kiedy użytkownik wpisuje nowy znak w polu wyszukiwania w aplikacji.
3. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

Ten interfejs API jest zwykle wywoływany za każdym razem, kiedy użytkownik wpisuje nowy znak w polu wyszukiwania w aplikacji. Im większa liczba wprowadzonych znaków, tym lepiej dopasowane sugerowane zapytania wyszukiwania zwraca interfejs API. Na przykład sugestie, które interfejs API może zwrócić dla pojedynczej litery `s`, będą prawdopodobnie mniej przydatne niż zwracane dla ciągu `sail`.

Poniższy przykład przedstawia pole wyszukiwania z listą rozwijaną sugerowanych terminów zapytania z interfejsu API automatycznego sugerowania Bing.

![Pole wyszukiwania z listą rozwijaną automatycznie sugerowanych terminów](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Gdy użytkownik wybierze sugestię z listy rozwijanej, możesz jej użyć, aby rozpocząć wyszukiwanie przy użyciu jednego z interfejsów API wyszukiwania Bing lub bezpośrednio przejść do strony wyników wyszukiwania Bing.

## <a name="next-steps"></a>Następne kroki

Aby szybko rozpocząć pracę z pierwszym żądaniem, zobacz [Making Your First Query](quickstarts/csharp.md) (Tworzenie pierwszego zapytania).

Zapoznaj się z dokumentacją [interfejsu API automatycznego sugerowania Bing w wersji 7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference). Dokumentacja zawiera listę punktów końcowych, nagłówków i parametrów zapytań, które są stosowane w żądaniach sugerowanych wyszukiwanych terminów, oraz definicje obiektów odpowiedzi.

Odwiedź [stronę centrum interfejsu API wyszukiwanie Bing](../bing-web-search/overview.md) , aby poznać inne dostępne interfejsy API.


Dowiedz się, jak przeszukiwać sieć Web przy użyciu [interfejs API wyszukiwania w sieci Web Bing](../bing-web-search/overview.md)i eksplorować inne[interfejsy API wyszukiwania Bing](../bing-web-search/index.yml).

Nie zapomnij przeczytać [wymagań w zakresie korzystania z usługi Bing i wyświetlania danych z niej](../bing-web-search/use-display-requirements.md), aby nie złamać żadnych reguł dotyczących korzystania z wyników wyszukiwania.