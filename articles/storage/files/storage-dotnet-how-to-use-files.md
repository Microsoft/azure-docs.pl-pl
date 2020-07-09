---
title: Tworzenie oprogramowania dla usługi Azure Files przy użyciu platformy .NET | Microsoft Docs
description: Dowiedz się, jak opracować aplikacje platformy .NET i usługi korzystające z usługi Azure Files do przechowywania danych plików.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 10/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 44602c65a08f2e76fa017022f6137a18481f2edd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515372"
---
# <a name="develop-for-azure-files-with-net"></a>Tworzenie oprogramowania dla usługi Azure Files przy użyciu platformy .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

W tym samouczku przedstawiono podstawy korzystania z platformy .NET do tworzenia aplikacji, które korzystają z usługi [Azure Files](storage-files-introduction.md) do przechowywania danych plików. W tym samouczku przedstawiono prostą aplikację konsolową do wykonywania podstawowych akcji przy użyciu platformy .NET i Azure Files:

* Pobierz zawartość pliku.
* Ustaw maksymalny rozmiar lub *przydział* udziału plików.
* Utwórz sygnaturę dostępu współdzielonego (klucz SAS) dla pliku, który korzysta z przechowywanych zasad dostępu zdefiniowanych w udziale.
* Skopiowanie pliku do innego pliku w tym samym koncie magazynu.
* Skopiowanie pliku do obiektu blob w tym samym koncie magazynu.
* Skorzystaj z metryk usługi Azure Storage w celu rozwiązywania problemów.

