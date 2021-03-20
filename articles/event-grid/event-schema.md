---
title: Schemat zdarzeń Azure Event Grid
description: Opisuje właściwości i schemat, które są obecne dla wszystkich zdarzeń. Zdarzenia składają się z zestawu pięciu wymaganych właściwości ciągu i wymaganego obiektu danych.
ms.topic: reference
ms.date: 07/07/2020
ms.openlocfilehash: 7ddc7c78c5a9e5ba2a57b21c45fb9fab65056ee9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86105884"
---
# <a name="azure-event-grid-event-schema"></a>Schemat zdarzeń Azure Event Grid

W tym artykule opisano właściwości i schemat, które są obecne dla wszystkich zdarzeń. Zdarzenia składają się z zestawu pięciu wymaganych właściwości ciągu i wymaganego obiektu danych. Właściwości są wspólne dla wszystkich zdarzeń z dowolnego wydawcy. Obiekt danych ma właściwości, które są specyficzne dla każdego wydawcy. W przypadku tematów systemowych te właściwości są specyficzne dla dostawcy zasobów, na przykład Azure Storage lub Azure Event Hubs.

Źródła zdarzeń wysyłają zdarzenia do Azure Event Grid w tablicy, które mogą mieć kilka obiektów zdarzeń. Podczas ogłaszania zdarzeń w temacie Event Grid tablica może mieć łączny rozmiar do 1 MB. Każde zdarzenie w tablicy jest ograniczone do 1 MB. Jeśli zdarzenie lub tablica jest większe niż limity rozmiaru, otrzymasz **ładunek odpowiedzi 413 za duży**. W przypadku operacji jest naliczana wartość 64 KB. Dlatego zdarzenia przekraczające 64 KB powodują naliczanie opłat za operacje, tak jakby były to wiele zdarzeń. Na przykład zdarzenie o 130 KB może pociągać za sobą operacje, tak jakby były to trzy oddzielne zdarzenia.

Event Grid wysyła zdarzenia do subskrybentów w tablicy, która ma pojedyncze zdarzenie. Takie zachowanie może ulec zmianie w przyszłości.

Schemat JSON dla zdarzenia Event Grid i każdego ładunku danych wydawcy platformy Azure można znaleźć w [magazynie schematów zdarzeń](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Schemat zdarzeń

W poniższym przykładzie przedstawiono właściwości, które są używane przez wszystkich wydawców zdarzeń:

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

Na przykład schemat opublikowany dla zdarzenia usługi Azure Blob Storage to:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Właściwości zdarzenia

Wszystkie zdarzenia mają takie same dane jak najwyższego poziomu:

| Właściwość | Typ | Wymagane | Opis |
| -------- | ---- | -------- | ----------- |
| temat | ciąg | Nie, ale jeśli ta wartość jest uwzględniona, musi dokładnie odpowiadać Event Grid tematu Azure Resource Manager identyfikator. Jeśli nie jest uwzględniony, Event Grid będzie sygnaturą zdarzenia. | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| subject | ciąg | Tak | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| eventType | ciąg | Tak | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Tak | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| identyfikator | ciąg | Tak | Unikatowy identyfikator zdarzenia. |
| dane | object | Nie | Dane zdarzenia specyficzne dla dostawcy zasobów. |
| dataVersion | ciąg | Nie, ale zostanie opatrzona pustą wartością. | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Niewymagane, ale jeśli to konieczne, musi być dokładnie zgodne ze schematem Event Grid `metadataVersion` (obecnie tylko `1` ). Jeśli nie jest uwzględniony, Event Grid będzie sygnaturą zdarzenia. | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Aby dowiedzieć się więcej o właściwościach w obiekcie danych, zobacz Źródło zdarzenia:

* [Subskrypcje platformy Azure (operacje zarządzania)](event-schema-subscriptions.md)
* [Container Registry](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Grupy zasobów (operacje zarządzania)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

W przypadku niestandardowych tematów Wydawca zdarzeń określa obiekt danych. Dane najwyższego poziomu powinny mieć te same pola co standardowe zdarzenia zdefiniowane przez zasób.

Podczas publikowania zdarzeń w niestandardowych tematach, należy utworzyć tematy dotyczące wydarzeń, które ułatwią subskrybentom dowiedzieć się, czy zainteresują się wydarzeniem. Subskrybenci używają podmiotu do filtrowania i kierowania zdarzeń. Rozważ podanie ścieżki do miejsca wystąpienia zdarzenia, dzięki czemu Subskrybenci mogą filtrować według segmentów tej ścieżki. Ścieżka umożliwia subskrybentom Zawężanie lub szerokie filtrowanie zdarzeń. Na przykład jeśli podano trzy ścieżki segmentu jak `/A/B/C` w temacie, subskrybenci mogą odfiltrować według pierwszego segmentu, `/A` Aby uzyskać obszerny zestaw zdarzeń. Subskrybenci uzyskują zdarzenia z podmiotami takimi jak `/A/B/C` lub `/A/D/E` . Inni Subskrybenci mogą odfiltrować, `/A/B` Aby uzyskać węższy zestaw zdarzeń.

Czasami podmiot wymaga więcej szczegółów na temat tego, co się stało. Na przykład, gdy plik zostanie dodany do kontenera, jest wydawcą **kont magazynu** `/blobServices/default/containers/<container-name>/blobs/<file>` . Subskrybent może filtrować według ścieżki, `/blobServices/default/containers/testcontainer` Aby uzyskać wszystkie zdarzenia dla tego kontenera, ale nie inne kontenery na koncie magazynu. Subskrybent może również filtrować lub kierować sufiksem, `.txt` Aby działał tylko z plikami tekstowymi.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
