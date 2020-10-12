---
title: Nawiązywanie połączenia z usługą funkcji sieci Web (WFS) | Mapy Microsoft Azure
description: Dowiedz się, jak nawiązać połączenie z usługą WFS, a następnie wykonać zapytanie dotyczące usługi WFS przy użyciu zestawu SDK sieci Web Azure Maps i modułu operacji we/wy.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: ab6eb4851b9f2574fc235056f8f512edcb2054bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321785"
---
# <a name="connect-to-a-wfs-service"></a>Nawiązywanie połączenia z usługą WFS

Usługa funkcji sieci Web (WFS) to usługa sieci Web służąca do wykonywania zapytań dotyczących danych przestrzennych, które mają ustandaryzowany interfejs API zdefiniowany przez Open Geospatial Consortium (OGC). `WfsClient`Klasa w module we/wy przestrzennym umożliwia deweloperom łączenie się z usługą WFS i wykonywanie zapytań dotyczących danych z usługi.

Klasa obsługuje następujące funkcje `WfsClient` :

- Obsługiwane wersje: `1.0.0` , `1.1.0` i `2.0.0`
- Obsługiwane operatory filtrów: porównania binarne, logika, Math, wartość i `bbox` .
- Żądania są wykonywane tylko przy użyciu programu `HTTP GET` .
- Obsługiwane operacje:

    | Operacja | Opis |
    | :-- | :-- |
    | GetCapabilities | Generuje dokument metadanych z prawidłowymi operacjami WFS i parametrami |
    | Getfeature | Zwraca wybór funkcji ze źródła danych |
    | DescribeFeatureType | Zwraca obsługiwane typy funkcji |

## <a name="using-the-wfs-client"></a>Korzystanie z klienta WFS

`atlas.io.ogc.WfsClient`Klasa w module we/wy przestrzennym ułatwia wykonywanie zapytań do usługi WFS i konwertowanie odpowiedzi na obiekty GEOJSON. Tego obiektu GEOJSON można następnie użyć do innych celów mapowania.

Poniższy kod wysyła zapytanie do usługi WFS i renderuje zwrócone funkcje na mapie.

<br/>

<iframe height='700' scrolling='no' title='Prosty przykład WFS' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz prosty Azure Maps <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>przykład WFS</a> piórem ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Obsługiwane filtry

Specyfikacja standardu WFS wykorzystuje filtry OGC. Poniższe filtry są obsługiwane przez klienta WFS, przy założeniu, że wywoływana usługa również obsługuje te filtry. Ciągi filtru niestandardowego mogą być przesyłane do `CustomFilter` klasy.

**Operatory logiczne**

- `And`
- `Or`
- `Not`

**Operatory wartości**

- `GmlObjectId`
- `ResourceId`

**Operatory matematyczne**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Operatory porównania**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

Poniższy kod ilustruje użycie różnych filtrów z klientem WFS.

<br/>

<iframe height='500' scrolling='no' title= 'Przykłady filtrów WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>przykładami filtru WFS</a> piórem Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>Eksplorator usługi WFS

Poniższy kod używa klienta WFS do eksplorowania usług WFS Services. Wybierz warstwę typów właściwości w ramach usługi i zobacz skojarzoną legendę.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'Eksplorator usługi WFS' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>Eksploratorem WFS usługi</a> piórem, Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Aby uzyskać dostęp do usług WFS hostowanych przez punkty końcowe z obsługą mechanizmu CORS, usługa serwera proxy z obsługą mechanizmu CORS może zostać przekazana do `proxyService` opcji klienta WFS, jak pokazano poniżej. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Korzystanie z podstawowych operacji](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)
