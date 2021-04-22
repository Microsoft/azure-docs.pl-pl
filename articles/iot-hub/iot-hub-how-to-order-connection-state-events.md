---
title: Zamawianie zdarzeń połączenia urządzenia Azure IoT Hub z/Azure Cosmos DB
description: W tym artykule opisano sposób zamawiania i nagrywania zdarzeń połączenia urządzenia z Azure IoT Hub przy użyciu Azure Cosmos DB w celu zachowania najnowszego stanu połączenia
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.custom: devx-track-azurecli
ms.openlocfilehash: bcbfc0941e3c97e96ebc3746b946553e67a10f93
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878546"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Porządkowanie zdarzeń połączenia urządzenia pochodzących z usługi Azure IoT Hub przy użyciu usługi Azure Cosmos DB

Azure Event Grid pomaga tworzyć aplikacje oparte na zdarzeniach i łatwo integrować zdarzenia IoT w rozwiązaniach biznesowych. Ten artykuł zawiera śledzenia konfiguracji, która może służyć do śledzenia i przechowywania najnowszego stanu połączenia urządzenia w Cosmos DB. Użyjemy numeru sekwencji dostępnego w zdarzeniach Urządzenie połączone i Odłączone urządzenie i będziemy przechowywać najnowszy stan w Cosmos DB. Użyjemy procedury składowanej, która jest logiką aplikacji wykonywaną względem kolekcji w Cosmos DB.

Numer sekwencji jest ciągiem reprezentacji liczby szesnastkowej. Aby zidentyfikować większą liczbę, można użyć porównania ciągów. Jeśli konwertujemy ciąg na wartość hex, liczba będzie liczbą 256-bitową. Numer sekwencji ściśle rośnie, a najnowsze zdarzenie będzie mieć większą liczbę niż inne zdarzenia. Jest to przydatne, jeśli często łączysz się i rozłączasz urządzenie oraz chcesz mieć pewność, że do wyzwolenia akcji podrzędnej jest używane tylko najnowsze zdarzenie, ponieważ program Azure Event Grid nie obsługuje kolejności zdarzeń.

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

