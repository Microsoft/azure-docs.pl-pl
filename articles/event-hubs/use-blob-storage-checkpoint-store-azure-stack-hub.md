---
title: Użyj magazynu obiektów Blob jako magazynu punktów kontrolnych w usłudze Azure Stack Hub (wersja zapoznawcza)
description: W tym artykule opisano sposób używania magazynu obiektów Blob jako magazynu punktów kontrolnych w centrach zdarzeń w usłudze Azure Stack Hub (w wersji zapoznawczej).
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 03/18/2020
ms.author: spelluru
ms.openlocfilehash: 2938099383c32eac493e4b4bb620f03c76ca5c44
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82023665"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Użyj magazynu obiektów Blob jako magazynu punktów kontrolnych — centra zdarzeń w usłudze Azure Stack Hub (wersja zapoznawcza)
Jeśli używasz usługi Azure Blob Storage jako magazynu punktów kontrolnych w środowisku, które obsługuje inną wersję SDK obiektów blob magazynu niż te, które są zwykle dostępne na platformie Azure, musisz użyć kodu, aby zmienić wersję interfejsu API usługi Storage do określonej wersji obsługiwanej przez to środowisko. Jeśli na przykład używasz [centrum zdarzeń w usłudze Azure Stack Hub w wersji 2002,](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)najwyższą dostępną wersją usługi Storage jest wersja 2017-11-09. W takim przypadku należy użyć kodu do kierowania wersji interfejsu API usługi magazynu do 2017-11-09. Na przykład, jak kierować określonej wersji interfejsu API magazynu, zobacz te przykłady w usłudze GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) lub [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python - [Synchroniczne,](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) [Asynchroniczne](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

> [!IMPORTANT]
> Usługa Event Hubs w usłudze Azure Stack Hub jest obecnie w [wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) i jest bezpłatna. 

Jeśli uruchomisz odbiornik Centrum zdarzeń, który używa magazynu obiektów Blob jako magazynu punktów kontrolnych bez kierowania na wersję, którą obsługuje usługa Azure Stack Hub, zostanie wyświetlony następujący komunikat o błędzie:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Przykładowy komunikat o błędzie w języku Python
W przypadku języka `azure.core.exceptions.HttpResponseError` Python błąd jest `on_error(partition_context, error)` przekazywany do obsługi błędów `EventHubConsumerClient.receive()`. Ale metoda `receive()` nie zgłasza wyjątek. `print(error)`wydrukuje następujące informacje o wyjątku:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

Rejestrator zarejestruje dwa ostrzeżenia, takie jak następujące:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Następne kroki

Zobacz następujący artykuł dowiesz się o partycjonowaniu i punktach kontrolnych: [Równoważenie obciążenia partycji w wielu wystąpieniach aplikacji](event-processor-balance-partition-load.md)
