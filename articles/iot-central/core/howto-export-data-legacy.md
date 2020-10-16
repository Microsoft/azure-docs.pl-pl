---
title: Eksportowanie danych z platformy Azure IoT Central (starsza wersja) | Microsoft Docs
description: Jak wyeksportować dane z aplikacji IoT Central platformy Azure do usługi Azure Event Hubs, Azure Service Bus i usługi Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/25/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 812fd0c10b63cfe469a10a99069f201fcc2cc658
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126741"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-legacy"></a>Eksportowanie danych IoT do miejsc docelowych w chmurze przy użyciu eksportu danych (starsza wersja)

> [!Note]
> W tym artykule opisano starsze funkcje eksportu danych w programie IoT Central.
>
> - Aby uzyskać informacje na temat nowych funkcji eksportu danych w wersji zapoznawczej, zobacz [Eksportowanie danych IoT do lokalizacji docelowych w chmurze przy użyciu funkcji eksportowania danych](./howto-export-data.md).
> - Aby dowiedzieć się więcej o różnicach między funkcjami eksportowania danych w wersji zapoznawczej i starszej wersji, zobacz [tabelę porównania](./howto-export-data.md#comparison-of-legacy-data-export-and-data-export).

W tym artykule opisano sposób korzystania z funkcji eksportu danych w usłudze Azure IoT Central. Ta funkcja umożliwia ciągłe eksportowanie danych do wystąpień **usługi azure Event Hubs**, **Azure Service Bus**lub **Azure Blob Storage** . Eksport danych korzysta z formatu JSON i może zawierać informacje telemetryczne, informacje o urządzeniu i szablon urządzenia. Użyj wyeksportowanych danych dla:

- Szczegółowe informacje i analiza z ciepłą ścieżką. Ta opcja obejmuje wyzwalanie reguł niestandardowych w programie Azure Stream Analytics, wyzwalanie niestandardowych przepływów pracy w programie Azure Logic Apps lub przekazywanie ich przez Azure Functions do przetworzenia.
- Analiza zimnej ścieżki, na przykład modele szkoleniowe, w Azure Machine Learning lub długoterminowej analizie trendów w programie Microsoft Power BI.

> [!Note]
> Po włączeniu eksportu danych można pobrać tylko dane z tego momentu. Obecnie nie można pobrać danych przez czas, gdy eksport danych jest wyłączony. Aby zachować więcej danych historycznych, należy wcześniej włączyć eksport danych.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz być administratorem w aplikacji IoT Central lub mieć uprawnienia do eksportowania danych.

## <a name="set-up-export-destination"></a>Skonfiguruj miejsce docelowe eksportu

Miejsce docelowe eksportu musi istnieć przed skonfigurowaniem eksportu danych.

### <a name="create-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Jeśli nie masz istniejącej przestrzeni nazw Event Hubs do eksportowania do programu, wykonaj następujące kroki:

1. Utwórz [nową przestrzeń nazw Event Hubs w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Więcej informacji można znaleźć w witrynie [Azure Event Hubs docs](../../event-hubs/event-hubs-create.md).

2. Wybierz subskrypcję. Dane można eksportować do innych subskrypcji, które nie znajdują się w tej samej subskrypcji, co aplikacja IoT Central. W tym przypadku łączysz się przy użyciu parametrów połączenia.

3. Utwórz centrum zdarzeń w przestrzeni nazw Event Hubs. Przejdź do obszaru nazw, a następnie wybierz pozycję **+ centrum zdarzeń** u góry, aby utworzyć wystąpienie centrum zdarzeń.

### <a name="create-service-bus-namespace"></a>Tworzenie przestrzeni nazw Service Bus

Jeśli nie masz istniejącej przestrzeni nazw Service Bus do eksportowania do programu, wykonaj następujące kroki:

1. Utwórz [nową przestrzeń nazw Service Bus w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Więcej informacji można znaleźć w dokumentacji [Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Wybierz subskrypcję. Dane można eksportować do innych subskrypcji, które nie znajdują się w tej samej subskrypcji, co aplikacja IoT Central. W tym przypadku łączysz się przy użyciu parametrów połączenia.

3. Aby utworzyć kolejkę lub temat do eksportowania, przejdź do obszaru nazw Service Bus i wybierz pozycję **+ Queue** lub **+ temat**.

W przypadku wybrania Service Bus jako miejsca docelowego eksportu kolejki i tematy nie mogą mieć włączonej sesji lub Wykrywanie duplikatów. Jeśli jedna z tych opcji jest włączona, niektóre komunikaty nie docierają do kolejki lub tematu.

### <a name="create-storage-account"></a>Tworzenie konta magazynu

Jeśli nie masz istniejącego konta usługi Azure Storage do eksportowania, wykonaj następujące czynności:

1. Utwórz [nowe konto magazynu w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Możesz dowiedzieć się więcej na temat tworzenia nowych [kont usługi Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-portal.md) lub [kont magazynu Azure Data Lake Storage v2](../../storage/common/storage-account-create.md). Eksport danych umożliwia zapisanie danych tylko na kontach magazynu, które obsługują blokowe obiekty blob. Na poniższej liście przedstawiono znane zgodne typy kont magazynu:

    |Warstwa wydajności|Typ konta|
    |-|-|
    |Standardowa|Ogólnego przeznaczenia v2|
    |Standardowa|Ogólnego przeznaczenia v1|
    |Standardowa|Blob Storage|
    |Premium|Blokuj Magazyn obiektów BLOB|

2. Utwórz kontener na koncie magazynu. Przejdź do swojego konta magazynu. W obszarze **BLOB Service**wybierz pozycję **Przeglądaj obiekty blob**. Wybierz pozycję **+ kontener** u góry, aby utworzyć nowy kontener.

## <a name="set-up-data-export"></a>Konfigurowanie eksportu danych

Teraz, gdy masz miejsce docelowe eksportu danych do programu, wykonaj następujące kroki, aby skonfigurować eksportowanie danych.

1. Zaloguj się do aplikacji IoT Central.

2. W lewym okienku wybierz pozycję **eksport danych**.

    > [!Tip]
    > Jeśli nie widzisz **eksportu danych** w okienku po lewej stronie, nie masz uprawnień do konfigurowania eksportu danych w aplikacji. Skontaktuj się z administratorem, aby skonfigurować eksportowanie danych.

3. Wybierz przycisk **+ Nowy** . Wybierz jedną z **BLOB Storage platformy Azure**, **platformy Azure Event Hubs**, **kolejki Azure Service Bus**lub **tematu Azure Service Bus** jako miejsce docelowe eksportu. Maksymalna liczba eksportów dla aplikacji wynosi pięć.

4. Wprowadź nazwę eksportu. W polu listy rozwijanej wybierz swoją **przestrzeń nazw**lub **wprowadź parametry połączenia**.

    - Widoczne są tylko konta magazynu, Event Hubs przestrzenie nazw i Service Bus przestrzenie nazw w ramach tej samej subskrypcji, w której znajduje się aplikacja IoT Central. Jeśli chcesz wyeksportować do lokalizacji docelowej poza tą subskrypcją, wybierz pozycję **wprowadź parametry połączenia** i zobacz krok 6.
    - W przypadku aplikacji utworzonych przy użyciu bezpłatnego planu cenowego jedynym sposobem konfiguracji eksportu danych jest użycie parametrów połączenia. Aplikacje w ramach bezpłatnego planu cenowego nie mają skojarzonej subskrypcji platformy Azure.

    ![Utwórz nowe centrum zdarzeń](media/howto-export-data-legacy/export-event-hub.png)

5. W polu listy rozwijanej wybierz centrum zdarzeń, kolejkę, temat lub kontener.

6. Obowiązkowe W przypadku wybrania opcji **wprowadź parametry połączenia**pojawi się nowe pole umożliwiające wklejenie parametrów połączenia. Aby uzyskać parametry połączenia dla:

    - Event Hubs lub Service Bus, przejdź do przestrzeni nazw w Azure Portal:
        - Aby użyć parametrów połączenia dla całej przestrzeni nazw:
            1. W obszarze **Ustawienia**wybierz pozycję **zasady dostępu współdzielonego** .
            2. Utwórz nowy klucz lub wybierz istniejący klucz, który ma uprawnienia do **wysyłania** .
            3. Skopiuj podstawowe lub pomocnicze parametry połączenia
        - Aby użyć parametrów połączenia dla określonego wystąpienia centrum zdarzeń lub kolejki Service Bus lub tematu, przejdź do pozycji **jednostki > Event Hubs** lub **jednostki > kolejki** lub **jednostki > tematy**. Wybierz konkretne wystąpienie i postępuj zgodnie z tymi samymi krokami, aby uzyskać parametry połączenia.
    - Konto magazynu przejdź do konta magazynu w Azure Portal:
        - Obsługiwane są tylko parametry połączenia dla całego konta magazynu. Parametry połączenia z zakresem jednego kontenera nie są obsługiwane.
          1. W obszarze **Ustawienia**wybierz pozycję **klucze dostępu** .
          2. Skopiuj parametry połączenia Klucz1 lub parametry połączenia klucz2

    Wklej w parametrach połączenia. Wpisz nazwę wystąpienia lub **nazwa kontenera**z uwzględnieniem wielkości liter.

7. W obszarze **dane do wyeksportowania**wybierz typy danych do wyeksportowania, ustawiając typ na wartość **włączone**.

8. Aby włączyć funkcję eksportowania danych, **upewnij się,** że **włączony** przełącznik jest włączony. Wybierz pozycję **Zapisz**.

9. Po kilku minutach dane zostaną wyświetlone w wybranym miejscu docelowym.

## <a name="export-contents-and-format"></a>Eksportuj zawartość i format

Wyeksportowane dane telemetryczne zawierają w całości komunikat wysyłany przez urządzenia do IoT Central, a nie tylko wartości telemetrii. Dane eksportowanych urządzeń zawierają zmiany właściwości i metadanych wszystkich urządzeń, a wyeksportowane szablony urządzeń zawierają zmiany we wszystkich szablonach urządzeń.

W przypadku Event Hubs i Service Bus dane są eksportowane niemal w czasie rzeczywistym. Dane są we `body` właściwości i są w formacie JSON. Przykłady znajdują się poniżej.

W przypadku usługi BLOB Storage dane są eksportowane raz na minutę, przy czym każdy plik zawierający partię zmian od ostatniego wyeksportowanego pliku. Eksportowane dane są umieszczane w trzech folderach w formacie JSON. Domyślne ścieżki na koncie magazynu:

- Dane telemetryczne: _{Container}/{App-ID}/Telemetry/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Urządzenia: _{Container}/{App-ID}/Devices/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Szablony urządzeń: _{Container}/{App-ID}/deviceTemplates/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Aby przeglądać wyeksportowane pliki w Azure Portal, przejdź do pliku i wybierz kartę **Edytuj obiekt BLOB** .

## <a name="telemetry"></a>Telemetria

W przypadku Event Hubs i Service Bus IoT Central wyeksportować nowy komunikat szybko po odebraniu komunikatu z urządzenia. Każdy wyeksportowany komunikat zawiera pełną wiadomość wysłaną przez urządzenie we właściwości Body w formacie JSON.

W przypadku usługi BLOB Storage komunikaty są przetwarzane wsadowo i eksportowane raz na minutę. Eksportowane pliki używają tego samego formatu co pliki komunikatów wyeksportowane przez [IoT Hub Routing komunikatów](../../iot-hub/tutorial-routing.md) do magazynu obiektów BLOB.

> [!NOTE]
> W przypadku usługi BLOB Storage upewnij się, że urządzenia wysyłają komunikaty, które mają `contentType: application/JSON` i `contentEncoding:utf-8` (lub `utf-16` `utf-32` ). Przykład można znaleźć w [dokumentacji IoT Hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) .

Urządzenie, które wysłało dane telemetryczne, jest reprezentowane przez identyfikator urządzenia (zobacz następujące sekcje). Aby uzyskać nazwy urządzeń, wyeksportuj dane urządzenia i skorelowania poszczególnych komunikatów przy użyciu **connectionDeviceId** , który odpowiada identyfikatorowi **deviceId** komunikatu urządzenia.

Poniższy przykład pokazuje komunikat otrzymany z centrum zdarzeń lub kolejki Service Bus lub tematu:

```json
{
  "temp":81.129693132351775,
  "humid":59.488071477541247,
  "EventProcessedUtcTime":"2020-04-07T09:41:15.2877981Z",
  "PartitionId":0,
  "EventEnqueuedUtcTime":"2020-04-07T09:38:32.7380000Z"
}
```

Ten komunikat nie zawiera identyfikatora urządzenia dla wysyłającego urządzenia.

Aby pobrać identyfikator urządzenia z danych komunikatu w kwerendzie Azure Stream Analytics, użyj funkcji [GetMetadataPropertyValue](/stream-analytics-query/getmetadatapropertyvalue) . Aby zapoznać się z przykładem, zobacz zapytanie w temacie [rozszerzając platformę Azure IoT Central z regułami niestandardowymi przy użyciu Stream Analytics, Azure Functions i SendGrid](./howto-create-custom-rules.md).

Aby pobrać identyfikator urządzenia w obszarze roboczym Azure Databricks lub Apache Spark, użyj [systemProperties](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md). Aby zapoznać się z przykładem, zapoznaj się z obszarem roboczym datakosteks w sekcji [rozszerzając platformę Azure IoT Central przy użyciu funkcji Azure Databricks](./howto-create-custom-analytics.md)

Poniższy przykład przedstawia rekord wyeksportowany do magazynu obiektów blob:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>Urządzenia

Każdy komunikat lub rekord w migawce reprezentuje jedną lub więcej zmian na urządzeniu oraz jego właściwości i urządzenia oraz w chmurze od ostatniego wyeksportowanego komunikatu. Komunikat zawiera:

- `id` urządzenia w IoT Central
- `displayName` urządzenia
- Identyfikator szablonu urządzenia w `instanceOf`
- `simulated` Flaga prawda, jeśli urządzenie jest urządzeniem symulowanym
- `provisioned` Flaga ma wartość true, jeśli urządzenie zostało zainicjowane
- `approved` Flaga ma wartość true, jeśli urządzenie zostało zatwierdzone do wysyłania danych
- Wartości właściwości
- `properties` uwzględnianie wartości właściwości urządzenia i chmury

Usunięte urządzenia nie są eksportowane. Obecnie nie ma żadnych wskaźników w wyeksportowanych komunikatach dotyczących usuniętych urządzeń.

W przypadku Event Hubs i Service Bus IoT Central wysyła komunikaty zawierające dane urządzenia do centrum zdarzeń lub kolejki Service Bus lub tematu niemal w czasie rzeczywistym.

W przypadku usługi BLOB Storage Nowa migawka zawierająca wszystkie zmiany od ostatniego zapisu zostanie wyeksportowana raz na minutę.

Poniższy przykładowy komunikat przedstawia informacje o urządzeniach i danych właściwości w centrum zdarzeń lub w kolejce Service Bus lub temacie:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Ta migawka jest przykładowym komunikatem, który pokazuje urządzenia i właściwości danych w magazynie obiektów BLOB. Eksportowane pliki zawierają jeden wiersz dla każdego rekordu.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Szablony urządzeń

Każdy rekord wiadomości lub migawki reprezentuje co najmniej jedną zmianę w opublikowanym szablonie urządzenia od ostatniego wyeksportowanego komunikatu. Informacje wysyłane w każdym komunikacie lub rekordzie obejmują:

- `id` szablonu urządzenia, który jest zgodny ze `instanceOf` strumieniem urządzeń powyżej
- `displayName` szablonu urządzenia
- Urządzenie `capabilityModel` , w tym jego `interfaces` definicje telemetrii, właściwości i poleceń
- `cloudProperties` definicje
- Przesłonięcia i początkowe wartości, wbudowane przy użyciu `capabilityModel`

Usunięte szablony urządzeń nie są eksportowane. Obecnie nie ma żadnych wskaźników w wyeksportowanych komunikatach dla usuniętych szablonów urządzeń.

W przypadku Event Hubs i Service Bus IoT Central wysyła komunikaty zawierające dane szablonu urządzenia do centrum zdarzeń lub kolejki Service Bus lub tematu niemal w czasie rzeczywistym.

W przypadku usługi BLOB Storage Nowa migawka zawierająca wszystkie zmiany od ostatniego zapisu zostanie wyeksportowana raz na minutę.

Ten przykład przedstawia komunikat dotyczący danych szablonów urządzeń w centrum zdarzeń lub Service Bus kolejki lub tematu:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Ta przykładowa migawka przedstawia komunikat zawierający dane dotyczące urządzenia i właściwości w usłudze BLOB Storage. Eksportowane pliki zawierają jeden wiersz dla każdego rekordu.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```

## <a name="data-format-change-notice"></a>Powiadomienie o zmianie formatu danych

> [!Note]
> Ta zmiana nie ma wpływ na format danych strumienia telemetrii. Dotyczy tylko strumieni danych urządzeń i urządzeń.

Jeśli masz istniejący eksport danych w aplikacji w wersji zapoznawczej przy włączonym strumieniu *urządzeń* i *szablonów urządzeń* , zaktualizuj eksport do **30 czerwca 2020**. To wymaganie dotyczy eksportów do usługi Azure Blob Storage, Azure Event Hubs i Azure Service Bus.

Od 3 lutego 2020 wszystkie nowe Eksporty w aplikacjach z włączonymi urządzeniami i szablonami urządzeń będą miały format danych opisany powyżej. Wszystkie eksporty utworzone przed tą datą pozostają w starym formacie danych do 30 czerwca 2020, podczas gdy te eksporty zostaną automatycznie zmigrowane do nowego formatu danych. Nowy format danych jest zgodny z [urządzeniem](/rest/api/iotcentral/devices/get), [właściwością](/rest/api/iotcentral/devices/getproperties)urządzenia, [właściwością chmury urządzenia](/rest/api/iotcentral/devices/getcloudproperties)i obiektami [szablonu urządzenia](/rest/api/iotcentral/devicetemplates/get) w IoT Central publicznym interfejsie API.

W przypadku **urządzeń**istotne różnice między starym formatem danych a nowym formatem danych obejmują:
- `@id` w przypadku urządzenia zostanie usunięta `deviceId` Nazwa `id` 
- `provisioned` dodano flagę opisującą stan aprowizacji urządzenia
- `approved` dodano flagę w celu opisania stanu zatwierdzenia urządzenia
- `properties` uwzględnianie jednostek w publicznym interfejsie API, w tym dotyczących urządzeń i właściwości chmury

W przypadku **szablonów urządzeń**istotne różnice między starym formatem danych a nowym formatem danych obejmują:

- `@id` Nazwa szablonu urządzenia została zmieniona na `id`
- `@type` dla szablonu urządzenia zmieniono nazwę na `types` , a teraz jest tablicą

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Urządzenia (format jest przestarzały z 3 lutego 2020)

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Szablony urządzeń (format jest przestarzały z 3 lutego 2020)

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wyeksportować dane do usługi Azure Event Hubs, Azure Service Bus i usługi Azure Blob Storage, przejdź do następnego kroku:

> [!div class="nextstepaction"]
> [Jak uruchomić analizę niestandardową z kostkami](./howto-create-custom-analytics.md)