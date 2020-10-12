---
title: Schemat CloudEvents v 1.0 z Azure Event Grid
description: Opisuje sposób używania schematu CloudEvents v 1.0 dla zdarzeń w Azure Event Grid. Usługa obsługuje zdarzenia w implementacji JSON zdarzeń w chmurze.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d17e92c28784ca31f3c9809c93e885b22c6a38d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324182"
---
# <a name="cloudevents-v10-schema-with-azure-event-grid"></a>Schemat CloudEvents v 1.0 z Azure Event Grid

Poza [domyślnym schematem zdarzeń](event-schema.md), Azure Event Grid natywnie obsługuje zdarzenia w [implementacji JSON CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) i [powiązania protokołu HTTP](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) to [otwarta Specyfikacja](https://github.com/cloudevents/spec/blob/v1.0/spec.md) dla opisywania danych zdarzenia.

CloudEvents upraszcza współdziałanie, dostarczając Typowy schemat zdarzeń do publikowania i zużywania zdarzeń opartych na chmurze. Ten schemat umożliwia korzystanie z jednolitych narzędzi, standardowych metod routingu & obsługi zdarzeń oraz uniwersalne sposoby deserializacji schematu zdarzenia zewnętrznego. Ze wspólnym schematem można łatwiej zintegrować prace między platformami.

CloudEvents jest tworzona przez kilku [współpracowników](https://github.com/cloudevents/spec/blob/master/community/contributors.md), w tym firmę Microsoft, za pośrednictwem [natywnej podstawy przetwarzania w chmurze](https://www.cncf.io/). Jest ona obecnie dostępna jako wersja 1,0.

W tym artykule opisano schemat CloudEvents z Event Grid.

## <a name="sample-event-using-cloudevents-schema"></a>Przykładowe zdarzenie przy użyciu schematu CloudEvents

Oto przykład zdarzenia usługi Azure Blob Storage w formacie CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Szczegółowy opis dostępnych pól, ich typów i definicji w CloudEvents v 1.0 jest [dostępny tutaj](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Wartości nagłówków dla zdarzeń dostarczonych w schemacie CloudEvents i schemacie Event Grid są takie same, z wyjątkiem `content-type` . W przypadku schematu CloudEvents wartość tego nagłówka to `"content-type":"application/cloudevents+json; charset=utf-8"` . W przypadku schematu Event Grid wartość tego nagłówka to `"content-type":"application/json; charset=utf-8"` .

## <a name="event-grid-for-cloudevents"></a>Event Grid CloudEvents

Można użyć Event Grid zarówno dla danych wejściowych, jak i wyjściowych zdarzeń w schemacie CloudEvents. CloudEvents można używać dla zdarzeń systemowych, takich jak zdarzenia Blob Storage i zdarzenia IoT Hub i zdarzenia niestandardowe. Może również przekształcić te zdarzenia do tyłu i do przodu.


| Schemat wejściowy       | Schemat danych wyjściowych
|--------------------|---------------------
| Format CloudEvents | Format CloudEvents
| Event Grid format  | Format CloudEvents
| Event Grid format  | Event Grid format

W przypadku wszystkich schematów zdarzeń Event Grid wymaga weryfikacji podczas publikowania w temacie Event Grid i podczas tworzenia subskrypcji zdarzeń. Aby uzyskać więcej informacji, zobacz [Event Grid zabezpieczenia i uwierzytelnianie](security-authentication.md).

## <a name="next-steps"></a>Następne kroki
Zobacz [, jak używać schematu CloudEvents v 1.0 z Event Grid](cloudevents-schema.md).  
