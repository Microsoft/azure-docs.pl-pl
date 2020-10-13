---
title: Zamów zdarzenia połączenia urządzenia fr Azure IoT Hub z/Azure Cosmos DB
description: W tym artykule opisano sposób porządkowania i rejestrowania zdarzeń połączeń urządzeń z usługi Azure IoT Hub przy użyciu Azure Cosmos DB do utrzymania najnowszego stanu połączenia
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.custom: devx-track-azurecli
ms.openlocfilehash: 815dc0a90d79323fb88c98867b5540105a6fa8cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91356200"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Porządkowanie zdarzeń połączenia urządzenia pochodzących z usługi Azure IoT Hub przy użyciu usługi Azure Cosmos DB

Azure Event Grid ułatwia tworzenie aplikacji opartych na zdarzeniach i łatwe integrowanie wydarzeń IoT z rozwiązaniami biznesowymi. W tym artykule przedstawiono konfigurację, która może służyć do śledzenia i przechowywania najnowszego stanu połączenia urządzenia w Cosmos DB. Będziemy używać numeru sekwencyjnego dostępnego w zdarzeniach urządzenia połączone i odłączone urządzenia oraz przechowywania najnowszego stanu w Cosmos DB. Będziemy używać procedury składowanej, która jest logiką aplikacji wykonywaną w odniesieniu do kolekcji w Cosmos DB.

Numer sekwencyjny to ciąg reprezentujący liczbę szesnastkową. Możesz użyć porównywania ciągów, aby zidentyfikować większą liczbę. Jeśli konwertujesz ciąg na wartość szesnastkową, to liczba będzie liczbą 256-bitową. Numer sekwencyjny jest ściśle rosnący, a najnowsze zdarzenie będzie miało wyższą liczbę niż inne zdarzenia. Jest to przydatne, jeśli używasz częstego łączenia i rozłączania urządzenia i chcesz upewnić się, że tylko najnowsze zdarzenie służy do wyzwalania akcji podrzędnej, ponieważ Azure Event Grid nie obsługuje porządkowania zdarzeń.

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

