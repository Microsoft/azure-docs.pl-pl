---
title: Wysyłanie lub odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu języka Python (Najnowsza wersja)
description: Ten artykuł zawiera Przewodnik dotyczący tworzenia aplikacji w języku Python, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu najnowszego pakietu Azure-eventhub.
ms.topic: quickstart
ms.date: 02/11/2020
ms.openlocfilehash: 4850dc9119f6ef43e687f476f66928f13713f051
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105955"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub"></a>Wysyłanie zdarzeń do i odbieranie zdarzeń z centrów zdarzeń przy użyciu języka Python (Azure-eventhub)
W tym przewodniku szybki start pokazano, jak wysyłać zdarzenia do i odbierać zdarzenia z centrum zdarzeń przy użyciu pakietu języka Python **platformy Azure** .

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli dopiero zaczynasz w usłudze Azure Event Hubs, zapoznaj się z tematem [Event Hubs Overview](event-hubs-about.md) przed wykonaniem tego przewodnika Szybki Start. 

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- **Subskrypcja Microsoft Azure**. Do korzystania z usług platformy Azure, w tym usługi Azure Event Hubs, potrzebna jest subskrypcja.  Jeśli nie masz istniejącego konta platformy Azure, możesz zarejestrować się w celu korzystania z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub skorzystać z korzyści dla subskrybentów MSDN podczas [tworzenia konta](https://azure.microsoft.com).
- Środowisko Python 2,7 lub 3,5 lub nowsze z zainstalowanym i zaktualizowanym programem PIP.
- Pakiet języka Python dla Event Hubs. 

    Aby zainstalować pakiet, Uruchom to polecenie w wierszu polecenia zawierającym Język Python w ścieżce:

    ```cmd
    pip install azure-eventhub
    ```

    Zainstaluj następujący pakiet na potrzeby otrzymywania zdarzeń za pomocą usługi Azure Blob Storage jako magazynu punktów kontrolnych:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń**. Pierwszym krokiem jest użycie [Azure Portal](https://portal.azure.com) do utworzenia przestrzeni nazw typu Event Hubs i uzyskanie poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Następnie Pobierz **Parametry połączenia dla przestrzeni nazw Event Hubs** , wykonując instrukcje z artykułu: [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Parametry połączenia są używane w dalszej części tego przewodnika Szybki Start.

## <a name="send-events"></a>Wysyłanie zdarzeń
W tej sekcji utworzysz skrypt języka Python służący do wysyłania zdarzeń do centrum zdarzeń utworzonego wcześniej.

1. Otwórz swój ulubiony Edytor Python, taki jak [Visual Studio Code](https://code.visualstudio.com/).
2. Utwórz skrypt o nazwie *send.py*. Ten skrypt wysyła wsadowe zdarzenia do centrum zdarzeń utworzonego wcześniej.
3. Wklej następujący kod do *send.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Aby uzyskać pełny kod źródłowy, w tym komentarze informacyjne, przejdź do [strony send_async. PR serwisu GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).
    

## <a name="receive-events"></a>Odbieranie zdarzeń
Ten przewodnik Szybki Start używa usługi Azure Blob Storage jako magazynu punktów kontrolnych. Magazyn punktów kontrolnych jest używany do utrwalania punktów kontrolnych (czyli ostatnich pozycji odczytu).  


> [!WARNING]
> Jeśli uruchomisz ten kod w Azure Stack centrum, wystąpią błędy środowiska uruchomieniowego, chyba że zostanie wybrana wersja interfejsu API określonej usługi Storage. Jest to spowodowane tym, że zestaw SDK Event Hubs korzysta z najnowszego dostępnego interfejsu API usługi Azure Storage dostępnego na platformie Azure, który może nie być dostępny na platformie Azure Stack Hub. Azure Stack Hub może obsługiwać inną wersję zestawu SDK obiektów blob magazynu niż te, które są zwykle dostępne na platformie Azure. Jeśli używasz magazynu blogów platformy Azure jako magazynu punktów kontrolnych, sprawdź [obsługiwaną wersję interfejsu API usługi Azure Storage dla kompilacji centrum Azure Stack](/azure-stack/user/azure-stack-acs-differences?#api-version) i wybierz tę wersję w kodzie. 
>
> Na przykład jeśli korzystasz z programu Azure Stack Hub w wersji 2005, najwyższa dostępna wersja usługi Storage to wersja 2019-02-02. Domyślnie Biblioteka klienta zestawu SDK Event Hubs używa najwyższej dostępnej wersji na platformie Azure (2019-07-07 w momencie wydania zestawu SDK). W takim przypadku, oprócz kroków opisanych w tej sekcji, należy również dodać kod docelowy interfejsu API usługi Storage w wersji 2019-02-02. Aby zapoznać się z przykładem dotyczącym konkretnej wersji interfejsu API usługi Storage, zobacz próbki [synchroniczne](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) i [asynchroniczne](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) w witrynie GitHub. 


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Tworzenie konta usługi Azure Storage i kontenera obiektów BLOB
Utwórz konto usługi Azure Storage i kontener obiektów blob, wykonując następujące czynności:

1. [Tworzenie konta usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Tworzenie kontenera obiektów blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Pobierz parametry połączenia z kontem magazynu](../storage/common/storage-configure-connection-string.md)

Pamiętaj, aby zarejestrować parametry połączenia i nazwę kontenera do późniejszego użycia w kodzie odbioru.


### <a name="create-a-python-script-to-receive-events"></a>Tworzenie skryptu w języku Python do odbierania zdarzeń

W tej sekcji utworzysz skrypt języka Python do odbierania zdarzeń z centrum zdarzeń:

1. Otwórz swój ulubiony Edytor Python, taki jak [Visual Studio Code](https://code.visualstudio.com/).
2. Utwórz skrypt o nazwie *recv.py*.
3. Wklej następujący kod do *recv.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method. Read from the beginning of the partition (starting_position: "-1")
            await client.receive(on_event=on_event,  starting_position="-1")

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Aby uzyskać pełny kod źródłowy, w tym dodatkowe komentarze informacyjne, przejdź do [strony recv_with_checkpoint_store_async serwisu GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Uruchamianie aplikacji odbiornika

Aby uruchomić skrypt, Otwórz wiersz polecenia z językiem Python w swojej ścieżce, a następnie uruchom następujące polecenie:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Uruchamianie aplikacji nadawcy

Aby uruchomić skrypt, Otwórz wiersz polecenia z językiem Python w swojej ścieżce, a następnie uruchom następujące polecenie:

```bash
python send.py
```

W oknie odbiorcy powinny być wyświetlane komunikaty wysłane do centrum zdarzeń.


## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki Start zostały wysłane i odebrane zdarzenia asynchronicznie. Aby dowiedzieć się, jak wysyłać i odbierać zdarzenia synchronicznie, przejdź do [strony sync_samples usługi GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

We wszystkich przykładach (synchronicznych i asynchronicznych) w witrynie GitHub przejdź do [biblioteki klienta Event Hubs platformy Azure, aby uzyskać przykłady dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
