---
title: Interpretowanie danych zdarzeń
titleSuffix: Azure Digital Twins
description: Zobacz, jak interpretować różne typy zdarzeń i ich różne komunikaty.
author: baanders
ms.author: baanders
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a0f2b971eae5d37e8fb0771e213075289af6c519
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98045261"
---
# <a name="understand-event-data"></a>Informacje o zdarzeniach

Różne zdarzenia w usłudze Azure Digital bliźniaczych reprezentacji dają **powiadomienia**, które umożliwiają zaplecze rozwiązania, gdy są wykonywane różne akcje. Są one następnie [kierowane](concepts-route-events.md) do różnych lokalizacji wewnątrz i na zewnątrz bliźniaczych reprezentacji cyfrowych platformy Azure, które mogą korzystać z tych informacji w celu podjęcia odpowiednich działań.

Istnieje kilka typów powiadomień, które mogą być generowane, a komunikaty powiadomień mogą wyglądać inaczej w zależności od typu zdarzenia, które zostały przez niego wygenerowane. W tym artykule przedstawiono szczegółowe informacje o różnych typach komunikatów i o tym, co może wyglądać.

Ten wykres pokazuje różne typy powiadomień:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

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

Zestaw pól, których treść zawiera, różni się od różnych typów powiadomień. Poniżej znajdują się dwie przykładowe treści wiadomości, aby uzyskać informacje o tym, co się ogólnie zaobserwuje i może obejmować.

Komunikat telemetrii:

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

Komunikat z powiadomieniem o cyklu życia:

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

## <a name="message-format-detail-for-different-event-types"></a>Szczegóły formatu wiadomości dla różnych typów zdarzeń

W tej sekcji szczegółowo omówiono różne typy powiadomień emitowanych przez IoT Hub i usługę Azure Digital bliźniaczych reprezentacji (lub inne usługi Azure IoT). Informacje o elementach wyzwalających każdy typ powiadomienia oraz zestaw pól zawartych w poszczególnych typach treści powiadomień.

### <a name="digital-twin-life-cycle-notifications"></a>Powiadomienia o cyklu życia cyfrowych przędzy

Wszystkie powiadomienia dotyczące emisji [Digital bliźniaczych reprezentacji](concepts-twins-graph.md) , niezależnie od tego, czy reprezentują one [IoT Hub urządzenia w usłudze Azure Digital bliźniaczych reprezentacji](how-to-ingest-iot-hub-data.md) . Dzieje się tak z powodu powiadomień dotyczących cyklu życia, które są związane z **samodzielnymi** przędzami cyfrowymi.

Powiadomienia o cyklu życia są wyzwalane, gdy:
* Zostanie utworzona dwuosiowa cyfra
* Usunięto dwuosiową cyfrę

#### <a name="properties"></a>Właściwości

Poniżej znajdują się pola w treści powiadomienia o cyklu życia.