* Aktywne Azure Cosmos DB konto interfejsu API SQL. Jeśli jeszcze tego nie zrobiono, zobacz [Tworzenie konta bazy danych](../cosmos-db/create-sql-api-java.md#create-a-database-account) dla przewodnika.

* Kolekcja w bazie danych. Zobacz [Dodawanie kolekcji](../cosmos-db/create-sql-api-java.md#add-a-container) dla przewodnika. Podczas tworzenia kolekcji Użyj `/id` klucza partycji.

* Centrum IoT Hub na platformie Azure. Jeśli jeszcze go nie masz, zobacz przewodnik [Wprowadzenie do usługi IoT Hub](iot-hub-csharp-csharp-getstarted.md).

## <a name="create-a-stored-procedure"></a>Tworzenie procedury składowanej

Najpierw należy utworzyć procedurę składowaną i skonfigurować ją w celu uruchomienia logiki porównującej numery sekwencji zdarzeń przychodzących i zapisania najnowszego zdarzenia na urządzenie w bazie danych.

1. W Cosmos DB interfejsie API SQL Wybierz pozycję **Eksplorator danych**  >  **Items**  >  **Nowa procedura składowana**.

   ![Utwórz procedurę składowaną](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Wprowadź **LatestDeviceConnectionState** dla identyfikatora procedury składowanej i wklej następujący **tekst w treści procedury składowanej**. Należy zauważyć, że ten kod powinien zastąpić wszelki istniejący kod w treści procedury składowanej. Ten kod obsługuje jeden wiersz na identyfikator urządzenia i rejestruje najnowszy stan połączenia tego identyfikatora urządzenia, identyfikując najwyższy numer sekwencyjny.

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};

      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Zapisz procedurę składowaną:

    ![Zapisz procedurę składowaną](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Najpierw należy utworzyć aplikację logiki i dodać wyzwalacz usługi Event Grid, który monitoruje grupę zasobów dla maszyny wirtualnej.

### <a name="create-a-logic-app-resource"></a>Tworzenie zasobu aplikacji logiki

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **+ Utwórz zasób**, wybierz pozycję **integracja** , a następnie **aplikację logiki**.

   ![Tworzenie aplikacji logiki](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Nadaj aplikacji logiki nazwę unikatową w ramach subskrypcji, a następnie wybierz tę samą subskrypcję, grupę zasobów i lokalizację, które są skojarzone z centrum IoT.

   ![Nowa aplikacja logiki](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Wybierz pozycję **Utwórz** , aby utworzyć aplikację logiki.

   Teraz został utworzony zasób platformy Azure dla Twojej aplikacji logiki. Po wdrożeniu Twojej aplikacji logiki na platformie Azure projektant aplikacji usługi Logic Apps pokaże szablony dla typowych wzorców, więc możesz szybciej rozpocząć pracę.

   > [!NOTE]
   > Aby ponownie znaleźć i otworzyć aplikację logiki, wybierz pozycję **grupy zasobów** i wybierz grupę zasobów, której używasz na potrzeby tej procedury. Następnie wybierz nową aplikację logiki. Spowoduje to otwarcie projektanta aplikacji logiki.

4. W Projektancie aplikacji logiki przewiń w prawo, aż zobaczysz typowe wyzwalacze. W obszarze **Szablony**wybierz pozycję **pusta aplikacja logiki** , aby można było skompilować aplikację logiki od podstaw.

### <a name="select-a-trigger"></a>Wybieranie wyzwalacza

Wyzwalacz to konkretne zdarzenie, które uruchamia aplikację logiki. W tym samouczku wyzwalacz, który uruchamia przepływ pracy, odbiera żądanie za pośrednictwem protokołu HTTP.

1. Na pasku wyszukiwania łączników i wyzwalaczy wpisz **http** i naciśnij klawisz ENTER.

2. Wybierz pozycję **Żądanie — Po odebraniu żądania HTTP** jako wyzwalacz.

   ![Wybieranie wyzwalacza żądania HTTP](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

   ![Użyj przykładowego ładunku do wygenerowania schematu](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Wklej poniższy przykładowy kod JSON w polu tekstowym, a następnie wybierz pozycję **Gotowe**:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

   ![Wklej przykładowy ładunek JSON](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Może pojawić się wyskakujące powiadomienie **Pamiętaj, aby w żądaniu uwzględnić nagłówek Content-Type ustawiony na wartość application/json**. Można zignorować ten komunikat i przejść do następnej sekcji.

### <a name="create-a-condition"></a>Tworzenie warunku

W przepływie pracy aplikacji logiki warunki ułatwiają wykonywanie określonych czynności po przekazaniu określonego warunku. Po spełnieniu warunku można zdefiniować żądaną akcję. W tym samouczku warunek polega na sprawdzeniu, czy typ elementu jest podłączony do urządzenia, czy urządzenie zostało odłączone. Akcja będzie wykonywać procedurę przechowywaną w bazie danych.

1. Wybierz pozycję **+ nowy krok** , a następnie pozycję **wbudowane**, a następnie Znajdź i wybierz pozycję **warunek**. Kliknij w obszarze **Wybierz wartość** , a w oknie zostanie wyświetlona zawartość dynamiczna — pola, które można wybrać. Wypełnij pola, tak jak pokazano poniżej, aby wykonać to tylko dla zdarzeń podłączonych do urządzenia i odłączonych do urządzeń:

   * Wybierz wartość: **EventType** — wybierz tę opcję z pól zawartości dynamicznej, która pojawia się po kliknięciu tego pola.
   * Zmień wartość "jest równa", aby kończyć **się znakiem.**
   * Wybierz wartość: **nected**.

     ![Warunek wypełnienia](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. W oknie dialogowym **Jeśli prawda** kliknij przycisk **Dodaj akcję**.
  
   ![Dodaj akcję w przypadku wartości true](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Wyszukaj Cosmos DB i wybierz **procedurę składowaną Azure Cosmos DB-Execute**

   ![Wyszukaj CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Wypełnij pole **cosmosdb-Connection** dla **nazwy połączenia** i wybierz wpis w tabeli, a następnie wybierz pozycję **Utwórz**. Zobaczysz panel **procedury składowane wykonaj** . Wprowadź wartości dla pól:

   **Identyfikator bazy danych**: todolist

   **Identyfikator kolekcji**: elementy

   **Identyfikator sproc**: LatestDeviceConnectionState

5. Wybierz pozycję **Dodaj nowy parametr**. W wyświetlonym menu rozwijanym zaznacz pola wyboru obok **klucza partycji** i **parametrów dla procedury składowanej**, a następnie kliknij dowolne miejsce na ekranie. Dodaje pole dla wartości klucza partycji i pola dla parametrów procedury składowanej.

   ![Zrzut ekranu przedstawia element procedury składowanej Execute z wybranym nowym parametrem.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Teraz wprowadź wartość i parametry klucza partycji, jak pokazano poniżej. Pamiętaj, aby umieścić w nawiasach i podwójne cudzysłowy, jak pokazano. Może być konieczne kliknięcie przycisku **Dodaj zawartość dynamiczną** w celu uzyskania prawidłowych wartości, których można użyć w tym miejscu.

   ![Zrzut ekranu przedstawia element procedury składowanej Execute z wprowadzonymi parametrami.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Upewnij się, że w górnej części okienka znajduje się informacja **o każdej**z nich, w obszarze **Wybierz dane wyjściowe z poprzednich kroków**upewnij się, że jest wybrana  **treść** .

   ![Wypełnij aplikację logiki dla — Each](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Zapisz aplikację logiki.

### <a name="copy-the-http-url"></a>Kopiowanie adresu URL HTTP

Przed opuszczeniem projektanta Logic Apps Skopiuj adres URL, na który nasłuchuje aplikacja logiki dla wyzwalacza. Ten adres URL zostanie użyty do skonfigurowania usługi Event Grid.

1. Kliknij pole konfiguracji wyzwalacza **Po odebraniu żądania HTTP**, aby je rozwinąć.

2. Skopiuj wartość pola **Adres URL żądania HTTP POST**, wybierając przycisk widoczny obok tego pola.

   ![Kopiowanie adresu URL żądania HTTP POST](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Zapisz ten adres URL — zostanie użyty w następnej sekcji.

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurowanie subskrypcji zdarzeń usługi IoT Hub

W tej sekcji skonfigurujesz usługę IoT Hub pod kątem publikowania zdarzeń na bieżąco.

1. W witrynie Azure Portal przejdź do centrum IoT Hub.

2. Wybierz pozycję **zdarzenia**.

   ![Wyświetlanie szczegółów usługi Event Grid](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Wybierz pozycję **+ subskrypcja zdarzeń**.

   ![Tworzenie nowej subskrypcji zdarzeń](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Wypełnij **szczegóły subskrypcji zdarzeń**: Podaj nazwę opisową i wybierz **schemat Event Grid**.

5. Wypełnij pola **typy zdarzeń** . Z listy rozwijanej wybierz pozycję tylko **urządzenie połączone** i **Rozłączono urządzenie** z menu. Kliknij w dowolnym miejscu na ekranie, aby zamknąć listę i zapisać wybrane opcje.

   ![Ustawianie typów zdarzeń do wyszukania](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. W obszarze **Szczegóły punktu końcowego**wybierz pozycję typ punktu końcowego jako **element webhook** i kliknij pozycję Wybierz punkt końcowy, a następnie wklej adres URL skopiowany z aplikacji logiki i potwierdź wybór.

   ![Wybierz adres URL punktu końcowego](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Formularz powinien teraz wyglądać podobnie do poniższego przykładu:

   ![Przykładowy formularz subskrypcji zdarzeń](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Wybierz pozycję **Utwórz**, aby zapisać subskrypcję zdarzeń.

## <a name="observe-events"></a>Obserwuj zdarzenia

Teraz, po skonfigurowaniu subskrypcji zdarzeń, Przetestujmy ją, łącząc urządzenie.

### <a name="register-a-device-in-iot-hub"></a>Rejestrowanie urządzenia w IoT Hub

1. Z poziomu usługi IoT Hub wybierz pozycję **urządzenia IoT**.

2. Wybierz pozycję **+ Dodaj** w górnej części okienka.

3. W polu **Identyfikator urządzenia** wpisz `Demo-Device-1`.

4. Wybierz pozycję **Zapisz**.

5. Można dodać wiele urządzeń z różnymi identyfikatorami urządzeń.

   ![Urządzenia dodane do centrum](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Kliknij urządzenie ponownie; teraz zostaną wypełnione parametry połączenia i klucze. Skopiuj **Parametry połączenia — klucz podstawowy** do późniejszego użycia.

   ![Parametry połączenia dla urządzenia](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Uruchom symulator Raspberry Pi

Użyjemy symulatora sieci Web Raspberry Pi, aby symulować połączenie z urządzeniem.

[Uruchom symulator Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Uruchamianie przykładowej aplikacji na symulatorze sieci Web Raspberry Pi

Spowoduje to wyzwolenie zdarzenia połączonego z urządzeniem.

1. W obszarze Kodowanie Zastąp symbol zastępczy w wierszu 15 ciągiem połączenia urządzenia IoT Hub platformy Azure zapisanym na końcu poprzedniej sekcji.

   ![Wklej w parametrach połączenia urządzenia](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Uruchom aplikację, wybierając pozycję **Uruchom**.

Zobaczysz coś podobnego do poniższego wyjścia, który pokazuje dane z czujnika i komunikaty wysyłane do centrum IoT Hub.

   ![Uruchamianie aplikacji](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Kliknij przycisk **Zatrzymaj** , aby zatrzymać symulator i wyzwolić zdarzenie **odłączenia urządzenia** .

Uruchomiono przykładową aplikację, która umożliwia zbieranie danych z czujnika i wysyłanie ich do centrum IoT.

### <a name="observe-events-in-cosmos-db"></a>Obserwuj zdarzenia w Cosmos DB

Wyniki wykonanej procedury składowanej można zobaczyć w dokumencie Cosmos DB. Oto jak to wygląda. Każdy wiersz zawiera najnowszy stan połączenia z urządzeniem dla każdego urządzenia.

   ![Jak uzyskać wyniki](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Zamiast korzystać z [Azure Portal](https://portal.azure.com), można wykonać kroki IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać szczegółowe informacje, zobacz strony interfejsu wiersza polecenia platformy Azure służące do [tworzenia subskrypcji zdarzeń](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) i [tworzenia urządzenia IoT](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Użycie zasobów w tym samouczku powoduje naliczanie opłat w ramach Twojej subskrypcji platformy Azure. Po zakończeniu próby wykonania samouczka i przetestowaniu wyników należy wyłączyć lub usunąć zasoby, które nie mają być zachowywane.

Jeśli nie chcesz utracić efektów pracy z aplikacją logiki, nie usuwaj jej, tylko wyłącz.

1. Przejdź do aplikacji logiki.

2. W bloku **Przegląd** wybierz pozycję **Usuń** lub **Wyłącz**.

    Każda subskrypcja może zawierać jedno centrum IoT Hub, z którego korzystanie jest bezpłatne. Jeśli w samouczku utworzono bezpłatne centrum, nie musisz go usuwać, ponieważ nie generuje ono kosztów.

3. Przejdź do centrum IoT Hub.

4. W bloku **Przegląd** wybierz pozycję **Usuń**.

    Być może zdecydujesz się zachować centrum IoT Hub, ale usunąć utworzoną subskrypcję zdarzeń.

5. W centrum IoT Hub wybierz pozycję **Event Grid**.

6. Wybierz subskrypcję zdarzeń, którą chcesz usunąć.

7. Wybierz pozycję **Usuń**.

Aby usunąć konto Azure Cosmos DB z Azure Portal, kliknij prawym przyciskiem myszy nazwę konta, a następnie kliknij pozycję **Usuń konto**. Zapoznaj się z szczegółowymi instrukcjami dotyczącymi [usuwania konta Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/manage-account).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [na temat oddziałania zdarzeń IoT Hub przy użyciu Event Grid do wyzwalania akcji](../iot-hub/iot-hub-event-grid.md)

* [Wypróbuj samouczek zdarzeń IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Dowiedz się więcej o tym, co jeszcze można zrobić, korzystając z [Event Grid](../event-grid/overview.md)
