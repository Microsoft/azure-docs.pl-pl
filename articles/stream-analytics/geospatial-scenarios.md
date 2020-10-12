---
title: Geoogrodzenie i agregacja geoprzestrzenna z Azure Stream Analytics
description: W tym artykule opisano, jak używać Azure Stream Analytics do geoogrodzenia i agregacji geoprzestrzennej.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: 9792641da4b3aebad047179e2c02dad757027801
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045267"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Scenariusze wieloogrodzeniowe i agregacji geoprzestrzennej z Azure Stream Analytics

Dzięki wbudowanym funkcjom geograficznym można używać Azure Stream Analytics do kompilowania aplikacji do scenariuszy takich jak zarządzanie flotą, udostępnianie samochodu, samochody połączone i śledzenie zasobów.

## <a name="geofencing"></a>Geofencing

Azure Stream Analytics obsługuje krótkoterminowe obliczenia geofencingu w czasie rzeczywistym w chmurze i w środowisku uruchomieniowym IoT Edge.

### <a name="geofencing-scenario"></a>Scenariusz geoogrodzenia

Firma produkcyjna musi śledzić zasoby w ich budynkach. Są one wyposażone w urządzenia GPS i chcą otrzymywać powiadomienia, jeśli urządzenie opuszcza określony obszar.

Dane referencyjne używane w tym przykładzie zawierają informacje o geoogrodzeniu dla budynków i urządzeń, które są dozwolone w poszczególnych budynkach. Należy pamiętać, że dane referencyjne mogą być statyczne lub wolno zmieniać. W tym scenariuszu są używane statyczne dane referencyjne. Strumień danych ciągle emituje identyfikator urządzenia i jego bieżącą pozycję.

### <a name="define-geofences-in-reference-data"></a>Definiowanie geoogrodzenia w danych referencyjnych

Geoogrodzenia można zdefiniować przy użyciu obiektu GEOJSON. W przypadku zadań ze zgodnością w wersji 1,2 i wyższych, geoogrodzenia można również zdefiniować za pomocą dobrze znanego tekstu (WKT) jako `NVARCHAR(MAX)` . WKT jest standardem Open Geospatial Consortium (OGC), który jest używany do reprezentowania danych przestrzennych w formacie tekstowym.

Wbudowane funkcje geograficzne mogą używać zdefiniowanych geoogrodzenia, aby dowiedzieć się, czy element znajduje się na lub z określonego wielokąta geograficznego.

Poniższa tabela zawiera przykładowe dane referencyjne geoogrodzenia, które mogą być przechowywane w usłudze Azure Blob Storage lub w tabeli Azure SQL. Każda lokacja jest reprezentowana przez wielokąta geoprzestrzenne, a każde urządzenie jest skojarzone z dozwolonym IDENTYFIKATORem witryny.

