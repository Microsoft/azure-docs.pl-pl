---
title: Funkcja platformy Azure jako procedura obsługi zdarzeń dla zdarzeń Azure Event Grid
description: Opisuje, jak można używać usługi Azure Functions jako obsługi zdarzeń dla zdarzeń Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8e48949bb5fecdf370fdf23146209ad757ffa062
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105765"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Funkcja platformy Azure jako procedura obsługi zdarzeń dla zdarzeń Event Grid

Program obsługi zdarzeń jest miejscem, w którym zdarzenie jest wysyłane. Program obsługi wykonuje akcję, aby przetworzyć zdarzenie. Kilka usług platformy Azure jest automatycznie konfigurowanych do obsługi zdarzeń, a **Azure Functions** jest jednym z nich. 

Użyj **Azure Functions** w architekturze bezserwerowej w celu reagowania na zdarzenia z Event Grid. W przypadku używania funkcji platformy Azure jako procedury obsługi Użyj wyzwalacza Event Grid zamiast ogólnego wyzwalacza HTTP. Event Grid automatycznie weryfikuje wyzwalacze Event Grid. W przypadku ogólnych wyzwalaczy HTTP należy samodzielnie zaimplementować [odpowiedź na weryfikację](webhook-event-delivery.md) .

Aby uzyskać więcej informacji, zobacz [Event Grid wyzwalacz dla Azure Functions](../azure-functions/functions-bindings-event-grid.md) w celu omówienia korzystania z wyzwalacza Event Grid w funkcjach.

## <a name="tutorials"></a>Samouczki

|Tytuł  |Opis  |
|---------|---------|
| [Szybki Start: obsługa zdarzeń za pomocą funkcji](custom-event-to-function.md) | Wysyła niestandardowe zdarzenie do funkcji do przetworzenia. |
| [Samouczek: Automatyzowanie zmiany rozmiarów załadowanych obrazów przy użyciu Event Grid](resize-images-on-storage-blob-upload-event.md) | Użytkownicy mogą przekazywać obrazy za poorednictwem aplikacji sieci Web do konta magazynu. Po utworzeniu obiektu blob magazynu Event Grid wysyła zdarzenie do aplikacji funkcji, która zmienia rozmiar przekazanego obrazu. |
| [Samouczek: przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md) | Gdy Event Hubs tworzy plik przechwytywania, Event Grid wysyła zdarzenie do aplikacji funkcji. Aplikacja pobiera plik przechwytywania i migruje dane do magazynu danych. |
| [Samouczek: przykłady dotyczące Azure Event Grid integracji Azure Service Bus](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid wysyła komunikaty z Service Bus tematu do aplikacji funkcji i aplikacji logiki. |

## <a name="rest-example-for-put"></a>Przykład REST (na potrzeby PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Następne kroki
Listę obsługiwanych programów obsługi zdarzeń zawiera artykuł [obsługi zdarzeń](event-handlers.md) . 
