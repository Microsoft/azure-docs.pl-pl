---
title: Zapytanie w usłudze Azure IoT Hub Routing komunikatów | Microsoft Docs
description: Dowiedz się więcej na temat języka zapytań dotyczących routingu komunikatów IoT Hub, którego możesz użyć, aby zastosować zaawansowane zapytania do wiadomości w celu uzyskania danych, które są dla Ciebie ważne.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 9b5463ba789a1bcfb707fb03c70f1a8464cb6b59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767351"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Składnia zapytania dotyczącego routingu komunikatów usługi IoT Hub

Routing komunikatów umożliwia użytkownikom kierowanie różnych typów danych, takich jak komunikaty telemetryczne urządzenia, zdarzenia cyklu życia urządzenia i zdarzenia zmiany z urządzeń z różnymi punktami końcowymi. Możesz również zastosować zaawansowane zapytania do tych danych przed ich kierowaniem, aby otrzymywać do nich dane. W tym artykule opisano język zapytań routingu komunikatów IoT Hub i przedstawiono kilka typowych wzorców zapytań.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Routing komunikatów umożliwia wykonywanie zapytań dotyczących właściwości komunikatów i treści wiadomości, a także znaczników sznurka urządzenia i właściwości sznurów urządzenia. Jeśli treść komunikatu nie jest JSON, routing komunikatów może nadal kierować komunikat, ale nie można zastosować zapytań do treści komunikatu.  Zapytania są opisane jako wyrażenia logiczne, w których wartość logiczna true powoduje, że zapytanie powiodło się, które kieruje wszystkie dane przychodzące, a wartość logiczna false kończy się niepowodzeniem i nie są kierowane żadne dane. Jeśli wyrażenie daje w wyniku wartość null lub undefined, jest traktowane jako FAŁSZ i w przypadku awarii zostanie wygenerowany błąd w dziennikach diagnostycznych. Składnia zapytania musi być prawidłowa dla trasy, która ma zostać zapisana i oceniona.  

## <a name="message-routing-query-based-on-message-properties"></a>Kwerenda routingu komunikatów oparta na właściwościach komunikatów 

IoT Hub definiuje [typowy format](iot-hub-devguide-messages-construct.md) dla wszystkich komunikatów przesyłanych z urządzenia do chmury w celu współdziałania z protokołami. IoT Hub komunikat przyjmuje następujący reprezentację w formacie JSON. Właściwości systemu są dodawane dla wszystkich użytkowników i identyfikują zawartość wiadomości. Użytkownicy mogą wybiórczo dodawać do wiadomości właściwości aplikacji. Zalecamy używanie unikatowych nazw właściwości jako IoT Hub komunikatów z urządzenia do chmury nie jest rozróżniana wielkość liter. Na przykład jeśli masz wiele właściwości o tej samej nazwie, IoT Hub wyśle tylko jedną z właściwości.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>Właściwości systemu

Właściwości systemu pomagają identyfikować zawartość i źródło komunikatów. 

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| contentType | ciąg | Użytkownik określa typ zawartości komunikatu. Aby zezwolić na zapytanie w treści wiadomości, należy ustawić wartość Application/JSON. |
| contentEncoding | ciąg | Użytkownik określa typ kodowania wiadomości. Dozwolone wartości to UTF-8, UTF-16, UTF-32, jeśli dla właściwości contentType ustawiono wartość Application/JSON. |
| iothub-Connection-ID urządzenia | ciąg | Ta wartość jest ustawiana przez IoT Hub i identyfikuje identyfikator urządzenia. Aby wykonać zapytanie, użyj `$connectionDeviceId` . |
| iothub — enqueuedtime | ciąg | Ta wartość jest ustawiana przez IoT Hub i reprezentuje rzeczywisty czas umieszczenie komunikatu w formacie UTC. Aby wykonać zapytanie, użyj `enqueuedTime` . |
| DT — schemat elementu | ciąg |  Ta wartość jest ustawiana przez Centrum IoT Hub w komunikatach przesyłanych z urządzenia do chmury. Zawiera identyfikator modelu urządzenia ustawiony w ramach połączenia urządzenia. Aby wykonać zapytanie, użyj `$dt-dataschema` . |
| DT — temat | ciąg | Nazwa składnika wysyłającego komunikaty z urządzenia do chmury. Aby wykonać zapytanie, użyj `$dt-subject` . |

Zgodnie z opisem w komunikatach [IoT Hub](iot-hub-devguide-messages-construct.md)w komunikacie są dostępne dodatkowe właściwości systemu. Oprócz powyższych właściwości w poprzedniej tabeli można także wysyłać zapytania **connectionDeviceId**, **connectionModuleId**.

### <a name="application-properties"></a>Właściwości aplikacji

