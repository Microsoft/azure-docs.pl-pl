---
title: Obsługiwane Szczegóły formatu danych | Mapy Microsoft Azure
description: Dowiedz się, w jaki sposób rozdzielone dane przestrzenne są analizowane w module operacji we/wy.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7227813f607ca18ee50f503a30b290414f333e21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91310173"
---
# <a name="supported-data-format-details"></a>Szczegóły obsługiwanego formatu danych

Ten artykuł zawiera szczegółowe informacje na temat obsługi odczytu i zapisu dla wszystkich tagów XML i Well-Known typów geometrii tekstu. Szczegółowo opisano sposób, w jaki rozdzielone dane przestrzenne są analizowane w module we/wy.

## <a name="supported-xml-namespaces"></a>Obsługiwane przestrzenie nazw XML

Moduł operacji we/wy w sklepie obsługuje tagi XML z następujących przestrzeni nazw.

| Prefiks przestrzeni nazw | Identyfikator URI przestrzeni nazw   | Uwagi                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Obsługa tylko do odczytu w plikach GeoRSS.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Obsługa tylko do odczytu w plikach GeoRSS.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Obsługa tylko do odczytu w plikach GPX. Analizuje i używa DisplayColor. Wszystkie inne właściwości dodawane do metadanych kształtu. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Obsługiwane w plikach GPX. Używa koloru linii. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Tylko do odczytu. GeoRSS zapisy przy użyciu formatu Atom.              |

## <a name="supported-xml-elements"></a>Obsługiwane elementy XML

Moduł operacji we/wy jest obsługiwany przez następujące elementy XML. Wszystkie tagi XML, które nie są obsługiwane, zostaną przekonwertowane na obiekt JSON. Następnie każdy tag zostanie dodany jako właściwość w `properties` polu kształtu nadrzędnego lub warstwy.

### <a name="kml-elements"></a>Elementy KML

Moduł operacji we/wy jest obsługiwany przez następujące elementy KML.

