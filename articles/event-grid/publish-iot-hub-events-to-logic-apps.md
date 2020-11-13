---
title: Samouczek — używanie zdarzeń IoT Hub do wyzwalania Azure Logic Apps
description: W tym samouczku przedstawiono sposób korzystania z usługi routingu zdarzeń Azure Event Grid, tworzenia zautomatyzowanych procesów w celu wykonywania Azure Logic Apps akcji opartych na zdarzeniach IoT Hub.
services: iot-hub, event-grid
author: philmea
ms.service: iot-hub
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 857ae8d824443e9a8abdac7c4a66e2b014be2be0
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566354"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Samouczek: Wysyłanie powiadomień e-mail dotyczących zdarzeń usługi Azure IoT Hub przy użyciu usług Event Grid i Logic Apps

Usługa Azure Event Grid pozwala reagować na zdarzenia usługi IoT Hub dzięki akcjom wyzwalanym w podrzędnych aplikacjach biznesowych.

W tym artykule przedstawiono przykładową konfigurację, która używa IoT Hub i Event Grid. Na końcu masz skonfigurowaną aplikację logiki Azure do wysyłania wiadomości e-mail z powiadomieniem za każdym razem, gdy urządzenie nawiąże połączenie lub odłączy się do centrum IoT. Event Grid może służyć do uzyskania czasowego powiadomienia o odłączeniu urządzeń krytycznych. Metryki i Diagnostyka mogą potrwać kilka (tj. 20 lub więcej — ale nie chcemy, aby w ciągu kilku minut było wyświetlane w dziennikach/alertach). Może to być nieakceptowalne dla infrastruktury krytycznej.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Konto e-mail od dowolnego dostawcy poczty e-mail obsługiwanego przez Azure Logic Apps, na przykład Office 365 Outlook lub Outlook.com. To konto e-mail służy do wysyłania powiadomień o zdarzeniach.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Nowe centrum IoT Hub można szybko utworzyć przy użyciu terminalu Azure Cloud Shell w portalu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

1. W prawym górnym rogu strony wybierz przycisk Cloud Shell.

   ![Przycisk Cloud Shell](./media/publish-iot-hub-events-to-logic-apps/portal-cloud-shell.png)

1. Uruchom poniższe polecenie, aby utworzyć nową grupę zasobów:

   ```azurecli
   az group create --name {your resource group name} --location westus
   ```
    
1. Uruchom następujące polecenie, aby utworzyć Centrum IoT:

   ```azurecli
   az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1 
   ```

1. Zminimalizuj Cloud Shell terminala. Powrócisz do powłoki w dalszej części tego samouczka.

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Następnie Utwórz aplikację logiki i Dodaj wyzwalacz usługi Event Grid protokołu HTTP, który przetwarza żądania z usługi IoT Hub. 

