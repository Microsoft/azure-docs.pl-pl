---
title: Publikowanie, subskrybowanie zdarzeń w Azure Event Grid chmury IoT Edge | Microsoft Docs
description: Publikowanie, subskrybowanie zdarzeń w chmurze przy użyciu elementu webhook z Event Grid na IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 12bcb54f4bfdf17209324febeba380ff7789fc0f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998989"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Samouczek: publikowanie, subskrybowanie zdarzeń w chmurze

W tym artykule omówiono wszystkie kroki wymagane do opublikowania i subskrybowania zdarzeń przy użyciu Event Grid na IoT Edge. W tym samouczku są stosowane usługi i funkcje platformy Azure jako program obsługi zdarzeń. Aby uzyskać dodatkowe typy miejsc docelowych, zobacz [programy obsługi zdarzeń](event-handlers.md).

Zapoznaj się z tematami [Event Grid](concepts.md) , aby zrozumieć, czym jest temat i subskrypcja usługi Event Grid.

## <a name="prerequisites"></a>Wymagania wstępne 
Aby można było ukończyć ten samouczek, potrzebne są:

* **Subskrypcja platformy Azure** — Utwórz [bezpłatne konto](https://azure.microsoft.com/free) , jeśli jeszcze go nie masz. 
* **Azure IoT Hub i IoT Edge Device** — wykonaj kroki opisane w przewodniku szybki start dla urządzeń z systemem [Linux](../../iot-edge/quickstart-linux.md) lub [Windows](../../iot-edge/quickstart.md) , jeśli jeszcze go nie masz.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Utwórz funkcję platformy Azure w Azure Portal

Wykonaj kroki opisane w [samouczku](../../azure-functions/functions-create-first-azure-function.md) , aby utworzyć funkcję platformy Azure. 

Zastąp fragment kodu następującym kodem:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

W nowej funkcji wybierz pozycję **Pobierz adres URL funkcji** w prawym górnym rogu, wybierz pozycję domyślne (**klawisz funkcji**), a następnie wybierz pozycję **Kopiuj**. W dalszej części tego samouczka zostanie użyta wartość adresu URL funkcji.

> [!NOTE]
> Zapoznaj się z dokumentacją [Azure Functions](../../azure-functions/functions-overview.md) , aby uzyskać więcej przykładów i samouczków dotyczących reagowania na zdarzenia korzystające z wyzwalaczy zdarzeń EventGrid.

## <a name="create-a-topic"></a>Tworzenie tematu

Jako wydawca zdarzenia musisz utworzyć temat dotyczący siatki zdarzeń. Temat odnosi się do punktu końcowego, w którym wydawcy mogą wysyłać zdarzenia do programu.

1. Utwórz topic2.jsprzy użyciu następującej zawartości. Szczegółowe informacje o ładunku można znaleźć w [dokumentacji interfejsu API](api.md) .

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Uruchom następujące polecenie, aby utworzyć temat. Należy zwrócić kod stanu HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Uruchom następujące polecenie, aby upewnić się, że temat został utworzony pomyślnie. Należy zwrócić kod stanu HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Przykładowe dane wyjściowe:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Tworzenie subskrypcji zdarzeń

Subskrybenci mogą rejestrować się w przypadku zdarzeń opublikowanych w temacie. Aby odebrać każde zdarzenie, subskrybenci będą musieli utworzyć subskrypcję usługi Event Grid w temacie zainteresowania.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Utwórz subscription2.jsprzy użyciu następującej zawartości. Szczegółowe informacje o ładunku można znaleźć w [dokumentacji interfejsu API](api.md) .

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > Element **EndpointType** określa, że subskrybent jest elementem webhook.  **EndpointUrl** określa adres URL, pod którym subskrybent nasłuchuje zdarzeń. Ten adres URL odpowiada przykładowi funkcji platformy Azure, który został wcześniej skonfigurowany.
2. Uruchom następujące polecenie, aby utworzyć subskrypcję. Należy zwrócić kod stanu HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Uruchom następujące polecenie, aby sprawdzić, czy subskrypcja została pomyślnie utworzona. Należy zwrócić kod stanu HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Przykładowe dane wyjściowe:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publikowanie zdarzenia

1. Utwórz event2.jsprzy użyciu następującej zawartości. Szczegółowe informacje o ładunku można znaleźć w [dokumentacji interfejsu API](api.md) .

    ```json
        [
          {
            "id": "eventId-func-1",
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
1. Uruchom następujące polecenie, aby opublikować wydarzenie

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Weryfikowanie dostarczania zdarzeń

Możesz wyświetlić zdarzenie dostarczone w Azure Portal w obszarze opcji **monitorowanie** funkcji.

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

* Uruchom następujące polecenie, aby usunąć temat i wszystkie jego subskrypcje

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Usuń funkcję platformy Azure utworzoną w Azure Portal.

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano tworzenie tematu, subskrypcji i opublikowanych zdarzeń usługi Event Grid. Teraz, gdy znasz podstawowe kroki, zobacz następujące artykuły:

* Aby rozwiązać problemy z używaniem Azure Event Grid na IoT Edge, zobacz [Przewodnik rozwiązywania problemów](troubleshoot.md).
* Utwórz/zaktualizuj subskrypcję za pomocą [filtrów](advanced-filtering.md).
* Konfiguracja trwałości modułu Event Grid w systemie [Linux](persist-state-linux.md) lub [Windows](persist-state-windows.md)
* Postępuj zgodnie z [dokumentacją](configure-client-auth.md) , aby skonfigurować uwierzytelnianie klienta
* Przekazuj zdarzenia do Azure Event Grid w chmurze, wykonując czynności opisane w tym [samouczku](forward-events-event-grid-cloud.md)
* [Monitorowanie tematów i subskrypcji na granicy](monitor-topics-subscriptions.md)
