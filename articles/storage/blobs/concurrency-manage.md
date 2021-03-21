---
title: Zarządzanie współbieżnością w usłudze BLOB Storage
titleSuffix: Azure Storage
description: Dowiedz się, jak zarządzać wieloma składnikami zapisywania do obiektu BLOB, implementując optymistyczną lub pesymistyczną współbieżność w aplikacji. Optymistyczna współbieżność sprawdza wartość ETag dla obiektu BLOB i porównuje ją z udostępnionym elementem ETag. Współbieżność pesymistyczna używa wyłącznej dzierżawy, aby zablokować obiekt BLOB do innych autorów.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ea0bed0884a3a03e2cd15b274b2afb0f054b0cbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96523520"
---
# <a name="managing-concurrency-in-blob-storage"></a>Zarządzanie współbieżnością w usłudze BLOB Storage

Nowoczesne aplikacje często mają wielu użytkowników Wyświetlanie i aktualizowanie danych jednocześnie. Deweloperzy aplikacji muszą dokładnie rozważyć, jak zapewnić użytkownikom końcowym przewidywalne środowisko, szczególnie w przypadku scenariuszy, w których wielu użytkowników może aktualizować te same dane. Istnieją trzy główne strategie współbieżności danych, które zazwyczaj rozważają deweloperzy:  

- **Optymistyczna współbieżność**: aplikacja wykonująca aktualizację będzie w ramach swojej aktualizacji określić, czy dane zostały zmienione od czasu ostatniego odczytu danych przez aplikację. Na przykład jeśli dwóch użytkowników przeglądających stronę typu wiki przetworzy aktualizację na tej stronie, platforma wiki musi upewnić się, że druga aktualizacja nie zastąpi pierwszej aktualizacji. Należy również upewnić się, że użytkownicy wiedzą, czy ich Aktualizacja zakończyła się pomyślnie. Ta strategia jest najczęściej używana w aplikacjach sieci Web.

- **Współbieżność pesymistyczna**: aplikacja, która zamierza wykonać aktualizację, będzie blokować obiekt, uniemożliwiając innym użytkownikom aktualizowanie danych do momentu zwolnienia blokady. Na przykład w scenariuszu podstawowej/pomocniczej replikacji danych, w której tylko podstawowy wykonuje aktualizacje, podstawowa zazwyczaj przechowuje wyłączną blokadę danych przez dłuższy czas, aby nikt inny nie mógł go zaktualizować.

- **Ostatni składnik zapisywania usługi WINS**: podejście, które pozwala na podejmowanie operacji aktualizowania bez wcześniejszego określenia, czy inna aplikacja zaktualizował dane od czasu odczytu. Takie podejście jest zwykle używane, gdy dane są partycjonowane w taki sposób, że wielu użytkowników nie będzie jednocześnie uzyskiwać dostępu do tych samych danych. Może być również przydatne, gdy trwa przetwarzanie strumieni danych o krótkim czasie.

Usługa Azure Storage obsługuje wszystkie trzy strategie, chociaż ma możliwość zapewnienia pełnej obsługi optymistycznej i pesymistycznej współbieżności. Usługa Azure Storage została zaprojektowana z myślą o założeniu silnego modelu spójności, który gwarantuje, że po wykonaniu operacji wstawiania lub aktualizacji kolejne operacje odczytu zwracają najnowszą aktualizację.

Oprócz wyboru odpowiedniej strategii współbieżności deweloperzy powinni również wiedzieć, jak platforma magazynowa izoluje zmiany, szczególnie zmiany w tym samym obiekcie w różnych transakcjach. Usługa Azure Storage używa izolacji migawek, aby zezwalać na operacje odczytu współbieżnie z operacjami zapisu w ramach jednej partycji. Izolacja migawki gwarantuje, że wszystkie operacje odczytu zwracają spójną migawkę danych, nawet gdy występują aktualizacje.

Można wybrać jednooptymistyczne lub pesymistyczne modele współbieżności, aby zarządzać dostępem do obiektów blob i kontenerów. Jeśli nie określisz jawnie strategii, domyślnie jest to ostatni składnik zapisywania usługi WINS.  

## <a name="optimistic-concurrency"></a>Współbieżność optymistyczna

Usługa Azure Storage przypisuje identyfikator do każdego przechowywanego obiektu. Ten identyfikator jest aktualizowany przy każdym wykonywaniu operacji zapisu na obiekcie. Identyfikator jest zwracany do klienta jako część odpowiedzi HTTP GET w nagłówku ETag zdefiniowanym przez protokół HTTP.

Klient wykonujący aktualizację może wysłać oryginalny element ETag wraz z nagłówkiem warunkowym, aby upewnić się, że aktualizacja będzie miała miejsce tylko w przypadku spełnienia określonego warunku. Na przykład jeśli określono nagłówek **if-Match** , usługa Azure Storage sprawdza, czy wartość ETag określona w żądaniu aktualizacji jest taka sama jak element ETag dla aktualizowanego obiektu. Aby uzyskać więcej informacji na temat nagłówków warunkowych, zobacz [Określanie nagłówków warunkowych dla operacji BLOB Service](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).

