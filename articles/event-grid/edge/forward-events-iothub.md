---
title: Prześlij dalej Event Grid zdarzenia do IoTHub-Azure Event Grid IoT Edge | Microsoft Docs
description: Prześlij dalej Event Grid zdarzenia do IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 36dc7d098892fb2be7c2ba3d75de7c7adef1a4f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171554"
---
# <a name="tutorial-forward-events-to-iothub"></a>Samouczek: Przekazywanie zdarzeń do IoTHub

W tym artykule omówiono wszystkie kroki, które należy wykonać, aby przekazywać zdarzenia Event Grid do innych modułów IoT Edge, IoTHub przy użyciu tras. Można to zrobić z następujących powodów:

* Nadal korzystaj z istniejących już inwestycji z routingiem usługi edgeHub
* Wolisz kierować wszystkie zdarzenia z urządzenia tylko za pośrednictwem IoT Hub

Aby ukończyć ten samouczek, musisz zrozumieć następujące kwestie:

- [Koncepcje Event Grid](concepts.md)
- [IoT Edge Hub](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Wymagania wstępne 
Aby można było ukończyć ten samouczek, potrzebne są:

* **Subskrypcja platformy Azure** — Utwórz [bezpłatne konto](https://azure.microsoft.com/free) , jeśli jeszcze go nie masz. 
* **Azure IoT Hub i IoT Edge Device** — wykonaj kroki opisane w przewodniku szybki start dla urządzeń z systemem [Linux](../../iot-edge/quickstart-linux.md) lub [Windows](../../iot-edge/quickstart.md) , jeśli jeszcze go nie masz.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Tworzenie tematu

Jako wydawca zdarzenia musisz utworzyć temat dotyczący siatki zdarzeń. Temat odnosi się do punktu końcowego, w którym wydawcy mogą wysyłać zdarzenia do programu.

1. Utwórz topic4.jsprzy użyciu następującej zawartości. Szczegółowe informacje o ładunku można znaleźć w [dokumentacji interfejsu API](api.md) .

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Uruchom następujące polecenie, aby utworzyć temat. Należy zwrócić kod stanu HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Uruchom następujące polecenie, aby upewnić się, że temat został utworzony pomyślnie. Należy zwrócić kod stanu HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Przykładowe dane wyjściowe:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Utwórz subskrypcję zdarzeń

Subskrybenci mogą rejestrować się w przypadku zdarzeń opublikowanych w temacie. Aby odebrać każde zdarzenie, należy utworzyć subskrypcję usługi Event Grid w temacie zainteresowania.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Utwórz subscription4.jsprzy użyciu następującej zawartości. Szczegółowe informacje o ładunku można znaleźć w [dokumentacji interfejsu API](api.md) .

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

   >[!NOTE]
   > `endpointType`Określa, że subskrybent jest `edgeHub` . `outputName`Określa dane wyjściowe, na których moduł Event Grid będzie kierować zdarzenia, które pasują do tej subskrypcji do edgeHub. Na przykład zdarzenia, które pasują do powyższej subskrypcji, będą zapisywane w `/messages/modules/eventgridmodule/outputs/sampleSub4` .
2. Uruchom następujące polecenie, aby utworzyć subskrypcję. Należy zwrócić kod stanu HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Uruchom następujące polecenie, aby sprawdzić, czy subskrypcja została pomyślnie utworzona. Należy zwrócić kod stanu HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Przykładowe dane wyjściowe:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Konfigurowanie trasy centrum brzegowego

Zaktualizuj trasę centrum brzegowego, aby przesłać dalej zdarzenia subskrypcji zdarzeń, które zostaną przekazane do IoTHub w następujący sposób:

1. Zaloguj się do witryny [Azure Portal](https://ms.portal.azure.com).
1. Przejdź do **IoT Hub**.
1. Wybierz **IoT Edge** z menu
1. Wybierz z listy urządzeń identyfikator urządzenia docelowego.
1. Wybierz pozycję **Ustaw moduły**.
1. Wybierz pozycję **dalej** i w sekcji trasy.
1. W obszarze trasy Dodaj nową trasę

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Na przykład

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > Powyższa trasa przekaże wszystkie zdarzenia dopasowane do tej subskrypcji, aby zostały przekazane do centrum IoT Hub. Funkcje [routingu centrum brzegowego](../../iot-edge/module-composition.md) umożliwiają dalsze filtrowanie i kierowanie zdarzeń Event Grid do innych modułów IoT Edge.

## <a name="setup-iot-hub-route"></a>Konfiguracja trasy IoT Hub

Zapoznaj się z [samouczkiem dotyczącym routingu IoT Hub](../../iot-hub/tutorial-routing.md) , aby skonfigurować trasę z Centrum IoT Hub, aby można było wyświetlać zdarzenia przekazane z modułu Event Grid. Użyj `true` do zapytania, aby zachować prosty samouczek.  



## <a name="publish-an-event"></a>Publikowanie zdarzenia

1. Utwórz event4.jsprzy użyciu następującej zawartości. Szczegółowe informacje o ładunku można znaleźć w [dokumentacji interfejsu API](api.md) .

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. Uruchom następujące polecenie, aby opublikować wydarzenie:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Weryfikowanie dostarczania zdarzeń

Instrukcje dotyczące wyświetlania zdarzeń można znaleźć w [samouczku IoT Hub Routing](../../iot-hub/tutorial-routing.md) .

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

* Uruchom następujące polecenie, aby usunąć temat i wszystkie jego subskrypcje na brzegu:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Usuń wszystkie zasoby utworzone podczas konfigurowania routingu IoTHub w chmurze.

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano tworzenie tematu usługi Event Grid, subskrypcji centrum usługi Edge i opublikowanych zdarzeń. Teraz, gdy znasz podstawowe kroki umożliwiające przekazanie do centrum brzegowego, zobacz następujące artykuły:

* Aby rozwiązać problemy z używaniem Azure Event Grid na IoT Edge, zobacz [Przewodnik rozwiązywania problemów](troubleshoot.md).
* Używanie filtrów tras [centrum programu Edge](../../iot-edge/module-composition.md) do partycjonowania zdarzeń
* Konfiguracja trwałości modułu Event Grid w systemie [Linux](persist-state-linux.md) lub [Windows](persist-state-windows.md)
* Postępuj zgodnie z [dokumentacją](configure-client-auth.md) , aby skonfigurować uwierzytelnianie klienta
* Przekazuj zdarzenia do Azure Event Grid w chmurze, wykonując czynności opisane w tym [samouczku](forward-events-event-grid-cloud.md)
* [Monitorowanie tematów i subskrypcji na granicy](monitor-topics-subscriptions.md)