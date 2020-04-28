---
title: Jak utworzyć funkcje zdefiniowane przez użytkownika — w usłudze Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Jak utworzyć funkcje zdefiniowane przez użytkownika, przydziały i przypisania ról w usłudze Azure Digital bliźniaczych reprezentacji.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: 232d85789c25e905873286eba6fda32c327a6e25
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76276935"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Jak utworzyć funkcje zdefiniowane przez użytkownika w usłudze Azure Digital bliźniaczych reprezentacji

[Funkcje zdefiniowane przez użytkownika](./concepts-user-defined-functions.md) umożliwiają użytkownikom konfigurowanie logiki niestandardowej, która ma być wykonywana z przychodzących komunikatów telemetrycznych i metadanych grafu przestrzennego. Użytkownicy mogą również wysyłać zdarzenia do wstępnie zdefiniowanych [punktów końcowych](./how-to-egress-endpoints.md).

W tym przewodniku przedstawiono przykład pokazujący, jak wykryć i ostrzec każdy odczyt, który przekracza określoną temperaturę z odebranych zdarzeń temperatury.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Dokumentacja biblioteki klienta

Funkcje dostępne jako metody pomocnika w środowisku uruchomieniowym funkcji zdefiniowanych przez użytkownika są wymienione w dokumencie [Dokumentacja biblioteki klienta](./reference-user-defined-functions-client-library.md) .

## <a name="create-a-matcher"></a>Tworzenie dopasowania

Dopasowania są obiektami grafów, które określają, jakie funkcje zdefiniowane przez użytkownika są uruchamiane dla danego komunikatu telemetrii.

- Porównań prawidłowy warunek dopasowania:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Prawidłowe cele warunku dopasowania:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

Poniższy przykład dopasowania zwraca wartość true dla dowolnego zdarzenia telemetrii czujnika z `"Temperature"` wartością typu danych. Można utworzyć wiele odpowiedników dla funkcji zdefiniowanej przez użytkownika, wykonując uwierzytelnione żądanie HTTP POST:

```URL
YOUR_MANAGEMENT_API_URL/matchers
```

Z treścią JSON:

```JSON
{
  "id": "3626464-f39b-46c0-d9b0c-436aysj55",
  "name": "Temperature Matcher",
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "conditions": [
    {
      "id": "ag7gq35cfu3-e15a-4e9c-6437-sj6w68sy44s",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ]
}
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Region serwera, w którym jest hostowane używane wystąpienie |

## <a name="create-a-user-defined-function"></a>Tworzenie funkcji zdefiniowanej przez użytkownika

Tworzenie funkcji zdefiniowanej przez użytkownika obejmuje utworzenie wieloczęściowego żądania HTTP do interfejsów API usługi Azure Digital bliźniaczych reprezentacji Management.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Po utworzeniu dopasowań należy przekazać fragment kodu funkcji z następującym uwierzytelnionym wieloczęściowym żądaniem HTTP POST do:

```URL
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Użyj następującej treści:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Wartość | Zamień na |
| --- | --- |
| USER_DEFINED_BOUNDARY | Nazwa granicy wieloczęściowej zawartości |
| YOUR_SPACE_IDENTIFIER | Identyfikator przestrzeni  |
| YOUR_MATCHER_IDENTIFIER | Identyfikator obiektu odpowiadającego, którego chcesz użyć |

1. Sprawdź, czy nagłówki obejmują: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Sprawdź, czy treść jest wieloczęściowa:

   - Pierwsza część zawiera wymagane metadane funkcji zdefiniowanej przez użytkownika.
   - Druga część zawiera logikę obliczeniową języka JavaScript.

1. W sekcji **USER_DEFINED_BOUNDARY** Zastąp wartości **spaceId** (`YOUR_SPACE_IDENTIFIER`) i **Matchs** (`YOUR_MATCHER_IDENTIFIER`).
1. Sprawdź, czy funkcja JavaScript zdefiniowana przez użytkownika jest podana jako `Content-Type: text/javascript`.

### <a name="example-functions"></a>Przykładowe funkcje

Ustaw odczytywanie danych telemetrycznych czujnika bezpośrednio dla czujnika z **temperaturą**typu dane `sensor.DataType`:

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

Parametr **telemetrii** uwidacznia atrybuty **SensorId** i **Message** , odpowiadające komunikatowi wysyłanemu przez czujnik. Parametr **kontekście wykonywania** uwidacznia następujące atrybuty:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

W następnym przykładzie rejestrujemy komunikat, jeśli odczyt danych telemetrycznych czujnika przekracza wstępnie zdefiniowany próg. Jeśli ustawienia diagnostyczne są włączone w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, dzienniki funkcji zdefiniowanych przez użytkownika również są przesyłane dalej:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

