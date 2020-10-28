---
title: Wyświetlanie informacji o współrzędnych mapy | Mapy Microsoft Azure
description: Dowiedz się, jak wyświetlać informacje o adresie na mapie, gdy użytkownik wybierze współrzędną.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 31e4004379340912051204786da592fe33a5bd63
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890755"
---
# <a name="get-information-from-a-coordinate"></a>Uzyskiwanie informacji na podstawie współrzędnych

W tym artykule pokazano, jak utworzyć wyszukiwanie odwrotnego adresu, które pokazuje adres klikniętej lokalizacji podręcznej.

Istnieją dwa sposoby przeszukiwania adresów odwrotnych. Jednym ze sposobów jest zbadanie [interfejsu API wyszukiwania odwrotnego adresu Azure Maps](/rest/api/maps/search/getsearchaddressreverse) za pomocą modułu usługi. Innym sposobem jest użycie [interfejsu API pobierania](https://fetch.spec.whatwg.org/) w celu żądania do [Azure Maps interfejsu API wyszukiwania wstecznego adresu](/rest/api/maps/search/getsearchaddressreverse) w celu znalezienia adresu. Oba sposoby zostały przedstawione poniżej.

## <a name="make-a-reverse-search-request-via-service-module"></a>Utwórz żądanie wyszukiwania wstecznego za pośrednictwem modułu usługi

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji z współrzędnych (modułu usługi)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Pobierz informacje z współrzędnych (modułu usługi)</a> przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie, pierwszy blok konstruuje obiekt mapy i ustawia mechanizm uwierzytelniania do korzystania z tokenu dostępu. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

Drugi blok kodu tworzy `TokenCredential` do uwierzytelniania żądań HTTP do Azure Maps przy użyciu tokenu dostępu. Następnie przekazuje `TokenCredential` do `atlas.service.MapsURL.newPipeline()` i tworzy wystąpienie [potoku](/javascript/api/azure-maps-rest/atlas.service.pipeline) . `searchURL`Reprezentuje adres URL służący do Azure Maps operacji [wyszukiwania](/rest/api/maps/search) .

Trzeci blok kodu aktualizuje styl kursora myszy do wskaźnika i tworzy obiekt [podręczny](/javascript/api/azure-maps-control/atlas.popup#open) . Aby uzyskać instrukcje, zobacz sekcję [Dodawanie okna podręcznego na mapie](./map-add-popup.md) .

Czwarty blok kodu dodaje [odbiornik zdarzeń](/javascript/api/azure-maps-control/atlas.map#events)kliknięcia myszą. Gdy jest wyzwalane, tworzy zapytanie wyszukiwania ze współrzędnymi klikniętego punktu. Następnie używa metody [getSearchAddressReverse](/javascript/api/azure-maps-rest/atlas.service.searchurl#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-), aby wykonać zapytanie dotyczące [zwrotnego interfejsu API uzyskiwania adresu wyszukiwania](/rest/api/maps/search/getsearchaddressreverse) dla adresu współrzędnych. Kolekcja funkcji GEOJSON jest następnie wyodrębniana przy użyciu `geojson.getFeatures()` metody z odpowiedzi.

Piąty blok kodu konfiguruje zawartość HTML podręczną, aby wyświetlić adres odpowiedzi dla klikniętej pozycji współrzędnej.

Wszystkie zmiany dotyczące kursora, obiektu podręcznego i zdarzenia kliknięcia są tworzone w [detektorze zdarzeń ładowania](/javascript/api/azure-maps-control/atlas.map#events)mapy. Ta struktura kodu zapewnia pełną załadowanie mapy przed pobraniem informacji o współrzędnych.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Utwórz żądanie wyszukiwania wstecznego za pośrednictwem interfejsu API pobierania

Kliknij mapę, aby przetworzyć żądanie odwrotnego kodu dla tej lokalizacji przy użyciu funkcji pobierania.

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji na podstawie współrzędnych' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Pobierz informacje z współrzędnych</a> przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu konstruuje obiekt mapy i ustawia mechanizm uwierzytelniania do korzystania z tokenu dostępu. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

Drugi blok kodu aktualizuje styl kursora myszy do wskaźnika. Tworzy wystąpienie obiektu [podręcznego](/javascript/api/azure-maps-control/atlas.popup#open) . Aby uzyskać instrukcje, zobacz sekcję [Dodawanie okna podręcznego na mapie](./map-add-popup.md) .

Trzeci blok kodu dodaje odbiornik zdarzeń do kliknięć myszą. Gdy klikniesz myszą, użyje [interfejsu API pobierania](https://fetch.spec.whatwg.org/) , aby wysłać zapytanie do [interfejsu API wyszukiwania](/rest/api/maps/search/getsearchaddressreverse) dla klikniętego adresu Azure Maps. W przypadku pomyślnej odpowiedzi zbierany jest adres dla klikniętej lokalizacji. Definiuje zawartość podręczną i położenie przy użyciu funkcji [SetOptions](/javascript/api/azure-maps-control/atlas.popup#setoptions-popupoptions-) klasy popup.

Wszystkie zmiany dotyczące kursora, obiektu podręcznego i zdarzenia kliknięcia są tworzone w [detektorze zdarzeń ładowania](/javascript/api/azure-maps-control/atlas.map#events)mapy. Ta struktura kodu gwarantuje całkowite załadowanie mapy przed pobraniem informacji o współrzędnych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące korzystania z usługi wyszukiwania](how-to-use-best-practices-for-search.md)

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Mapa](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Okno podręczne](/javascript/api/azure-maps-control/atlas.popup)

Zobacz następujące artykuły, aby zapoznać się z pełnymi przykładami kodu:

> [!div class="nextstepaction"]
> [Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B](./map-route.md)

> [!div class="nextstepaction"]
> [Wyświetlanie ruchu](./map-show-traffic.md)