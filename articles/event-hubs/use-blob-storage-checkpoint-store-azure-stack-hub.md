---
title: Użycie usługi Blob Storage jako magazynu punktów kontrolnych w usłudze Azure Stack Hub
description: W tym artykule opisano sposób użycia Blob Storage jako magazynu punktów kontrolnych w Event Hubs w centrum Azure Stack.
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: 07d7cf844480a9a88468c17cecc7ca38cca5d176
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97007827"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub"></a>Używanie Blob Storage jako magazynu punktów kontrolnych — Event Hubs w centrum Azure Stack
Jeśli używasz platformy Azure Blob Storage jako magazynu punktów kontrolnych w środowisku obsługującym inną wersję zestawu SDK magazynu obiektów BLOB niż te, które są zwykle dostępne na platformie Azure, musisz użyć kodu, aby zmienić wersję interfejsu API usługi magazynu na określoną wersję obsługiwaną przez to środowisko. Na przykład jeśli używasz [Event Hubs w centrum Azure Stack w wersji 2002](/azure-stack/user/event-hubs-overview), najwyższa dostępna wersja usługi Storage to wersja 2017-11-09. W takim przypadku należy użyć kodu, aby docelowa wersja interfejsu API usługi Storage do 2017-11-09. Aby zapoznać się z przykładem dotyczącym konkretnej wersji interfejsu API usługi Storage, zobacz następujące przykłady w witrynie GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)
- [Język Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) lub  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python — [synchroniczne](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [asynchroniczne](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

Jeśli zostanie uruchomiony Event Hubs Receiver, który używa Blob Storage jako magazynu punktów kontrolnych bez określania wersji obsługiwanej przez centrum Azure Stack, zostanie wyświetlony następujący komunikat o błędzie:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Przykładowy komunikat o błędzie w języku Python
W przypadku języka Python błąd `azure.core.exceptions.HttpResponseError` jest przesyłany do procedury obsługi błędu `on_error(partition_context, error)` programu `EventHubConsumerClient.receive()` . Jednak Metoda `receive()` nie zgłasza wyjątku. `print(error)` Program będzie drukował następujące informacje o wyjątku:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

Rejestrator będzie rejestrował dwa ostrzeżenia, takie jak następujące:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat partycjonowania i tworzenia punktów kontrolnych można znaleźć w następującym artykule: [równoważenie obciążenia partycji w wielu wystąpieniach aplikacji](event-processor-balance-partition-load.md)
