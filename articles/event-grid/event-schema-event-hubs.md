---
title: Event Hubs platformy Azure jako źródło Event Grid
description: Opisuje właściwości, które są dostępne dla zdarzeń centrów zdarzeń z Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 960aa1fe7184e1d02d28fdc135907119fee8f123
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86113687"
---
# <a name="azure-event-hubs-as-an-event-grid-source"></a>Event Hubs platformy Azure jako źródło Event Grid

Ten artykuł zawiera właściwości i schemat zdarzeń centrów zdarzeń.Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md).

## <a name="event-grid-event-schema"></a>Schemat zdarzeń usługi Event Grid

### <a name="available-event-types"></a>Dostępne typy zdarzeń

Event Hubs emituje typ zdarzenia **Microsoft. EventHub. CaptureFileCreated** podczas tworzenia pliku przechwytywania.

### <a name="example-event"></a>Przykładowe zdarzenie

To przykładowe zdarzenie pokazuje schemat zdarzenia centrów zdarzeń wywoływany, gdy funkcja przechwytywania przechowuje plik: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

### <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| subject | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| eventType | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| identyfikator | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | object | Dane zdarzenia centrum zdarzeń. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| fileUrl | ciąg | Ścieżka do pliku przechwytywania. |
| Typ | ciąg | Typ pliku przechwytywania. |
| partitionId | ciąg | Identyfikator fragmentu. |
| sizeInBytes | liczba całkowita | Rozmiar pliku. |
| eventCount | liczba całkowita | Liczba zdarzeń w pliku. |
| firstSequenceNumber | liczba całkowita | Najmniejszy numer sekwencji z kolejki. |
| lastSequenceNumber | liczba całkowita | Ostatni numer sekwencji z kolejki. |
| firstEnqueueTime | ciąg | Po raz pierwszy z kolejki. |
| lastEnqueueTime | ciąg | Ostatnia godzina z kolejki. |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki

|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md) | Gdy Event Hubs tworzy plik przechwytywania, Event Grid wysyła zdarzenie do aplikacji funkcji. Aplikacja pobiera plik przechwytywania i migruje dane do magazynu danych. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
* Aby uzyskać informacje na temat obsługi zdarzeń centrów zdarzeń, zobacz [przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md).