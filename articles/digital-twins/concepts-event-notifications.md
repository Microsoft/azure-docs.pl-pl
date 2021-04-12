---
title: Powiadomienia o zdarzeniach
titleSuffix: Azure Digital Twins
description: Zobacz, jak interpretować różne typy zdarzeń i ich różne komunikaty.
author: baanders
ms.author: baanders
ms.date: 4/8/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q4
ms.openlocfilehash: 42842b00120b7e918ca5b790cce92a74ab1b99d5
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259988"
---
# <a name="event-notifications"></a>Powiadomienia o zdarzeniach

Różne zdarzenia w usłudze Azure Digital bliźniaczych reprezentacji dają **powiadomienia**, które umożliwiają zaplecze rozwiązania, gdy są wykonywane różne akcje. Są one następnie [kierowane](concepts-route-events.md) do różnych lokalizacji wewnątrz i na zewnątrz bliźniaczych reprezentacji cyfrowych platformy Azure, które mogą korzystać z tych informacji w celu podjęcia odpowiednich działań.

Istnieje kilka typów powiadomień, które mogą być generowane, a komunikaty powiadomień mogą wyglądać inaczej w zależności od typu zdarzenia, które zostały przez niego wygenerowane. W tym artykule przedstawiono szczegółowe informacje o różnych typach komunikatów i o tym, co może wyglądać.

Ten wykres pokazuje różne typy powiadomień:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="notification-structure"></a>Struktura powiadomień

Ogólnie rzecz biorąc powiadomienia składają się z dwóch części: nagłówka i treści. 

### <a name="event-notification-headers"></a>Nagłówki powiadomień o zdarzeniach

Nagłówki komunikatów powiadomień są reprezentowane za pomocą par klucz-wartość. W zależności od używanego protokołu (MQTT, AMQP lub HTTP) nagłówki wiadomości będą serializowane w różny sposób. W tej sekcji omówiono ogólne informacje nagłówka dotyczące komunikatów powiadomień, niezależnie od wybranego protokołu i serializacji.

