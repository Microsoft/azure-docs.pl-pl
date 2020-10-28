---
title: Dodaj warstwę mapy Open Geospatial Consortium (OGC) | Mapy Microsoft Azure
description: Dowiedz się, jak nałożyć warstwę mapy OGC na mapę oraz jak używać różnych opcji w klasie OgcMapLayer.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7d94a681fa987a4b23dbcda744c2f8516da4437a
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891669"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Dodaj warstwę mapy z Open Geospatial Consortium (OGC)

`atlas.layer.OgcMapLayer`Klasa może nakładać obrazy usług mapy sieci Web (WMS) i obrazów usług mapy sieci Web (WMTS) na mapie. WMS jest standardowym protokołem opracowanym przez OGC do obsługi obrazów mapy geograficznej za pośrednictwem Internetu. Odwołanie do obrazu jest procesami kojarzenia obrazu z lokalizacją geograficzną. WMTS jest również standardowym protokołem opracowanym przez OGC. Jest ona przeznaczona do obsługi wstępnie renderowanych i geoodwołaniowych kafelków mapy.

W poniższych sekcjach opisano funkcje usługi mapy sieci Web, które są obsługiwane przez `OgcMapLayer` klasę.

**Usługa mapy sieci Web (WMS)**

- Obsługiwane wersje: `1.0.0` , `1.1.0` , `1.1.1` i `1.3.0`
- Usługa musi obsługiwać `EPSG:3857` system projekcji lub obsługiwać reprojekcje.
- GetFeatureInfo wymaga, aby usługa obsługiwała `EPSG:4326` lub obsługiwała przedziały. 
- Obsługiwane operacje:

    | Operacja | Opis |
    | :-- | :-- |
    | GetCapabilities | Pobiera metadane dotyczące usługi z obsługiwanymi możliwościami |
    | GetMap | Pobiera obraz mapy dla określonego regionu |
    | GetFeatureInfo | Pobiera `feature_info` , który zawiera dane podstawowe dotyczące funkcji |

**Usługa kafelków mapy sieci Web (WMTS)**

- Obsługiwane wersje: `1.0.0`
- Kafelki muszą być kwadratowe, na przykład `TileWidth == TileHeight` .
- Obsługiwane przez KSR `EPSG:3857` : `GoogleMapsCompatible` 
- Identyfikator TileMatrix musi być wartością całkowitą, która odpowiada poziomowi powiększenia na mapie. Na mapie platformy Azure poziom powiększenia jest wartością z zakresu od `"0"` do `"22"` . Jest to `"0"` obsługiwane, ale `"00"` nie jest obsługiwane.
- Obsługiwane operacje:

    | Operacja | Opis |
    | :-- | :-- |
    | GetCapabilities | Pobiera obsługiwane operacje i funkcje |
    | Getkafelk | Pobiera obraz dla określonego kafelka |

## <a name="overlay-an-ogc-map-layer"></a>Nałóż warstwę mapy OGC

`url`Może to być podstawowy adres URL usługi lub pełny adres URL z kwerendą w celu uzyskania możliwości usługi. W zależności od podanych informacji klient WFS może wypróbować kilka standardowych formatów adresów URL, aby określić, jak początkowo uzyskać dostęp do usługi.

Poniższy kod przedstawia sposób nakładki warstwy mapy OGC na mapie.

<br/>

<iframe height='700' scrolling='no' title='Przykład warstwy mapy OGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>przykładem warstwy mapy OGC</a> pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Opcje warstwy mapy OGC

Poniższy przykład ilustruje różne opcje warstwy mapy OGC. Aby edytować pióro kodu, możesz kliknąć przycisk pióra kodu w prawym górnym rogu.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy mapy OGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>Opcje warstwy mapy OGC</a> pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Eksplorator usługi mapy sieci Web OGC

Następujące Narzędzie nakłada obrazy z usług mapy sieci Web (WMS) i usługi kafelków mapy sieci Web (WMTS) jako warstwy. Możesz wybrać warstwy, które mają być renderowane na mapie. Możesz również wyświetlić skojarzone legendy dla tych warstw.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Eksplorator usługi mapy sieci Web OGC' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>Eksploratorem OGC usługi mapy sieci Web</a> programu piórem, Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Możesz również określić ustawienia mapy, aby użyć usługi proxy. Usługa serwera proxy umożliwia ładowanie zasobów hostowanych w domenach, które nie mają włączonego mechanizmu CORS.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [OgcMapLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Zobacz następujące artykuły, które zawierają przykłady kodu, które można dodać do Twoich map:

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Korzystanie z podstawowych operacji](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)