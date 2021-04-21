---
title: Tworzenie oprogramowania dla usługi Azure Files przy użyciu platformy .NET | Microsoft Docs
description: Dowiedz się, jak tworzyć aplikacje i usługi .NET, które używają Azure Files do przechowywania danych.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c00f001ae3cba9420a137a42f9f696619584d50
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817381"
---
# <a name="develop-for-azure-files-with-net"></a>Tworzenie oprogramowania dla usługi Azure Files przy użyciu platformy .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Poznaj podstawy tworzenia aplikacji .NET, które używają Azure Files [do](storage-files-introduction.md) przechowywania danych. W tym artykule pokazano, jak utworzyć prostą aplikację konsoli, aby wykonać następujące czynności za pomocą programu .NET i Azure Files:

- Pobierz zawartość pliku.
- Ustaw maksymalny rozmiar (limit przydziału) dla udziału plików.
- Utwórz sygnaturę dostępu współdzielonego dla pliku.
- Skopiowanie pliku do innego pliku w tym samym koncie magazynu.
- Skopiowanie pliku do obiektu blob w tym samym koncie magazynu.
- Utwórz migawkę udziału plików.
- Przywracanie pliku z migawki udziału.
- Użyj metryk usługi Azure Storage na potrzeby rozwiązywania problemów.

