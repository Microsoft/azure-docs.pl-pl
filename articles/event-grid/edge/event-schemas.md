---
title: Schematy zdarzeń — Azure Event Grid IoT Edge | Microsoft Docs
description: Schematy zdarzeń w Event Grid na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: ea36c40f2038d016afb0c45944a98d4d90df6240
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171571"
---
# <a name="event-schemas"></a>Schematy zdarzeń

Moduł Event Grid akceptuje i dostarcza zdarzenia w formacie JSON. Obecnie istnieją trzy schematy obsługiwane przez Event Grid:-

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

Można skonfigurować schemat, który musi być zgodny z wydawcą podczas tworzenia tematu. Jeśli nie zostanie określony, wartość domyślna to **EventGridSchema**. Zdarzenia, które nie są zgodne z oczekiwanym schematem, zostaną odrzucone.

Subskrybenci mogą również skonfigurować schemat, w którym mają zostać dostarczone zdarzenia. Jeśli nie zostanie określony, wartością domyślną jest schemat tematu.
Obecnie schemat dostarczania subskrybenta musi być zgodny ze schematem wejściowym tematu. 

## <a name="eventgrid-schema"></a>Schemat EventGrid

Schemat EventGrid składa się z zestawu wymaganych właściwości, z którymi jednostka publikowania musi być zgodna. Każdy Wydawca musi wypełnić pola najwyższego poziomu.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>Właściwości schematu EventGrid

Wszystkie zdarzenia mają następujące dane najwyższego poziomu:

| Właściwość | Typ | Wymagane | Opis |
| -------- | ---- | ----------- |-----------
| temat | ciąg | Nie | Powinien być zgodny z tematem, na którym został opublikowany. Event Grid wypełnia ją nazwą tematu, na którym jest publikowana, jeśli nie została określona. |
| subject | ciąg | Tak | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| eventType | ciąg | Tak | Typ zdarzenia dla tego źródła zdarzeń, na przykład BlobCreated. |
| eventTime | ciąg | Tak | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| ID (Identyfikator) | ciąg | Nie | Unikatowy identyfikator zdarzenia. |
| dane | object | Nie | Służy do przechwytywania danych zdarzeń specyficznych dla jednostki publikowania. |
| dataVersion | ciąg | Tak | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Nie | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

### <a name="example--eventgrid-schema-event"></a>Przykład — zdarzenie schematu EventGrid

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>Schemat CustomEvent

W schemacie niestandardowym nie ma obowiązkowych właściwości, które są wymuszane jak schemat EventGrid. Jednostka publikowania może całkowicie sterować schematem zdarzeń. Zapewnia ona maksymalną elastyczność i umożliwia scenariusze, w których już istnieje system oparty na zdarzeniach i chce ponownie używać istniejących zdarzeń i/lub nie mają być powiązane z określonym schematem.

### <a name="custom-schema-properties"></a>Niestandardowe właściwości schematu

Brak obowiązkowych właściwości. Aby określić ładunek, należy do jednostki publikowania.

### <a name="example--custom-schema-event"></a>Przykład: niestandardowe zdarzenie schematu

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>Schemat CloudEvent

Oprócz powyższych schematów Event Grid natywnie obsługuje zdarzenia w [schemacie JSON CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md). CloudEvents to otwarta Specyfikacja dla opisywania danych zdarzenia. Upraszcza to współdziałanie, dostarczając Typowy schemat zdarzeń do publikowania i zużywania zdarzeń. Jest częścią [CNCF](https://www.cncf.io/) , a obecnie dostępna wersja to 1,0-RC1.

### <a name="cloudevent-schema-properties"></a>Właściwości schematu CloudEvent

Zapoznaj się ze [specyfikacją CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) we właściwościach obowiązkowej koperty.

### <a name="example--cloud-event"></a>Przykład — wydarzenie w chmurze
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