|Witryny|Nazwa witryny|Wirtualnym ogrodzeniu|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Kompiluje kompilacje 41"|"WIELOKĄT ((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|B|
|2|"Kompiluje kompilacje 40"|"WIELOKĄT ((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|Z|
|3|"Redmond — budowanie 22"|"WIELOKĄT ((-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|S|

### <a name="generate-alerts-with-geofence"></a>Generowanie alertów z geoogrodzeniem

Urządzenia mogą emitować ich identyfikatory i lokalizacje co minutę za pomocą strumienia o nazwie `DeviceStreamInput` . Poniższa tabela zawiera strumień danych wejściowych.

|Identyfikator|Położenie geopozycji|
|--------|-----------|
|Z|"POINT (-122.13292341559497 47.636318374032726)"|
|B|"POINT (-122.13338475554553 47.63743531308874)"|
|S|"POINT (-122.13354001095752 47.63627622505007)"|

Można napisać zapytanie, które dołącza strumień urządzenia z danymi referencyjnymi geoogrodzenia i generuje alert za każdym razem, gdy urządzenie znajduje się poza dozwolonym kompilacją.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

Poniższa ilustracja przedstawia geoogrodzenie. Możesz zobaczyć, gdzie urządzenia są zgodne z danymi wejściowymi strumienia.

![Tworzenie granic geograficznych](./media/geospatial-scenarios/building-geofences.png)

Urządzenie "C" znajduje się w obrębie kompilacji o IDENTYFIKATORze 2, co nie jest dozwolone w zależności od danych referencyjnych. To urządzenie powinno znajdować się w obrębie budynku o IDENTYFIKATORze 3. Uruchomienie tego zadania spowoduje wygenerowanie alertu dotyczącego określonego naruszenia.

### <a name="site-with-multiple-allowed-devices"></a>Lokacja z wieloma dozwolonymi urządzeniami

Jeśli lokacja programu zezwala na wiele urządzeń, można zdefiniować w niej tablicę identyfikatorów urządzeń `AllowedDeviceID` i można użyć funkcji User-Defined w `WHERE` celu sprawdzenia, czy identyfikator urządzenia strumienia pasuje do dowolnego identyfikatora urządzenia na tej liście. Aby uzyskać więcej informacji, zapoznaj się z samouczkiem [UDF języka JavaScript](stream-analytics-javascript-user-defined-functions.md) dla zadań w chmurze i samouczka [UDF języka C#](stream-analytics-edge-csharp-udf.md) dla zadań brzegowych.

## <a name="geospatial-aggregation"></a>Agregacja geoprzestrzenna

Azure Stream Analytics obsługuje agregacji geograficznej w czasie rzeczywistym w chmurze i w środowisku uruchomieniowym IoT Edge.

### <a name="geospatial-aggregation-scenario"></a>Scenariusz agregacji geograficznej

Firma cab chce skompilować aplikację w czasie rzeczywistym w celu obsługi swoich sterowników cab w celu zaspokojenia zabiegów w obszarach miasta, w których obecnie występuje wyższy popyt.

Firma przechowuje regiony logiczne miasta jako dane referencyjne. Każdy region jest definiowany przez RegionID, RegionName i geoogrodzenie.

### <a name="define-the-geofences"></a>Zdefiniuj geoogrodzenie

Poniższa tabela zawiera przykładowe dane referencyjne geoogrodzenia, które mogą być przechowywane w usłudze Azure Blob Storage lub w tabeli Azure SQL. Każdy region jest reprezentowany przez Wielokąt geograficzny, który jest używany do skorelowania żądań pochodzących z danych przesyłanych strumieniowo.

Te wielokąty są przeznaczone tylko do celów informacyjnych i nie reprezentują rzeczywistych oddzieleń logicznych lub fizycznych.

|RegionID|RegionName|Wirtualnym ogrodzeniu|
|--------|----------|--------|
|1|Tłumaczone|"WIELOKĄT ((-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264))"|
|2|"Chinatown"|"WIELOKĄT ((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005|
|3|"Tribeca"|"WIELOKĄT ((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Agregowanie danych w oknie czasu

Poniższa tabela zawiera dane przesyłane strumieniowo "kolarstwu".

|UserID|FromLocation|ToLocation|TripRequestedTime|
|------|------------|----------|-----------------|
|Z|"POINT (-74.00726861389182 40.71610611981975)"|"POINT (-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|B|"POINT (-74.00249841021645 40.723827238895666)"|"POINT (-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|S|"POINT (-73.99680120565864 40.716439898624024)"|"POINT (-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|„D”|"POINT (-74.00741090068288 40.71615626755086)"|"POINT (-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

Następujące zapytanie sprzęga strumień urządzenia z danymi referencyjnymi geoogrodzenia i oblicza liczbę żądań na region w przedziale czasu 15 minut co minutę.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

To zapytanie zwraca liczbę żądań co minutę przez okres ostatnich 15 minut przez każdy region w miejscowości. Te informacje mogą być łatwo wyświetlane przez pulpit nawigacyjny Power BI lub wysyłane do wszystkich sterowników jako wiadomości SMS za pośrednictwem integracji z usługami takimi jak Azure Functions.

Poniższy obraz ilustruje dane wyjściowe zapytania do Power BI pulpitu nawigacyjnego. 

![Wyniki wyjściowe na pulpicie nawigacyjnym Power BI](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Stream Analytics funkcji geoprzestrzennych](stream-analytics-geospatial-functions.md)
* [Funkcje geoprzestrzenne (Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