Poniższy kod wyzwala powiadomienie, jeśli poziom temperatury rośnie powyżej wstępnie zdefiniowanej stałej:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

Aby uzyskać bardziej złożony przykładowy kod funkcji zdefiniowany przez użytkownika, zapoznaj się z [przewodnikiem Szybki Start](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Utwórz przypisanie roli

Utwórz przypisanie roli dla funkcji zdefiniowanej przez użytkownika do uruchomienia. Jeśli dla funkcji zdefiniowanej przez użytkownika nie istnieje przypisanie roli, nie będzie ono miało odpowiednich uprawnień do współdziałania z interfejsem API zarządzania ani dostępu do wykonywania akcji na obiektach grafu. Akcje, które może wykonywać funkcja zdefiniowana przez użytkownika, są określane i definiowane za pośrednictwem kontroli dostępu opartej na rolach w ramach interfejsów API usługi Azure Digital bliźniaczych reprezentacji Management. Na przykład funkcje zdefiniowane przez użytkownika mogą być ograniczone do zakresu przez określenie pewnych ról lub określonych ścieżek kontroli dostępu. Aby uzyskać więcej informacji, Przeczytaj dokumentację [kontroli dostępu opartą na rolach](./security-role-based-access-control.md) .

1. [Wykonaj zapytanie dotyczące systemowego interfejsu API](./security-create-manage-role-assignments.md#retrieve-all-roles) dla wszystkich ról, aby uzyskać identyfikator roli, która ma zostać przypisana do funkcji zdefiniowanej przez użytkownika. Zrób to, wykonując uwierzytelnione żądanie HTTP GET:

    ```URL
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Zachowaj żądany identyfikator roli. Zostanie ona przeniesiona jako atrybut treści JSON **roleId** (`YOUR_DESIRED_ROLE_IDENTIFIER`) poniżej.

1. **objectid** (`YOUR_USER_DEFINED_FUNCTION_ID`) będzie identyfikatorem funkcji zdefiniowanej przez użytkownika, który został utworzony wcześniej.
1. Znajdź wartość **Path** (`YOUR_ACCESS_CONTROL_PATH`), wykonując zapytania dotyczące spacji w `fullpath`.
1. Skopiuj zwróconą `spacePaths` wartość. Użyjesz poniższego. Wykonaj uwierzytelnione żądanie HTTP GET:

    ```URL
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Wartość | Zamień na |
    | --- | --- |
    | YOUR_SPACE_NAME | Nazwa miejsca, którego chcesz użyć |

1. Wklej zwróconą `spacePaths` wartość do **ścieżki** , aby utworzyć przypisanie roli funkcji zdefiniowanej przez użytkownika, WYKONUJĄC uwierzytelnione żądanie HTTP Post:

    ```URL
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    Z treścią JSON:

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Wartość | Zamień na |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identyfikator żądanej roli |
    | YOUR_USER_DEFINED_FUNCTION_ID | Identyfikator funkcji zdefiniowanej przez użytkownika, która ma być używana |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | Identyfikator określający typ funkcji zdefiniowanej przez użytkownika (`UserDefinedFunctionId`) |
    | YOUR_ACCESS_CONTROL_PATH | Ścieżka kontroli dostępu |

>[!TIP]
> Zapoznaj się z artykułem [jak utworzyć przypisania ról i zarządzać nimi,](./security-create-manage-role-assignments.md) Aby uzyskać więcej informacji o operacjach i punktach końcowych interfejsu API zarządzania funkcjami zdefiniowanym przez użytkownika.

## <a name="send-telemetry-to-be-processed"></a>Wyślij dane telemetryczne do przetworzenia

Czujnik zdefiniowany w grafie analizy przestrzennej wysyła dane telemetryczne. Z kolei Telemetria wyzwala wykonywanie przekazanej funkcji zdefiniowanej przez użytkownika. Procesor danych odbiera dane telemetryczne. Następnie tworzony jest plan wykonania dla wywołania funkcji zdefiniowanej przez użytkownika.

1. Pobierz odpowiedniki dla czujnika, z którego zostało wygenerowane odczytanie.
1. W zależności od tego, które dopasowania zostały pomyślnie ocenione, Pobierz skojarzone funkcje zdefiniowane przez użytkownika.
1. Wykonaj każdą funkcję zdefiniowaną przez użytkownika.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [tworzyć punkty końcowe usługi Azure Digital bliźniaczych reprezentacji](./how-to-egress-endpoints.md) do wysyłania zdarzeń do programu.

- Aby uzyskać więcej informacji na temat routingu w usłudze Azure Digital bliźniaczych reprezentacji, Odczytaj [zdarzenia i komunikaty routingu](./concepts-events-routing.md).

- Zapoznaj się z [dokumentacją dotyczącą biblioteki klienta](./reference-user-defined-functions-client-library.md).