### <a name="create-a-logic-app-resource"></a>Tworzenie zasobu aplikacji logiki

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób** , a następnie w polu wyszukiwania wpisz ciąg "aplikacja logiki" i wybierz pozycję Zwróć. Wybierz pozycję **aplikacja logiki** z wyników.

   ![Tworzenie aplikacji logiki](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Na następnym ekranie wybierz pozycję **Utwórz**. 

1. Nadaj aplikacji logiki nazwę unikatową w ramach subskrypcji, a następnie wybierz tę samą subskrypcję, grupę zasobów i lokalizację, które są skojarzone z centrum IoT. 

   ![Pola dla tworzenia aplikacji logiki](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Wybierz pozycję **Przejrzyj i utwórz**.

1. Sprawdź ustawienia, a następnie wybierz pozycję **Utwórz**.

1. Po utworzeniu zasobu wybierz pozycję **Przejdź do zasobu**. 

1. W projektancie Logic Apps strony wyświetlają się w dół, aby wyświetlić **Szablony**. Wybierz pozycję **pusta aplikacja logiki** , aby można było utworzyć aplikację logiki od podstaw.

### <a name="select-a-trigger"></a>Wybieranie wyzwalacza

Wyzwalacz to konkretne zdarzenie, które uruchamia aplikację logiki. W tym samouczku wyzwalacz, który uruchamia przepływ pracy, odbiera żądanie za pośrednictwem protokołu HTTP.  

1. Na pasku wyszukiwania łączników i wyzwalaczy wpisz ciąg **HTTP**.

1. Przewiń wyniki i wybierz **żądanie — gdy żądanie HTTP zostanie odebrane** jako wyzwalacz. 

   ![Wybieranie wyzwalacza żądania HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**. 

   ![Użyj przykładowego ładunku](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Wklej kod JSON *schematu zdarzenia połączonego z urządzeniem* do pola tekstowego, a następnie wybierz pozycję **gotowe** :

   ```json
     [{  
      "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
      "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
      "subject": "devices/LogicAppTestDevice",
      "eventType": "Microsoft.Devices.DeviceConnected",
      "eventTime": "2018-06-02T19:17:44.4383997Z",
      "data": {
          "deviceConnectionStateEventInfo": {
            "sequenceNumber":
              "000000000000000001D4132452F67CE200000002000000000000000000000001"
          },
        "hubName": "egtesthub1",
        "deviceId": "LogicAppTestDevice",
        "moduleId" : "DeviceModuleID"
      }, 
      "dataVersion": "1",
      "metadataVersion": "1"
    }]
   ```

   To zdarzenie służy do publikowania, gdy urządzenie jest połączone z usługą IoT Hub.

> [!NOTE]
> Może pojawić się wyskakujące powiadomienie **Pamiętaj, aby w żądaniu uwzględnić nagłówek Content-Type ustawiony na wartość application/json**. Można zignorować ten komunikat i przejść do następnej sekcji. 

### <a name="create-an-action"></a>Tworzenie akcji

Akcje to kroki, które są wykonywane, gdy wyzwalacz uruchomi przepływ pracy aplikacji logiki. W tym samouczku akcja polega na wysłaniu powiadomienia e-mail pochodzącego od dostawcy poczty e-mail. 

1. Wybierz pozycję **Nowy krok**. Spowoduje to otwarcie okna umożliwiającego **wybranie akcji**.

1. Wyszukaj w **programie Outlook**.

1. W oparciu o Twojego dostawcę poczty e-mail znajdź i wybierz zgodny łącznik. Ten samouczek używa **Outlook.com**. Kroki dla innych dostawców poczty e-mail są podobne. 

   ![Wybieranie łącznika dostawcy poczty e-mail](./media/publish-iot-hub-events-to-logic-apps/outlook-step.png)

1. Wybierz akcję **Wyślij wiadomość e-mail (wersja 2)** . 

1. Wybierz pozycję **Zaloguj** się i zaloguj się na swoim koncie e-mail. Wybierz pozycję **tak** , aby zezwolić aplikacji na dostęp do informacji.

1. Utwórz szablon wiadomości e-mail. 

   * **Do** : Wprowadź adres e-mail, na który mają być wysyłane wiadomości e-mail z powiadomieniami. Na potrzeby tego samouczka użyj konta e-mail, do którego masz dostęp, w celach testowych. 

   * **Temat** : Wpisz tekst tematu. Po kliknięciu pola tekstowego temat możesz wybrać zawartość dynamiczną do uwzględnienia. Na przykład w tym samouczku używamy `IoT Hub alert: {eventType}` . Jeśli nie widzisz zawartości dynamicznej, zaznacz hiperlink **Dodawanie zawartości dynamicznej** — spowoduje to włączenie i wyłączenie tej opcji.

   * **Treść** : Wpisz tekst wiadomości e-mail. Wybierz właściwości JSON przy użyciu narzędzia selektora, aby dodać zawartość dynamiczną na podstawie danych zdarzenia. Jeśli zawartość dynamiczna nie jest widoczna, zaznacz hiperlink **Dodaj zawartość dynamiczną** poniżej pola tekstowego **treść** . Jeśli nie pokazywane są odpowiednie pola, kliknij przycisk *więcej* na ekranie zawartości dynamicznej, aby uwzględnić pola z poprzedniej akcji.

   Przykładowy szablon wiadomości e-mail może wyglądać następująco:

   ![Wpisywanie informacji dotyczących wiadomości e-mail.](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Wybierz pozycję **Zapisz** w projektancie Logic Apps.  

### <a name="copy-the-http-url"></a>Kopiowanie adresu URL HTTP

Przed zamknięciem projektanta aplikacji usługi Logic Apps skopiuj adres URL, za pomocą którego aplikacja logiki odbiera wyzwalacz. Ten adres URL zostanie użyty do skonfigurowania usługi Event Grid. 

1. Kliknij pole konfiguracji wyzwalacza **Po odebraniu żądania HTTP** , aby je rozwinąć. 

1. Skopiuj wartość pola **Adres URL żądania HTTP POST** , wybierając przycisk widoczny obok tego pola. 

   ![Kopiowanie adresu URL żądania HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Zapisz ten adres URL — zostanie użyty w następnej sekcji. 

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurowanie subskrypcji zdarzeń usługi IoT Hub

W tej sekcji skonfigurujesz usługę IoT Hub pod kątem publikowania zdarzeń na bieżąco. 

1. W witrynie Azure Portal przejdź do centrum IoT Hub. Możesz to zrobić, wybierając pozycję **grupy zasobów** , a następnie wybierz grupę zasobów dla tego samouczka, a następnie wybierz z listy zasobów Centrum IoT Hub.

1. Wybierz pozycję **zdarzenia**.

   ![Wyświetlanie szczegółów usługi Event Grid](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

1. Wybierz pozycję **subskrypcja zdarzeń**. 

   ![Tworzenie nowej subskrypcji zdarzeń](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

1. Utwórz subskrypcję zdarzeń, korzystając z następujących wartości: 

   1. W sekcji **szczegóły subskrypcji zdarzeń** :
      1. Podaj **nazwę** subskrypcji zdarzenia. 
      2. Wybierz **schemat zdarzeń Event Grid** dla **schematu zdarzenia**. 
   2. W sekcji **szczegółów tematu** :
      1. Upewnij się, że **Typ tematu** jest ustawiony na **IoT Hub**. 
      2. Upewnij się, że nazwa usługi IoT Hub jest ustawiona jako wartość pola **zasobu źródłowego** . 
      3. Wprowadź nazwę **tematu systemowego** , który zostanie utworzony. Aby dowiedzieć się więcej na temat tematów systemowych, zobacz [Omówienie tematów systemowych](system-topics.md).
   3. W sekcji **typy zdarzeń** :
      1. Wybierz listę rozwijaną **Filtruj do typów zdarzeń** .
      1. Usuń zaznaczenie pól wyboru **utworzone urządzenie** i **usunięte urządzenia** , pozostawiając wybrane pola wyboru tylko **połączone urządzenie** i **rozłączone urządzenie** .

         ![Wybieranie typów zdarzeń subskrypcji](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   
   4. W sekcji **Szczegóły punktu końcowego** : 
       1. Wybierz **Typ punktu końcowego** jako **element webhook**.
       2. Kliknij pozycję **Wybierz punkt końcowy** , wklej adres URL skopiowany z aplikacji logiki i potwierdź wybór.

         ![wybieranie adresu url punktu końcowego](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         Po zakończeniu okienko powinno wyglądać tak, jak w poniższym przykładzie: 

         ![Przykładowy formularz subskrypcji zdarzeń](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

1.  Wybierz pozycję **Utwórz**.

## <a name="simulate-a-new-device-connecting-and-sending-telemetry"></a>Symuluj nowe urządzenie łączące i wysyłające dane telemetryczne

Przetestuj aplikację logiki, szybko symulując połączenie z urządzeniem przy użyciu interfejsu wiersza polecenia platformy Azure. 

1. Wybierz przycisk Cloud Shell, aby ponownie otworzyć Terminal.

1. Uruchom następujące polecenie, aby utworzyć symulowaną tożsamość urządzenia:
    
     ```azurecli 
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName}
    ```

1. Uruchom następujące polecenie, aby symulować połączenie urządzenia z IoT Hub i wysyłanie danych telemetrycznych:

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. Gdy symulowane urządzenie zostanie połączone z IoT Hub, otrzymasz wiadomość e-mail z powiadomieniem o zdarzeniu "DeviceConnected".

1. Po zakończeniu symulacji otrzymasz wiadomość e-mail z powiadomieniem o zdarzeniu "DeviceDisconnected". 

    ![Przykład wiadomości e-mail dotyczącej alertu](./media/publish-iot-hub-events-to-logic-apps/alert-mail.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Użycie zasobów w tym samouczku powoduje naliczanie opłat w ramach Twojej subskrypcji platformy Azure. Po zakończeniu próby wykonania samouczka i przetestowaniu wyników należy wyłączyć lub usunąć zasoby, które nie mają być zachowywane. 

Aby usunąć wszystkie zasoby utworzone w tym samouczku, Usuń grupę zasobów. 

1. Wybierz pozycję **grupy zasobów** , a następnie wybierz grupę zasobów utworzoną dla tego samouczka.

2. W okienku Grupa zasobów wybierz pozycję **Usuń grupę zasobów**. Zostanie wyświetlony monit o wprowadzenie nazwy grupy zasobów, a następnie można ją usunąć. Wszystkie zawarte w niej zasoby również zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej — zobacz [Reacting to IoT Hub events by using Event Grid to trigger actions (Reagowanie na zdarzenia usługi IoT Hub przez wyzwalanie akcji za pomocą usługi Event Grid)](../iot-hub/iot-hub-event-grid.md).
* [Dowiedz się, jak porządkować zdarzenia podłączania i odłączania urządzeń](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Dowiedz się, co jeszcze możesz robić przy użyciu usługi [Event Grid](overview.md).

Aby zapoznać się z pełną listą obsługiwanych łączników aplikacji logiki, zobacz [Omówienie łączników](/connectors/).
