---
title: Kanał informacyjny zmiany procesu w usłudze Azure Blob Storage (wersja zapoznawcza) | Microsoft Docs
description: Dowiedz się, jak przetwarzać dzienniki źródeł zmian w aplikacji klienckiej platformy .NET
author: normesta
ms.author: normesta
ms.date: 06/18/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: dedf1174e00f5bb75822fb720a592af86121ec2d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691432"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Kanał informacyjny zmiany procesu w usłudze Azure Blob Storage (wersja zapoznawcza)

Źródło zmian zawiera dzienniki transakcji wszystkich zmian, które wystąpiły w obiektach Blob i metadanych obiektów BLOB na koncie magazynu. W tym artykule opisano sposób odczytywania rekordów źródła zmian przy użyciu biblioteki procesora źródła zmian obiektów BLOB.

Aby dowiedzieć się więcej na temat źródła zmian, zobacz temat [zmiana źródła danych w usłudze Azure Blob Storage (wersja zapoznawcza)](storage-blob-change-feed.md).

> [!NOTE]
> Kanał informacyjny zmiany jest w publicznej wersji zapoznawczej i jest dostępny w regionach **westcentralus** i **westus2** . Aby dowiedzieć się więcej na temat tej funkcji wraz ze znanymi problemami i ograniczeniami, zobacz temat [Obsługa kanałów zmian w usłudze Azure Blob Storage](storage-blob-change-feed.md). Biblioteka procesora kanału informacyjnego zmian może ulec zmianie między tymi elementami i wtedy, gdy ta biblioteka będzie ogólnie dostępna.

## <a name="get-the-blob-change-feed-processor-library"></a>Pobieranie biblioteki procesora kanału informacyjnego zmiany obiektu BLOB

1. Otwórz okno polecenia (na przykład: Windows PowerShell).
2. W katalogu projektu Zainstaluj pakiet NuGet **Azure. Storage. Blobs. Changefeed** .

```console
dotnet add package Azure.Storage.Blobs.ChangeFeed --source https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json --version 12.0.0-dev.20200604.2
```
## <a name="read-records"></a>Odczytaj rekordy

> [!NOTE]
> Kanał informacyjny zmiany jest niezmienna i tylko do odczytu na koncie magazynu. Dowolna liczba aplikacji może jednocześnie odczytywać i przetwarzać Źródło zmian oraz niezależnie zmieniać ich wygodę. Rekordy nie są usuwane ze źródła zmian, gdy aplikacja je odczytuje. Stan odczytu lub iteracji każdego zużywanego czytnika jest niezależny i obsługiwany tylko przez aplikację.

Ten przykład wykonuje iterację wszystkich rekordów w źródle zmian, dodaje je do listy, a następnie zwraca tę listę do obiektu wywołującego.
 
```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

Ten przykład drukuje do konsoli kilka wartości z każdego rekordu na liście. 

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>Wznów odczytywanie rekordów z zapisanej pozycji

Możesz zapisać swoją pozycję odczytu w kanale informacyjnym zmiany, a następnie wznowić iterację w rekordach w przyszłości. Możesz zapisać pozycję odczytu, pobierając kursor źródła zmian. Kursor jest **ciągiem** , a aplikacja może zapisać ten ciąg w dowolny sposób, który ma sens dla projektu aplikacji (na przykład: do pliku lub bazy danych).

Ten przykład wykonuje iterację wszystkich rekordów w źródle zmian, dodaje je do listy i zapisuje kursor. Lista i kursor są zwracane do obiektu wywołującego. 

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {
    
        changeFeedEvents.Add(changeFeedEvent);             
    }
    
    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>Przetwarzanie strumienia rekordów

Możesz wybrać, aby przetwarzać rekordy źródła zmian w miarę ich odbierania. Zobacz [specyfikacje](storage-blob-change-feed.md#specifications). Zalecamy, aby sondować zmiany co godzinę lub tak.

Ten przykład okresowo sonduje zmiany.  Jeśli istnieją zmiany rekordów, ten kod przetwarza te rekordy i zapisuje wskaźnik źródła zmian. W ten sposób, jeśli proces zostanie zatrzymany, a następnie ponownie uruchomiony, aplikacja może użyć kursora do wznowienia przetwarzania rekordów w miejscu, w którym zostało ono ostatnio pozostawione. Ten przykład zapisuje kursor do lokalnego pliku konfiguracji aplikacji, ale aplikacja może zapisać ją w dowolnym formularzu, który jest najbardziej sensowny dla danego scenariusza. 

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            
                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>Odczytywanie rekordów w zakresie czasu

Można odczytywać rekordy, które mieszczą się w określonym zakresie czasu. Ten przykład wykonuje iterację wszystkich rekordów w źródle zmian, które mieszczą się w zakresie od 3:00 do marca 2 2017 i 2:00 AM w dniu 7 2019 października, dodaje je do listy, a następnie zwraca tę listę do obiektu wywołującego.

### <a name="selecting-segments-for-a-time-range"></a>Wybieranie segmentów dla zakresu czasu

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2017, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 10, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

Określony czas rozpoczęcia jest zaokrąglany w dół do najbliższej godziny, a czas zakończenia jest zaokrąglany do najbliższej godziny. Istnieje możliwość, że użytkownicy mogą zobaczyć zdarzenia, które wystąpiły przed upływem czasu rozpoczęcia i czasu zakończenia. Istnieje również możliwość, że niektóre zdarzenia występujące między czasem rozpoczęcia i zakończenia nie będą wyświetlane. Wynika to z faktu, że zdarzenia mogą być rejestrowane w ciągu godziny wcześniejszej niż godzina rozpoczęcia lub w ciągu godziny po zakończeniu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o dziennikach źródła zmian. Zobacz artykuł [Zmiana kanału informacyjnego w usłudze Azure Blob Storage (wersja zapoznawcza)](storage-blob-change-feed.md)
