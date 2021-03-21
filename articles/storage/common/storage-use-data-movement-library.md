---
title: Transferowanie danych za pomocą biblioteki przenoszenia danych dla platformy .NET
titleSuffix: Azure Storage
description: Użyj biblioteki przenoszenia danych do przenoszenia lub kopiowania danych do lub z obiektów blob i plików. Kopiowanie danych do usługi Azure Storage z plików lokalnych lub kopiowanie danych z lub między kontami magazynu. Łatwo Migruj dane do usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 8ec35a651d4d3ef9e0877463329a654bc7491f4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023418"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Przenoszenie danych za pomocą biblioteki przenoszenia danych

Biblioteka przenoszenia danych usługi Azure Storage jest międzyplatformową biblioteką typu open source, która została zaprojektowana w celu zapewnienia wysokiej wydajności przekazywania, pobierania i kopiowania obiektów blob i plików. Biblioteka przenoszenia danych oferuje wygodne metody, które nie są dostępne w bibliotece klienta usługi Azure Storage dla platformy .NET. Te metody zapewniają możliwość ustawienia liczby operacji równoległych, śledzenia postępu transferu, łatwego wznowienia anulowanego transferu i wielu innych.

Ta biblioteka korzysta również z platformy .NET Core, co oznacza, że można jej używać podczas kompilowania aplikacji .NET dla systemów Windows, Linux i macOS. Aby dowiedzieć się więcej na temat platformy .NET Core, zapoznaj się z [dokumentacją programu .NET Core](https://dotnet.github.io/). Ta biblioteka działa również dla tradycyjnych aplikacji .NET Framework dla systemu Windows.

W tym dokumencie przedstawiono sposób tworzenia aplikacji konsolowej .NET Core, która działa w systemach Windows, Linux i macOS, oraz wykonuje następujące scenariusze:

- Przekaż pliki i katalogi do Blob Storage.
- Zdefiniuj liczbę operacji równoległych podczas przesyłania danych.
- Śledź postęp transferu danych.
- Wznów anulowany transfer danych.
- Skopiuj plik z adresu URL do Blob Storage.
- Kopiuj z Blob Storage do Blob Storage.

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio Code](https://code.visualstudio.com/)
- [Konto usługi Azure Storage](storage-account-create.md)

## <a name="setup"></a>Konfiguracja

1. Zapoznaj się z [przewodnikiem instalacji programu .NET Core](https://www.microsoft.com/net/core) , aby zainstalować program .NET Core. Podczas wybierania środowiska wybierz opcję wiersza polecenia.
2. W wierszu polecenia Utwórz katalog dla projektu. Przejdź do tego katalogu, a następnie wpisz polecenie, `dotnet new console -o <sample-project-name>` Aby utworzyć projekt konsoli języka C#.
3. Otwórz ten katalog w Visual Studio Code. Ten krok można szybko wykonać przy użyciu wiersza polecenia, wpisując `code .` w systemie Windows.
4. Zainstaluj [rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) z witryny Visual Studio Code Marketplace. Uruchom ponownie Visual Studio Code.
5. W tym momencie powinny pojawić się dwa wiersze. Jeden z nich służy do dodawania "wymaganych zasobów do kompilowania i debugowania". Kliknij przycisk "tak". Inny monit jest przeznaczony do przywracania nierozwiązanych zależności. Kliknij pozycję "Przywróć".
6. Zmodyfikuj `launch.json` w obszarze, `.vscode` Aby używać terminalu zewnętrznego jako konsoli programu. To ustawienie powinno być odczytane jako `"console": "externalTerminal"`
7. Visual Studio Code umożliwia debugowanie aplikacji .NET Core. Naciśnij klawisz, `F5` Aby uruchomić aplikację, i sprawdź, czy Twoja konfiguracja działa. Powinna zostać wyświetlona wartość "Hello world!" wydrukowany w konsoli programu.

## <a name="add-the-data-movement-library-to-your-project"></a>Dodawanie biblioteki przenoszenia danych do projektu

1. Dodaj najnowszą wersję biblioteki przenoszenia danych do `dependencies` sekcji `<project-name>.csproj` pliku. W momencie zapisu ta wersja będzie `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Monit powinien zostać wyświetlony w celu przywrócenia projektu. Kliknij przycisk "Przywróć". Możesz również przywrócić projekt z wiersza polecenia, wpisując polecenie `dotnet restore` w katalogu głównym katalogu projektu.

Modyfikuj `<project-name>.csproj` :

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

Pierwszym krokiem jest skonfigurowanie "szkieletu" kodu naszej aplikacji. Ten kod poprosi nas o nazwę konta magazynu i klucz konta, a następnie używa tych poświadczeń do utworzenia `CloudStorageAccount` obiektu. Ten obiekt jest używany do współpracy z kontem magazynu we wszystkich scenariuszach transferu. Kod również poprosi nas o wybranie typu operacji transferu, która ma zostać wykonana.

Modyfikuj `Program.cs` :

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

## <a name="upload-a-local-file-to-a-blob"></a>Przekaż plik lokalny do obiektu BLOB

Dodaj metody `GetSourcePath` i `GetBlob` do `Program.cs` :

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

Zmodyfikuj `TransferLocalFileToAzureBlob` metodę:

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

Ten kod poprosi nas o ścieżkę do pliku lokalnego, nazwę nowego lub istniejącego kontenera oraz nazwę nowego obiektu BLOB. `TransferManager.UploadAsync`Metoda wykonuje przekazywanie przy użyciu tych informacji.

Naciśnij klawisz `F5` , aby uruchomić aplikację. Możesz sprawdzić, czy nastąpiło przekazanie, wyświetlając konto magazynu za pomocą [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/).

## <a name="set-the-number-of-parallel-operations"></a>Ustaw liczbę operacji równoległych

Jedną z funkcji oferowanej przez bibliotekę przenoszenia danych jest możliwość ustawienia liczby operacji równoległych w celu zwiększenia przepływności transferu danych. Domyślnie Biblioteka przenoszenia danych ustawia liczbę operacji równoległych na 8 * liczbę rdzeni na komputerze.

Należy pamiętać, że wiele operacji równoległych w środowisku o niskiej przepustowości może obciążyć połączenie sieciowe i w rzeczywistości uniemożliwić wykonywanie operacji w pełni. Należy eksperymentować z tym ustawieniem, aby określić, co najlepiej sprawdza się w oparciu o dostępną przepustowość sieci.

Dodajmy kod, który umożliwi nam ustawienie liczby operacji równoległych. Dodajmy również kod, który wydłuża czas trwania transferu.

Dodaj `SetNumberOfParallelOperations` metodę do `Program.cs` :

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Zmodyfikuj `ExecuteChoice` metodę, aby użyć `SetNumberOfParallelOperations` :

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

Zmodyfikuj `TransferLocalFileToAzureBlob` metodę, aby użyć czasomierza:

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

## <a name="track-transfer-progress"></a>Postęp przenoszenia śledzenia

Znajomość czasu, przez jaki zajęło transfer danych, jest pomocna. Jednak możliwość sprawdzenia postępu transferu *w trakcie* operacji transferu będzie jeszcze lepsza. Aby zrealizować ten scenariusz, musimy utworzyć `TransferContext` obiekt. `TransferContext`Obiekt znajduje się w dwóch formach: `SingleTransferContext` i `DirectoryTransferContext` . Dawniej jest przeniesienie pojedynczego pliku, a drugi jest przeznaczony do przenoszenia katalogu plików.

Dodaj metody `GetSingleTransferContext` i `GetDirectoryTransferContext` do `Program.cs` :

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

Zmodyfikuj `TransferLocalFileToAzureBlob` metodę, aby użyć `GetSingleTransferContext` :

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

## <a name="resume-a-canceled-transfer"></a>Wznów anulowany transfer

Kolejną wygodną funkcją oferowaną przez bibliotekę przenoszenia danych jest możliwość wznowienia anulowanego transferu. Dodajmy kod, który pozwala na tymczasowe anulowanie transferu przez wpisanie `c` , a następnie wznowienie transferu 3 sekund później.

Modyfikuj `TransferLocalFileToAzureBlob` :

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

Do tej pory nasza `checkpoint` wartość zawsze jest ustawiona na `null` . Teraz, jeśli anulujesz transfer, pobieramy ostatni punkt kontrolny naszego transferu, a następnie użyjemy tego nowego punktu kontrolnego w naszym kontekście transferu.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Transferowanie katalogu lokalnego do magazynu obiektów BLOB

Disappointing, jeśli biblioteka przenoszenia danych może przesyłać tylko jeden plik jednocześnie. Na szczęście, nie jest to przypadek. Biblioteka przenoszenia danych umożliwia Transferowanie katalogów plików i wszystkich podkatalogów. Dodajmy kod, który umożliwi nam wykonywanie tych czynności.

Najpierw Dodaj metodę `GetBlobDirectory` do `Program.cs` :

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

Istnieje kilka różnic między tą metodą i metodą przekazywania pojedynczego pliku. Teraz używamy `TransferManager.UploadDirectoryAsync` i metody, która została `getDirectoryTransferContext` utworzona wcześniej. Ponadto udostępniamy teraz `options` wartość naszej operacji przekazywania, która pozwala nam wskazać, że chcemy uwzględnić podkatalogi w naszym przekazywaniu.

## <a name="copy-a-file-from-url-to-a-blob"></a>Kopiowanie pliku z adresu URL do obiektu BLOB

Teraz Dodajmy kod, który umożliwi nam skopiowanie pliku z adresu URL do obiektu blob platformy Azure.

Modyfikuj `TransferUrlToAzureBlob` :

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

Jednym ważnym przypadkiem użycia tej funkcji jest przeniesienie danych z innej usługi w chmurze (np. AWS) na platformę Azure. O ile masz adres URL, który zapewnia dostęp do zasobu, możesz łatwo przenieść ten zasób do obiektów blob platformy Azure przy użyciu `TransferManager.CopyAsync` metody. W tej metodzie wprowadzono również nowy parametr Boolean. Ustawienie tego parametru na `true` wskazuje, że chcemy wykonać asynchroniczną kopię po stronie serwera. Ustawienie tego parametru w taki sposób, aby `false` wskazywał kopię synchroniczną — oznacza, że zasób jest najpierw pobierany na komputer lokalny, a następnie przekazywany do obiektu blob platformy Azure. Jednak kopia synchroniczna jest obecnie dostępna tylko do kopiowania z jednego zasobu magazynu platformy Azure do innego.

## <a name="copy-a-blob"></a>Kopiowanie obiektu BLOB

Inną funkcją, która jest unikatowa w bibliotece przenoszenia danych, jest możliwość kopiowania z jednego zasobu magazynu platformy Azure do innego.

Modyfikuj `TransferAzureBlobToAzureBlob` :

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

W tym przykładzie ustawimy parametr Boolean w na tak `TransferManager.CopyAsync` , `false` aby wskazywał, że chcemy wykonać kopię synchroniczną. Oznacza to, że zasób jest najpierw pobierany na komputer lokalny, a następnie przekazywany do obiektu blob platformy Azure. Opcja kopiowania synchronicznego to doskonały sposób, aby zapewnić, że operacja kopiowania ma spójną szybkość. Z kolei szybkość asynchronicznej kopiowania po stronie serwera jest zależna od dostępnej przepustowości sieci na serwerze, która może się różnić. Jednak kopia synchroniczna może generować dodatkowe koszty ruchu wychodzącego w porównaniu do kopii asynchronicznej. Zalecanym podejściem jest użycie synchronicznej kopii na maszynie wirtualnej platformy Azure, która znajduje się w tym samym regionie co źródłowe konto magazynu, aby uniknąć kosztu wychodzącego.

Aplikacja przenoszenia danych została ukończona. [Pełny przykładowy kod jest dostępny w serwisie GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Następne kroki

[Dokumentacja biblioteki przenoszenia danych usługi Azure Storage](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
