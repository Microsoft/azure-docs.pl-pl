---
title: Programy obsługi zdarzeń i miejsca docelowe — Azure Event Grid IoT Edge | Microsoft Docs
description: Programy obsługi zdarzeń i miejsca docelowe w Event Grid na krawędzi
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 18e2b944f60ebdf8a1b0c60ba446a13df13134c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171588"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Programy obsługi zdarzeń i miejsca docelowe w Event Grid na krawędzi

Program obsługi zdarzeń to miejsce, w którym zdarzenie do dalszej akcji lub przetwarzania zdarzenia. Za pomocą Event Grid w module Edge program obsługi zdarzeń może znajdować się na tym samym urządzeniu brzegowym, innym urządzeniu lub w chmurze. Możesz użyć dowolnego elementu webhook do obsługi zdarzeń lub wysłać zdarzenia do jednego z natywnych programów obsługi, takich jak Azure Event Grid.

Ten artykuł zawiera informacje dotyczące sposobu konfigurowania każdego z nich.

## <a name="webhook"></a>Elementu webhook

Aby opublikować w punkcie końcowym elementu webhook, ustaw dla opcji wartość `endpointType` `WebHook` i podaj:

* endpointUrl: adres URL punktu końcowego elementu webhook

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Aby opublikować w punkcie końcowym w chmurze Azure Event Grid, ustaw dla opcji wartość `endpointType` `eventGrid` i podaj:

* endpointUrl: adres URL tematu Event Grid w chmurze
* sasKey: klucz SAS tematu Event Grid
* Nazwa tematu: należy oznaczyć wszystkie zdarzenia wychodzące do Event Grid. Nazwa tematu jest przydatna podczas ogłaszania w temacie domeny Event Grid.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                 "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
    }
   ```

## <a name="iot-edge-hub"></a>IoT Edge Hub

Aby opublikować w module centrum brzegowego, ustaw dla opcji wartość `endpointType` `edgeHub` i podaj:

* outputName: dane wyjściowe, na których moduł Event Grid będzie kierować zdarzenia, które pasują do tej subskrypcji do edgeHub. Na przykład zdarzenia zgodne z poniższą subskrypcją będą zapisywane w/messages/modules/eventgridmodule/outputs/sampleSub4.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "edgeHub",
              "properties": {
                "outputName": "sampleSub4"
              }
            }
          }
        }
    ```

## <a name="event-hubs"></a>Event Hubs

Aby opublikować w centrum zdarzeń, ustaw polecenie `endpointType` na `eventHub` i podaj:

* connectionString: parametry połączenia dla określonego centrum zdarzeń, które są przeznaczone do wygenerowania za pośrednictwem zasad dostępu współdzielonego.

    >[!NOTE]
    > Parametry połączenia muszą być specyficzne dla jednostki. Używanie parametrów połączenia z przestrzenią nazw nie będzie działało. Parametry połączenia specyficzne dla jednostki można wygenerować, przechodząc do określonego centrum zdarzeń, które chcesz opublikować w witrynie Azure Portal, a następnie klikając pozycję **zasady dostępu współdzielonego** , aby wygenerować nowy ciąg connecection specyficzny dla jednostki.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Kolejki usługi Service Bus

Aby opublikować w kolejce Service Bus, ustaw dla opcji `endpointType` wartość `serviceBusQueue` i podaj:

* connectionString: parametry połączenia dla konkretnej kolejki Service Bus są generowane przez zasady dostępu współdzielonego.

    >[!NOTE]
    > Parametry połączenia muszą być specyficzne dla jednostki. Używanie parametrów połączenia z przestrzenią nazw nie będzie działało. Wygeneruj parametry połączenia specyficzne dla jednostki, przechodząc do konkretnej kolejki Service Bus, w której chcesz opublikować w witrynie Azure Portal, a następnie klikając pozycję **zasady dostępu współdzielonego** , aby wygenerować nowy ciąg connecection specyficzny dla jednostki.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Tematy dotyczące usługi Service Bus

Aby opublikować w temacie Service Bus, ustaw polecenie `endpointType` na `serviceBusTopic` i podaj:

* connectionString: parametry połączenia dla określonego tematu Service Bus, które są przeznaczone do wygenerowania w ramach zasad dostępu współdzielonego.

    >[!NOTE]
    > Parametry połączenia muszą być specyficzne dla jednostki. Używanie parametrów połączenia z przestrzenią nazw nie będzie działało. Wygeneruj parametry połączenia specyficzne dla jednostki, przechodząc do określonego tematu Service Bus, w którym chcesz opublikować w witrynie Azure Portal, a następnie klikając pozycję **zasady dostępu współdzielonego** , aby wygenerować nowy ciąg connecection specyficzny dla jednostki.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Kolejki usługi Storage

Aby opublikować w kolejce magazynu, ustaw polecenie  `endpointType` na `storageQueue` i podaj:

* QueueName: Nazwa kolejki magazynu, w której jest publikowany.
* connectionString: parametry połączenia dla konta magazynu, w którym znajduje się kolejka magazynu.

    >[!NOTE]
    > Nieliniowe Event Hubs, kolejki Service Bus i tematy Service Bus, parametry połączenia używane dla kolejek magazynu nie są specyficzne dla jednostki. Zamiast tego należy użyć parametrów połączenia dla konta magazynu.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```