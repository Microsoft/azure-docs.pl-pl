---
title: Analizowanie JSON i AVRO w Azure Stream Analytics
description: W tym artykule opisano sposób działania na złożonych typach danych, takich jak tablice, JSON, dane w formacie CSV.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6c2eb4225cb014b3251d12470e4e9827150a5cf2
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123357"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analizowanie danych JSON i Avro w Azure Stream Analytics

Azure Stream Analytics obsługiwać zdarzenia przetwarzania w formatach danych CSV, JSON i Avro. Dane JSON i Avro mogą być strukturalne i zawierać niektóre złożone typy, takie jak obiekty zagnieżdżone (rekordy) i tablice. 

>[!NOTE]
>Pliki AVRO utworzone przez funkcję przechwytywania centrum zdarzeń używają określonego formatu, który wymaga użycia funkcji *deserializacji niestandardowej* . Aby uzyskać więcej informacji, zobacz [odczytywanie danych wejściowych w dowolnym formacie za pomocą deserializatorów niestandardowych platformy .NET](./custom-deserializer-examples.md).
>
>Deserializacja Stream Analytics AVRO nie obsługuje typu mapy. Stream Analytics nie może odczytać obiektów BLOB przechwytywania funkcji EventHub, ponieważ funkcja przechwytywania EventHub używa map.


## <a name="record-data-types"></a>Rejestruj typy danych
Typy danych rekordów służą do reprezentowania tablic JSON i Avro, gdy w strumieniach danych wejściowych są używane odpowiednie formaty. Przykładowe przykłady przedstawiają czujnik próbek, który odczytuje zdarzenia wejściowe w formacie JSON. Oto przykład pojedynczego zdarzenia:

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```

### <a name="access-nested-fields-in-known-schema"></a>Dostęp do zagnieżdżonych pól w znanym schemacie
Użyj notacji kropki (.), aby łatwo uzyskać dostęp do zagnieżdżonych pól bezpośrednio z zapytania. Na przykład to zapytanie wybiera współrzędne geograficzne i Długość geograficzna we właściwości Location w poprzednich danych JSON. Notacja kropka może służyć do nawigowania na wielu poziomach, jak pokazano poniżej.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

Wynik:

|Identyfikator|Usługę|Długo|Temperatura|Wersja|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Zaznacz wszystkie właściwości
Można wybrać wszystkie właściwości zagnieżdżonego rekordu przy użyciu symbolu wieloznacznego "*". Rozpatrzmy następujący przykład:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Wynik:

|Identyfikator|Usługę|Długo|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Dostęp do zagnieżdżonych pól, gdy nazwa właściwości jest zmienną

Użyj funkcji [GetRecordPropertyValue](/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) , jeśli nazwa właściwości jest zmienną. Pozwala to na tworzenie zapytań dynamicznych bez nazw właściwości zakodowana.

Załóżmy na przykład, że przykładowy strumień danych musi **być połączony z danymi referencyjnymi** zawierającymi progi dla każdego czujnika urządzenia. Poniżej przedstawiono fragment kodu dotyczący takich danych referencyjnych.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

Celem jest dołączenie naszego przykładowego zestawu danych z góry artykułu do tych danych referencyjnych i wyjście jednego zdarzenia dla każdej miary czujnika powyżej jego progu. Oznacza to, że nasze pojedyncze zdarzenie powyżej może generować wiele zdarzeń wyjściowych, jeśli wiele czujników przekracza ich wartości progowe, łącząc się z sprzężeniem. Aby osiągnąć podobne wyniki bez sprzężenia, zapoznaj się z sekcją poniżej.

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**GetRecordPropertyValue** wybiera właściwość w *SensorReadings* , która jest zgodna z nazwą właściwości pochodzącą z danych referencyjnych. Następnie skojarzona wartość z *SensorReadings* jest wyodrębniana.

Wynik:

|Identyfikator|Czujnikname|AlertMessage|
|-|-|-|
|12345|Wilgotność|Alert: czujnik powyżej progu|

### <a name="convert-record-fields-into-separate-events"></a>Konwertuj pola rekordów na oddzielne zdarzenia

Aby przekonwertować pola rekordu na oddzielne zdarzenia, użyj operatora [apply](/stream-analytics-query/apply-azure-stream-analytics) razem z funkcją [GetRecordProperties](/stream-analytics-query/getrecordproperties-azure-stream-analytics) .

W przypadku oryginalnych danych przykładowych można użyć następującego zapytania do wyodrębnienia właściwości do różnych zdarzeń.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Wynik:

|Identyfikator|Czujnikname|AlertMessage|
|-|-|-|
|12345|Temperatura|80|
|12345|Wilgotność|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|SensorMetadata|[obiekt obiektu]|

W [przypadku korzystania z programu z usługą](/stream-analytics-query/with-azure-stream-analytics)można kierować te zdarzenia do różnych miejsc docelowych:

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>Analizuj rekord JSON w danych referencyjnych SQL
W przypadku używania Azure SQL Database jako danych referencyjnych w zadaniu możliwe jest posiadanie kolumny zawierającej dane w formacie JSON. Przykład przedstawiono poniżej.

|Identyfikator|Dane|
|-|-|
|12345|{"Key": "wartość1"}|
|54321|{"Key": "wartość2"}|

Rekord JSON można przeanalizować w kolumnie *dane* , pisząc prostą funkcję języka JavaScript zdefiniowaną przez użytkownika.

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

Następnie można utworzyć krok w zapytaniu Stream Analytics, jak pokazano poniżej, aby uzyskać dostęp do pól rekordów JSON.

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>Array — typy danych

Typy danych tablicy to uporządkowana Kolekcja wartości. Poniżej przedstawiono niektóre typowe operacje dotyczące wartości tablicy. W tych przykładach użyto funkcji [GetArrayElement](/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](/stream-analytics-query/getarraylength-azure-stream-analytics)i operator [apply](/stream-analytics-query/apply-azure-stream-analytics) .

Oto przykład pojedynczego zdarzenia. Obie `CustomSensor03` i `SensorMetadata` są typu **Array** :

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>Praca z określonym elementem tablicy

Wybierz element tablicy w określonym indeksie (zaznaczając pierwszy element tablicy):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Wynik:

|pierwszyelement|
|-|
|12|

### <a name="select-array-length"></a>Wybierz Długość tablicy

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Wynik:

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Konwertuj elementy tablicy na oddzielne zdarzenia

Zaznacz wszystkie elementy tablicy jako pojedyncze zdarzenia. Operator [apply](/stream-analytics-query/apply-azure-stream-analytics) wraz z wbudowaną funkcją [GetArrayElements](/stream-analytics-query/getarrayelements-azure-stream-analytics) wyodrębnia wszystkie elementy tablicy jako pojedyncze zdarzenia:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

Wynik:

|DeviceId|ArrayIndex|ArrayValue|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
Wynik:

|DeviceId|smKey|smValue|
|-|-|-|
|12345|Producent|ABC|
|12345|Wersja|1.2.45|

Jeśli wyodrębnione pola muszą pojawić się w kolumnach, można przestawiać zestaw danych przy użyciu składni [with](/stream-analytics-query/with-azure-stream-analytics) obok operacji [Join](/stream-analytics-query/join-azure-stream-analytics) . To dołączenie wymaga warunku [granicy czasu](/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) , który uniemożliwia duplikowanie:

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

Wynik:

|DeviceId|Usługę|Długo|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Zobacz też
[Typy danych w Azure Stream Analytics](/stream-analytics-query/data-types-azure-stream-analytics)