| Nazwa elementu         | Odczyt    | Zapisywanie   | Uwagi                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | partial | tak     | Obiekt jest analizowany, ale nie jest używany do pozycjonowania kształtu.                                                                    |
| `AddressDetails`     | partial | nie      | Obiekt jest analizowany, ale nie jest używany do pozycjonowania kształtu.                                                                    |
| `atom:author`        | tak     | tak     |                                                                                                                            |
| `atom:link`          | tak     | tak     |                                                                                                                            |
| `atom:name`          | tak     | tak     |                                                                                                                            |
| `BalloonStyle`       | partial | partial | `displayMode` nie jest obsługiwana. Konwertowany na `PopupTemplate` . Aby napisać, Dodaj `popupTemplate` Właściwość jako właściwość funkcji, dla której chcesz ją zapisać. |
| `begin`              | tak     | tak     |                                                                                                                            |
| `color`              | tak     | tak     | Obejmuje `#AABBGGRR` i `#BBGGRR` . Przeanalizowane w ciągu koloru CSS                                                           |
| `colorMode`          | tak     | nie      |                                                                                                                            |
| `coordinates`        | tak     | tak     |                                                                                                                            |
| `Data`               | tak     | tak     |                                                                                                                            |
| `description`        | tak     | tak     |                                                                                                                            |
| `displayName`        | tak     | tak     |                                                                                                                            |
| `Document`           | tak     | tak     |                                                                                                                            |
| `drawOrder`          | partial | nie      | Odczytaj dla nakładki naziemnej i posortuj je. 
| `east`               | tak     | tak     |                                                                                                                            |
| `end`                | tak     | tak     |                                                                                                                            |
| `ExtendedData`       | tak     | tak     | Obsługuje zamiany w postaci niewpisanej `Data` , `SimpleData` lub `Schema` i elementów `$[dataName]` .                      |
| `extrude`            | partial | partial | Obsługiwane tylko dla wielokątów. Wielogeometria, która ma wielokąty o różnej wysokości, zostanie podzielona na poszczególne funkcje. Style linii nie są obsługiwane. Wielokąty o wysokości 0 będą renderowane jako płaski wielokąt. Podczas odczytywania Wysokość pierwszej współrzędnej w pierścieniu zewnętrznym zostanie dodana jako właściwość Height wielokąta. Następnie Wysokość pierwszej współrzędnej zostanie użyta do renderowania wielokąta na mapie. |
| `fill`               | tak     | tak     |                                                                                                                            |
| `Folder`             | tak     | tak     |                                                                                                                            |
| `GroundOverlay`      | tak     | tak     | `color` nie jest obsługiwana                                                                                                   |
| `heading`            | partial | nie      | Przeanalizowane, ale nie renderowane przez `SimpleDataLayer` . Tylko zapisy, jeśli dane są przechowywane we właściwości kształtu.                 |
| `hotSpot`            | tak     | partial | Tylko zapisy, jeśli dane są przechowywane we właściwości kształtu. Jednostki są zwracane tylko jako "piksele".                         |
| `href`               | tak     | tak     |                                                                                                                            |
| `Icon`               | partial | partial | Przeanalizowane, ale nie renderowane przez `SimpleDataLayer` . Zapisuje tylko Właściwość Icon kształtu, jeśli zawiera on dane identyfikatora URI. `href`Obsługiwane są tylko. |
| `IconStyle`          | partial | partial | `icon`, `heading` , `colorMode` , i `hotspots` wartości są analizowane, ale nie są renderowane przez `SimpleDataLayer`         |
| `innerBoundaryIs`    | tak     | tak     |                                                                                                                            |
| `kml`                | tak     | tak     |                                                                                                                            |
| `LabelStyle`         | nie      | nie      |                                                                                                                            |
| `LatLonBox`          | tak     | tak     |                                                                                                                            |
| `gx:LatLonQuad`      | tak     | tak     |                                                                                                                            |
| `LinearRing`         | tak     | tak     |                                                                                                                            |
| `LineString`         | tak     | tak     |                                                                                                                            |
| `LineStyle`          | tak     | tak     | `colorMode` nie jest obsługiwana.                                                                                         |
| `Link`               | tak     | nie      | Tylko `href` Właściwość jest obsługiwana w przypadku linków sieciowych.                                                                   |
| `MultiGeometry`      | partial | partial | Może zostać rozbite do poszczególnych funkcji podczas odczytu.                                                                     |
| `name`               | tak     | tak     |                                                                                                                            |
| `NetworkLink`        | tak     | nie      | Linki muszą znajdować się w tej samej domenie co dokument.                                                                  |
| `NetworkLinkControl` | nie      | nie      |                                                                                                                            |
| `north`              | tak     | tak     |                                                                                                                            |
| `open`               | tak     | tak     |                                                                                                                            |
| `outerBoundaryIs`    | tak     | tak     |                                                                                                                            |
| `outline`            | tak     | tak     |                                                                                                                            |
| `overlayXY`          | nie      | nie      |                                                                                                                            |
| `Pair`               | partial | nie      | `normal`Obsługiwany jest tylko styl w elemencie `StyleMap` . `highlight` nie jest obsługiwana.                                   |
| `phoneNumber`        | tak     | tak     |                                                                                                                            |
| `PhotoOverlay`       | nie      | nie      |                                                                                                                            |
| `Placemark`          | tak     | tak     |                                                                                                                            |
| `Point`              | tak     | tak     |                                                                                                                            |
| `Polygon`            | tak     | tak     |                                                                                                                            |
| `PolyStyle`          | tak     | tak     |                                                                                                                            |
| `Region`             | partial | partial | `LatLongBox` jest obsługiwana na poziomie dokumentu.                                                                      |
| `rotation`           | nie      | nie      |                                                                                                                            |
| `rotationXY`         | nie      | nie      |                                                                                                                            |
| `scale`              | nie      | nie      |                                                                                                                            |
| `Schema`             | tak     | tak     |                                                                                                                            |
| `SchemaData`         | tak     | tak     |                                                                                                                            |
| `schemaUrl`          | partial | tak     | Nie obsługuje ładowania stylów z dokumentów zewnętrznych, które nie są uwzględnione w KMZ.                             |
| `ScreenOverlay`      | nie      | nie      |                                                                                                                            |
| `screenXY`           | nie      | nie      |                                                                                                                            |
| `SimpleData`         | tak     | tak     |                                                                                                                            |
| `SimpleField`        | tak     | tak     |                                                                                                                            |
| `size`               | nie      | nie      |                                                                                                                            |
| `Snippet`            | partial | partial | `maxLines` atrybut jest ignorowany.                                                                                  |
| `south`              | tak     | tak     |                                                                                                                            |
| `Style`              | tak     | tak     |                                                                                                                            |
| `StyleMap`           | partial | nie      | Obsługiwany jest tylko styl Normalny w elemencie `StyleMap` .                                                                        |
| `styleUrl`           | partial | tak     | Adresy URL w stylu zewnętrznym nie są obsługiwane.                                                                         |
| `text`               | tak     | tak     | Zastąpienie elementu `$[geDirections]` nie jest obsługiwane                                                                          |
| `textColor`          | tak     | tak     |                                                                                                                            |
| `TimeSpan`           | tak     | tak     |                                                                                                                            |
| `TimeStamp`          | tak     | tak     |                                                                                                                            |
| `value`              | tak     | tak     |                                                                                                                            |
| `viewRefreshMode`    | partial | nie      |  W przypadku wskazania do usługi WMS `onStop` są obsługiwane tylko dla nakładki naziemnej. Dołączy `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` do adresu URL i aktualizuje się w miarę przenoszenia mapy.  |
| `visibility`         | tak     | tak     |                                                                                                                            |
| `west`               | tak     | tak     |                                                                                                                            |
| `when`               | tak     | tak     |                                                                                                                            |
| `width`              | tak     | tak     |                                                                                                                            |

