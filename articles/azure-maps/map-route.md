---
title: Wyświetlanie kierunków tras na mapie | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak wyświetlać wskazówki między dwiema lokalizacjami na mapie przy użyciu zestawu Microsoft Azure Web SDK mapy.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 9d0197a16c8074ce961c2b403724149929f566f7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890721"
---
# <a name="show-directions-from-a-to-b"></a>Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B

W tym artykule pokazano, jak utworzyć żądanie trasy i wyświetlić trasę na mapie.

Istnieją dwa sposoby, aby to zrobić. Pierwszy sposób polega na wysyłaniu zapytań do [interfejsu API tras Azure Maps](/rest/api/maps/route/getroutedirections) za pomocą modułu usługi. Drugi sposób polega na użyciu [interfejsu API pobierania](https://fetch.spec.whatwg.org/) do przesyłania żądania wyszukiwania do [Azure Maps interfejsu API tras](/rest/api/maps/route/getroutedirections). Obie metody omówiono poniżej.

## <a name="query-the-route-via-service-module"></a>Wykonywanie zapytań dotyczących trasy za pośrednictwem modułu usługi

<iframe height='500' scrolling='no' title='Pokaż wskazówki od A do B na mapie (moduł usługi)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Pokaż wskazówki od A do B na mapie (module usługi)</a> przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie, pierwszy blok konstruuje obiekt mapy i ustawia mechanizm uwierzytelniania do korzystania z tokenu dostępu. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

Drugi blok kodu tworzy `TokenCredential` do uwierzytelniania żądań HTTP do Azure Maps z tokenem dostępu. Następnie przekazuje `TokenCredential` do `atlas.service.MapsURL.newPipeline()` i tworzy wystąpienie [potoku](/javascript/api/azure-maps-rest/atlas.service.pipeline) . `routeURL`Reprezentuje adres URL służący do Azure Maps operacji [routingu](/rest/api/maps/route) .

Trzeci blok kodu tworzy i dodaje obiekt [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) do mapy.

Czwarty blok kodu tworzy obiekty [punktów](/javascript/api/azure-maps-control/atlas.data.point) początkowych i końcowych i dodaje je do obiektu DataSource.

Wiersz jest [funkcją](/javascript/api/azure-maps-control/atlas.data.feature) dla LineString. [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) renderuje obiekty liniowe otoczone w [źródle danych](/javascript/api/azure-maps-control/atlas.source.datasource) jako linie na mapie. Czwarty blok kodu tworzy i dodaje warstwę linii do mapy. Zobacz właściwości warstwy linii pod adresem [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions).

[Warstwa symboli](/javascript/api/azure-maps-control/atlas.layer.symbollayer) używa tekstów lub ikon do renderowania danych opartych na punkcie, które są opakowane w [źródle](/javascript/api/azure-maps-control/atlas.source.datasource). Teksty lub ikony są renderowane jako symbole na mapie. Piąty blok kodu tworzy i dodaje warstwę symboli do mapy.

Szósty blok kodu wysyła zapytania do usługi routingu Azure Maps, która jest częścią [modułu usługi](how-to-use-services-module.md). Metoda [CalculateRouteDirections](/javascript/api/azure-maps-rest/atlas.service.routeurl#methods) RouteURL jest używana do uzyskiwania trasy między punktem początkowym i końcowym. Kolekcja funkcji GEOJSON z odpowiedzi jest wyodrębniana przy użyciu `geojson.getFeatures()` metody i jest dodawana do źródła danych. Następnie renderuje odpowiedź jako trasę na mapie. Aby uzyskać więcej informacji na temat dodawania linii do mapy, zobacz [Dodawanie linii na mapie](map-add-line-layer.md).

Ostatni blok kodu ustawia granice mapy przy użyciu właściwości [Setcamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) mapy.

Kwerenda trasy, źródło danych, symbol, warstwy liniowe i granice aparatu są tworzone wewnątrz [odbiornika zdarzeń](/javascript/api/azure-maps-control/atlas.map#events). Ta struktura kodu gwarantuje, że wyniki są wyświetlane tylko po całkowitym załadowaniu mapy.

## <a name="query-the-route-via-fetch-api"></a>Wykonywanie zapytań dotyczących trasy za pośrednictwem interfejsu API pobierania

<iframe height='500' scrolling='no' title='Pokaż kierunki od A do B na mapie' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Pokaż wskazówki od A do B na mapie</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu konstruuje obiekt mapy i ustawia mechanizm uwierzytelniania do korzystania z tokenu dostępu. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

Drugi blok kodu tworzy i dodaje obiekt [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) do mapy.

Trzeci blok kodu tworzy punkty początkowy i docelowy dla trasy. Następnie dodaje je do źródła danych. Aby uzyskać instrukcje dotyczące korzystania z funkcji [Addpinys](/javascript/api/azure-maps-control/atlas.map), [na mapie można zobaczyć kod PIN](map-add-pin.md) .

[LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) renderuje obiekty liniowe otoczone w [źródle danych](/javascript/api/azure-maps-control/atlas.source.datasource) jako linie na mapie. Czwarty blok kodu tworzy i dodaje warstwę linii do mapy. Zobacz właściwości warstwy linii pod adresem [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions).

[Warstwa symboli](/javascript/api/azure-maps-control/atlas.layer.symbollayer) używa tekstu lub ikon do renderowania danych opartych na punkcie [w postaci symboli](/javascript/api/azure-maps-control/atlas.source.datasource) na mapie. Piąty blok kodu tworzy i dodaje warstwę symboli do mapy. Zobacz właściwości warstwy symboli w [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Następny blok kodu tworzy `SouthWest` i `NorthEast` wskazuje od punktów początkowych i docelowych oraz określa granice mapy za pomocą właściwości [setcamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) mapy.

Ostatni blok kodu używa [interfejsu API pobierania](https://fetch.spec.whatwg.org/) do żądania wyszukiwania do [Azure Maps interfejsu API tras](/rest/api/maps/route/getroutedirections). Odpowiedź jest następnie analizowana. Jeśli odpowiedź zakończyła się pomyślnie, informacje o szerokości i długości geograficznej są używane do tworzenia tablicy a linii przez połączenie tych punktów. Dane wiersza są następnie dodawane do źródła danych w celu renderowania trasy na mapie. Aby uzyskać instrukcje, można zobaczyć [, jak dodać wiersz na mapie](map-add-line-layer.md) .

Kwerenda trasy, źródło danych, symbol, warstwy liniowe i granice aparatu są tworzone wewnątrz [odbiornika zdarzeń](/javascript/api/azure-maps-control/atlas.map#events). W tym przypadku chcemy upewnić się, że wyniki są wyświetlane po całkowitym załadowaniu mapy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące korzystania z usługi routingu](how-to-use-best-practices-for-search.md)

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Mapa](/javascript/api/azure-maps-control/atlas.map)

Zobacz następujące artykuły, aby zapoznać się z pełnymi przykładami kodu:

> [!div class="nextstepaction"]
> [Pokaż ruch na mapie](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Korzystanie z zdarzeń dotyczących mapy i myszy](./map-events.md)