Ten proces jest następujący:  

1. Pobieranie obiektu BLOB z usługi Azure Storage. Odpowiedź zawiera wartość nagłówka HTTP ETag, która identyfikuje bieżącą wersję obiektu.
1. Podczas aktualizowania obiektu BLOB należy uwzględnić wartość ETag uzyskaną w kroku 1 w nagłówku warunkowym **if-Match** żądania zapisu. Usługa Azure Storage porównuje wartość ETag w żądaniu z bieżącą wartością ETag obiektu BLOB.
1. Jeśli bieżąca wartość ETag obiektu BLOB jest inna niż określona w nagłówku warunkowym **if-Match** dostarczonym w żądaniu, usługa Azure Storage zwraca kod stanu HTTP 412 (niepowodzenie warunku wstępnego). Ten błąd wskazuje klientowi, że inny proces zaktualizował obiekt BLOB od momentu jego pierwszego pobrania przez klienta.
1. Jeśli bieżąca wartość ETag obiektu BLOB jest taka sama jak wersja elementu ETag w nagłówku warunku **if-Match** w żądaniu, usługa Azure Storage wykonuje żądaną operację i aktualizuje bieżącą wartość ETag obiektu BLOB.  

Poniższy przykład kodu przedstawia sposób konstruowania warunku **if-Match** w żądaniu zapisu sprawdzającym wartość ETag dla obiektu BLOB. Usługa Azure Storage szacuje, czy bieżący element ETag obiektu BLOB jest taki sam jak element ETag podany w żądaniu i wykonuje operację zapisu tylko wtedy, gdy dwie wartości ETag są zgodne. Jeśli inny proces zaktualizował obiekt BLOB w tymczasowym, usługa Azure Storage zwróci komunikat o stanie HTTP 412 (niepowodzenie warunku wstępnego).  

# <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

Usługa Azure Storage obsługuje również inne nagłówki warunkowe, w tym jak **If-Modified-** AS, **if-Unmodified** -a i **If-None-Match**. Aby uzyskać więcej informacji, zobacz [Określanie nagłówków warunkowych dla operacji usługi BLOB Service](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

## <a name="pessimistic-concurrency-for-blobs"></a>Współbieżność pesymistyczna dla obiektów BLOB

Aby zablokować obiekt BLOB do użytku wyłącznego, możesz uzyskać dzierżawę. Po pozyskaniu dzierżawy należy określić czas trwania dzierżawy. Wartość skończonej dzierżawy może być ważna od od 15 do 60 sekund. Dzierżawa może być również nieskończona, co jest ograniczone do wyłącznej blokady. Możesz odnowić dzierżawę, aby ją przedłużyć i można zwolnić dzierżawę po jej zakończeniu. Usługa Azure Storage automatycznie zwalnia skończone dzierżawy po ich wygaśnięciu.  

Dzierżawy umożliwiają obsługę różnych strategii synchronizacji, w tym wyłączne operacje odczytu/zapisu, wyłącz operacje odczytu i zapisu oraz udostępnione operacje odczytu zapisu/wyłącznych. W przypadku istnienia dzierżawy usługa Azure Storage wymusza wyłączny dostęp do operacji zapisu dla posiadacza dzierżawy. Jednak zapewnienie wyłączności operacji odczytu wymaga, aby deweloper upewnił się, że wszystkie aplikacje klienckie używają identyfikatora dzierżawy i że tylko jeden klient w danym momencie ma prawidłowy identyfikator dzierżawy. Operacje odczytu, które nie zawierają identyfikatora dzierżawy, powodują odczyty udostępnione.  

W poniższym przykładzie kodu pokazano, jak uzyskać wyłączną dzierżawę obiektu BLOB, zaktualizować zawartość obiektu BLOB, podając identyfikator dzierżawy, a następnie zwolnij dzierżawę. Jeśli dzierżawa jest aktywna i nie podano identyfikatora dzierżawy w żądaniu zapisu, operacja zapisu kończy się niepowodzeniem z kodem błędu 412 (warunek wstępny nie powiódł się).  

# <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>Współbieżność pesymistyczna dla kontenerów

Dzierżawy w kontenerach umożliwiają korzystanie z tych samych strategii synchronizacji, które są obsługiwane w przypadku obiektów blob, w tym wyłącznych odczytów zapisu/udostępniania, wyłącznego zapisu/odczytu i udostępniania zapisu/odczytu na wyłączność. Jednak w przypadku kontenerów blokada wyłączna jest wymuszana tylko w operacjach usuwania. Aby usunąć kontener z aktywną dzierżawą, klient musi uwzględnić aktywny identyfikator dzierżawy z żądaniem usuwania. Wszystkie inne operacje kontenera będą się kończyć pomyślnie w kontenerze dzierżawionym bez identyfikatora dzierżawy.  

## <a name="next-steps"></a>Następne kroki

* [Określanie nagłówków warunkowych dla operacji Blob service](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
* [Kontener dzierżawy](/rest/api/storageservices/lease-container)
* [Dzierżawienie obiektu blob](/rest/api/storageservices/lease-blob)