### <a name="georss-elements"></a>Elementy GeoRSS

Moduł operacji we/wy jest obsługiwany przez następujące elementy GeoRSS.

| Nazwa elementu             | Odczyt    | Zapisywanie | Uwagi                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | tak     | tak   |                                                                                                |
| `atom:category`          | tak     | tak   |                                                                                                |
| `atom:content`           | tak     | tak   |                                                                                                |
| `atom:contributor`       | tak     | tak   |                                                                                                |
| `atom:email`             | tak     | tak   |                                                                                                |
| `atom:entry`             | tak     | tak   |                                                                                                |
| `atom:feed`              | tak     | tak   |                                                                                                |
| `atom:icon`              | tak     | tak   |                                                                                                |
| `atom:id`                | tak     | tak   |                                                                                                |
| `atom:link`              | tak     | tak   |                                                                                                |
| `atom:logo`              | tak     | tak   |                                                                                                |
| `atom:name`              | tak     | tak   |                                                                                                |
| `atom:published`         | tak     | tak   |                                                                                                |
| `atom:rights`            | tak     | tak   |                                                                                                |
| `atom:source`            | tak     | tak   |                                                                                                |
| `atom:subtitle`          | tak     | tak   |                                                                                                |
| `atom:summary`           | tak     | tak   |                                                                                                |
| `atom:title`             | tak     | tak   |                                                                                                |
| `atom:updated`           | tak     | tak   |                                                                                                |
| `atom:uri`               | tak     | tak   |                                                                                                |
| `geo:lat`                | tak     | nie    | Zapisany jako `georss:point` .                                                                   |
| `geo:lon`                | tak     | nie    | Zapisany jako `georss:point` .                                                                   |
| `geo:long`               | tak     | nie    | Zapisany jako `georss:point` .                                                                   |
| `georss:box`             | tak     | nie    | Odczytaj jako Wielokąt i nadana `subType` Właściwość "Rectangle"                                |
| `georss:circle`          | tak     | tak   |                                                                                                |
| `georss:elev`            | tak     | tak   |                                                                                                |
| `georss:featurename`     | tak     | tak   |                                                                                                |
| `georss:featuretypetag`  | tak     | tak   |                                                                                                |
| `georss:floor`           | tak     | tak   |                                                                                                |
| `georss:line`            | tak     | tak   |                                                                                                |
| `georss:point`           | tak     | tak   |                                                                                                |
| `georss:polygon`         | tak     | tak   |                                                                                                |
| `georss:radius`          | tak     | tak   |                                                                                                |
| `georss:relationshiptag` | tak     | tak   |                                                                                                |
| `georss:where`           | tak     | tak   |                                                                                                |
| `geourl:latitude`        | tak     | nie    | Zapisany jako `georss:point` .                                                                   |
| `geourl:longitude`       | tak     | nie    | Zapisany jako `georss:point` .                                                                   |
| `position`               | tak     | nie    | Niektóre źródła XML będą otaczać GML za pomocą znacznika położenia zamiast otaczania go `georss:where` tagiem. Odczytaje ten tag, ale pisze przy użyciu `georss:where` znacznika. |
| `rss`                    | tak     | nie    | GeoRSS zapisywana w formacie ATOM.                                                                 |
| `rss:author`             | tak     | partial | Zapisany jako `atom:author` .                                                                 |
| `rss:category`           | tak     | partial | Zapisany jako `atom:category` .                                                               |
| `rss:channel`            | tak     | nie    |                                                                                                |
| `rss:cloud`              | tak     | nie    |                                                                                                |
| `rss:comments`           | tak     | nie    |                                                                                                |
| `rss:copyright`          | tak     | partial | Zapisany jako `atom:rights` kształt If nie ma `rights` `properties` już właściwości.       |
| `rss:description`        | tak     | partial | Zapisany jako `atom:content` kształt If nie ma `content` `properties` już właściwości.      |
| `rss:docs`               | tak     | nie    |                                                                                                |
| `rss:enclosure`          | tak     | nie    |                                                                                                |
| `rss:generator`          | tak     | nie    |                                                                                                |
| `rss:guid`               | tak     | partial | Zapisany jako `atom:id` kształt If nie ma `id` `properties` już właściwości.         |
| `rss:image`              | tak     | partial | Zapisany jako `atom:logo` kształt If nie ma `logo` `properties` już właściwości.      |
| `rss:item`               | tak     | partial | Zapisany jako `atom:entry` .                                                                  |
| `rss:language`           | tak     | nie    |                                                                                                |
| `rss:lastBuildDate`      | tak     | partial | Zapisany jako `atom:updated` kształt If nie ma `updated` `properties` już właściwości.     |
| `rss:link`               | tak     | partial | Zapisany jako `atom:link` .                                                                   |
| `rss:managingEditor`     | tak     | partial | Zapisany jako `atom:contributor` .                                                            |
| `rss:pubDate`            | tak     | partial | Zapisany jako `atom:published` kształt If nie ma `published` `properties` już właściwości.  |
| `rss:rating`             | tak     | nie    |                                                                                                |
| `rss:skipDays`           | tak     | nie    |                                                                                                |
| `rss:skipHours`          | tak     | nie    |                                                                                                |
| `rss:source`             | tak     | partial | Zapisany jako `atom:source` zawierający `atom:link` .                                       |
| `rss:textInput`          | tak     | nie    |                                                                                                |
| `rss:title`              | tak     | partial | Zapisany jako `atom:title` .                                                                  |
| `rss:ttl`                | tak     | nie    |                                                                                                |
| `rss:webMaster`          | tak     | nie    |                                                                                                |