Aby dowiedzieć się więcej o Azure Files, zobacz [Co to jest Azure Files?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Opis interfejsów API platformy .NET

Usługa Azure Files oferuje dwa obszerne podejścia do aplikacji klienckich: blok komunikatów serwera (SMB) i interfejs REST. W programie .NET te `System.IO` podejścia są `Azure.Storage.Files.Shares` abstrakcją interfejsów API i .

Interfejs API | Kiedy stosować | Uwagi
----|-------------|------
[System.IO](/dotnet/api/system.io) | Twoja aplikacja: <ul><li>Musi odczytywać/zapisywać pliki przy użyciu funkcji SMB</li><li>Działa na urządzeniu, które ma dostęp do konta usługi Azure Files za pośrednictwem portu 445</li><li>Nie musi zarządzać żadnymi ustawieniami administracyjnymi udziału plików</li></ul> | We/Wy plików zaimplementowane Azure Files za pośrednictwem SMB jest zwykle takie samo jak we/wy z dowolnym sieciowym udziałem plików lub lokalnym urządzeniem magazynu. Wprowadzenie do wielu funkcji na platformie .NET, w tym we/wy plików, można znaleźć w [samouczku Aplikacja konsolowa.](/dotnet/csharp/tutorials/console-teleprompter)
[Azure.Storage.Files.Shares](/dotnet/api/azure.storage.files.shares) | Twoja aplikacja: <ul><li>Nie można uzyskać dostępu do Azure Files przy użyciu SMB na porcie 445 ze względu na ograniczenia zapory lub isp</li><li>Wymaga funkcji administracyjnych, takich jak możliwość ustawiania przydziału udziału plików lub tworzenia sygnatury dostępu współdzielonego</li></ul> | W tym artykule pokazano, jak używać funkcji dla we/wy plików przy użyciu rest zamiast SMB i zarządzania `Azure.Storage.Files.Shares` udziałem plików.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Tworzenie aplikacji konsolowej i uzyskiwanie zestawu

Biblioteki klienta Azure Files można używać w aplikacji .NET dowolnego typu. Należą do nich aplikacje w chmurze, aplikacje internetowe, klasyczne i mobilne platformy Azure. W tym przewodniku dla uproszczenia utworzymy aplikację konsolowa.

W programie Visual Studio utwórz nową aplikację konsoli dla systemu Windows. W poniższych krokach przedstawiono sposób tworzenia aplikacji konsolowej w programie Visual Studio 2019. Procedura jest podobna w innych wersjach programu Visual Studio.

1. Uruchom Visual Studio i wybierz **pozycję Utwórz nowy projekt.**
1. W **oknie Tworzenie nowego projektu** wybierz pozycję Aplikacja konsoli **(.NET Framework)** dla języka C#, a następnie wybierz pozycję **Dalej.**
1. Na **stronie Konfigurowanie nowego projektu** wprowadź nazwę aplikacji, a następnie wybierz pozycję **Utwórz.**

Dodaj wszystkie przykłady kodu w tym artykule do `Program` klasy w *pliku Program.cs.*

## <a name="use-nuget-to-install-the-required-packages"></a>Użycie pakietu NuGet w celu zainstalowania wymaganych pakietów

Zapoznaj się z tymi pakietami w projekcie:

# <a name="azure-net-sdk-v12"></a>[Zestaw Azure \. NET SDK w wersji 12](#tab/dotnet)

- [Podstawowa biblioteka platformy Azure dla platformy .NET:](https://www.nuget.org/packages/Azure.Core/)ten pakiet jest implementacją potoku klienta platformy Azure.
- [Azure Storage Blob klienta dla platformy .NET:](https://www.nuget.org/packages/Azure.Storage.Blobs/)ten pakiet zapewnia programowy dostęp do zasobów obiektów blob na koncie magazynu.
- [Biblioteka klienta usługi Azure Storage Files dla platformy .NET:](https://www.nuget.org/packages/Azure.Storage.Files.Shares/)ten pakiet zapewnia programowy dostęp do zasobów plików na koncie magazynu.
- Biblioteka Menedżer konfiguracji dla programu [.NET:](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/)ten pakiet zawiera klasę przechowującą i pobierania wartości w pliku konfiguracji.

Aby uzyskać pakiety, możesz użyć pakietu NuGet. Wykonaj następujące kroki:

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet.**
1. Na **stronie Menedżer pakietów NuGet** wybierz pozycję **Przeglądaj.** Następnie wyszukaj i wybierz pozycję **Azure.Core,** a następnie wybierz pozycję **Zainstaluj.**

   Ten krok instaluje pakiet i jego zależności.

1. Wyszukaj i zainstaluj następujące pakiety:

   - **Azure.Storage.Blobs**
   - **Azure.Storage.Files.Shares**
   - **System.Configuration.ConfigurationManager**

# <a name="azure-net-sdk-v11"></a>[Zestaw Azure \. NET SDK w wersji 11](#tab/dotnetv11)

- [Microsoft Azure Storage biblioteki dla programu .NET:](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)ten pakiet zapewnia programowy dostęp do typowych zasobów na koncie magazynu.
- [Microsoft Azure Storage blob dla platformy .NET:](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)ten pakiet zapewnia programowy dostęp do zasobów obiektów blob na koncie magazynu.
- [Microsoft Azure Storage plików dla programu .NET:](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)ten pakiet zapewnia programowy dostęp do zasobów plików na koncie magazynu.
- [Microsoft Azure Menedżer konfiguracji biblioteki dla programu .NET:](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)ten pakiet zawiera klasę do analizowania parametrów połączenia w pliku konfiguracji, niezależnie od tego, gdzie jest uruchomiona aplikacja.

Aby uzyskać pakiety, możesz użyć pakietu NuGet. Wykonaj następujące kroki:

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet.**
1. W **programie NuGet Menedżer pakietów** wybierz pozycję **Przeglądaj.** Następnie wyszukaj i wybierz pozycję **Microsoft.Azure.Storage.Blob,** a następnie wybierz pozycję **Zainstaluj.**

   Ten krok instaluje pakiet i jego zależności.
1. Wyszukaj i zainstaluj następujące pakiety:

   - **Microsoft.Azure.Storage.Common**
   - **Microsoft.Azure.Storage.File**
   - **Microsoft.Azure.ConfigurationManager**

---

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Zapisywanie poświadczeń konta magazynu w App.config magazynu

Następnie zapisz poświadczenia w plikuApp.config *projektu.* W **Eksplorator rozwiązań** kliknij dwukrotnie plik i edytuj go tak, aby `App.config` był podobny do poniższego przykładu.

# <a name="azure-net-sdk-v12"></a>[Zestaw Azure \. NET SDK w wersji 12](#tab/dotnet)

Zastąp `myaccount` element nazwą konta magazynu, a element `mykey` kluczem konta magazynu.

:::code language="xml" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/app.config" highlight="5,6,7":::

# <a name="azure-net-sdk-v11"></a>[Zestaw Azure \. NET SDK w wersji 11](#tab/dotnetv11)

Zastąp `myaccount` element nazwą konta magazynu, a element `StorageAccountKeyEndingIn==` kluczem konta magazynu.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString"
      value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
  </appSettings>
</configuration>
```

---

> [!NOTE]
> Emulator magazynu Azurite nie obsługuje obecnie Azure Files. Aby można było pracować z kontem usługi Azure Storage w chmurze, w celu współpracy z Azure Files.

## <a name="add-using-directives"></a>Dodawanie dyrektyw using

W **Eksplorator rozwiązań** pliku otwórz *plik Program.cs* i dodaj na początku pliku następujące dyrektywy using.

# <a name="azure-net-sdk-v12"></a>[Zestaw Azure \. NET SDK w wersji 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UsingStatements":::

# <a name="azure-net-sdk-v11"></a>[Zestaw Azure \. NET SDK w wersji 11](#tab/dotnetv11)

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

---

## <a name="access-the-file-share-programmatically"></a>Programowy dostęp do udziału plików

W pliku *Program.cs* dodaj następujący kod, aby programowo uzyskać dostęp do udziału plików.

# <a name="azure-net-sdk-v12"></a>[Zestaw Azure \. NET SDK w wersji 12](#tab/dotnet)

Następująca metoda tworzy udział plików, jeśli jeszcze nie istnieje. Metoda rozpoczyna się od utworzenia [obiektu ShareClient](/dotnet/api/azure.storage.files.shares.shareclient) na pomocą parametrów połączenia. Następnie przykład próbuje pobrać utworzony wcześniej plik. Wywołaj tę metodę z `Main()` metody .

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShare":::

# <a name="azure-net-sdk-v11"></a>[Zestaw Azure \. NET SDK w wersji 11](#tab/dotnetv11)

Następnie dodaj następującą zawartość do metody po kodzie przedstawionym `Main()` powyżej, aby pobrać ciąg połączenia. Ten kod pobiera odwołanie do utworzonego wcześniej pliku i wyprowadza jego zawartość.

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

---

## <a name="set-the-maximum-size-for-a-file-share"></a>Ustawianie maksymalnego rozmiaru udziału plików

Począwszy od wersji 5.x Azure Files klienta programu , można ustawić limit przydziału (maksymalny rozmiar) udziału plików. Można również sprawdzić, ile danych jest obecnie przechowywanych w udziale.

Ustawienie limitu przydziału udziału ogranicza całkowity rozmiar plików przechowywanych w tym udziału. Jeśli całkowity rozmiar plików w udziału przekracza limit przydziału, klienci nie mogą zwiększyć rozmiaru istniejących plików. Klienci nie mogą również tworzyć nowych plików, chyba że te pliki są puste.

W poniższym przykładzie pokazano, jak sprawdzić bieżące użycie udziału oraz jak ustawić limit przydziału w udziale.

# <a name="azure-net-sdk-v12"></a>[Zestaw Azure \. NET SDK w wersji 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_SetMaxShareSize":::

# <a name="azure-net-sdk-v11"></a>[Zestaw Azure \. NET SDK w wersji 11](#tab/dotnetv11)

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

---

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generowanie sygnatury dostępu współdzielonego dla pliku lub udziału plików

Począwszy od wersji 5.x biblioteki klienta Azure Files, można wygenerować sygnaturę dostępu współdzielonego (SAS) dla udziału plików lub dla pojedynczego pliku.

# <a name="azure-net-sdk-v12"></a>[Zestaw Azure \. NET SDK w wersji 12](#tab/dotnet)

Następująca przykładowa metoda zwraca sygnaturę dostępu współdzielonego dla pliku w określonym udziału.

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_GetFileSasUri":::

# <a name="azure-net-sdk-v11"></a>[Zestaw Azure \. NET SDK w wersji 11](#tab/dotnetv11)

Można również utworzyć zapisane zasady dostępu w udziałach plików, aby zarządzać sygnaturami dostępu współdzielonych. Zalecamy utworzenie zapisanych zasad dostępu, ponieważ umożliwiają one odwołanie sygnatury dostępu współdzielonego w przypadku naruszenia zabezpieczeń. Poniższy przykład tworzy zapisane zasady dostępu w udziału. W przykładzie te zasady są używane do zapewnienia ograniczeń sygnatury dostępu współdzielonego dla pliku w udziałach.

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

---

Aby uzyskać więcej informacji na temat tworzenia i używania sygnatur dostępu współdzielonych, zobacz [Jak działa sygnatura dostępu współdzielona](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works).

## <a name="copy-files"></a>Kopiowanie plików

Począwszy od wersji 5.x biblioteki Azure Files klienta programu , można skopiować plik do innego pliku, plik do obiektu blob lub obiekt blob do pliku.

Możesz również użyć programu AzCopy, aby skopiować jeden plik do innego lub skopiować obiekt blob do pliku lub na drugi. Zobacz [Wprowadzenie do programu AzCopy.](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

> [!NOTE]
> W przypadku kopiowania obiektu blob do pliku lub pliku do obiektu blob konieczne jest autoryzowanie dostępu do obiektu źródłowego za pomocą sygnatury dostępu współdzielonego, nawet jeśli kopiowanie odbywa się w ramach tego samego konta magazynu.

### <a name="copy-a-file-to-another-file"></a>Kopiowanie pliku do innego pliku

Poniższy przykładowy kod kopiuje plik do innego pliku w tym samym udziale. Możesz użyć [uwierzytelniania klucza wspólnego,](/rest/api/storageservices/authorize-with-shared-key) aby wykonać kopię, ponieważ ta operacja kopiuje pliki w ramach tego samego konta magazynu.

# <a name="azure-net-sdk-v12"></a>[Zestaw Azure \. NET SDK w wersji 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFile":::

# <a name="azure-net-sdk-v11"></a>[Zestaw Azure \. NET SDK w wersji 11](#tab/dotnetv11)

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

---

### <a name="copy-a-file-to-a-blob"></a>Kopiowanie pliku do obiektu blob

Poniższy przykładowy kod tworzy plik i kopiuje go do obiektu blob w ramach tego samego konta magazynu. Dla pliku źródłowego tworzona jest sygnatura dostępu współdzielonego, za pomocą której usługa autoryzuje dostęp do tego pliku podczas operacji kopiowania.

# <a name="azure-net-sdk-v12"></a>[Zestaw Azure \. NET SDK w wersji 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFileToBlob":::

# <a name="azure-net-sdk-v11"></a>[Zestaw Azure \. NET SDK w wersji 11](#tab/dotnetv11)

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

---

W ten sam sposób można skopiować obiekt blob do pliku. Jeśli obiekt źródłowy jest obiektem blob, utwórz sygnaturę dostępu współdzielonego w celu autoryzacji dostępu do tego obiektu blob podczas operacji kopiowania.

## <a name="share-snapshots"></a>Migawki udziałów

Począwszy od wersji 8.5 Azure Files klienta programu , można utworzyć migawkę udziału. Można również wyświetlać listę migawek udziałów oraz je przeglądać i usuwać. Po utworzeniu migawki udziałów są tylko do odczytu.

### <a name="create-share-snapshots"></a>Tworzenie migawek udziałów

W poniższym przykładzie przedstawiono sposób tworzenia migawki udziału plików.

# <a name="azure-net-sdk-v12"></a>[Zestaw Azure \. NET SDK w wersji 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShareSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Zestaw Azure \. NET SDK w wersji 11](#tab/dotnetv11)

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

---

### <a name="list-share-snapshots"></a>Wyświetlanie listy migawek udziałów

Poniższy przykład zawiera listę migawek w udziału.

# <a name="azure-net-sdk-v12"></a>[Zestaw Azure \. NET SDK w wersji 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListShareSnapshots":::

# <a name="azure-net-sdk-v11"></a>[Zestaw Azure \. NET SDK w wersji 11](#tab/dotnetv11)

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

---

### <a name="list-files-and-directories-within-share-snapshots"></a>Tworzenie listy plików i katalogów w ramach migawek udziałów

Poniższy przykład umożliwia przeglądanie plików i katalogów w ramach migawek udziałów.

# <a name="azure-net-sdk-v12"></a>[Zestaw Azure \. NET SDK w wersji 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListSnapshotContents":::

# <a name="azure-net-sdk-v11"></a>[Zestaw Azure \. NET SDK w wersji 11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

---

### <a name="restore-file-shares-or-files-from-share-snapshots"></a>Przywracanie udziałów plików lub plików z migawek udziałów

Tworzenie migawki udziału plików umożliwia odzyskanie poszczególnych plików lub całego udziału plików.

Aby przywrócić plik z migawki udziału plików, można utworzyć zapytanie o migawki udziałów w udziale plików. Następnie można pobrać plik, który należy do określonej migawki udziału. Użyj tej wersji, aby bezpośrednio odczytać lub przywrócić plik.

# <a name="azure-net-sdk-v12"></a>[Zestaw Azure \. NET SDK w wersji 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_RestoreFileFromSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Zestaw Azure \. NET SDK w wersji 11](#tab/dotnetv11)

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

---

### <a name="delete-share-snapshots"></a>Usuwanie migawek udziałów

W poniższym przykładzie przedstawiono sposób usuwania migawki udziału plików.

# <a name="azure-net-sdk-v12"></a>[Zestaw Azure \. NET SDK w wersji 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_DeleteSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Zestaw Azure \. NET SDK w wersji 11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

---

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Rozwiązywanie Azure Files przy użyciu metryk<a name="troubleshooting-azure-files-using-metrics"></a>

Azure Storage Analytics obsługuje metryki dla Azure Files. Dane metryk umożliwiają śledzenie żądań i diagnozowanie problemów.

Metryki dla Azure Files można włączyć z [Azure Portal](https://portal.azure.com). Metryki można również włączyć programowo, wywołując operację Ustawianie właściwości usługi plików przy użyciu interfejsu API REST lub jedną z jego analogi w Azure Files klienta. [](/rest/api/storageservices/set-file-service-properties)

Poniższy przykład kodu przedstawia sposób użycia biblioteki klienta .NET w celu włączenia metryk dla Azure Files.

# <a name="azure-net-sdk-v12"></a>[Zestaw Azure \. NET SDK w wersji 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UseMetrics":::

# <a name="azure-net-sdk-v11"></a>[Zestaw Azure \. NET SDK w wersji 11](#tab/dotnetv11)

Najpierw dodaj następujące dyrektywy `using` do pliku *Program.cs* wraz z dodanymi powyżej dyrektywami:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Chociaż usługi Azure Blobs, Azure Tables i Azure Queues używają typu udostępnionego w przestrzeni nazw , Azure Files używa własnego typu , typu `ServiceProperties` `Microsoft.Azure.Storage.Shared.Protocol` w przestrzeni `FileServiceProperties` `Microsoft.Azure.Storage.File.Protocol` nazw. Jednak w celu skompilowania poniższego kodu należy odwołać się do obu przestrzeni nazw z kodu.

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

---

Jeśli napotkasz jakiekolwiek problemy, możesz zapoznać się z tematem [Rozwiązywanie problemów Azure Files w systemie Windows.](storage-troubleshoot-windows-file-connection-problems.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na Azure Files, zobacz następujące zasoby:

### <a name="conceptual-articles-and-videos"></a>Artykuły koncepcyjne i filmy

- [Azure Files: a frictionless cloud SMB file system for Windows and Linux (Azure Files: płynnie działający system plików SMB w chmurze dla systemów Windows i Linux)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Korzystanie z usługi Azure Files w systemie Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Narzędzia dostępne dla usługi Magazyn plików

- [Wprowadzenie do narzędzia AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
- [Rozwiązywanie problemów z usługą Azure Files w systemie Windows](./storage-troubleshoot-windows-file-connection-problems.md)

### <a name="reference"></a>Odwołanie

- [Interfejsy API usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage)
- [Interfejs API REST usługi File (Plik)](/rest/api/storageservices/File-Service-REST-API)