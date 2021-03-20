---
title: Publikowanie, subskrybowanie zdarzeń lokalnie — Azure Event Grid IoT Edge | Microsoft Docs
description: Publikuj, Subskrybuj zdarzenia lokalnie przy użyciu elementu webhook z Event Grid na IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2a7cc864366bd9a35c96dd453c0dc68f77d8abd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171452"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Samouczek: publikowanie, subskrybowanie zdarzeń lokalnie

W tym artykule przeprowadzimy Cię przez wszystkie kroki wymagane do opublikowania i subskrybowania zdarzeń przy użyciu Event Grid w IoT Edge.

> [!NOTE]
> Aby dowiedzieć się więcej na temat Azure Event Grid tematów i subskrypcji, zobacz temat [Event Grid koncepcje](concepts.md).

## <a name="prerequisites"></a>Wymagania wstępne 
Aby można było ukończyć ten samouczek, potrzebne są:

* **Subskrypcja platformy Azure** — Utwórz [bezpłatne konto](https://azure.microsoft.com/free) , jeśli jeszcze go nie masz. 
* **Azure IoT Hub i IoT Edge Device** — wykonaj kroki opisane w przewodniku szybki start dla [urządzeń](../../iot-edge/quickstart.md) z systemem [Linux](../../iot-edge/quickstart-linux.md) lub Windows, jeśli jeszcze go nie masz.

## <a name="deploy-event-grid-iot-edge-module"></a>Wdróż moduł IoT Edge Event Grid

Istnieje kilka sposobów wdrażania modułów na urządzeniu IoT Edge i wszystkie z nich pracują Azure Event Grid w IoT Edge. W tym artykule opisano kroki wdrażania Event Grid na IoT Edge z Azure Portal.

>[!NOTE]
> W tym samouczku zostanie wdrożony moduł Event Grid bez trwałości. Oznacza to, że wszystkie tematy i subskrypcje utworzone w tym samouczku zostaną usunięte po ponownym wdrożeniu modułu. Aby uzyskać więcej informacji na temat sposobu konfiguracji trwałości, zobacz następujące artykuły: [utrwalanie stanu w systemie Linux](persist-state-linux.md) lub [w stanie utrwalania systemu Windows](persist-state-windows.md). W przypadku obciążeń produkcyjnych zaleca się zainstalowanie modułu Event Grid z trwałością.


### <a name="select-your-iot-edge-device"></a>Wybierz urządzenie IoT Edge

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Przejdź do IoT Hub.
1. Wybierz pozycję **IoT Edge** z menu w sekcji **Automatyczne zarządzanie urządzeniami** . 
1. Kliknij identyfikator urządzenia docelowego z listy urządzeń
1. Wybierz pozycję **Ustaw moduły**. Nie otwieraj strony. Wykonaj kroki opisane w następnej sekcji.

### <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia to dokument JSON, który opisuje moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości modułu bliźniaczych reprezentacji. Azure Portal zawiera kreatora, który przeprowadzi Cię przez proces tworzenia manifestu wdrożenia, zamiast ręcznego tworzenia dokumentu JSON.  Składa się z trzech kroków: **Dodawanie modułów**, **Określanie tras** i **przeglądanie wdrożenia**.

### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **moduły wdrażania** wybierz pozycję **Dodaj** .
1. Z typów modułów na liście rozwijanej wybierz pozycję **IoT Edge module**
1. Podaj nazwę, obraz i opcje tworzenia kontenera:

   * **Nazwa**: eventgridmodule
   * **Identyfikator URI obrazu**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opcje tworzenia kontenera**:

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Kliknij pozycję **Zapisz**.
 1. Przejdź do następnej sekcji, aby dodać moduł subskrybenta Azure Event Grid przed ich wdrożeniem.

    >[!IMPORTANT]
    > W tym samouczku zostanie wdrożony moduł Event Grid z wyłączonym uwierzytelnianiem klienta. W przypadku obciążeń produkcyjnych zaleca się włączenie uwierzytelniania klienta. Aby uzyskać więcej informacji na temat bezpiecznego konfigurowania modułu Event Grid, zobacz [zabezpieczenia i uwierzytelnianie](security-authentication.md).
    > 
    > Jeśli używasz maszyny wirtualnej platformy Azure jako urządzenia brzegowego, Dodaj regułę portu przychodzącego, aby zezwolić na ruch przychodzący na porcie 4438. Aby uzyskać instrukcje dotyczące dodawania reguły, zobacz [Jak otworzyć porty na maszynie wirtualnej](../../virtual-machines/windows/nsg-quickstart-portal.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Wdróż moduł IoT Edge subskrybenta Event Grid

W tej sekcji przedstawiono sposób wdrażania innego modułu IoT, który będzie działać jako procedura obsługi zdarzeń, do którego mogą być dostarczane zdarzenia.

### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **modułów wdrażania** wybierz ponownie przycisk **Dodaj** . 
1. Z typów modułów na liście rozwijanej wybierz pozycję **IoT Edge module**
1. Podaj nazwę, obraz i opcje tworzenia kontenera:

   * **Nazwa**: subskrybent
   * **Identyfikator URI obrazu**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Opcje tworzenia kontenera**: brak
1. Kliknij pozycję **Zapisz**.
1. Kliknij przycisk **dalej** , aby przejść do sekcji trasy

 ### <a name="setup-routes"></a>Skonfiguruj trasy

Zachowaj trasy domyślne, a następnie wybierz pozycję **dalej** , aby przejść do sekcji Przegląd

### <a name="submit-the-deployment-request"></a>Prześlij żądanie wdrożenia

1. Sekcja Przegląd przedstawia manifest wdrożenia JSON, który został utworzony na podstawie wybranych opcji w poprzedniej sekcji. Upewnij się, że na liście JSON są widoczne zarówno moduły: **eventgridmodule** , jak i **subskrybent** . 
1. Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Prześlij**. Po przesłaniu wdrożenia wrócisz do strony **urządzenia** .
1. W **sekcji modułów** Sprawdź, czy na liście znajdują się zarówno **eventgrid** , jak i moduły **subskrybenta** . I sprawdź, czy w polu **Deployment (wdrożenie** i **raportowane przez urządzenia** ) ustawiono wartość **tak**.

    Uruchomienie modułu na urządzeniu może potrwać kilka minut, a następnie zgłoszone z powrotem do IoT Hub. Odśwież stronę, aby zobaczyć zaktualizowany stan.

## <a name="create-a-topic"></a>Tworzenie tematu

Jako wydawca zdarzenia musisz utworzyć temat dotyczący siatki zdarzeń. W Azure Event Grid temat dotyczy punktu końcowego, w którym wydawcy mogą wysyłać zdarzenia do programu.

1. Utwórz topic.jsprzy użyciu następującej zawartości. Aby uzyskać szczegółowe informacje o ładunku, zapoznaj się z naszą [dokumentacją interfejsu API](api.md).

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Uruchom następujące polecenie, aby utworzyć temat z siatką zdarzeń. Upewnij się, że został wyświetlony kod stanu HTTP `200 OK` .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Uruchom następujące polecenie, aby upewnić się, że temat został utworzony pomyślnie. Należy zwrócić kod stanu HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   Przykładowe dane wyjściowe:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Tworzenie subskrypcji zdarzeń

Subskrybenci mogą rejestrować się w przypadku zdarzeń opublikowanych w temacie. Aby odebrać każde zdarzenie, musisz utworzyć subskrypcję Event Gridową dla tematu zainteresowania.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Utwórz subscription.jsprzy użyciu następującej zawartości. Aby uzyskać szczegółowe informacje o ładunku, zapoznaj się z naszą [dokumentacją interfejsu API](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > Właściwość **EndpointType** określa, że subskrybent jest elementem **webhook**.  **EndpointUrl** określa adres URL, pod którym subskrybent nasłuchuje zdarzeń. Ten adres URL odnosi się do wdrożonego wcześniej przykładu subskrybenta platformy Azure.
2. Uruchom następujące polecenie, aby utworzyć subskrypcję tematu. Upewnij się, że został wyświetlony kod stanu HTTP `200 OK` .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Uruchom następujące polecenie, aby sprawdzić, czy subskrypcja została pomyślnie utworzona. Należy zwrócić kod stanu HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Przykładowe dane wyjściowe:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publikowanie zdarzenia

1. Utwórz event.jsprzy użyciu następującej zawartości. Aby uzyskać szczegółowe informacje o ładunku, zapoznaj się z naszą [dokumentacją interfejsu API](api.md).

    ```json
        [
          {
            "id": "eventId-func-0",
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
1. Uruchom następujące polecenie, aby opublikować zdarzenie.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Weryfikowanie dostarczania zdarzeń

1. SSH lub RDP do maszyny wirtualnej IoT Edge.
1. Sprawdź dzienniki subskrybenta:

    W systemie Windows uruchom następujące polecenie:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   W systemie Linux Uruchom następujące polecenie:

    ```sh
    sudo docker logs subscriber
    ```

    Przykładowe dane wyjściowe:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

* Uruchom następujące polecenie, aby usunąć temat i wszystkie jego subskrypcje.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Usuń moduł subskrybenta z urządzenia IoT Edge.


## <a name="next-steps"></a>Następne kroki
W tym samouczku opisano tworzenie tematu, subskrypcji i opublikowanych zdarzeń usługi Event Grid. Teraz, gdy znasz podstawowe kroki, zobacz następujące artykuły: 

- Aby rozwiązać problemy z używaniem Azure Event Grid na IoT Edge, zobacz [Przewodnik rozwiązywania problemów](troubleshoot.md).
- Utwórz/zaktualizuj subskrypcję za pomocą [filtrów](advanced-filtering.md).
- Włącz trwałość modułu Event Grid w systemie [Linux](persist-state-linux.md) lub [Windows](persist-state-windows.md)
- Postępuj zgodnie z [dokumentacją](configure-client-auth.md) , aby skonfigurować uwierzytelnianie klienta
- Przekazuj zdarzenia do Azure Functions w chmurze, wykonując czynności opisane w tym [samouczku](pub-sub-events-webhook-cloud.md)
- [Reagowanie na zdarzenia Blob Storage w IoT Edge](react-blob-storage-events-locally.md)
- [Monitorowanie tematów i subskrypcji na granicy](monitor-topics-subscriptions.md)