### <a name="gml-elements"></a>Elementy GML

Moduł operacji we/wy jest obsługiwany przez następujące elementy GML. 

| Nazwa elementu            | Odczyt | Zapisywanie | Uwagi                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | tak  | nie    | Zapisany jako `gml:posList` .                                                              |
| `gml:curveMember`       | tak  | nie    |                                                                                        |
| `gml:curveMembers`      | tak  | nie    |                                                                                        |
| `gml:Box`               | tak  | nie    | Zapisany jako `gml:Envelope` .                                                             |
| `gml:description`       | tak  | tak   |                                                                                        |
| `gml:Envelope`          | tak  | tak   |                                                                                        |
| `gml:exterior`          | tak  | tak   |                                                                                        |
| `gml:Feature`           | tak  | nie    | Zapisywane jako kształt.                                                                    |
| `gml:FeatureCollection` | tak  | nie    | Zapisany jako kolekcja geometrii.                                                      |
| `gml:featureMember`     | tak  | nie    | Zapisany jako kolekcja geometrii.                                                      |
| `gml:geometry`          | tak  | nie    | Zapisywane jako kształt.                                                                    |
| `gml:geometryMember`    | tak  | tak   |                                                                                        |
| `gml:geometryMembers`   | tak  | tak   |                                                                                        |
| `gml:identifier`        | tak  | tak   |                                                                                        |
| `gml:innerBoundaryIs`   | tak  | nie    | Zapisano przy użyciu `gml.interior` .                                                          |
| `gml:interior`          | tak  | tak   |                                                                                        |
| `gml:LinearRing`        | tak  | tak   |                                                                                        |
| `gml:LineString`        | tak  | tak   |                                                                                        |
| `gml:lineStringMember`  | tak  | tak   |                                                                                        |
| `gml:lineStringMembers` | tak  | nie    |                                                                                        |
| `gml:MultiCurve`        | tak  | nie    | Tylko odczytuje `gml:LineString` członków. Zapisany jako `gml.MultiLineString`                  |
| `gml:MultiGeometry`     | partial  | partial   | Odczytaj tylko jako Funkcjacollection.                                              |
| `gml:MultiLineString`   | tak  | tak   |                                                                                        |
| `gml:MultiPoint`        | tak  | tak   |                                                                                        |
| `gml:MultiPolygon`      | tak  | tak   |                                                                                        |
| `gml:MultiSurface`      | tak  | nie    | Tylko odczytuje `gml:Polygon` członków. Zapisany jako `gml.MultiPolygon`                        |
| `gml:name`              | tak  | tak   |                                                                                        |
| `gml:outerBoundaryIs`   | tak  | nie    | Zapisano przy użyciu `gml.exterior` .                                                          |
| `gml:Point`             | tak  | tak   |                                                                                        |
| `gml:pointMember`       | tak  | tak   |                                                                                        |
| `gml:pointMembers`      | tak  | nie    |                                                                                        |
| `gml:Polygon`           | tak  | tak   |                                                                                        |
| `gml:polygonMember`     | tak  | tak   |                                                                                        |
| `gml:polygonMembers`    | tak  | nie    |                                                                                        |
| `gml:pos`               | tak  | tak   |                                                                                        |
| `gml:posList`           | tak  | tak   |                                                                                        |
| `gml:surfaceMember`     | tak  | tak   |                                                                                        |