| Nazwa | Wartość |
| --- | --- |
| `id` | Identyfikator powiadomienia, na przykład identyfikator UUID lub licznik obsługiwany przez usługę. `source` + `id` jest unikatowy dla każdego oddzielnego zdarzenia. |
| `source` | Nazwa wystąpienia usługi IoT Hub lub Digital bliźniaczych reprezentacji platformy Azure, na przykład *MyHub.Azure-Devices.NET* lub *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `specversion` | *1.0*<br>Wiadomość jest zgodna z tą wersją [specyfikacji CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | Identyfikator dwucyfrowego przędzy |
| `time` | Sygnatura czasowa dla momentu wykonania operacji na przędze |
| `traceparent` | Kontekst śledzenia W3C dla zdarzenia |

#### <a name="body-details"></a>Szczegóły treści

Treść to dotknięta cyfra cyfrowa, reprezentowana w formacie JSON. Schematem tego jest *Digital bliźniaczych reprezentacji Resource 7,1*.

W przypadku zdarzeń tworzenia ładunek odzwierciedla stan sznurka po utworzeniu zasobu, dlatego powinien obejmować wszystkie elementy generowane przez system, podobnie jak `GET` wywołanie.

Oto przykład treści dla urządzenia [IoT Plug and Play (PNP)](../iot-pnp/overview-iot-plug-and-play.md) ze składnikami i bez właściwości najwyższego poziomu. Właściwości, które nie mają sensu dla urządzeń (takich jak raportowane właściwości), powinny być pomijane.

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

Oto inny przykład cyfrowej dwuosiowej. Ten plik jest oparty na [modelu](concepts-models.md)i nie obsługuje składników:

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

### <a name="digital-twin-relationship-change-notifications"></a>Powiadomienia o zmianie relacji cyfrowych przędzy

**Powiadomienia o zmianach relacji** są wyzwalane, gdy zostanie utworzona, zaktualizowana lub usunięta jakakolwiek relacja dwucyfrowej dwuosiowej. 

#### <a name="properties"></a>Właściwości

Poniżej przedstawiono pola w treści powiadomienia o zmianie krawędzi.

| Nazwa    | Wartość |
| --- | --- |
| `id` | Identyfikator powiadomienia, na przykład identyfikator UUID lub licznik obsługiwany przez usługę. `source` + `id` jest unikatowy dla każdego oddzielnego zdarzenia |
| `source` | Nazwa wystąpienia usługi Azure Digital bliźniaczych reprezentacji, na przykład *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `specversion` | *1.0*<br>Wiadomość jest zgodna z tą wersją [specyfikacji CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`
|`datacontenttype`| `application/json` |
| `subject` | Identyfikator relacji, np. `<twinID>/relationships/<relationshipID>` |
| `time` | Sygnatura czasowa dla momentu wykonania operacji w relacji |
| `traceparent` | Kontekst śledzenia W3C dla zdarzenia |

#### <a name="body-details"></a>Szczegóły treści

Treść jest ładunkiem relacji, również w formacie JSON. Używa tego samego formatu co `GET` żądanie dla relacji za pośrednictwem [interfejsu API DigitalTwins](/rest/api/digital-twins/dataplane/twins). 

"Aktualizacja relacji" oznacza, że właściwości relacji zostały zmienione. 

Oto przykład powiadomienia o relacji aktualizacji w celu zaktualizowania właściwości:

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

W przypadku `Relationship.Delete` , treść jest taka sama jak `GET` żądanie i pobiera najnowszy stan przed usunięciem.

Oto przykład powiadomienia dotyczącego tworzenia lub usuwania relacji:

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

### <a name="digital-twin-change-notifications"></a>Powiadomienia o zmianach cyfrowych przędzy

**Powiadomienia cyfrowych przędzy zmiany** są wyzwalane, gdy jest aktualizowana cyfrowa dwuosiowa, na przykład:
* Po zmianie wartości właściwości lub metadanych.
* Gdy zmieniają się cyfrowe sznurki lub metadane składników. Przykładem tego scenariusza jest zmiana modelu dwucyfrowego sznurka.

#### <a name="properties"></a>Właściwości

Poniżej znajdują się pola w treści powiadomienia o zmianach z podpisem cyfrowym.

| Nazwa    | Wartość |
| --- | --- |
| `id` | Identyfikator powiadomienia, na przykład identyfikator UUID lub licznik obsługiwany przez usługę. `source` + `id` jest unikatowy dla każdego oddzielnego zdarzenia |
| `source` | Nazwa wystąpienia usługi IoT Hub lub Digital bliźniaczych reprezentacji platformy Azure, na przykład *MyHub.Azure-Devices.NET* lub *mydigitaltwins.westus2.azuredigitaltwins.NET*
| `specversion` | *1.0*<br>Wiadomość jest zgodna z tą wersją [specyfikacji CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | Identyfikator dwucyfrowego przędzy |
| `time` | Sygnatura czasowa dla momentu wykonania operacji na dwuosiowej |
| `traceparent` | Kontekst śledzenia W3C dla zdarzenia |

#### <a name="body-details"></a>Szczegóły treści

Treść `Twin.Update` powiadomienia jest dokumentem poprawki JSON zawierającym aktualizację wieloosiowej sieci.

Załóżmy na przykład, że cyfrowa dwuosiowa została zaktualizowana przy użyciu następującej poprawki.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component-2.json":::

Odpowiednie powiadomienie (jeśli synchronicznie wykonywane przez usługę, takie jak usługa Azure Digital bliźniaczych reprezentacji, aktualizacja wielocyfrowej sznurka) będzie miało treść taką jak:

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

## <a name="next-steps"></a>Następne kroki

Zobacz, jak tworzyć punkty końcowe i trasy do dostarczania zdarzeń:
* [*Instrukcje: Zarządzanie punktami końcowymi i trasami*](how-to-manage-routes-apis-cli.md)

Lub Dowiedz się więcej na temat interfejsów API Digital bliźniaczych reprezentacji i opcji zestawu SDK usługi Azure:
* [*Instrukcje: korzystanie z interfejsów API i zestawów SDK Digital bliźniaczych reprezentacji na platformie Azure*](how-to-use-apis-sdks.md)