Aby dowiedzieć się więcej na temat Azure Files, zobacz [co to jest Azure Files?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Opis interfejsów API platformy .NET

Usługa Azure Files oferuje dwa obszerne podejścia do aplikacji klienckich: blok komunikatów serwera (SMB) i interfejs REST. W programie .NET `System.IO` `WindowsAzure.Storage` interfejsy API i są abstrakcyjne.

Interfejs API | Kiedy stosować | Uwagi
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | Twoja aplikacja: <ul><li>Wymaga odczytu/zapisu plików przy użyciu protokołu SMB</li><li>Działa na urządzeniu, które ma dostęp do konta usługi Azure Files za pośrednictwem portu 445</li><li>Nie musi zarządzać żadnymi ustawieniami administracyjnymi udziału plików</li></ul> | We/wy plików wdrożonych przy użyciu Azure Files za pośrednictwem protokołu SMB zwykle jest taka sama jak we/wy z dowolnym sieciowym udziałem plików lub lokalnym urządzeniem magazynującym. Aby zapoznać się z wprowadzeniem do wielu funkcji platformy .NET, w tym plików we/wy, zobacz Samouczek dotyczący [aplikacji konsolowej](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter) .
[Microsoft. Azure. Storage. File](/dotnet/api/overview/azure/storage?view=azure-dotnet#version-11x) | Twoja aplikacja: <ul><li>Nie można uzyskać dostępu do Azure Files przy użyciu protokołu SMB na porcie 445 z powodu ograniczeń zapory lub usługodawcy internetowego</li><li>Wymaga funkcji administracyjnych, takich jak możliwość ustawiania przydziału udziału plików lub tworzenia sygnatury dostępu współdzielonego</li></ul> | W tym artykule przedstawiono sposób użycia `Microsoft.Azure.Storage.File` funkcji we/wy na plikach przy użyciu protokołu REST zamiast SMB i zarządzania udziałem plików.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Tworzenie aplikacji konsolowej i uzyskiwanie zestawu

W programie Visual Studio utwórz nową aplikację konsoli dla systemu Windows. Poniższe kroki pokazują, jak utworzyć aplikację konsolową w programie Visual Studio 2019. Procedura jest podobna w innych wersjach programu Visual Studio.

1. Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.
1. W obszarze **Utwórz nowy projekt**wybierz pozycję **aplikacja konsoli (.NET Framework)** dla języka C#, a następnie wybierz pozycję **dalej**.
1. W obszarze **Konfigurowanie nowego projektu**wprowadź nazwę aplikacji, a następnie wybierz pozycję **Utwórz**.

Wszystkie przykłady kodu w tym samouczku można dodać do `Main()` metody pliku aplikacji konsolowej `Program.cs` .

Biblioteka klienta usługi Azure Storage może być używana w dowolnym typie aplikacji .NET. Te typy obejmują usługę w chmurze lub aplikację sieci Web platformy Azure oraz aplikacje dla komputerów stacjonarnych i mobilnych. W tym przewodniku dla uproszczenia przedstawiono aplikację konsolową.

## <a name="use-nuget-to-install-the-required-packages"></a>Użycie pakietu NuGet w celu zainstalowania wymaganych pakietów

Zapoznaj się z tymi pakietami w projekcie, aby ukończyć ten samouczek:

* [Microsoft Azure Storage wspólna biblioteka dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  Ten pakiet zapewnia programistyczny dostęp do wspólnych zasobów na koncie magazynu.
* [Biblioteka obiektów BLOB Microsoft Azure Storage dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  Ten pakiet zapewnia programistyczny dostęp do zasobów obiektów BLOB na koncie magazynu.
* [Biblioteka plików Microsoft Azure Storage dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  Ten pakiet zapewnia programistyczny dostęp do zasobów plików na koncie magazynu.
* [Biblioteka Configuration Manager Microsoft Azure dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  Ten pakiet udostępnia klasę do analizowania parametrów połączenia w pliku konfiguracji, wszędzie tam, gdzie aplikacja jest uruchamiana.

Pakiet NuGet służy do pobrania obu pakietów. Wykonaj następujące kroki:

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.
1. W **Menedżerze pakietów NuGet**wybierz pozycję **Przeglądaj**. Następnie wyszukaj i wybierz pozycję **Microsoft. Azure. Storage. blob**, a następnie wybierz pozycję **Zainstaluj**.

   Ten krok powoduje zainstalowanie pakietu wraz z jego zależnościami.
1. Wyszukaj i Zainstaluj następujące pakiety:

   * **Microsoft. Azure. Storage. Common**
   * **Microsoft. Azure. Storage. File**
   * **Microsoft.Azure.ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Zapisz poświadczenia konta magazynu w pliku App.config

Następnie Zapisz poświadczenia w `App.config` pliku projektu. W **Eksplorator rozwiązań**kliknij dwukrotnie `App.config` plik i zmodyfikuj go tak, aby był podobny do poniższego przykładu. Zastąp ciąg `myaccount` nazwą konta magazynu i `mykey` kluczem konta magazynu.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> Najnowsza wersja emulatora magazynu Azure nie obsługuje usługi Azure Files. Aby można było pracować z usługą Azure Files, parametry połączenia muszą wskazywać konto usługi Azure Storage w chmurze.

## <a name="add-using-directives"></a>Dodawanie dyrektyw using

W **Eksplorator rozwiązań**Otwórz `Program.cs` plik i Dodaj następujące dyrektywy using na początku pliku.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Programowy dostęp do udziału plików

Następnie Dodaj poniższą zawartość do `Main()` metody po kodzie pokazanym powyżej, aby pobrać parametry połączenia. Ten kod pobiera odwołanie do utworzonego wcześniej pliku i wyświetla jego zawartość.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Aby zobaczyć dane wyjściowe, uruchom aplikację konsolową.

## <a name="set-the-maximum-size-for-a-file-share"></a>Ustawianie maksymalnego rozmiaru udziału plików

Począwszy od wersji 5. x biblioteki klienta usługi Azure Storage, można ustawić limit przydziału (maksymalny rozmiar) udziału plików. Można również sprawdzić, ile danych jest obecnie przechowywanych w udziale.

Ustawienie limitu przydziału udziału ogranicza łączny rozmiar plików przechowywanych w udziale. Jeśli łączny rozmiar plików w udziale przekracza limit przydziału ustawiony w udziale, klienci nie mogą zwiększyć rozmiaru istniejących plików. Klienci nie mogą tworzyć nowych plików, chyba że te pliki są puste.

W poniższym przykładzie pokazano, jak sprawdzić bieżące użycie udziału oraz jak ustawić limit przydziału w udziale.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generowanie sygnatury dostępu współdzielonego dla pliku lub udziału plików

Począwszy od wersji 5.x biblioteki klienta usługi Azure Storage, można wygenerować sygnaturę dostępu współdzielonego dla udziału plików lub dla pojedynczego pliku. Możesz również utworzyć przechowywane zasady dostępu w udziale plików, aby zarządzać sygnaturami dostępu współdzielonego. Zaleca się utworzenie zasad dostępu przechowywanego, ponieważ umożliwia odwoływanie skojarzeń zabezpieczeń w przypadku naruszenia zabezpieczeń.

Poniższy przykład tworzy zasady dostępu przechowywanego w udziale. W przykładzie zastosowano te zasady, aby zapewnić ograniczenia dla sygnatury dostępu współdzielonego dla pliku w udziale.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Aby uzyskać więcej informacji na temat tworzenia i używania sygnatur dostępu współdzielonego, zobacz [jak działa sygnatura dostępu współdzielonego](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works).

## <a name="copy-files"></a>Kopiowanie plików

Począwszy od wersji 5.x biblioteki klienta usługi Azure Storage, można kopiować pliki do innych plików, pliki do obiektów blob oraz obiekty blob do plików. W następnych sekcjach pokazano, jak programowo wykonać te operacje kopiowania.

Można również użyć AzCopy do kopiowania jednego pliku do innego lub kopiowania obiektu BLOB do pliku lub w inny sposób. Zobacz Rozpoczynanie [pracy z usługą AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> W przypadku kopiowania obiektu blob do pliku lub pliku do obiektu blob konieczne jest autoryzowanie dostępu do obiektu źródłowego za pomocą sygnatury dostępu współdzielonego, nawet jeśli kopiowanie odbywa się w ramach tego samego konta magazynu.
>

### <a name="copy-a-file-to-another-file"></a>Kopiowanie pliku do innego pliku

Poniższy przykładowy kod kopiuje plik do innego pliku w tym samym udziale. Ponieważ ta operacja kopiowania Kopiuje między plikami na tym samym koncie magazynu, można użyć uwierzytelniania klucza współużytkowanego do wykonania kopii.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

### <a name="copy-a-file-to-a-blob"></a>Kopiowanie pliku do obiektu blob

Poniższy przykładowy kod tworzy plik i kopiuje go do obiektu blob w ramach tego samego konta magazynu. Dla pliku źródłowego tworzona jest sygnatura dostępu współdzielonego, za pomocą której usługa autoryzuje dostęp do tego pliku podczas operacji kopiowania.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

W ten sam sposób można skopiować obiekt blob do pliku. Jeśli obiekt źródłowy jest obiektem blob, utwórz sygnaturę dostępu współdzielonego w celu autoryzacji dostępu do tego obiektu blob podczas operacji kopiowania.

## <a name="share-snapshots"></a>Migawki udziałów

Od wersji 8.5 biblioteki klienta usługi Azure Storage można tworzyć migawki udziałów. Można również wyświetlać listę migawek udziałów oraz je przeglądać i usuwać. Migawki udziałów są przeznaczone tylko do odczytu, nie można w ich obrębie wykonywać żadnych operacji zapisu.

### <a name="create-share-snapshots"></a>Tworzenie migawek udziałów

W poniższym przykładzie przedstawiono sposób tworzenia migawki udziału plików.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

### <a name="list-share-snapshots"></a>Wyświetlanie listy migawek udziałów

W poniższym przykładzie przedstawiono sposób wyświetlania listy migawek udziałów w udziale.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

### <a name="browse-files-and-directories-within-share-snapshots"></a>Przeglądanie plików i katalogów w obrębie migawek udziałów

W poniższym przykładzie przedstawiono sposób przeglądania plików i katalogów w ramach migawek udziałów.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>Wyświetlanie listy udziałów i udziałów migawek oraz przywracanie udziałów plików lub plików z migawek udziałów

Po utworzeniu migawki udziału plików można w przyszłości odzyskiwać poszczególne pliki lub cały udział plików.

Aby przywrócić plik z migawki udziału plików, można utworzyć zapytanie o migawki udziałów w udziale plików. Następnie można pobrać plik, który należy do określonej migawki udziału. Użyj tej wersji do bezpośredniego odczytywania i porównywania lub przywracania.

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

### <a name="delete-share-snapshots"></a>Usuwanie migawek udziałów

W poniższym przykładzie przedstawiono sposób usuwania migawki udziału plików.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Rozwiązywanie problemów z Azure Files przy użyciu metryk<a name="troubleshooting-azure-files-using-metrics"></a>

Funkcja analizy usługi Azure Storage obsługuje teraz metryki na potrzeby usługi Azure Files. Dane metryk umożliwiają śledzenie żądań i diagnozowanie problemów.

Można włączyć metryki dla Azure Files z [Azure Portal](https://portal.azure.com). Można również programowo włączyć metryki, wywołując operację ustawiania właściwości usługi plików za pomocą interfejsu API REST lub jednego z jego analogek w bibliotece klienta usługi Storage.

Poniższy przykładowy kod pokazuje, jak włączyć metryki dla usługi Azure Files za pomocą biblioteki klienta usługi Storage programu .NET.

Najpierw Dodaj następujące `using` dyrektywy do `Program.cs` pliku wraz z tymi, które zostały dodane powyżej:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Chociaż obiekty blob platformy Azure, tabele platformy Azure i kolejki platformy Azure używają typu współużytkowanego `ServiceProperties` w `Microsoft.Azure.Storage.Shared.Protocol` przestrzeni nazw, Azure Files używa własnego typu, `FileServiceProperties` typu w `Microsoft.Azure.Storage.File.Protocol` przestrzeni nazw. Należy jednak odwoływać się do obu przestrzeni nazw w kodzie, aby można było skompilować Poniższy kod.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Jeśli napotkasz jakiekolwiek problemy, możesz zapoznać się z tematem [rozwiązywania problemów Azure Files problemów w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Files, zobacz następujące zasoby:

### <a name="conceptual-articles-and-videos"></a>Artykuły koncepcyjne i filmy

* [Azure Files: a frictionless cloud SMB file system for Windows and Linux (Azure Files: płynnie działający system plików SMB w chmurze dla systemów Windows i Linux)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Używanie usługi Azure Files z systemem Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Narzędzia dostępne dla usługi Magazyn plików

* [Wprowadzenie do narzędzia AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Rozwiązywanie problemów z usługą Azure Files w systemie Windows](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Dokumentacja

* [Interfejsy API usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage)
* [Interfejs API REST usługi File (Plik)](/rest/api/storageservices/File-Service-REST-API)

### <a name="blog-posts"></a>Wpisy na blogach

* [Usługa Azure File Storage, teraz ogólnie dostępna](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File Storage](https://azure.microsoft.com/blog/inside-azure-file-storage/) (Za kulisami usługi Azure File Storage)
* [Wprowadzenie do usługi plików Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persisting connections to Microsoft Azure Files (Utrwalanie połączeń z plikami na platformie Microsoft Azure)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