#### <a name="additional-notes"></a>dodatkowe uwagi

- Elementy członkowskie będą przeszukiwane w poszukiwaniu geometrii, która może być przypadana w elementach podrzędnych. Ta operacja wyszukiwania jest niezbędna, ponieważ wiele formatów XML, które zwiększają się z GML, nie może umieszczać geometrii jako bezpośredniego elementu członkowskiego.
- `srsName` jest częściowo obsługiwana dla współrzędnych WGS84 i następujących kodów:[EPSG: 4326](https://epsg.io/4326)) i sieci Web Merkatora ([EPSG: 3857](https://epsg.io/3857) lub jeden z kodów alternatywnych. Każdy inny układ współrzędnych zostanie przeanalizowany jako WGS84.
- O ile nie zostanie określony podczas odczytywania źródła danych XML, kolejność osi jest określana na podstawie wskazówek w źródle danych XML. Podano preferencję dla kolejności osi "Szerokość geograficzna".
- O ile nie określono niestandardowej przestrzeni nazw GML dla właściwości podczas zapisywania do pliku GML, dodatkowe informacje o właściwościach nie zostaną dodane.

### <a name="gpx-elements"></a>Elementy GPX

Moduł operacji we/wy jest obsługiwany przez następujące elementy GPX.

| Nazwa elementu             | Odczyt    | Zapisywanie   | Uwagi                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | tak     | tak     |                                                                                             |
| `gpx:author`             | tak     | tak     |                                                                                             |
| `gpx:bounds`             | tak     | tak     | Konwertowane na LocationRect podczas odczytu.                                                    |
| `gpx:cmt`                | tak     | tak     |                                                                                             |
| `gpx:copyright`          | tak     | tak     |                                                                                             |
| `gpx:desc`               | tak     | tak     | Kopiowany do właściwości Description, gdy odczyt ma być wyrównany do innych formatów XML.               |
| `gpx:dgpsid`             | tak     | tak     |                                                                                             |
| `gpx:ele`                | tak     | tak     |                                                                                             |
| `gpx:extensions`         | partial | partial | Podczas odczytu informacje o stylu są wyodrębniane. Wszystkie inne rozszerzenia zostaną spłaszczone do prostego obiektu JSON. Zapisywane są tylko informacje o stylu kształtu. |
| `gpx:geoidheight`        | tak     | tak     |                                                                                             |
| `gpx:gpx`                | tak     | tak     |                                                                                             |
| `gpx:hdop`               | tak     | tak     |                                                                                             |
| `gpx:link`               | tak     | tak     |                                                                                             |
| `gpx:magvar`             | tak     | tak     |                                                                                             |
| `gpx:metadata`           | tak     | tak     |                                                                                             |
| `gpx:name`               | tak     | tak     |                                                                                             |
| `gpx:pdop`               | tak     | tak     |                                                                                             |
| `gpx:rte`                | tak     | tak     |                                                                                             |
| `gpx:rtept`              | tak     | tak     |                                                                                             |
| `gpx:sat`                | tak     | tak     |                                                                                             |
| `gpx:src`                | tak     | tak     |                                                                                             |
| `gpx:sym`                | tak     | tak     | Wartość jest przechwytywana, ale nie jest używana do zmiany ikony pinezki.                               |
| `gpx:text`               | tak     | tak     |                                                                                             |
| `gpx:time`               | tak     | tak     |                                                                                             |
| `gpx:trk`                | tak     | tak     |                                                                                             |
| `gpx:trkpt`              | tak     | tak     |                                                                                             |
| `gpx:trkseg`             | tak     | tak     |                                                                                             |
| `gpx:type`               | tak     | tak     |                                                                                             |
| `gpx:vdop`               | tak     | tak     |                                                                                             |
| `gpx:wpt`                | tak     | tak     |                                                                                             |
| `gpx_style:color`        | tak     | tak     |                                                                                             |
| `gpx_style:line`         | partial | partial | `color`, `opacity` , `width` , `lineCap` są obsługiwane.                                           |
| `gpx_style:opacity`      | tak     | tak     |                                                                                             |
| `gpx_style:width`        | tak     | tak     |                                                                                             |
| `gpxx:DisplayColor`      | tak     | nie      | Służy do określania koloru kształtu. Podczas pisania `gpx_style:line` kolor zostanie użyty.  |
| `gpxx:RouteExtension`    | partial | nie      | Wszystkie właściwości są odczytywane `properties` . Tylko `DisplayColor` jest używany.                     |
| `gpxx:TrackExtension`    | partial | nie      | Wszystkie właściwości są odczytywane `properties` . Tylko `DisplayColor` jest używany.                     |
| `gpxx:WaypointExtension` | partial | nie      | Wszystkie właściwości są odczytywane `properties` . Tylko `DisplayColor` jest używany.                     |
| `gpx:keywords`           | tak     | tak     |                                                                                             |
| `gpx:fix`                | tak     | tak     |                                                                                             |

#### <a name="additional-notes"></a>dodatkowe uwagi

Podczas pisania;

- Wielopunkty zostaną podzielone na poszczególne waypoints.
- Wielokąty i wielowielokąty będą zapisywane jako ścieżki. 
  
## <a name="supported-well-known-text-geometry-types"></a>Obsługiwane Well-Known typy geometrii tekstu

| Typ geometrii | Odczyt | Zapisywanie |
|--------------|:----:|:-----:|
| MOMENT | x | x |
| PUNKT Z | x | x | 
| PUNKT M | x | x<sup>[2]</sup> |
| ZM PUNKTU | x<sup>[1]</sup><sup>[2]</sup> | | 
| LINESTRING | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| TWORZĄC | x | x |
| WIELOKĄT Z | x | x |
| WIELOKĄT M | x | x<sup>[2]</sup> |
| ZM WIELOKĄT | x<sup>[1]</sup><sup>[2]</sup> | | 
| USŁUG | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1 \] tylko parametr Z i jest przechwytywany i dodawany jako trzecia wartość w wartości pozycji.