Niektóre powiadomienia są zgodne ze standardem [CloudEvents](https://cloudevents.io/) . Zgodność CloudEvents jest następująca.
* Powiadomienia emitowane z urządzeń nadal są zgodne z istniejącymi specyfikacjami powiadomień
* Powiadomienia przetwarzane i emitowane przez IoT Hub nadal są zgodne z istniejącymi specyfikacjami powiadomienia, z wyjątkiem sytuacji, w których IoT Hub decydują o obsłudze CloudEvents, na przykład przez Event Grid
* Powiadomienia emitowane z [cyfrowego bliźniaczych reprezentacji](concepts-twins-graph.md) z [modelem](concepts-models.md) są zgodne z CloudEvents
* Powiadomienia przetwarzane i emitowane przez usługę Azure Digital bliźniaczych reprezentacji są zgodne z CloudEvents

Usługi muszą dodać numer sekwencyjny na wszystkich powiadomieniach, aby wskazać ich kolejność, lub zachować własne porządkowanie w inny sposób. 

Powiadomienia wysyłane przez usługę Azure Digital bliźniaczych reprezentacji do Event Grid będą automatycznie formatowane jako schemat CloudEvents lub schemat EventGridEvent, w zależności od typu schematu zdefiniowanego w temacie usługi Event Grid. 

Atrybuty rozszerzenia w nagłówkach zostaną dodane jako właściwości w schemacie Event Grid wewnątrz ładunku. 

### <a name="event-notification-bodies"></a>Jednostki powiadomień o zdarzeniach

Treści komunikatów powiadomień są opisane w tym miejscu w formacie JSON. W zależności od serializacji dotyczącej treści wiadomości (na przykład JSON, CBOR, protobuf itp.) treść komunikatu może być serializowana inaczej.

Zestaw pól, których treść zawiera, różni się od różnych typów powiadomień.

Poniższe sekcje zawierają szczegółowe informacje o różnych typach powiadomień emitowanych przez IoT Hub i usługę Azure Digital bliźniaczych reprezentacji (lub inne usługi Azure IoT). Informacje o elementach wyzwalających każdy typ powiadomienia oraz zestaw pól zawartych w poszczególnych typach treści powiadomień.

## <a name="digital-twin-change-notifications"></a>Powiadomienia o zmianach cyfrowych przędzy

**Powiadomienia cyfrowych przędzy zmiany** są wyzwalane, gdy jest aktualizowana cyfrowa dwuosiowa, na przykład:
* Po zmianie wartości właściwości lub metadanych.
* Gdy zmieniają się cyfrowe sznurki lub metadane składników. Przykładem tego scenariusza jest zmiana modelu dwucyfrowego sznurka.

### <a name="properties"></a>Właściwości

Poniżej znajdują się pola w treści powiadomienia o zmianach z podpisem cyfrowym.

| Nazwa    | Wartość |
| --- | --- |
| `id` | Identyfikator powiadomienia, na przykład identyfikator UUID lub licznik obsługiwany przez usługę. `source` + `id` jest unikatowy dla każdego oddzielnego zdarzenia |
| `source` | Nazwa wystąpienia usługi IoT Hub lub Digital bliźniaczych reprezentacji platformy Azure, na przykład *MyHub.Azure-Devices.NET* lub *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `data` | Dokument poprawki JSON opisujący aktualizację do sznurka. Aby uzyskać szczegółowe informacje, zobacz [szczegóły treści](#body-details) poniżej. |
| `specversion` | *1.0*<br>Wiadomość jest zgodna z tą wersją [specyfikacji CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | Identyfikator dwucyfrowego przędzy |
| `time` | Sygnatura czasowa dla momentu wykonania operacji na dwuosiowej |
| `traceparent` | Kontekst śledzenia W3C dla zdarzenia |

### <a name="body-details"></a>Szczegóły treści

W komunikacie `data` pole zawiera dokument poprawki JSON zawierający aktualizację do wielocyfrowej dwuosiowej.

Załóżmy na przykład, że cyfrowa dwuosiowa została zaktualizowana przy użyciu następującej poprawki.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component-2.json":::

Dane w odpowiednim powiadomieniu (jeśli są synchronicznie wykonywane przez usługę, takie jak usługa Azure Digital bliźniaczych reprezentacji w celu zaktualizowania cyfrowej sieci dwuosiowej), powinny mieć treść taką jak:

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

Są to informacje, które zostaną umieszczone w `data` polu komunikatu z powiadomieniem o cyklu życia.

## <a name="digital-twin-lifecycle-notifications"></a>Powiadomienia dotyczące cyklu życia cyfrowych przędzy

Wszystkie powiadomienia dotyczące emisji [Digital bliźniaczych reprezentacji](concepts-twins-graph.md) , niezależnie od tego, czy reprezentują one [IoT Hub urządzenia w usłudze Azure Digital bliźniaczych reprezentacji](how-to-ingest-iot-hub-data.md) . Dzieje się tak z powodu **powiadomień dotyczących cyklu życia**, które są związane z samodzielnymi przędzami cyfrowymi.

Powiadomienia o cyklu życia są wyzwalane, gdy:
* Zostanie utworzona dwuosiowa cyfra
* Usunięto dwuosiową cyfrę

### <a name="properties"></a>Właściwości

Poniżej przedstawiono pola w treści powiadomienia o cyklu życia.

| Nazwa | Wartość |
| --- | --- |
| `id` | Identyfikator powiadomienia, na przykład identyfikator UUID lub licznik obsługiwany przez usługę. `source` + `id` jest unikatowy dla każdego oddzielnego zdarzenia. |
| `source` | Nazwa wystąpienia usługi IoT Hub lub Digital bliźniaczych reprezentacji platformy Azure, na przykład *MyHub.Azure-Devices.NET* lub *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `data` | Dane dotyczące sznurka, w którym wystąpiło zdarzenie cyklu życia. Aby uzyskać szczegółowe informacje, zobacz [szczegóły treści](#body-details-1) poniżej. |
| `specversion` | *1.0*<br>Wiadomość jest zgodna z tą wersją [specyfikacji CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | Identyfikator dwucyfrowego przędzy |
| `time` | Sygnatura czasowa dla momentu wykonania operacji na przędze |
| `traceparent` | Kontekst śledzenia W3C dla zdarzenia |

### <a name="body-details"></a>Szczegóły treści

Oto przykład komunikatu z powiadomieniem o cyklu życia: 

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

W komunikacie `data` pole zawiera dane w formacie JSON, którego dotyczy. Schematem tego jest *Digital bliźniaczych reprezentacji Resource 7,1*.

W przypadku zdarzeń tworzenia `data` ładunek odzwierciedla stan sznurka po utworzeniu zasobu, dlatego powinien obejmować wszystkie elementy generowane przez system, podobnie jak `GET` wywołanie.

Poniżej znajduje się przykład danych dla urządzenia [IoT Plug and Play (PNP)](../iot-pnp/overview-iot-plug-and-play.md) ze składnikami i bez właściwości najwyższego poziomu. Właściwości, które nie mają sensu dla urządzeń (takich jak raportowane właściwości), powinny być pomijane. Są to informacje, które zostaną umieszczone w `data` polu komunikatu z powiadomieniem o cyklu życia.

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

Oto kolejny przykład danych cyfrowych sznurów. Ten plik jest oparty na [modelu](concepts-models.md)i nie obsługuje składników:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

## <a name="digital-twin-relationship-change-notifications"></a>Powiadomienia o zmianie relacji cyfrowych przędzy

**Powiadomienia o zmianach relacji** są wyzwalane, gdy zostanie utworzona, zaktualizowana lub usunięta jakakolwiek relacja dwucyfrowej dwuosiowej. 

### <a name="properties"></a>Właściwości

Poniżej przedstawiono pola w treści powiadomienia o zmianie relacji.

| Nazwa    | Wartość |
| --- | --- |
| `id` | Identyfikator powiadomienia, na przykład identyfikator UUID lub licznik obsługiwany przez usługę. `source` + `id` jest unikatowy dla każdego oddzielnego zdarzenia |
| `source` | Nazwa wystąpienia usługi Azure Digital bliźniaczych reprezentacji, na przykład *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `data` | Ładunek relacji, która została zmieniona. Aby uzyskać szczegółowe informacje, zobacz [szczegóły treści](#body-details-2) poniżej. |
| `specversion` | *1.0*<br>Wiadomość jest zgodna z tą wersją [specyfikacji CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | Identyfikator relacji, np. `<twinID>/relationships/<relationshipID>` |
| `time` | Sygnatura czasowa dla momentu wykonania operacji w relacji |
| `traceparent` | Kontekst śledzenia W3C dla zdarzenia |

### <a name="body-details"></a>Szczegóły treści

Wewnątrz wiadomości `data` pole zawiera ładunek relacji w formacie JSON. Używa tego samego formatu co `GET` żądanie dla relacji za pośrednictwem [interfejsu API DigitalTwins](/rest/api/digital-twins/dataplane/twins). 

Oto przykład danych powiadomienia o relacji aktualizacji. "Aktualizacja relacji" oznacza, że właściwości relacji zostały zmienione, przez co dane są wyświetlane w zaktualizowanej właściwości i jej nowej wartości. Są to informacje, które będą się pojawiać w `data` polu wiadomości e-mail dotyczącej relacji między cyframi.

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

Oto przykład danych dla powiadomienia o utworzeniu lub usunięciu relacji. W przypadku `Relationship.Delete` , treść jest taka sama jak `GET` żądanie i pobiera najnowszy stan przed usunięciem.

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

## <a name="digital-twin-telemetry-messages"></a>Wiadomości telemetryczne cyfrowej przędzy

**Komunikaty telemetryczne** są odbierane w usłudze Azure Digital bliźniaczych reprezentacji z połączonych urządzeń, które zbierają i wysyłają pomiary.

### <a name="properties"></a>Właściwości

Poniżej przedstawiono pola w treści komunikatu telemetrii.

| Nazwa    | Wartość |
| --- | --- |
| `id` | Identyfikator powiadomienia, który jest dostarczany przez klienta podczas wywoływania interfejsu API telemetrii. |
| `source` | W pełni kwalifikowana nazwa sznurka, do którego wysłano zdarzenie telemetrii. Używa następującego formatu: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>` . |
| `specversion` | *1.0*<br>Wiadomość jest zgodna z tą wersją [specyfikacji CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `microsoft.iot.telemetry` |
| `data` | Komunikat telemetrii, który został wysłany do bliźniaczych reprezentacji. Ładunek nie jest modyfikowany i może nie być wyrównany ze schematem sznurka, który wysłał dane telemetryczne. |
| `dataschema` | Schemat danych to identyfikator modelu sznurka lub składnika, który emituje dane telemetryczne. Na przykład `dtmi:example:com:floor4;2`. |
| `datacontenttype` | `application/json` |
| `traceparent` | Kontekst śledzenia W3C dla zdarzenia. |

### <a name="body-details"></a>Szczegóły treści

Treść zawiera pomiar danych telemetrycznych wraz z pewnymi kontekstowymi informacjami o urządzeniu.

Oto przykład treści wiadomości telemetrycznych: 

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat dostarczania zdarzeń do różnych miejsc docelowych przy użyciu punktów końcowych i tras:
* [*Koncepcje: trasy zdarzeń*](concepts-route-events.md)
