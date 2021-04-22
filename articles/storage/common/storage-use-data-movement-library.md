---
title: Transfer danych za pomocą biblioteki przenoszenia danych dla programu .NET
titleSuffix: Azure Storage
description: Użyj biblioteki przenoszenia danych, aby przenieść lub skopiować dane do lub z zawartości obiektów blob i plików. Kopiowanie danych do usługi Azure Storage z plików lokalnych lub kopiowanie danych w ramach lub między kontami magazynu. Łatwe migrowanie danych do usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: f87379f48f82757916aef0fa0d358835f48cb9a5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875939"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Przenoszenie danych za pomocą biblioteki przenoszenia danych

Biblioteka usługi Azure Storage Data Movement to międzyplatformowa biblioteka open source, która została zaprojektowana do przekazywania, pobierania i kopiowania obiektów blob i plików o wysokiej wydajności. Biblioteka przenoszenia danych udostępnia wygodne metody, które nie są dostępne w bibliotece klienta usługi Azure Storage dla platformy .NET. Te metody zapewniają możliwość ustawienia liczby operacji równoległych, śledzenia postępu transferu, łatwego wznawiania anulowanych transferów i wielu innych operacji.

Ta biblioteka korzysta również z programu .NET Core, co oznacza, że można jej używać podczas tworzenia aplikacji .NET dla systemów Windows, Linux i macOS. Aby dowiedzieć się więcej na temat programu .NET Core, zapoznaj się z dokumentacją [programu .NET Core.](https://dotnet.github.io/) Ta biblioteka działa również w przypadku tradycyjnych aplikacji .NET Framework dla systemu Windows.

W tym dokumencie przedstawiono sposób tworzenia aplikacji konsolowej .NET Core, która działa w systemach Windows, Linux i macOS oraz wykonuje następujące scenariusze:

- Przekaż pliki i katalogi do Blob Storage.
- Zdefiniuj liczbę operacji równoległych podczas przesyłania danych.
- Śledzenie postępu transferu danych.
- Wznów anulowany transfer danych.
- Skopiuj plik z adresu URL do Blob Storage.
- Skopiuj z Blob Storage do Blob Storage.

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio Code](https://code.visualstudio.com/)
- Konto [usługi Azure Storage](storage-account-create.md)

## <a name="setup"></a>Konfiguracja

1. Odwiedź stronę [.NET Core Installation Guide (Przewodnik](https://dotnet.microsoft.com/download) instalacji programu .NET Core), aby zainstalować zestaw .NET Core SDK. Podczas wybierania środowiska wybierz opcję wiersza polecenia.
2. W wierszu polecenia utwórz katalog dla projektu. Przejdź do tego katalogu, a następnie wpisz , `dotnet new console -o <sample-project-name>` aby utworzyć projekt konsoli języka C#.
3. Otwórz ten katalog w Visual Studio Code. Ten krok można szybko wykonać za pośrednictwem wiersza polecenia, wpisując `code .` polecenie w systemie Windows.
4. Zainstaluj rozszerzenie [C# z](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) witryny Visual Studio Code Marketplace. Uruchom Visual Studio Code.
5. W tym momencie powinny zostać wyświetlony dwa monity. Jednym z nich jest dodanie "wymaganych zasobów do kompilowania i debugowania". Kliknij pozycję "tak". Innym monitem jest przywrócenie nierozwiązanych zależności. Kliknij pozycję "Przywróć".
6. `launch.json`Zmodyfikuj w obszarze , aby używać `.vscode` terminalu zewnętrznego jako konsoli. To ustawienie powinno być odczytywane jako `"console": "externalTerminal"`
7. Visual Studio Code umożliwia debugowanie aplikacji .NET Core. Naciśnij `F5` , aby uruchomić aplikację i sprawdzić, czy konfiguracja działa. Powinien zostać wyświetlony Hello world!" wydrukowane w konsoli.

## <a name="add-the-data-movement-library-to-your-project"></a>Dodawanie biblioteki ruchu danych do projektu

1. Dodaj najnowszą wersję biblioteki ruchu danych do `dependencies` sekcji `<project-name>.csproj` pliku. W momencie pisania tego tekstu ta wersja byłaby `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Powinien zostać wyświetlony monit o przywrócenie projektu. Kliknij przycisk "Przywróć". Możesz również przywrócić projekt z wiersza polecenia, wpisując polecenie w katalogu głównym `dotnet restore` projektu.

Zmodyfikuj `<project-name>.csproj` :

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>Konfigurowanie szkieletu aplikacji

Pierwszym krokiem jest skonfigurowanie "szkieletu" kodu naszej aplikacji. Ten kod wyświetla monit o nazwę konta magazynu i klucz konta oraz używa tych poświadczeń do utworzenia `CloudStorageAccount` obiektu. Ten obiekt jest używany do interakcji z kontem usługi Storage we wszystkich scenariuszach transferu. Kod monituje nas również o wybranie typu operacji transferu, która ma być wykonywana.

Zmodyfikuj `Program.cs` :

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="upload-a-local-file-to-a-blob"></a>Przekazywanie pliku lokalnego do obiektu blob

Dodaj metody i `GetSourcePath` `GetBlob` do metody `Program.cs` :

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Zmodyfikuj `TransferLocalFileToAzureBlob` metodę :

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

Ten kod wyświetla monit o ścieżkę do pliku lokalnego, nazwę nowego lub istniejącego kontenera oraz nazwę nowego obiektu blob. Metoda `TransferManager.UploadAsync` wykonuje przekazywanie przy użyciu tych informacji.

Naciśnij `F5` , aby uruchomić aplikację. Aby sprawdzić, czy przekazywanie wystąpiło, wyświetl konto usługi Storage za pomocą [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/).

## <a name="set-the-number-of-parallel-operations"></a>Ustawianie liczby operacji równoległych

Jedną z funkcji oferowanych przez bibliotekę przenoszenia danych jest możliwość ustawienia liczby operacji równoległych w celu zwiększenia przepływności transferu danych. Domyślnie biblioteka ruchu danych ustawia liczbę operacji równoległych na 8 * liczbę rdzeni na maszynie.

Należy pamiętać, że wiele operacji równoległych w środowisku o niskiej przepustowości może przeciążyć połączenie sieciowe i w rzeczywistości uniemożliwić pełne ukończenie operacji. Musisz poeksperymentować z tym ustawieniem, aby określić, co działa najlepiej na podstawie dostępnej przepustowości sieci.

Dodajmy kod, który umożliwia ustawienie liczby operacji równoległych. Dodajmy również kod, który ile czasu zajmuje ukończenie transferu.

Dodaj metodę `SetNumberOfParallelOperations` do metody `Program.cs` :

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

`ExecuteChoice`Zmodyfikuj metodę , aby użyć metody `SetNumberOfParallelOperations` :

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

`TransferLocalFileToAzureBlob`Zmodyfikuj metodę , aby używać czasomierza:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Śledzenie postępu transferu

Wiedza o tym, jak długo trwało przesyłanie danych, jest przydatna. Jednak możliwość zobaczenia postępu transferu *podczas* operacji transferu byłaby jeszcze lepsza. Aby osiągnąć ten scenariusz, musimy utworzyć `TransferContext` obiekt . Obiekt `TransferContext` ma dwie formy: i `SingleTransferContext` `DirectoryTransferContext` . Pierwszy z nich jest do przesyłania pojedynczego pliku, a drugi do transferu katalogu plików.

Dodaj metody i `GetSingleTransferContext` `GetDirectoryTransferContext` do metody `Program.cs` :

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

`TransferLocalFileToAzureBlob`Zmodyfikuj metodę , aby użyć metody `GetSingleTransferContext` :

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>Wznawianie anulowanych transferów

Inną wygodną funkcją oferowaną przez bibliotekę przenoszenia danych jest możliwość wznowienia anulowanej operacji transferu. Dodajmy kod, który umożliwia tymczasowe anulowanie transferu, wpisując , a następnie wznowić `c` transfer po 3 sekundach.

Zmodyfikuj `TransferLocalFileToAzureBlob` :

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Do tej pory nasza `checkpoint` wartość zawsze była ustawiona na `null` . Teraz, jeśli anulujemy przeniesienie, pobierzemy ostatni punkt kontrolny przeniesienia, a następnie użyjemy tego nowego punktu kontrolnego w kontekście transferu.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Transferowanie katalogu lokalnego do usługi Blob Storage

Byłoby to przytłaczające, gdyby biblioteka przenoszenia danych mogła przesyłać tylko jeden plik na raz. Na szczęście tak nie jest. Biblioteka przenoszenia danych umożliwia transfer katalog plików i wszystkich jego podkatalogów. Dodajmy kod, który pozwala nam to zrobić.

Najpierw dodaj metodę do `GetBlobDirectory` metody `Program.cs` :

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Następnie zmodyfikuj `TransferLocalDirectoryToAzureBlobDirectory` :

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Istnieje kilka różnic między tą metodą a metodą przekazywania pojedynczego pliku. Teraz używamy metody `TransferManager.UploadDirectoryAsync` i , która została utworzona `getDirectoryTransferContext` wcześniej. Ponadto teraz zapewniamy wartość operacji przekazywania, która pozwala wskazać, że chcemy uwzględnić `options` podkatalogi w naszym przekazywaniu.

## <a name="copy-a-file-from-url-to-a-blob"></a>Kopiowanie pliku z adresu URL do obiektu blob

Teraz dodajmy kod, który umożliwia skopiowanie pliku z adresu URL do obiektu blob platformy Azure.

Zmodyfikuj `TransferUrlToAzureBlob` :

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Jednym z ważnych przypadków użycia tej funkcji jest konieczność przeniesienia danych z innej usługi w chmurze (np. AWS) na platformę Azure. Jeśli masz adres URL, który zapewnia dostęp do zasobu, możesz łatwo przenieść ten zasób do usługi Azure Blobs przy użyciu `TransferManager.CopyAsync` metody . Ta metoda wprowadza również nowy parametr logiczny. Ustawienie tego parametru na wartość wskazuje, że chcemy wykonać asynchroniczną kopię `true` po stronie serwera. Ustawienie tego parametru na wartość wskazuje kopię synchroniczną — oznacza to, że zasób jest najpierw pobierany na maszynę `false` lokalną, a następnie przekazywany do obiektu blob platformy Azure. Jednak kopia synchroniczna jest obecnie dostępna tylko do kopiowania z jednego zasobu usługi Azure Storage do innego.

## <a name="copy-a-blob"></a>Kopiowanie obiektu blob

Inną funkcją, która jest unikatowo dostarczana przez bibliotekę ruchu danych, jest możliwość kopiowania z jednego zasobu usługi Azure Storage do innego.

Zmodyfikuj `TransferAzureBlobToAzureBlob` :

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, CopyMethod.ServiceSideAsyncCopy, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

W tym przykładzie ustawimy parametr logiczny w parametrze na , aby wskazać, że chcemy wykonać `TransferManager.CopyAsync` `false` kopię synchroniczną. Oznacza to, że zasób jest najpierw pobierany na naszą maszynę lokalną, a następnie przekazywany do obiektu blob platformy Azure. Opcja kopiowania synchronicznego jest doskonałym sposobem zapewnienia spójnej szybkości operacji kopiowania. Z kolei szybkość asynchronicznego kopiowania po stronie serwera zależy od dostępnej przepustowości sieci na serwerze, która może się zmieniać. Jednak kopiowanie synchroniczne może wygenerować dodatkowy koszt wychodzący w porównaniu z kopią asynchroniczną. Zalecanym podejściem jest użycie kopii synchronicznej na maszynie wirtualnej platformy Azure, która znajduje się w tym samym regionie co źródłowe konto magazynu, aby uniknąć kosztu wychodzącego.

Aplikacja do przemieszczania danych jest teraz ukończona. [Pełny przykład kodu jest dostępny w witrynie GitHub.](https://github.com/azure-samples/storage-dotnet-data-movement-library-app)

## <a name="next-steps"></a>Następne kroki

[Dokumentacja referencyjna biblioteki usługi Azure Storage Data Movement.](https://azure.github.io/azure-storage-net-data-movement)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
