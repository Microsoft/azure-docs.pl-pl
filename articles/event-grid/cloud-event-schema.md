---
title: Używanie usługi Azure Event Grid ze zdarzeniami w schemacie CloudEvents
description: W tym artykule opisano sposób używania schematu CloudEvents dla zdarzeń w usłudze Azure Event Grid. Usługa obsługuje zdarzenia w implementacji JSON cloud events.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 127095bef2c67a93097bf90bea54ca1b44b16c58
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394386"
---
# <a name="cloudevents-v10-schema-with-event-grid"></a>Schemat CloudEvents w wersji 1.0 z siatką zdarzeń

Oprócz [domyślnego schematu zdarzeń usługa](event-schema.md)Azure Event Grid obsługuje zdarzenia w [implementacji JSON powiązania protokołu CloudEvents w wersji 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) i [http.](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md) [CloudEvents](https://cloudevents.io/) to [otwarta specyfikacja](https://github.com/cloudevents/spec/blob/v1.0/spec.md) opisująca dane zdarzeń.

CloudEvents upraszcza współdziałanie, udostępniając wspólny schemat zdarzeń do publikowania i likwale zdarzeń opartych na chmurze. Ten schemat umożliwia jednolite narzędzia, standardowe sposoby routingu & obsługi zdarzeń i uniwersalne sposoby deserializacji schematu zdarzeń zewnętrznych. Za pomocą wspólnego schematu można łatwiej zintegrować pracę na różnych platformach.

CloudEvents jest budowany przez kilku [współpracowników,](https://github.com/cloudevents/spec/blob/master/community/contributors.md)w tym Microsoft, za pośrednictwem [Cloud Native Computing Foundation.](https://www.cncf.io/) Jest obecnie dostępny w wersji 1.0.

W tym artykule opisano schemat CloudEvents z siatką zdarzeń.

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

Szczegółowy opis dostępnych pól, ich typów i definicji w cloudevents v1.0 jest [dostępny tutaj](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Wartości nagłówków dla zdarzeń dostarczanych w schemacie CloudEvents i event `content-type`grid są takie same, z wyjątkiem . W przypadku schematu CloudEvents `"content-type":"application/cloudevents+json; charset=utf-8"`ta wartość nagłówka jest . W przypadku schematu siatki zdarzeń `"content-type":"application/json; charset=utf-8"`ta wartość nagłówka to .

## <a name="event-grid-for-cloudevents"></a>Siatka zdarzeń dla cloudevents

Można użyć usługi Event Grid dla danych wejściowych i wyjściowych zdarzeń w schemacie CloudEvents. CloudEvents można używać dla zdarzeń systemowych, takich jak zdarzenia magazynu obiektów Blob i zdarzenia Usługi IoT Hub i zdarzenia niestandardowe. Może również przekształcić te zdarzenia na drucie tam iz powrotem.


| Schemat wprowadzania       | Schemat danych wyjściowych
|--------------------|---------------------
| Format CloudEvents | Format CloudEvents
| Format siatki zdarzeń  | Format CloudEvents
| Format siatki zdarzeń  | Format siatki zdarzeń

Dla wszystkich schematów zdarzeń usługa Event Grid wymaga weryfikacji podczas publikowania w temacie siatki zdarzeń i podczas tworzenia subskrypcji zdarzeń. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia i uwierzytelnianie w siatce zdarzeń](security-authentication.md).

## <a name="next-steps"></a>Następne kroki
Zobacz [Jak używać schematu CloudEvents w wersji 1.0 z siatką zdarzeń](cloudevents-schema.md).  