* Aktywne konto Azure Cosmos DB interfejsu API SQL. Jeśli jeszcze go nie utworzono, zobacz [Tworzenie konta bazy danych,](../cosmos-db/create-sql-api-java.md#create-a-database-account) aby uzyskać przewodnik.

* Kolekcja w bazie danych. Zobacz [Dodawanie kolekcji,](../cosmos-db/create-sql-api-java.md#add-a-container) aby uzyskać przewodnik. Podczas tworzenia kolekcji użyj `/id` dla klucza partycji.

* Centrum IoT Hub na platformie Azure. Jeśli jeszcze go nie masz, zobacz przewodnik [Wprowadzenie do usługi IoT Hub](./quickstart-send-telemetry-dotnet.md).

## <a name="create-a-stored-procedure"></a>Tworzenie procedury składowanej

Najpierw utwórz procedurę składowaną i skonfiguruj ją tak, aby uruchamiała logikę, która porównuje numery sekwencji zdarzeń przychodzących i rejestruje najnowsze zdarzenie na urządzenie w bazie danych.

1. W interfejsie API SQL Cosmos DB wybierz pozycję **Eksplorator danych** Items New Stored Procedure ( Nowa  >    >  **procedura składowana).**

   ![Tworzenie procedury składowanej](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Wprowadź **wartość LatestDeviceConnectionState** jako identyfikator procedury składowanej i wklej następujący tekst w treści **procedury składowanej**. Należy pamiętać, że ten kod powinien zastąpić dowolny istniejący kod w treści procedury składowanej. Ten kod utrzymuje jeden wiersz na identyfikator urządzenia i rejestruje najnowszy stan połączenia tego identyfikatora urządzenia, identyfikując najwyższy numer sekwencji.

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

    ![zapisywanie procedury składowanej](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Najpierw należy utworzyć aplikację logiki i dodać wyzwalacz usługi Event Grid, który monitoruje grupę zasobów dla maszyny wirtualnej.

### <a name="create-a-logic-app-resource"></a>Tworzenie zasobu aplikacji logiki

1. Na stronie [Azure Portal](https://portal.azure.com)pozycję **+ Utwórz zasób, wybierz** pozycję **Integracja,** a następnie pozycję **Aplikacja logiki.**

   ![Tworzenie aplikacji logiki](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Nadaj aplikacji logiki nazwę unikatową w ramach subskrypcji, a następnie wybierz tę samą subskrypcję, grupę zasobów i lokalizację, które są skojarzone z centrum IoT.

   ![Nowa aplikacja logiki](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Wybierz **pozycję Utwórz,** aby utworzyć aplikację logiki.

   Teraz został utworzony zasób platformy Azure dla Twojej aplikacji logiki. Po wdrożeniu Twojej aplikacji logiki na platformie Azure projektant aplikacji usługi Logic Apps pokaże szablony dla typowych wzorców, więc możesz szybciej rozpocząć pracę.

   > [!NOTE]
   > Aby ponownie znaleźć i otworzyć aplikację logiki, wybierz pozycję **Grupy zasobów** i wybierz grupę zasobów używaną w tym celu. Następnie wybierz nową aplikację logiki. Spowoduje to otwarcie Projektanta aplikacji logiki.

4. W Projektancie aplikacji logiki przewiń w prawo, aż zobaczysz typowe wyzwalacze. W **obszarze** Szablony wybierz **pozycję Pusta aplikacja logiki,** aby utworzyć aplikację logiki od podstaw.

### <a name="select-a-trigger"></a>Wybieranie wyzwalacza

Wyzwalacz to konkretne zdarzenie, które uruchamia aplikację logiki. W tym samouczku wyzwalacz, który uruchamia przepływ pracy, odbiera żądanie za pośrednictwem protokołu HTTP.

1. Na pasku wyszukiwania łączników i wyzwalaczy wpisz **HTTP** i naciśnij klawisz Enter.

2. Wybierz pozycję **Żądanie — Po odebraniu żądania HTTP** jako wyzwalacz.

   ![Wybieranie wyzwalacza żądania HTTP](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

   ![Generowanie schematu przy użyciu przykładowego ładunku](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

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

   ![Wklejanie przykładowego ładunku JSON](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Może pojawić się wyskakujące powiadomienie **Pamiętaj, aby w żądaniu uwzględnić nagłówek Content-Type ustawiony na wartość application/json**. Można zignorować ten komunikat i przejść do następnej sekcji.

### <a name="create-a-condition"></a>Tworzenie warunku

W przepływie pracy aplikacji logiki warunki pomagają uruchamiać określone akcje po zakończeniu tego określonego warunku. Gdy warunek zostanie spełniony, można zdefiniować żądaną akcję. W tym samouczku warunkiem jest sprawdzenie, czy typ zdarzenia jest połączony z urządzeniem, czy urządzenie jest odłączone. Akcją będzie wykonanie procedury składowanej w bazie danych.

1. Wybierz **pozycję + Nowy krok,** a następnie pozycję **Wbudowany**, a następnie znajdź i wybierz **pozycję Warunek**. Kliknij pozycję **Wybierz wartość.** Zostanie otwarte okno z zawartością dynamiczną — polami, które można wybrać. Wypełnij pola, jak pokazano poniżej, aby wykonać je tylko dla zdarzeń Urządzenie połączone i Odłączone urządzenie:

   * Wybierz wartość: **eventType** — wybierz ją z pól w zawartości dynamicznej, która jest wyświetlana po kliknięciu tego pola.
   * Zmiana wartości "is equal to" na **kończy się na**.
   * Wybierz wartość: **nected**.

     ![Warunek wypełnienia](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. W **oknie dialogowym w** przypadku wartości true kliknij pozycję **Dodaj akcję**.
  
   ![Dodawanie akcji w przypadku wartości true](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Wyszukaj pozycję Cosmos DB i wybierz Azure Cosmos DB **— Wykonaj procedurę składowaną**

   ![Wyszukiwanie bazy danych CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Wypełnij pole **cosmosdb-connection** dla **pola Nazwa połączenia** i wybierz wpis w tabeli, a następnie wybierz pozycję **Utwórz**. Zostanie wyświetlony panel **Wykonywanie procedury składowanej.** Wprowadź wartości pól:

   **Identyfikator bazy danych:** ToDoList

   **Identyfikator kolekcji:** Elementy

   **Sproc ID**: LatestDeviceConnectionState

5. Wybierz **pozycję Dodaj nowy parametr**. Na wyświetlonej liście rozwijanej zaznacz pola  obok pola Klucz partycji i Parametry procedury składowanej, a następnie kliknij dowolne inne miejsce na ekranie. Dodaje pole dla wartości klucza partycji i pole dla parametrów procedury składowanej.

   ![Zrzut ekranu przedstawia element Execute stored procedure (Wykonaj procedurę składowaną) z wybranym parametrem Add new (Dodaj nowy parametr).](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Teraz wprowadź wartość klucza partycji i parametry, jak pokazano poniżej. Pamiętaj, aby umieścić w nawiasach kwadratowych i podwójnych cudzysłowach, jak pokazano. Może być trzeba kliknąć pozycję **Dodaj zawartość dynamiczną,** aby uzyskać prawidłowe wartości, których można użyć tutaj.

   ![Zrzut ekranu przedstawia element Execute stored procedure (Wykonaj procedurę składowaną) z wprowadzonymi parametrami.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. W górnej części okienka z instrukcjami For **Each** w obszarze Wybierz dane wyjściowe **z poprzednich** kroków upewnij się,  **że** wybrano pozycję Treść.

   ![wypełnianie aplikacji logiki dla każdego](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Zapisz aplikację logiki.

### <a name="copy-the-http-url"></a>Kopiowanie adresu URL HTTP

Przed opuszczeniem aplikacji Logic Apps Designer skopiuj adres URL, na który aplikacja logiki nasłuchuje wyzwalacza. Ten adres URL zostanie użyty do skonfigurowania usługi Event Grid.

1. Kliknij pole konfiguracji wyzwalacza **Po odebraniu żądania HTTP**, aby je rozwinąć.

2. Skopiuj wartość pola **Adres URL żądania HTTP POST**, wybierając przycisk widoczny obok tego pola.

   ![Kopiowanie adresu URL żądania HTTP POST](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Zapisz ten adres URL — zostanie użyty w następnej sekcji.

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurowanie subskrypcji zdarzeń usługi IoT Hub

W tej sekcji skonfigurujesz usługę IoT Hub pod kątem publikowania zdarzeń na bieżąco.

1. W witrynie Azure Portal przejdź do centrum IoT Hub.

2. Wybierz pozycję **Zdarzenia**.

   ![Wyświetlanie szczegółów usługi Event Grid](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Wybierz **pozycję + Subskrypcja zdarzeń.**

   ![Tworzenie nowej subskrypcji zdarzeń](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Wypełnij pole **Szczegóły subskrypcji zdarzeń:** podaj opisową nazwę i wybierz pozycję **Event Grid schemacie**.

5. Wypełnij pola **Typy** zdarzeń. Z listy rozwijanej wybierz z menu tylko **pozycję Urządzenie połączone** i Urządzenie zostało odłączone.  Kliknij dowolne miejsce na ekranie, aby zamknąć listę i zapisać wybrane opcje.

   ![Ustawianie typów zdarzeń do wyszukiwania](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. W **obszarze Szczegóły punktu** końcowego wybierz pozycję Typ punktu końcowego jako web **hook,** kliknij pozycję wybierz punkt końcowy i wklej adres URL skopiowany z aplikacji logiki i potwierdź wybór.

   ![Wybieranie adresu URL punktu końcowego](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Formularz powinien teraz wyglądać podobnie do poniższego przykładu:

   ![Przykładowy formularz subskrypcji zdarzeń](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Wybierz pozycję **Utwórz**, aby zapisać subskrypcję zdarzeń.

## <a name="observe-events"></a>Obserwowanie zdarzeń

Po skonfigurowaniu subskrypcji zdarzeń przetestujmy ją, łącząc urządzenie.

### <a name="register-a-device-in-iot-hub"></a>Rejestrowanie urządzenia w IoT Hub

1. W centrum IoT hub wybierz pozycję **Urządzenia IoT.**

2. Wybierz **pozycję +Dodaj** w górnej części okienka.

3. W polu **Identyfikator urządzenia** wpisz `Demo-Device-1`.

4. Wybierz pozycję **Zapisz**.

5. Możesz dodać wiele urządzeń z różnymi identyfikatorami urządzeń.

   ![Urządzenia dodane do centrum](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Kliknij ponownie urządzenie. Parametry połączenia i klucze zostaną wypełnione. Skopiuj wartość **Connection string -- primary key (Ciąg połączenia — klucz podstawowy)** do późniejszego użycia.

   ![ConnectionString dla urządzenia](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Uruchamianie symulatora urządzenia Raspberry Pi

Zasymulujmy połączenie urządzenia za pomocą internetowego symulatora urządzenia Raspberry Pi.

[Uruchamianie symulatora urządzenia Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Uruchamianie przykładowej aplikacji w symulatorze internetowym urządzenia Raspberry Pi

Spowoduje to wyzwolenie zdarzenia połączonego z urządzeniem.

1. W obszarze kodowania zastąp symbol zastępczy w wierszu 15 swoimi Azure IoT Hub połączenia urządzenia zapisane na końcu poprzedniej sekcji.

   ![Wklejanie parametrów połączenia urządzenia](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Uruchom aplikację, wybierając pozycję **Uruchom**.

Zostaną wyświetlony dane wyjściowe podobne do następujących, które pokazują dane czujnika i komunikaty wysyłane do centrum IoT.

   ![Uruchamianie aplikacji](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Kliknij **przycisk Zatrzymaj,** aby zatrzymać symulator i wyzwolić **zdarzenie Odłączone** urządzenie.

Masz teraz przykładową aplikację do zbierania danych z czujników i wysyłania ich do centrum IoT.

### <a name="observe-events-in-cosmos-db"></a>Obserwowanie zdarzeń w Cosmos DB

Wyniki wykonanej procedury składowanej można wyświetlić w Cosmos DB danych. Oto jak to wygląda. Każdy wiersz zawiera najnowszy stan połączenia urządzenia dla każdego urządzenia.

   ![Jak wynik](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Zamiast używać [interfejsu Azure Portal](https://portal.azure.com), możesz wykonać IoT Hub za pomocą interfejsu wiersza polecenia platformy Azure. Aby uzyskać szczegółowe informacje, zobacz strony interfejsu wiersza polecenia platformy Azure dotyczące tworzenia [subskrypcji](/cli/azure/eventgrid/event-subscription) zdarzeń [i tworzenia urządzenia IoT.](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Użycie zasobów w tym samouczku powoduje naliczanie opłat w ramach Twojej subskrypcji platformy Azure. Po zakończeniu korzystania z samouczka i testowania wyników wyłącz lub usuń zasoby, których nie chcesz zachować.

Jeśli nie chcesz utracić efektów pracy z aplikacją logiki, nie usuwaj jej, tylko wyłącz.

1. Przejdź do aplikacji logiki.

2. W bloku **Przegląd** wybierz pozycję **Usuń lub** **Wyłącz.**

    Każda subskrypcja może zawierać jedno centrum IoT Hub, z którego korzystanie jest bezpłatne. Jeśli w samouczku utworzono bezpłatne centrum, nie musisz go usuwać, ponieważ nie generuje ono kosztów.

3. Przejdź do centrum IoT Hub.

4. W bloku **Przegląd** wybierz pozycję **Usuń**.

    Być może zdecydujesz się zachować centrum IoT Hub, ale usunąć utworzoną subskrypcję zdarzeń.

5. W centrum IoT Hub wybierz pozycję **Event Grid**.

6. Wybierz subskrypcję zdarzeń, którą chcesz usunąć.

7. Wybierz pozycję **Usuń**.

Aby usunąć konto Azure Cosmos DB z Azure Portal, kliknij prawym przyciskiem myszy nazwę konta, a następnie kliknij polecenie **Usuń konto.** Zobacz szczegółowe instrukcje [dotyczące usuwania Azure Cosmos DB konta.](../cosmos-db/how-to-manage-database-account.md)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [reagowaniu na zdarzenia IoT Hub za](../iot-hub/iot-hub-event-grid.md) pomocą Event Grid wyzwalania akcji

* [Wypróbuj samouczek dotyczący IoT Hub zdarzeń](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Dowiedz się, co jeszcze można zrobić za pomocą [Event Grid](../event-grid/overview.md)