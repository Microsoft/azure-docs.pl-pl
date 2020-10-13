---
title: Zarządzanie zasobami i powiązanymi jednostkami przy użyciu zestawu SDK platformy Media Services .NET
description: Dowiedz się, jak zarządzać zasobami i powiązanymi jednostkami przy użyciu zestawu Media Services SDK dla platformy .NET.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: b615e6526eb4187d354c4e276147743156b10bc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89266021"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Zarządzanie zasobami i powiązanymi jednostkami przy użyciu zestawu SDK platformy Media Services .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)

W tym temacie pokazano, jak zarządzać jednostkami Azure Media Services przy użyciu platformy .NET.

Począwszy od 1 kwietnia 2017 roku, wszystkie rekordy zadań na Twoim koncie, które są starsze niż 90 dni, będą automatycznie usuwane wraz ze skojarzonymi rekordami zadań podrzędnych nawet wtedy, gdy całkowita liczba rekordów jest mniejsza niż maksymalny limit przydziału. Na przykład 1 kwietnia 2017 każdy rekord zadania na koncie starszym niż 31 grudnia 2016 zostanie automatycznie usunięty. Jeśli zachodzi potrzeba archiwizowania informacji o zadaniu/zadaniu, można użyć kodu opisanego w tym temacie.

## <a name="prerequisites"></a>Wymagania wstępne

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 

## <a name="get-an-asset-reference"></a>Pobierz odwołanie do zasobu
Często zadaniem jest uzyskanie odwołania do istniejącego elementu zawartości w Media Services. Poniższy przykład kodu pokazuje, jak uzyskać odwołanie do zasobu z kolekcji Assets obiektu kontekstu serwera na podstawie identyfikatora zasobu. Poniższy przykład kodu używa zapytania LINQ, aby uzyskać odwołanie do istniejącego obiektu IAsset.

```csharp
    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }
```

## <a name="list-all-assets"></a>Wyświetl listę wszystkich zasobów
Wraz ze wzrostem liczby elementów zawartości w magazynie warto wyświetlić listę zasobów. Poniższy przykład kodu pokazuje, jak wykonać iterację kolekcji elementów zawartości w obiekcie kontekstu serwera. W przypadku każdego elementu zawartości przykład kodu zapisuje także niektóre jego wartości właściwości w konsoli. Na przykład każdy element zawartości może zawierać wiele plików multimedialnych. Przykład kodu zapisuje wszystkie pliki skojarzone z poszczególnymi zasobami.

```csharp
    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="get-a-job-reference"></a>Pobierz odwołanie do zadania

Podczas pracy z zadaniami przetwarzania w Media Services kodzie często trzeba uzyskać odwołanie do istniejącego zadania na podstawie identyfikatora. Poniższy przykład kodu pokazuje, jak uzyskać odwołanie do obiektu IJob z kolekcji jobs.

Może być konieczne uzyskanie odwołania do zadania podczas uruchamiania długotrwałego zadania kodowania i konieczność sprawdzenia stanu zadania w wątku. W takich przypadkach, gdy metoda zwraca z wątku, należy pobrać odświeżone odwołanie do zadania.

```csharp
    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }
```

## <a name="list-jobs-and-assets"></a>Wyświetlanie listy zadań i zasobów
Ważne powiązane zadanie to lista zasobów ze skojarzonymi z nimi zadaniami w Media Services. Poniższy przykład kodu pokazuje, jak wyświetlić listę każdego obiektu IJob, a następnie dla każdego zadania wyświetlić właściwości dotyczące zadania, wszystkie powiązane zadania, wszystkie zasoby wejściowe i wszystkie zasoby wyjściowe. Kod w tym przykładzie może być przydatny w przypadku wielu innych zadań. Jeśli na przykład chcesz wyświetlić listę zasobów wyjściowych z jednego lub więcej uruchomionych wcześniej zadań kodowania, ten kod pokazuje, jak uzyskać dostęp do zasobów wyjściowych. Jeśli masz odwołanie do wyjściowego elementu zawartości, możesz dostarczyć zawartość do innych użytkowników lub aplikacji, pobierając je lub dostarczając adresy URL. 

Aby uzyskać więcej informacji na temat opcji dostarczania zasobów, zobacz [dostarczanie zasobów przy użyciu zestawu Media Services SDK dla platformy .NET](media-services-deliver-streaming-content.md).

```csharp
    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="list-all-access-policies"></a>Wyświetl wszystkie zasady dostępu