\[2 \] M parametr nie jest przechwytywany.

## <a name="delimited-spatial-data-support"></a>Ograniczanie obsługi danych przestrzennych

Rozdzielane dane przestrzenne, takie jak pliki z wartościami rozdzielanymi przecinkami (CSV), często mają kolumny zawierające dane przestrzenne. Na przykład mogą istnieć kolumny zawierające informacje o szerokości geograficznej i długości geograficznej. W Well-Known formacie tekstowym może istnieć kolumna, która zawiera dane geometryczne przestrzenne.

### <a name="spatial-data-column-detection"></a>Wykrywanie kolumn danych przestrzennych

Podczas odczytywania rozdzielanego pliku zawierającego dane przestrzenne nagłówek zostanie przeanalizowany, aby określić, które kolumny zawierają pola lokalizacji. Jeśli nagłówek zawiera informacje o typie, będzie używany do rzutowania wartości komórek na odpowiedni typ. Jeśli nagłówek nie zostanie określony, pierwszy wiersz zostanie przeanalizowany i użyty do wygenerowania nagłówka. Podczas analizowania pierwszego wiersza sprawdzana jest zgodność z nazwami kolumn o następujących nazwach w sposób niezależny od wielkości liter. Kolejność nazw jest priorytetem, w przypadku gdy w pliku znajdują się dwie lub więcej nazw.

