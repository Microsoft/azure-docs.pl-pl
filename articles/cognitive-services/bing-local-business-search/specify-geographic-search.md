---
title: Użyj granic geograficznych, aby filtrować wyniki z interfejsu API wyszukiwania lokalnego firmy Bing
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego artykułu, aby dowiedzieć się, jak filtrować wyniki wyszukiwania za pomocą interfejsu API wyszukiwania lokalnego usługi Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 2e72abd777ac4834385fd916b730d438b8540203
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490265"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Użyj granic geograficznych, aby filtrować wyniki z interfejsu API wyszukiwania lokalnego firmy Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Interfejs API wyszukiwania lokalnego usługi Bing umożliwia ustawianie granic w określonym obszarze geograficznym, który ma być przeszukiwany przy użyciu `localCircularView` `localMapView` parametrów lub zapytania. Upewnij się, że w zapytaniach można używać tylko jednego parametru. 

Jeśli termin wyszukiwania zawiera jawną lokalizację geograficzną, lokalny interfejs API usługi Bing będzie automatycznie używać go do ustawiania granic dla wyników wyszukiwania. Na przykład jeśli termin wyszukiwania to `sailing in San Diego` , `San Diego` będzie używany jako lokalizacja, a wszystkie inne określone lokalizacje w parametrach zapytania lub w nagłówkach użytkowników zostaną zignorowane. 

Jeśli lokalizacja geograficzna nie zostanie wykryta w wyszukiwanym terminie i nie określono lokalizacji geograficznej przy użyciu parametrów zapytania, interfejs API wyszukiwania lokalnego usługi Bing podejmie próbę ustalenia lokalizacji z `X-Search-ClientIP` lub `X-Search-Location` nagłówków żądania. Jeśli nie określono żadnego nagłówka, interfejs API określi lokalizację adresu IP klienta żądania lub współrzędne GPS dla urządzeń przenośnych.

## <a name="localcircularview"></a>localCircularView

`localCircularView`Parametr tworzy okrągły obszar geograficzny wokół zestawu współrzędne szerokości geograficznej/długości geograficznej zdefiniowanej przez promień. Przy użyciu tego parametru odpowiedzi z interfejsu API wyszukiwania lokalnego usługi Bing będą obejmować tylko lokalizacje w tym okręgu, w przeciwieństwie do `localMapView` parametru, który może zawierać nieznacznie spoza obszaru wyszukiwania.

Aby określić cykliczny obszar wyszukiwania geograficznego, wybierz szerokość i długość geograficzną, które mają być używane jako środek okręgu i promień w metrach. Ten parametr można następnie dołączyć do ciągu zapytania, na przykład: `q=Restaurants&localCircularView=47.6421,-122.13715,5000` .

Ukończ zapytanie:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

`localMapView`Parametr określa prostokątny obszar geograficzny do przeszukania, wykorzystując dwa zestawy współrzędnych, aby określić jego rogi południowo-zachodni i północ. W przypadku korzystania z tego parametru odpowiedzi z interfejsu API wyszukiwania lokalnego usługi Bing mogą obejmować lokalizacje wewnątrz i po nim poza określonym obszarem, w przeciwieństwie do `localCircularView` parametru, który obejmuje tylko lokalizacje w obszarze wyszukiwania.

Aby określić prostokątny obszar wyszukiwania, należy wybrać dwa zestawy współrzędnych szerokości/długości geograficznej, które mają być narożne i zachodnie rogi granicy. Pamiętaj, aby najpierw zdefiniować współrzędne Południowo-Wschodnia, jak w poniższym przykładzie: `localMapView=47.619987,-122.181671,47.6421,-122.13715` .

Ukończ zapytanie:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Następne kroki
- [Lokalne wyszukiwanie biznesowe w języku Java — Szybki Start](quickstarts/local-search-java-quickstart.md)
- [Lokalne wyszukiwanie biznesowe — Przewodnik Szybki Start](quickstarts/local-quickstart.md)
- [Lokalny węzeł wyszukiwania biznesowego — Szybki Start](quickstarts/local-search-node-quickstart.md)
- [Lokalne wyszukiwanie biznesowe w języku Python — Szybki Start](quickstarts/local-search-python-quickstart.md)