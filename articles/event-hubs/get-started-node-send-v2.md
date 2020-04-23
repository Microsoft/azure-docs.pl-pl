---
title: Wysyłanie lub odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu języka JavaScript (najnowsze)
description: Ten artykuł zawiera przewodnik do tworzenia aplikacji JavaScript, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu najnowszego pakietu azure/event-hubs w wersji 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: 42c58a84c71b6573814e82517373db6900a83658
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025020"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs-version-5"></a>Wysyłanie zdarzeń do lub odbieranie zdarzeń z centrów zdarzeń przy użyciu języka JavaScript (usługa azure/event-hubs w wersji 5)
Ten przewodnik Szybki start pokazuje sposób wysyłania zdarzeń do centrum zdarzeń i odbierania ich z wykorzystaniem pakietu JavaScript **usługi azure/event-hubs w wersji 5.** 

> [!IMPORTANT]
> Ten przewodnik Szybki start korzysta z najnowszego pakietu usługi azure/event-hubs w wersji 5. Aby uzyskać szybki start, który używa starego pakietu azure/event-hubs w wersji 2, zobacz [Wysyłanie i odbieranie zdarzeń przy użyciu usługi azure/event-hubs w wersji 2](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli jesteś nowy w usłudze Azure Event Hubs, zobacz [Centrum zdarzeń omówienie](event-hubs-about.md) przed rozpoczęciem tego przewodnika Szybki start. 

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- **Subskrypcja platformy Microsoft Azure**. Aby korzystać z usług platformy Azure, w tym usługi Azure Event Hubs, potrzebujesz subskrypcji.  Jeśli nie masz istniejącego konta platformy Azure, możesz zarejestrować się w celu [uzyskania bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub skorzystać z korzyści dla subskrybenta MSDN podczas [tworzenia konta.](https://azure.microsoft.com)
- Node.js w wersji 8.x lub nowszej. Pobierz najnowszą [wersję pomocy technicznej długoterminowej (LTS).](https://nodejs.org)  
- Visual Studio Code (zalecane) lub inne zintegrowane środowisko programistyczne (IDE).  
- Aktywny obszar nazw centrów zdarzeń i centrum zdarzeń. Aby je utworzyć, wykonaj następujące czynności: 

   1. W [witrynie Azure portal](https://portal.azure.com)utwórz obszar nazw typu *Usługi eventów,* a następnie uzyskaj poświadczenia zarządzania, które aplikacja musi komunikować się z centrum zdarzeń. 
   1. Aby utworzyć obszar nazw i centrum zdarzeń, postępuj zgodnie z instrukcjami w przewodnikach [Szybki start: Tworzenie centrum zdarzeń przy użyciu portalu Azure](event-hubs-create.md).
   1. Kontynuuj, postępując zgodnie z instrukcjami zawartymi w tym przewodniku Szybki start. 
   1. Aby uzyskać parametry połączenia dla obszaru nazw Centrum zdarzeń, postępuj zgodnie z instrukcjami w [obszarze Pobierz ciąg połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Nagraj ciąg połączenia, który będzie używany w dalszej części tego przewodnika Szybki start.
- **Utwórz obszar nazw Centrów zdarzeń i centrum zdarzeń**. Pierwszym krokiem jest użycie [witryny Azure Portal](https://portal.azure.com) do utworzenia obszaru nazw typu Event Hubs i uzyskania poświadczeń zarządzania, których aplikacja potrzebuje do komunikowania się z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Następnie pobierz **ciąg połączenia dla obszaru nazw Centrum zdarzeń,** postępując zgodnie z instrukcjami z artykułu: [Pobierz parametry połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Ciąg połączenia należy użyć w dalszej części tego przewodnika Szybki start.

### <a name="install-the-npm-package"></a>Zainstaluj pakiet npm
Aby zainstalować [pakiet Node Package Manager (npm) dla centrów zdarzeń,](https://www.npmjs.com/package/@azure/event-hubs)otwórz wiersz polecenia, który ma *npm* w swojej ścieżce, zmień katalog na folder, w którym chcesz zachować próbki, a następnie uruchom to polecenie:

```shell
npm install @azure/event-hubs
```

Po stronie odbiorczej należy zainstalować jeszcze dwa pakiety. W tym przewodniku Szybki start używasz magazynu obiektów Blob platformy Azure do utrwalania punktów kontrolnych, aby program nie odczytywali zdarzeń, które zostały już odczytane. Wykonuje punkty kontrolne metadanych na odebranych wiadomości w regularnych odstępach czasu w obiekcie blob. Takie podejście ułatwia kontynuowanie otrzymywania wiadomości później od miejsca, w którym zostało przerwane.

Uruchom następujące polecenia:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Wysyłanie zdarzeń

W tej sekcji utworzysz aplikację JavaScript, która wysyła zdarzenia do centrum zdarzeń.

1. Otwórz ulubiony edytor, na przykład [Visual Studio Code](https://code.visualstudio.com).
1. Utwórz plik o nazwie *send.js*i wklej do niego następujący kod:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. W kodzie użyj wartości rzeczywistych, aby zastąpić następujące elementy:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Uruchom, `node send.js` aby wykonać ten plik. To polecenie wysyła partię trzech zdarzeń do Centrum zdarzeń.
1. W witrynie Azure portal sprawdź, czy centrum zdarzeń odebrało wiadomości. W sekcji **Metryki** przełącz się do widoku **Wiadomości.** Odśwież stronę, aby zaktualizować wykres. Może upłynąć kilka sekund, aby pokazać, że wiadomości zostały odebrane.

    [![Sprawdź, czy centrum zdarzeń odebrało wiadomości](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Pełny kod źródłowy, w tym dodatkowe komentarze informacyjne, przejdź do [strony GitHub sendEvents.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Gratulacje! Zdarzenia zostały wysłane do centrum zdarzeń.


## <a name="receive-events"></a>Odbieranie zdarzeń
W tej sekcji otrzymasz zdarzenia z Centrum zdarzeń przy użyciu magazynu magazynu obiektów Blob usługi Azure w aplikacji JavaScript. Wykonuje punkty kontrolne metadanych na odebranych wiadomości w regularnych odstępach czasu w obiekcie blob usługi Azure Storage. Takie podejście ułatwia kontynuowanie otrzymywania wiadomości później od miejsca, w którym zostało przerwane.

> [!NOTE]
> Jeśli korzystasz z usługi Azure Stack Hub, ta platforma może obsługiwać inną wersję SDK obiektów blob magazynu niż te zwykle dostępne na platformie Azure. Na przykład jeśli korzystasz [z usługi Azure Stack Hub w wersji 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), najwyższą dostępną wersją usługi Storage jest wersja 2017-11-09. W takim przypadku oprócz następujących kroków w tej sekcji, należy również dodać kod do docelowej wersji interfejsu API usługi magazynu 2017-11-09. Aby uzyskać przykład sposobu kierowania na określoną wersję interfejsu API magazynu, zobacz przykłady [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) i [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) w usłudze GitHub. Aby uzyskać więcej informacji na temat wersji usługi Azure Storage obsługiwanych w usłudze Azure Stack Hub, zobacz [usługi Azure Stack Hub storage: Różnice i zagadnienia.](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences)


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Tworzenie konta magazynu platformy Azure i kontenera obiektów blob
Aby utworzyć konto magazynu platformy Azure i kontener obiektów blob w nim, wykonaj następujące akcje:

1. [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Tworzenie kontenera obiektów blob na koncie magazynu](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Pobierz ciąg połączenia z kontem magazynu](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Pamiętaj, aby nagrać parametry połączenia i nazwę kontenera do późniejszego użycia w kodzie odbierania.

### <a name="write-code-to-receive-events"></a>Napisz kod, aby odbierać zdarzenia

1. Otwórz ulubiony edytor, na przykład [Visual Studio Code](https://code.visualstudio.com).
1. Utwórz plik o nazwie *receive.js*i wklej do niego następujący kod:

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. W kodzie użyj wartości rzeczywistych, aby zastąpić następujące wartości:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Uruchom `node receive.js` w wierszu polecenia, aby wykonać ten plik. Okno powinno wyświetlać komunikaty o odebranych zdarzeniach.

    > [!NOTE]
    > Aby uzyskać pełny kod źródłowy, w tym dodatkowe komentarze informacyjne, przejdź do [strony GitHub receiveEventsUsingCheckpointStore.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsUsingCheckpointStore.js).

Gratulacje! Zdarzenia zostały odebrane z centrum zdarzeń. Program odbiornika będzie odbierać zdarzenia ze wszystkich partycji domyślnej grupy odbiorców w centrum zdarzeń.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z tymi przykładami na GitHub:

- [Przykłady skryptów w języku JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Przykłady typescript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