#### <a name="latitude"></a>Szerokość geograficzna

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Długość geograficzna

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Noszeniu

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Lokalizacja geograficzna

Pierwszy wiersz danych będzie skanowany pod kątem ciągów, które są w Well-Known formacie tekstowym. 

### <a name="delimited-data-column-types"></a>Rozdzielane typy kolumn danych

Podczas skanowania wiersza nagłówka wszystkie informacje o typie, które są w nazwie kolumny, zostaną wyodrębnione i użyte do rzutowania komórek w tej kolumnie. Oto przykład nazwy kolumny, która ma wartość typu: "ColumnName (typeName)". Obsługiwane są następujące nazwy typu bez uwzględniania wielkości liter:

#### <a name="numbers"></a>Liczby

- EDM. Int64
- int
- długi
- EDM. Double
- float
- double
- liczba

#### <a name="booleans"></a>Wartości logiczne

- EDM. Boolean
- bool
- boolean

#### <a name="dates"></a>Daty

- EDM. DateTime
- data
- datetime

#### <a name="geography"></a>Lokalizacja geograficzna

- EDM. Geography
- geograficzne

#### <a name="strings"></a>Ciągi

- EDM. String
- varchar
- tekst
- ciąg

Jeśli nie można wyodrębnić informacji o typie z nagłówka, a dynamiczna opcja wpisywania jest włączona podczas odczytywania, każda komórka zostanie przeanalizowana indywidualnie, aby określić, jaki typ danych najlepiej nadaje się do rzutowania.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

[Odczytywanie i zapisywanie danych przestrzennych](spatial-io-read-write-spatial-data.md)