W Media Services można zdefiniować zasady dostępu dla zasobu lub jego plików. Zasady dostępu definiują uprawnienia do pliku lub zasobu (typ dostępu i czas trwania). W kodzie Media Services zwykle definiuje się zasady dostępu, tworząc obiekt IAccessPolicy, a następnie kojarząc go z istniejącym elementem zawartości. Następnie utworzysz obiekt ILocator, który umożliwia uzyskanie bezpośredniego dostępu do zasobów w Media Services. Projekt programu Visual Studio, który jest dołączony do tej serii dokumentacji, zawiera kilka przykładów kodu, które pokazują, jak tworzyć i przypisywać zasady dostępu oraz lokalizatory do zasobów.

Poniższy przykład kodu pokazuje, jak wyświetlić listę wszystkich zasad dostępu na serwerze oraz typ uprawnień skojarzonych z każdym z nich. Innym przydatnym sposobem wyświetlania zasad dostępu jest wyświetlenie listy wszystkich obiektów ILocator na serwerze, a następnie dla każdego lokalizatora można wyświetlić listę skojarzonych zasad dostępu przy użyciu właściwości AccessPolicy.

```csharp
    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
```
    
## <a name="limit-access-policies"></a>Ograniczanie zasad dostępu 

>[!NOTE]
> Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). 

Na przykład można utworzyć ogólny zestaw zasad o następującym kodzie, który będzie wykonywany tylko raz w aplikacji. Identyfikatory można rejestrować w pliku dziennika w celu późniejszego użycia:

```csharp
    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);
```

Następnie można użyć istniejących identyfikatorów w kodzie w następujący sposób:

```csharp
    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());
```

## <a name="list-all-locators"></a>Wyświetl listę wszystkich lokalizatorów
Lokalizator to adres URL, który zapewnia bezpośrednią ścieżkę dostępu do elementu zawartości, wraz z uprawnieniami do elementu zawartości, zgodnie z definicją stowarzyszonych zasad dostępu lokalizatora. Każdy element zawartości może mieć kolekcję obiektów ILocator skojarzonych z nią we właściwości Locators. Kontekst serwera ma również kolekcję lokalizatorów, która zawiera wszystkie lokalizatory.

Poniższy przykład kodu wyświetla listę wszystkich lokalizatorów na serwerze. Dla każdego lokalizatora Wyświetla identyfikator powiązanych zasobów i zasad dostępu. Wyświetla również typ uprawnień, datę wygaśnięcia i pełną ścieżkę do elementu zawartości.

Należy pamiętać, że ścieżka lokalizatora do elementu zawartości jest tylko podstawowym adresem URL dla elementu zawartości. Aby utworzyć bezpośrednie ścieżki do poszczególnych plików, do których użytkownik lub aplikacja może przeszukać, kod musi dodać ścieżkę pliku do ścieżki lokalizatora. Aby uzyskać więcej informacji o tym, jak to zrobić, zobacz temat [dostarczanie zasobów przy użyciu zestawu Media Services SDK dla platformy .NET](media-services-deliver-streaming-content.md).

```csharp
    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }
```

## <a name="enumerating-through-large-collections-of-entities"></a>Wyliczanie przez duże kolekcje jednostek
Podczas wykonywania zapytania o jednostki istnieje limit 1000 jednostek, które są zwracane w tym samym czasie, ponieważ Public REST v2 ogranicza wyniki zapytania do 1000 wyników. Należy użyć Skip i Take podczas wyliczania w dużych kolekcjach jednostek. 

Poniższa funkcja pętle przez wszystkie zadania z podanego konta Media Services. Media Services Zwraca 1000 zadań w kolekcji zadań. Funkcja korzysta z funkcji Skip i podejmuje się, aby upewnić się, że wszystkie zadania są wyliczane (na wypadek, gdyby w Twoim koncie było więcej niż 1000 zadań).

```csharp
    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
```

## <a name="delete-an-asset"></a>Usuwanie elementu zawartości
Poniższy przykład usuwa element zawartości.

```csharp
    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }
```

## <a name="delete-a-job"></a>Usuwanie zadania
Aby usunąć zadanie, należy sprawdzić stan zadania określony we właściwości stan. Zadania, które zostały zakończone lub anulowane, mogą zostać usunięte, a zadania, które znajdują się w niektórych innych Stanach, takich jak kolejkowane, zaplanowane lub przetwarzane, muszą zostać anulowane jako pierwsze, a następnie można je usunąć.

Poniższy przykład kodu przedstawia metodę usuwania zadania, sprawdzając Stany zadań, a następnie usuwając po zakończeniu lub anulowaniu stanu. Ten kod zależy od poprzedniej sekcji w tym temacie w celu uzyskania odwołania do zadania: Uzyskaj odwołanie do zadania.

```csharp
    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }
```


## <a name="delete-an-access-policy"></a>Usuwanie zasad dostępu
Poniższy przykład kodu pokazuje, jak uzyskać odwołanie do zasad dostępu w oparciu o identyfikator zasad, a następnie usunąć zasady.

```csharp
    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }
```


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
