---
title: Kanał informacyjny zmiany procesu w usłudze Azure Blob Storage (wersja zapoznawcza) | Microsoft Docs
description: Dowiedz się, jak przetwarzać dzienniki źródeł zmian w aplikacji klienckiej platformy .NET
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74111856"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Kanał informacyjny zmiany procesu w usłudze Azure Blob Storage (wersja zapoznawcza)

Źródło zmian zawiera dzienniki transakcji wszystkich zmian, które wystąpiły w obiektach Blob i metadanych obiektów BLOB na koncie magazynu. W tym artykule opisano sposób odczytywania rekordów źródła zmian przy użyciu biblioteki procesora źródła zmian obiektów BLOB.

Aby dowiedzieć się więcej na temat źródła zmian, zobacz temat [zmiana źródła danych w usłudze Azure Blob Storage (wersja zapoznawcza)](storage-blob-change-feed.md).

> [!NOTE]
> Kanał informacyjny zmiany jest w publicznej wersji zapoznawczej i jest dostępny w regionach **westcentralus** i **westus2** . Aby dowiedzieć się więcej na temat tej funkcji wraz ze znanymi problemami i ograniczeniami, zobacz temat [Obsługa kanałów zmian w usłudze Azure Blob Storage](storage-blob-change-feed.md). Biblioteka procesora kanału informacyjnego zmian może ulec zmianie między tymi elementami i wtedy, gdy ta biblioteka będzie ogólnie dostępna.

## <a name="get-the-blob-change-feed-processor-library"></a>Pobieranie biblioteki procesora kanału informacyjnego zmiany obiektu BLOB

1. W programie Visual Studio Dodaj adres URL `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` do źródeł pakietów NuGet. 

   Aby dowiedzieć się, jak to zrobić, zobacz [źródła pakietów](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. W Menedżerze pakietów NuGet Znajdź pakiet **Microsoft. Azure. Storage. Changefeed** i zainstaluj go w projekcie. 

   Aby dowiedzieć się, jak to zrobić, zobacz [Znajdowanie i instalowanie pakietu](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

## <a name="connect-to-the-storage-account"></a>Nawiązywanie połączenia z kontem magazynu

Przeanalizuj parametry połączenia, wywołując metodę [CloudStorageAccount. TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) . 

Następnie Utwórz obiekt, który reprezentuje Blob Storage na koncie magazynu, wywołując metodę [CloudStorageAccount. CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient) .

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>Inicjowanie źródła zmian

Dodaj następujące instrukcje using na początku pliku kodu. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Następnie Utwórz wystąpienie klasy **ChangeFeed** przez wywołanie metody **GetContainerReference** . Przekaż nazwę kontenera źródła zmian.

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>Odczytywanie rekordów

> [!NOTE]
> Kanał informacyjny zmiany jest niezmienna i tylko do odczytu na koncie magazynu. Dowolna liczba aplikacji może jednocześnie odczytywać i przetwarzać Źródło zmian oraz niezależnie zmieniać ich wygodę. Rekordy nie są usuwane ze źródła zmian, gdy aplikacja je odczytuje. Stan odczytu lub iteracji każdego zużywanego czytnika jest niezależny i obsługiwany tylko przez aplikację.

Najprostszym sposobem odczytywania rekordów jest utworzenie wystąpienia klasy **ChangeFeedReader** . 

Ten przykład wykonuje iterację wszystkich rekordów w źródle zmian, a następnie drukuje do konsoli kilka wartości z każdego rekordu. 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>Wznawianie odczytywania rekordów z zapisanej pozycji

Możesz zapisać swoją pozycję odczytu w kanale informacyjnym zmiany i wznowić iterację rekordów w przyszłości. W dowolnym momencie można zapisać stan iteracji źródła zmian przy użyciu metody **ChangeFeedReader. SerializeState ()** . Stan jest **ciągiem** , a aplikacja może zapisać ten stan na podstawie projektu aplikacji (na przykład: do bazy danych lub pliku).

```csharp
    string currentReadState = processor.SerializeState();
```

Możesz kontynuować iterację rekordów z ostatniego stanu, tworząc **ChangeFeedReader** przy użyciu metody **CreateChangeFeedReaderFromPointerAsync** .

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>Przetwarzanie strumienia rekordów

Możesz wybrać, aby przetwarzać rekordy źródła zmian w miarę ich odbierania. Zobacz [specyfikacje](storage-blob-change-feed.md#specifications).

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>Odczytywanie rekordów w zakresie czasu

Kanał informacyjny zmiany jest podzielony na segmenty godzinowe na podstawie czasu zdarzenia zmiany. Zobacz [specyfikacje](storage-blob-change-feed.md#specifications). Można odczytywać rekordy ze segmentów źródła zmian, które mieszczą się w określonym zakresie czasu.

Ten przykład pobiera godziny rozpoczęcia wszystkich segmentów. Następnie wykonuje iterację za pomocą tej listy, dopóki czas rozpoczęcia nie jest dłuższy niż czas ostatniego możliwego do użycia segmentu lub poza czasem zakończenia żądanego zakresu. 

### <a name="selecting-segments-for-a-time-range"></a>Wybieranie segmentów dla zakresu czasu

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>Odczytywanie rekordów w segmencie

Rekordy można odczytywać z poszczególnych segmentów lub zakresów segmentów.

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>Odczytaj rekordy zaczynające się od czasu

Rekordy źródła danych zmian można odczytać z segmentu początkowego aż do końca. Podobnie jak w przypadku odczytywania rekordów w zakresie czasu, można wyświetlić listę segmentów i wybrać segment, z którego ma zostać rozpoczęta iteracja.

Ten przykład pobiera wartość [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) pierwszego segmentu do przetworzenia.

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

Ten przykład przetwarza zmiany rekordów źródła danych, rozpoczynając od [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) segmentu początkowego.

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> Segment może mieć dzienniki źródła zmian w co najmniej jednym *chunkFilePath*. W przypadku wielu *chunkFilePath* system ma wewnętrznie podzielić rekordy na wiele fragmentów w celu zarządzania przepływem na potrzeby publikowania. Jest gwarantowane, że każda partycja segmentu będzie zawierać zmiany dla wzajemnie wykluczających się obiektów blob i może być przetwarzane niezależnie bez naruszania kolejności. Można użyć klasy **ChangeFeedSegmentShardReader** do iteracji przez rekordy na poziomie fragmentu, jeśli jest to najbardziej wydajne dla danego scenariusza.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o dziennikach źródła zmian. Zobacz artykuł [Zmiana kanału informacyjnego w usłudze Azure Blob Storage (wersja zapoznawcza)](storage-blob-change-feed.md)