Właściwości aplikacji są ciągami zdefiniowanymi przez użytkownika, które można dodać do wiadomości. Te pola są opcjonalne.  

### <a name="query-expressions"></a>Wyrażenia zapytań

Zapytanie dotyczące właściwości systemu komunikatów musi być poprzedzone `$` symbolem. Zapytania dotyczące właściwości aplikacji są dostępne wraz z ich nazwą i nie powinny być poprzedzone `$` znakiem. Jeśli nazwa właściwości aplikacji zaczyna się od `$` , IoT Hub wyszuka ją we właściwościach systemu i nie zostanie znaleziona, będzie wyglądać we właściwościach aplikacji. Na przykład: 

Aby wykonać zapytanie o Właściwość system contentEncoding 

```sql
$contentEncoding = 'UTF-8'
```

Aby wykonać zapytanie o Właściwość processingPath aplikacji:

```sql
processingPath = 'hot'
```

Aby połączyć te zapytania, można użyć wyrażeń i funkcji logicznych:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Pełna lista obsługiwanych operatorów i funkcji jest wyświetlana w [wyrażeniach i warunkach](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="message-routing-query-based-on-message-body"></a>Zapytanie routingu komunikatów na podstawie treści wiadomości

Aby włączyć zapytania dotyczące treści wiadomości, komunikat powinien znajdować się w formacie JSON zakodowanym w formacie UTF-8, UTF-16 lub UTF-32. Wartość `contentType` musi być ustawiona na `application/JSON` i `contentEncoding` na jeden z obsługiwanych kodowań UTF we właściwości System. Jeśli te właściwości nie są określone, IoT Hub nie będzie szacować wyrażenia zapytania w treści komunikatu. 

Poniższy przykład pokazuje, jak utworzyć komunikat z poprawnie sformułowaną i zakodowaną treścią JSON: 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

> [!NOTE] 
> Pokazuje, jak obsłużyć kodowanie treści w języku JavaScript. Jeśli chcesz zobaczyć przykład w języku C#, Pobierz [przykłady w języku c# dla usługi Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rozpakuj plik master.zip. Plik Program.cs rozwiązania Visual Studio *SimulatedDevice*przedstawia sposób kodowania i przesyłania komunikatów do IoT Hub. Jest to ten sam przykład używany do testowania routingu wiadomości, zgodnie z opisem w [samouczku dotyczącym routingu wiadomości](tutorial-routing.md). W dolnej części Program.cs ma także metodę odczytywania w jednym z zakodowanych plików, dekodowanie go i zapisanie go jako ASCII, aby można było go odczytać. 


### <a name="query-expressions"></a>Wyrażenia zapytań

Zapytanie o treść wiadomości musi być poprzedzone prefiksem `$body` . W wyrażeniu zapytania można użyć odwołania do treści, odwołania do tablicy treści lub wielu odwołań do treści. Wyrażenie zapytania może także łączyć treść z odwołaniem przy użyciu właściwości systemu komunikatów i komunikatów o właściwościach aplikacji. Na przykład następujące są wszystkie prawidłowe wyrażenia zapytania: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

## <a name="message-routing-query-based-on-device-twin"></a>Zapytanie routingu komunikatów na podstawie sznurka urządzenia 

Routing komunikatów umożliwia wykonywanie zapytań dotyczących tagów i właściwości [sznurka urządzenia](iot-hub-devguide-device-twins.md) , które są obiektami JSON. Obsługiwane jest również wykonywanie zapytań dotyczących sznurka modułu. Poniżej przedstawiono przykładowe znaczniki i właściwości dotyczące sznurka urządzenia.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>Wyrażenia zapytań

Zapytanie dotyczące sznurka komunikatu musi być poprzedzone prefiksem `$twin` . Wyrażenie zapytania może również łączyć tag bliźniaczy lub odwołanie do właściwości z odwołaniem do treści, właściwościami systemu komunikatów i odwołaniami do właściwości aplikacji komunikatów. Zalecamy używanie unikatowych nazw w tagach i właściwościach, ponieważ w kwerendzie nie jest rozróżniana wielkość liter. Dotyczy to zarówno bliźniaczych reprezentacji urządzeń, jak i modułu bliźniaczych reprezentacji. Należy również zrezygnować z używania `twin` , `$twin` , `body` , lub `$body` , jako nazwy właściwości. Na przykład następujące są wszystkie prawidłowe wyrażenia zapytania: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

Zapytanie routingu dotyczące sznurka treści lub urządzenia z kropką w ładunku lub nazwie właściwości nie jest obsługiwane.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [routingu komunikatów](iot-hub-devguide-messages-d2c.md).
* Wypróbuj [samouczek routingu komunikatów](tutorial-routing.md).
