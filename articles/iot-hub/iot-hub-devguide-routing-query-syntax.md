---
title: Wykonywanie zapytań Azure IoT Hub routingu komunikatów | Microsoft Docs
description: Dowiedz się więcej IoT Hub zapytań routingu komunikatów, który umożliwia stosowanie rozbudowanych zapytań do komunikatów w celu odbierania ważnych danych.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: c4ba48377d868404ff130ec458e50e2b42fae977
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790521"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Składnia zapytania dotyczącego routingu komunikatów usługi IoT Hub

Routing komunikatów umożliwia użytkownikom kierowanie różnych typów danych, a mianowicie komunikatów telemetrycznych urządzenia, zdarzeń cyklu życia urządzenia i zdarzeń zmiany bliźniaczej reprezentacji urządzenia do różnych punktów końcowych. Możesz również zastosować do tych danych rozbudowane zapytania przed ich kierowaniem w celu odbierania ważnych dla Ciebie danych. W tym artykule opisano IoT Hub zapytań routingu komunikatów i przedstawiono niektóre typowe wzorce zapytań.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Routing komunikatów umożliwia wykonywanie zapytań o właściwości komunikatu i treść komunikatu, a także tagi bliźniaczej reprezentacji urządzenia i właściwości bliźniaczej reprezentacji urządzenia. Jeśli treść komunikatu nie jest w notcie JSON, routing komunikatów nadal może przekierowyować komunikat, ale nie można zastosować zapytań do treści komunikatu.  Zapytania są opisane jako wyrażenia logiczne, w których wartość logiczna true sprawia, że zapytanie kończy się powodzeniem, co kieruje wszystkie przychodzące dane, a wartość logiczna false kończy się niepowodzeniem zapytania i żadne dane nie są kierowane. Jeśli wyrażenie ma wartość null lub jest niezdefiniowane, jest ono traktowane jako fałsz i w przypadku awarii zostanie wygenerowany błąd IoT Hub [trasy](monitor-iot-hub-reference.md#routes) dzienników zasobów. Składnia zapytania musi być poprawna, aby trasa została zapisana i oceniona.  

## <a name="message-routing-query-based-on-message-properties"></a>Zapytanie routingu komunikatów oparte na właściwościach komunikatu 

W IoT Hub zdefiniowano wspólny [format](iot-hub-devguide-messages-construct.md) dla wszystkich komunikatów z urządzenia do chmury w celu współdziałania między protokołami. IoT Hub przyjmuje następującą reprezentację komunikatu w danych JSON. Właściwości systemu są dodawane dla wszystkich użytkowników i identyfikują zawartość komunikatu. Użytkownicy mogą selektywnie dodawać właściwości aplikacji do komunikatu. Zalecamy używanie unikatowych nazw właściwości, ponieważ IoT Hub komunikatów z urządzenia do chmury nie jest zróżnicowyana wielkość liter. Jeśli na przykład masz wiele właściwości o tej samej nazwie, IoT Hub wyśle tylko jedną z właściwości.  

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

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Contenttype | ciąg | Użytkownik określa typ zawartości komunikatu. Aby zezwolić na zapytanie w treści komunikatu, tę wartość należy ustawić na wartość application/JSON. |
| contentEncoding | ciąg | Użytkownik określa typ kodowania komunikatu. Dozwolone wartości to UTF-8, UTF-16 i UTF-32, jeśli dla właściwości contentType ustawiono wartość application/JSON. |
| iothub-connection-device-id | ciąg | Ta wartość jest ustawiana IoT Hub i identyfikuje identyfikator urządzenia. Aby odpytować, `$connectionDeviceId` użyj . |
| iothub-enqueuedtime | ciąg | Ta wartość jest ustawiana IoT Hub i reprezentuje rzeczywisty czas kolejkowania komunikatu w czasie UTC. Aby odpytować, `enqueuedTime` użyj . |
| dt-dataschema | ciąg |  Ta wartość jest ustawiana przez centrum IoT hub w przypadku komunikatów wysyłanych z urządzenia do chmury. Zawiera on identyfikator modelu urządzenia ustawiony w połączeniu urządzenia. Aby odpytować, `$dt-dataschema` użyj . |
| podmiot dt | ciąg | Nazwa składnika, który wysyła komunikaty z urządzenia do chmury. Aby odpytować, `$dt-subject` użyj . |

Zgodnie z opisem [w IoT Hub komunikatów](iot-hub-devguide-messages-construct.md)istnieją dodatkowe właściwości systemowe w komunikacie. Oprócz powyższych właściwości w poprzedniej tabeli można również utworzyć zapytanie **connectionDeviceId,** **connectionModuleId**.

### <a name="application-properties"></a>Właściwości aplikacji

Właściwości aplikacji to ciągi zdefiniowane przez użytkownika, które można dodać do komunikatu. Te pola są opcjonalne.  

### <a name="query-expressions"></a>Wyrażenia zapytań

Zapytanie dotyczące właściwości systemu komunikatów musi być poprzedzone `$` symbolem . Zapytania dotyczące właściwości aplikacji są dostępne przy użyciu ich nazwy i nie powinny być poprzedzone `$` symbolem . Jeśli nazwa właściwości aplikacji zaczyna się od , IoT Hub wyszuka ją we właściwościach systemu i nie zostanie znaleziona, a następnie wyszuka we `$` właściwościach aplikacji. Na przykład: 

Aby odpytać o zawartość właściwości systemuEncoding 

```sql
$contentEncoding = 'UTF-8'
```

Aby kwerendy dotyczące właściwości aplikacji processingPath:

```sql
processingPath = 'hot'
```

Aby połączyć te zapytania, można użyć wyrażeń logicznych i funkcji:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Pełna lista obsługiwanych operatorów i funkcji jest wyświetlana w [wyrażeniu i warunkach](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="message-routing-query-based-on-message-body"></a>Zapytanie routingu komunikatów oparte na treści komunikatu

Aby włączyć wykonywanie zapytań w treści komunikatu, komunikat powinien być w formacie JSON zakodowanym w formacie UTF-8, UTF-16 lub UTF-32. Właściwość `contentType` musi być ustawiona na i na `application/JSON` jedno z `contentEncoding` obsługiwanych kodowań UTF we właściwości systemowej. Jeśli te właściwości nie zostaną określone, IoT Hub nie oceni wyrażenia zapytania w treści komunikatu. 

W poniższym przykładzie pokazano, jak utworzyć komunikat z poprawnie uformowaną i zakodowaną treścią JSON: 

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
> W tym przykładzie pokazano, jak obsługiwać kodowanie treści w języku JavaScript. Jeśli chcesz zobaczyć przykład w języku C#, pobierz przykłady dla języka C# usługi [Azure IoT.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Rozpakować master.zip pliku. Plik Program.cs rozwiązania Visual Studio *SimulatedDevice* pokazuje, jak kodować i przesyłać komunikaty do IoT Hub. Jest to ten sam przykład używany do testowania routingu komunikatów, jak wyjaśniono w samouczku [Routing komunikatów.](tutorial-routing.md) W dolnej części pliku Program.cs znajduje się również metoda odczytu w jednym z zakodowanych plików, jego dekodowania i zapisu z powrotem jako pliku ASCII, aby można było go odczytać. 


### <a name="query-expressions"></a>Wyrażenia zapytań

Zapytanie w treści komunikatu musi być poprzedzone prefiksem `$body` . W wyrażeniu zapytania można użyć odwołania do treści, odwołania do tablicy treści lub wielu odwołań do treści. Wyrażenie zapytania może również łączyć odwołanie do treści z właściwościami systemu komunikatów i odwołaniem do właściwości aplikacji komunikatów. Na przykład wszystkie prawidłowe wyrażenia zapytania są następujące: 

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

> [!NOTE] 
> Zapytania i funkcje można uruchamiać tylko dla właściwości w treści odwołania. Nie można uruchamiać zapytań ani funkcji dotyczących całego odwołania do treści. Na przykład następujące zapytanie nie jest *obsługiwane* i zwróci zapytanie `undefined` :
> 
> ```sql
> $body[0] = 'Feb'
> ```

## <a name="message-routing-query-based-on-device-twin"></a>Zapytanie routingu komunikatów oparte na bliźniaczej reprezentacji urządzenia 

Routing komunikatów umożliwia wykonywanie zapytań o [tagi](iot-hub-devguide-device-twins.md) i właściwości bliźniaczej reprezentacji urządzenia, które są obiektami JSON. Obsługiwane jest również wykonywanie zapytań w bliźniaczej reprezentacji modułu. Poniżej przedstawiono przykład tagów i właściwości bliźniaczej reprezentacji urządzenia.

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

Zapytanie w bliźniaczej reprezentacji komunikatu musi mieć prefiks `$twin` . Wyrażenie zapytania może również połączyć tag bliźniaczej reprezentacji lub odwołanie do właściwości z odwołaniem do treści, właściwościami systemu komunikatów i odwołaniem do właściwości aplikacji komunikatów. Zalecamy używanie unikatowych nazw w tagach i właściwościach, ponieważ w zapytaniu nie jest wielkość liter. Dotyczy to zarówno bliźniaczych reprezentacji urządzeń, jak i bliźniaczych reprezentacji modułu. Nie należy również używać `twin` , , lub , jako nazw `$twin` `body` `$body` właściwości. Na przykład wszystkie prawidłowe wyrażenia zapytania są następujące: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

Zapytanie routingu dla treści lub bliźniaczej reprezentacji urządzenia z okresem w ładunku lub nazwie właściwości nie jest obsługiwane.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [routingu komunikatów.](iot-hub-devguide-messages-d2c.md)
* Wypróbuj [samouczek routingu komunikatów](tutorial-routing.md).
