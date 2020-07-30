---
title: Wysyłanie i odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu języka JavaScript (starsza wersja)
description: Ten artykuł zawiera Przewodnik dotyczący tworzenia aplikacji JavaScript, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu starego pakietu Azure/Event-Hubs w wersji 2.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: ffa6b821d617731bd6ee5cbb9bec8bd6d856a7a6
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87414126"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Szybki Start: wysyłanie zdarzeń do i odbieranie zdarzeń z platformy Azure Event Hubs przy użyciu języka JavaScript ( @azure/event-hubs wersja 2)
W tym przewodniku szybki start pokazano, jak tworzyć aplikacje JavaScript do wysyłania zdarzeń do i odbierania zdarzeń z centrum zdarzeń przy użyciu pakietu Azure/Event-Hubs Version 2 JavaScript. 

> [!WARNING]
> Ten przewodnik Szybki Start używa starego pakietu Azure/Event-Hubs w wersji 2. Przewodnik Szybki Start, który używa najnowszej **wersji 5** pakietu, znajduje się w temacie [wysyłanie i odbieranie zdarzeń przy użyciu platformy Azure/eventhubs w wersji 5](get-started-node-send-v2.md). Aby przenieść aplikację z używania starego pakietu do nowego, zapoznaj się z [przewodnikiem migracji z platformy Azure/eventhubs w wersji 1 do wersji 5](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md). 


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli dopiero zaczynasz w usłudze Azure Event Hubs, zapoznaj się z tematem [Event Hubs Overview](event-hubs-about.md) przed wykonaniem tego przewodnika Szybki Start. 

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- **Subskrypcja Microsoft Azure**. Do korzystania z usług platformy Azure, w tym usługi Azure Event Hubs, potrzebna jest subskrypcja.  Jeśli nie masz istniejącego konta platformy Azure, możesz zarejestrować się w celu korzystania z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub skorzystać z korzyści dla subskrybentów MSDN podczas [tworzenia konta](https://azure.microsoft.com).
- Node.js w wersji 8. x lub nowszej. Pobierz najnowszą wersję programu LTS z programu [https://nodejs.org](https://nodejs.org) .
- Visual Studio Code (zalecane) lub dowolne inne środowisko IDE
- **Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń**. Pierwszym krokiem jest użycie [Azure Portal](https://portal.azure.com) do utworzenia przestrzeni nazw typu Event Hubs i uzyskanie poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć obszar nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md), a następnie przejdź do poniższych kroków opisanych w tym samouczku. Następnie Pobierz parametry połączenia dla przestrzeni nazw centrum zdarzeń, wykonując instrukcje podane w artykule: [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Te parametry połączenia będą potrzebne w dalszej części tego samouczka.


### <a name="install-npm-package"></a>Zainstaluj pakiet npm
Aby zainstalować [pakiet npm dla Event Hubs](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0), Otwórz wiersz polecenia, który znajduje `npm` się w jego ścieżce, Zmień katalog na folder, w którym chcesz uzyskać przykłady, a następnie Uruchom to polecenie.

```shell
npm install @azure/event-hubs@2
```

Aby zainstalować [pakiet npm dla hosta procesora zdarzeń](https://www.npmjs.com/package/@azure/event-processor-host), zamiast tego uruchom poniższe polecenie

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Wysyłanie zdarzeń

W tej sekcji pokazano, jak utworzyć aplikację JavaScript, która wysyła zdarzenia do centrum zdarzeń. 

> [!NOTE]
> Ten przewodnik Szybki start możesz pobrać jako przykład z witryny [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), zastąpić ciągi `EventHubConnectionString` i `EventHubName` wartościami swojego centrum zdarzeń, a następnie uruchomić go. Alternatywnie możesz utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com). 
2. Utwórz plik o nazwie `send.js` i wklej do niego Poniższy kod. Pobierz parametry połączenia dla przestrzeni nazw centrum zdarzeń, wykonując instrukcje podane w artykule: [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Wprowadź parametry połączenia i nazwę centrum zdarzeń w powyższym kodzie
4. Następnie uruchom polecenie `node send.js` w wierszu polecenia, aby wykonać ten plik. Spowoduje to wysłanie zdarzeń 100 do centrum zdarzeń

Gratulacje! Wysłano zdarzenia do centrum zdarzeń.


## <a name="receive-events"></a>Odbieranie zdarzeń

W tej sekcji pokazano, jak utworzyć aplikację JavaScript, która odbiera zdarzenia z pojedynczej partycji domyślnej grupy odbiorców w centrum zdarzeń. 

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com). 
2. Utwórz plik o nazwie `receive.js` i wklej do niego Poniższy kod.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Wprowadź parametry połączenia i nazwę centrum zdarzeń w powyższym kodzie.
4. Następnie uruchom polecenie `node receive.js` w wierszu polecenia, aby wykonać ten plik. Spowoduje to odebranie zdarzeń z jednej z partycji domyślnej grupy odbiorców w centrum zdarzeń

Gratulacje! Odebrano zdarzenia z centrum zdarzeń.

## <a name="receive-events-using-event-processor-host"></a>Odbieranie zdarzeń za pomocą hosta procesora zdarzeń

W tej sekcji pokazano, jak odbierać zdarzenia z centrum zdarzeń za pomocą usługi Azure [klasy eventprocessorhost](event-hubs-event-processor-host.md) w aplikacji JavaScript. Klasy eventprocessorhost (EPH) pomaga efektywnie odbierać zdarzenia z centrum zdarzeń przez tworzenie odbiorników we wszystkich partycjach w grupie odbiorców centrum zdarzeń. W Azure Storage Blob są wykonywane punkty kontrolne metadanych odebranych komunikatów w regularnych odstępach czasu. Takie podejście ułatwia dalsze otrzymywanie komunikatów z miejsca, w którym zostało przerwane w późniejszym czasie.

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com). 
2. Utwórz plik o nazwie `receiveAll.js` i wklej do niego Poniższy kod.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Wprowadź parametry połączenia i nazwę centrum zdarzeń w powyższym kodzie wraz z parametrami połączenia dla Blob Storage platformy Azure
4. Następnie uruchom polecenie `node receiveAll.js` w wierszu polecenia, aby wykonać ten plik.

Gratulacje! Odebrano zdarzenia z centrum zdarzeń za pomocą hosta procesora zdarzeń. Spowoduje to odebranie zdarzeń ze wszystkich partycji domyślnej grupy odbiorców w centrum zdarzeń

## <a name="next-steps"></a>Następne kroki
Przeczytaj następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologia w usłudze Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)
- Zapoznaj się z innymi przykładami JavaScript dotyczącymi [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) i [hosta procesora zdarzeń](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) w witrynie GitHub
