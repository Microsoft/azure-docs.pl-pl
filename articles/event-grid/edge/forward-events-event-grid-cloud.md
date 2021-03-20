---
title: Prześlij dalej zdarzenia brzegowe do Event Grid chmury Azure Event Grid IoT Edge | Microsoft Docs
description: Prześlij dalej zdarzenia brzegowe do chmury Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: bfe150a45c70bc5bed18f8e929c9567905cd38f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171605"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Samouczek: Przekazywanie zdarzeń do Event Grid chmury

W tym artykule omówiono wszystkie kroki niezbędne do przechodzenia do przodu zdarzeń brzegowych w celu Event Grid w chmurze platformy Azure. Można to zrobić z następujących powodów:

* Reagowanie na zdarzenia brzegowe w chmurze.
* Przekazuj zdarzenia do Event Grid w chmurze i korzystaj z usługi Azure Event Hubs lub kolejek usługi Azure Storage, aby buforować zdarzenia przed ich przetworzeniem w chmurze.

 Aby ukończyć ten samouczek, musisz znać Event Grid koncepcje w witrynie [Edge](concepts.md) i na [platformie Azure](../concepts.md). Aby uzyskać dodatkowe typy miejsc docelowych, zobacz [programy obsługi zdarzeń](event-handlers.md). 

## <a name="prerequisites"></a>Wymagania wstępne 
Aby można było ukończyć ten samouczek, potrzebne są:

* **Subskrypcja platformy Azure** — Utwórz [bezpłatne konto](https://azure.microsoft.com/free) , jeśli jeszcze go nie masz. 
* **Azure IoT Hub i IoT Edge Device** — wykonaj kroki opisane w przewodniku szybki start dla urządzeń z systemem [Linux](../../iot-edge/quickstart-linux.md) lub [Windows](../../iot-edge/quickstart.md) , jeśli jeszcze go nie masz.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Utwórz temat i subskrypcję usługi Event Grid w chmurze

Utwórz temat i subskrypcję usługi Event Grid w chmurze, wykonując czynności opisane w [tym samouczku](../custom-event-quickstart-portal.md). Zwróć uwagę `topicURL` , `sasKey` i `topicName` nowo utworzonego tematu, który będzie używany w dalszej części tego samouczka.

Jeśli na przykład został utworzony temat o nazwie `testegcloudtopic` w regionie zachodnie stany USA, wartości będą wyglądać następująco:

* **TopicUrl**: `https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **Temat**: `testegcloudtopic`
* **SasKey**: dostępne pod **AccessKeyem** tematu. Użyj **Klucz1**.

## <a name="create-event-grid-topic-at-the-edge"></a>Tworzenie tematu siatki zdarzeń na krawędzi

1. Utwórz topic3.jsprzy użyciu następującej zawartości. Szczegółowe informacje o ładunku można znaleźć w [dokumentacji interfejsu API](api.md) .

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Uruchom następujące polecenie, aby utworzyć temat. Należy zwrócić kod stanu HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Uruchom następujące polecenie, aby upewnić się, że temat został utworzony pomyślnie. Należy zwrócić kod stanu HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Przykładowe dane wyjściowe:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Utwórz subskrypcję Event Grid na brzegu

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Utwórz subscription3.jsprzy użyciu następującej zawartości. Szczegółowe informacje o ładunku można znaleźć w [dokumentacji interfejsu API](api.md) .

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

   >[!NOTE]
   > **EndpointUrl** określa, że adres URL tematu Event Grid w chmurze. **SasKey** odwołuje się do klucza tematu Event Grid w chmurze. Wartość w **temaciename** zostanie użyta do sygnatury wszystkich zdarzeń wychodzących do Event Grid. Może to być przydatne podczas ogłaszania w temacie domeny Event Grid. Aby uzyskać więcej informacji na temat domeny Event Grid, zobacz [domeny zdarzeń](../event-domains.md)

    Na przykład
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. Uruchom następujące polecenie, aby utworzyć subskrypcję. Należy zwrócić kod stanu HTTP 200 OK.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Uruchom następujące polecenie, aby sprawdzić, czy subskrypcja została pomyślnie utworzona. Należy zwrócić kod stanu HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Przykładowe dane wyjściowe:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Publikowanie zdarzenia na brzegu

1. Utwórz event3.jsprzy użyciu następującej zawartości. Szczegółowe informacje o ładunku można znaleźć w [dokumentacji interfejsu API](api.md) .

    ```json
        [
          {
            "id": "eventId-egcloud-0",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```

1. Uruchom następujące polecenie:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>Weryfikowanie zdarzenia brzegowego w chmurze

Aby uzyskać informacje dotyczące wyświetlania zdarzeń dostarczonych przez temat chmury, zobacz [samouczek](../custom-event-quickstart-portal.md).

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

* Uruchom następujące polecenie, aby usunąć temat i wszystkie jego subskrypcje

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Usuń temat i subskrypcje utworzone w chmurze (Azure Event Grid).

## <a name="next-steps"></a>Następne kroki

W tym samouczku opublikowano zdarzenie na krawędzi i przekazano je do Event Grid w chmurze platformy Azure. Teraz, gdy znasz podstawowe kroki, aby przekazywać do Event Grid w chmurze:

* Aby rozwiązać problemy z używaniem Azure Event Grid na IoT Edge, zobacz [Przewodnik rozwiązywania problemów](troubleshoot.md).
* Przekazuj zdarzenia do IoTHub, wykonując czynności opisane w tym [samouczku](forward-events-iothub.md)
* Przekazuj zdarzenia do elementu webhook w chmurze, wykonując czynności opisane w tym [samouczku](pub-sub-events-webhook-cloud.md)
* [Monitorowanie tematów i subskrypcji na granicy](monitor-topics-subscriptions.md)
