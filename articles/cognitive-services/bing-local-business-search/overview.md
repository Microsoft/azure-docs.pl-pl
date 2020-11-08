---
title: Co to jest interfejs API wyszukiwania lokalnych firm Bing?
titleSuffix: Azure Cognitive Services
description: Interfejs API wyszukiwania lokalnych firm Bing jest usługą RESTful, która pozwala aplikacjom znajdować informacje o lokalnych miejscach i firmach na podstawie zapytań wyszukiwania.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: ee31eac55298c86a7bdd784ea54c9a0c8caa47a4
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94364619"
---
# <a name="what-is-bing-local-business-search"></a>Co to jest lokalne wyszukiwanie w biznesie Bing?

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).
Lokalny interfejs API wyszukiwania biznesowego Bing to usługa RESTful, która umożliwia aplikacjom Znajdowanie informacji o lokalnych firmach na podstawie zapytań wyszukiwania. Na przykład, `q=<business-name> in Redmond, Washington` lub `q=Italian restaurants near me` . 

## <a name="features"></a>Funkcje
| Cechy | Opis |  
| -- | -- | 
| [Znajdowanie lokalnych firm i lokalizacji](quickstarts/local-quickstart.md) | Interfejs API wyszukiwania lokalnego firmy Bing umożliwia zlokalizowanie wyników zapytania. Wyniki obejmują adres URL witryny internetowej firmy oraz wyświetla tekst, numer telefonu i lokalizację geograficzną, w tym: współrzędne GPS, miasto, ulica adresu |  
| [Filtrowanie wyników lokalnych ze granicami geograficznymi](specify-geographic-search.md) | Dodaj współrzędne jako parametry wyszukiwania, aby ograniczyć wyniki do określonego obszaru geograficznego, określonego przez okrągły obszar lub kwadratowe pole ograniczające. | 
| [Filtrowanie lokalnych wyników firmy według kategorii](local-categories.md) | Wyszukaj lokalne wyniki biznesowe według kategorii. Ta opcja używa odwrotnej lokalizacji IP lub współrzędnej GPS obiektu wywołującego, aby zwracać zlokalizowane wyniki w różnych kategoriach firmy.|

## <a name="workflow"></a>Przepływ pracy
Wywołaj interfejs API wyszukiwania w usłudze Bing Local Business Search z dowolnego języka programowania, który może wykonywać żądania HTTP i analizować odpowiedzi JSON. Ta usługa jest dostępna przy użyciu interfejsu API REST.
 
1. Utwórz [konto interfejsu API Cognitive Services](../cognitive-services-apis-create-account.md)  z dostępem do interfejsy API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).   
2. Kodowanie w adresie URL wyszukiwanych terminów dla `q=""` parametru zapytania. Na przykład: `q=nearby+restaurant` lub `q=nearby%20restaurant`. W razie konieczności Ustaw również stronicowanie. 
3. Wyślij [żądanie do interfejsu API wyszukiwania lokalnego usługi Bing](quickstarts/local-quickstart.md) 
4. Analizowanie odpowiedzi w formacie JSON 

> [!NOTE]
> Obecnie lokalne wyszukiwanie biznesowe: 
> * Obsługuje tylko `en-US` rynek. 
> * Program nie obsługuje automatyczne sugerowanie Bing. 

## <a name="next-steps"></a>Następne kroki
- [Zapytanie i odpowiedź](local-search-query-response.md)
- [Lokalne wyszukiwanie biznesowe — Szybki Start](quickstarts/local-quickstart.md)
- [Dokumentacja interfejsu API wyszukiwania lokalnych firm Bing](local-search-reference.md)
- [Wymagania dotyczące użycia i wyświetlania](../bing-web-search/use-display-requirements.md)