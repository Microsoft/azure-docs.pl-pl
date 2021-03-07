---
title: Wprowadzenie do Azure Stream Analytics funkcji geoprzestrzennych
description: W tym artykule opisano funkcje geograficzne, które są używane w Azure Stream Analytics zadaniach.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: a5e65ddacfd4c168d4b97816e448951bacc2b534
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438544"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Wprowadzenie do Stream Analytics funkcji geoprzestrzennych

Funkcje geoprzestrzenne w Azure Stream Analytics włączają analizę w czasie rzeczywistym w przypadku przesyłania strumieniowego danych geoprzestrzennych. Za pomocą zaledwie kilku wierszy kodu możesz opracować rozwiązanie klasy produkcyjnej dla złożonych scenariuszy. Te funkcje obsługują wszystkie typy WKT oraz punkt GEOJSON, Wielokąt i LineString.

Przykładowe scenariusze, które mogą korzystać z funkcji geoprzestrzennych, to m.in.:

* Udostępnianie przez jadące
* Zarządzanie flotą
* Śledzenie zasobów
* Ogrodzenie geograficzne
* Śledzenie telefonów w witrynach komórkowych

Język zapytań Stream Analytics ma siedem wbudowanych funkcji geoprzestrzennych: **CreateLineString**, dołączenia, tworzenie **wielokąta**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS** i **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

`CreateLineString`Funkcja akceptuje punkty i zwraca LineString GEOJSON, który można wykreślić jako linię na mapie. Musisz mieć co najmniej dwa punkty, aby utworzyć LineString. Punkty LineString zostaną połączone w pożądanej kolejności.

Poniższe zapytanie używa `CreateLineString` do tworzenia LineString przy użyciu trzech punktów. Pierwszy punkt jest tworzony na podstawie danych wejściowych przesyłanych strumieniowo, a pozostałe dwa są tworzone ręcznie.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|latitude|Długość geograficzna|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Przykład danych wyjściowych  

 {"Type": "LineString", "współrzędne": [[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5]]}

 {"Type": "LineString", "współrzędne": [[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5]]}

Aby dowiedzieć się więcej, odwiedź odwołanie [CreateLineString](/stream-analytics-query/createlinestring) .

## <a name="createpoint"></a>CreatePoint

`CreatePoint`Funkcja akceptuje szerokość geograficzną i długość i zwraca punkt GEOJSON, który można wykreślić na mapie. Latitudes i Długość geograficzna muszą być typu **zmiennoprzecinkowego** .

Poniższe przykładowe zapytanie używa `CreatePoint` do tworzenia punktu przy użyciu Latitudes i długości geograficznej danych wejściowych przesyłanych strumieniowo.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|latitude|Długość geograficzna|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Przykład danych wyjściowych
  
 {"Type": "Point", "współrzędne": [-10,2, 3,0]}  
  
 {"Type": "Point", "współrzędne": [20,2321,-87,33]}  

Aby dowiedzieć się więcej, odwiedź odwołanie do [punktu](/stream-analytics-query/createpoint) .

## <a name="createpolygon"></a>CreatePolygon

`CreatePolygon`Funkcja akceptuje punkty i zwraca rekord wielokąta GEOJSON. Kolejność punktów musi następować po prawej stronie lub w prawo. Wyobraź sobie, że jest to idące od jednego punktu do drugiego w kolejności, w jakiej zostały zgłoszone. Środek wielokąta będzie znajdował się w lewej i cały czas.

Poniższe przykładowe zapytanie używa `CreatePolygon` do tworzenia wielokąta z trzech punktów. Pierwsze dwa punkty są tworzone ręcznie, a ostatni punkt jest tworzony na podstawie danych wejściowych.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|latitude|Długość geograficzna|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Przykład danych wyjściowych  

 {"Type": "Wielokąt", "współrzędne": [[[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5], [-10,2, 3,0]]]}
 
 {"Type": "Wielokąt", "współrzędne": [[[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5], [20,2321,-87,33]]]}

Aby dowiedzieć się więcej, odwiedź odwołanie do [wielokąta](/stream-analytics-query/createpolygon) .


## <a name="st_distance"></a>ST_DISTANCE
`ST_DISTANCE`Funkcja zwraca odległość między dwoma geometrie w metrach. 

Poniższe zapytanie używa `ST_DISTANCE` do generowania zdarzenia, gdy stacja gazowa jest mniejsza niż 10 km od samochodu.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Aby dowiedzieć się więcej, odwiedź stronę referencyjną [ST_DISTANCE](/stream-analytics-query/st-distance) .

## <a name="st_overlaps"></a>ST_OVERLAPS
`ST_OVERLAPS`Funkcja porównuje dwie geometrie. Jeśli geometrie się pokrywa, funkcja zwraca 1. Funkcja zwraca wartość 0, jeśli geometrie nie nakłada się na siebie. 

Poniższe zapytanie używa `ST_OVERLAPS` do generowania zdarzenia, gdy kompilacja znajduje się w możliwej oblewania strefy.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

Poniższe przykładowe zapytanie generuje zdarzenie, gdy burza jest skierowana do samochodu.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Aby dowiedzieć się więcej, odwiedź stronę referencyjną [ST_OVERLAPS](/stream-analytics-query/st-overlaps) .

## <a name="st_intersects"></a>ST_INTERSECTS
`ST_INTERSECTS`Funkcja porównuje dwie geometrie. Jeśli geometrie Intersect, funkcja zwraca 1. Funkcja zwraca wartość 0, jeśli geometrie nie przecina siebie nawzajem.

Poniższe przykładowe zapytanie używa `ST_INTERSECTS` do określenia, czy ukryte Road przecina drogę.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"Type": "LineString", "współrzędne": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"Type": "LineString", "współrzędne": [[0,0, 10,0], [0,0, 0,0], [0,0,-10,0]]}|  
|{"Type": "LineString", "współrzędne": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"Type": "LineString", "współrzędne": [[-10,0, 10,0], [0,0, 10,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Przykład danych wyjściowych  

 1  
  
 0  

Aby dowiedzieć się więcej, odwiedź stronę referencyjną [ST_INTERSECTS](/stream-analytics-query/st-intersects) .

## <a name="st_within"></a>ST_WITHIN
`ST_WITHIN`Funkcja określa, czy geometria znajduje się w innej geometrii. Jeśli pierwszy jest zawarty w ostatnim, funkcja zwróci wartość 1. Funkcja zwróci wartość 0, jeśli pierwsza geometria nie znajduje się w ciągu ostatniej.

Poniższy Przykładowa kwerenda służy `ST_WITHIN` do określenia, czy punkt docelowy dostawy znajduje się w obrębie danego wielokąta magazynu.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|deliveryDestination|hurtowni|  
|-------------------------|---------------|  
|{"Type": "Point", "współrzędne": [76,6, 10,1]}|{"Type": "Wielokąt", "współrzędne": [[0,0, 0,0], [10,0, 0,0], [10,0, 10,0], [0,0, 10,0], [0,0, 0,0]]}|  
|{"Type": "Point", "współrzędne": [15,0, 15,0]}|{"Type": "Wielokąt", "współrzędne": [[10,0, 10,0], [20,0, 10,0], [20,0, 20,0], [10,0, 20,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Przykład danych wyjściowych  

 0  
  
 1  

Aby dowiedzieć się więcej, odwiedź stronę referencyjną [ST_WITHIN](/stream-analytics-query/st-within) .

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)
