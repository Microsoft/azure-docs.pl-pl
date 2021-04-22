---
title: 'Szybki start: biblioteka klienta usługi Azure Blob Storage dla platformy .NET'
description: W tym przewodniku Szybki start dowiesz się, jak za pomocą biblioteki klienta usługi Azure Blob Storage dla platformy .NET utworzyć kontener i obiekt blob w magazynie obiektów blob (obiekt). Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać listę wszystkich obiektów blob w kontenerze.
author: twooley
ms.author: twooley
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 631b01dadacf81d66c3f42dc1401d2cf492316b8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869243"
---
# <a name="quickstart-azure-blob-storage-client-library-v11-for-net"></a>Szybki start: biblioteka klienta usługi Azure Blob Storage w wersji 11 dla platformy .NET

Wprowadzenie do biblioteki Azure Blob Storage klienta w wersji 11 dla programu .NET. Azure Blob Storage to rozwiązanie firmy Microsoft do magazynowania obiektów dla chmury. Wykonaj kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Magazyn obiektów blob jest zoptymalizowany pod kątem przechowywania olbrzymich ilości danych bez struktury.

> [!NOTE]
> W tym przewodniku Szybki start jest używana starsza wersja biblioteki klienta usługi Azure Blob Storage. Aby rozpocząć pracę z najnowszą wersją, zobacz Szybki start: biblioteka klienta usługi [Azure Blob Storage w wersji 12 dla platformy .NET.](storage-quickstart-blobs-dotnet.md)

Użyj biblioteki Azure Blob Storage klienta dla programu .NET, aby:

* Tworzenie kontenera
* Ustawianie uprawnień dla kontenera
* Tworzenie obiektu blob w usłudze Azure Storage
* Pobieranie obiektu blob na komputer lokalny
* Lista wszystkich obiektów blob w kontenerze
* Usuwanie kontenera

Dodatkowe zasoby:

* [Dokumentacja referencyjna interfejsu API](/dotnet/api/overview/azure/storage)
* [Kod źródłowy biblioteki](https://github.com/Azure/azure-storage-net/tree/master/Blob)
* [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)
* [Samples](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/)
* Konto usługi Azure Storage [— tworzenie konta magazynu](../common/storage-account-create.md)
* Bieżące [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) dla twojego systemu operacyjnego. Pamiętaj, aby pobrać zestaw SDK, a nie środowisko uruchomieniowe.

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji odpowiedzialności za przygotowanie projektu do pracy z biblioteką Azure Blob Storage klienta dla programu .NET.

### <a name="create-the-project"></a>Tworzenie projektu

Najpierw utwórz aplikację platformy .NET Core o nazwie *blob-quickstart.*

1. W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj polecenia , aby utworzyć nową aplikację konsolową o nazwie `dotnet new` *blob-quickstart.* To polecenie tworzy prosty projekt języka C# "Hello world" z pojedynczym plikiem źródłowym: *Program.cs.*

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Przejdź do nowo utworzonego *folderu blob-quickstart* i skompilowaj aplikację, aby sprawdzić, czy wszystko jest dobrze.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

Oczekiwane dane wyjściowe z kompilacji powinny wyglądać podobnie do tych:

```output
C:\QuickStarts\blob-quickstart> dotnet build
Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 44.31 ms for C:\QuickStarts\blob-quickstart\blob-quickstart.csproj.
  blob-quickstart -> C:\QuickStarts\blob-quickstart\bin\Debug\netcoreapp2.1\blob-quickstart.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:03.08
```

### <a name="install-the-package"></a>Instalowanie pakietu

W katalogu aplikacji zainstaluj bibliotekę klienta Azure Blob Storage pakietu .NET przy użyciu `dotnet add package` polecenia .

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwórz plik *Program.cs* w edytorze
2. Usuwanie `Console.WriteLine` instrukcji
3. Dodawanie `using` dyrektyw
4. Tworzenie `ProcessAsync` metody, w której będzie znajdować się główny kod przykładu
5. Asynchroniczne wywołanie `ProcessAsync` metody z metody `Main`

Oto kod:

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

namespace blob_quickstart
{
    class Program
    {
        public static async Task Main()
        {
            Console.WriteLine("Azure Blob Storage - .NET quickstart sample\n");

            await ProcessAsync();

            Console.WriteLine("Press any key to exit the sample application.");
            Console.ReadLine();
        }

        private static async Task ProcessAsync()
        {
        }
    }
}
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiowanie poświadczeń z witryny Azure Portal

Gdy przykładowa aplikacja wykonuje żądanie do usługi Azure Storage, musi być autoryzowana. Aby autoryzować żądanie, dodaj poświadczenia konta magazynu do aplikacji jako ciąg połączenia. Wyświetl poświadczenia konta magazynu, wykonując następujące czynności:

1. Przejdź do [Azure Portal](https://portal.azure.com).
2. Odszukaj konto magazynu.
3. W sekcji **Ustawienia** omówienia kont magazynu wybierz pozycję **Klucze dostępu**. W tym miejscu możesz przeglądać klucze dostępu do konta oraz pełne parametry połączenia dla każdego klucza.
4. Znajdź wartość **Parametry połączenia** w obszarze **key1** i wybierz przycisk **Kopiuj**, aby skopiować parametry połączenia. W następnym kroku dodasz wartość parametrów połączenia do zmiennej środowiskowej.

    ![Zrzut ekranu pokazujący sposób kopiowania parametrów połączenia z witryny Azure Portal](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

Po skopiowaniu parametrów połączenia zapisz je w nowej zmiennej środowiskowej na komputerze, na którym uruchomiona jest aplikacja. Aby ustawić zmienną środowiskową, otwórz okno konsoli i postępuj zgodnie z instrukcjami dla systemu operacyjnego. Zastąp `<yourconnectionstring>` ciąg rzeczywistymi ciągami połączenia.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

Po dodaniu zmiennej środowiskowej w systemie Windows należy uruchomić nowe wystąpienie okna polecenia.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

Po dodaniu zmiennej środowiskowej uruchom ponownie wszystkie uruchomione programy, które będą potrzebować odczytania zmiennej środowiskowej. Na przykład przed kontynuowaniem uruchom ponownie środowisko projektowe lub edytor.

## <a name="object-model"></a>Model obiektów

Usługa Azure Blob Storage jest zoptymalizowana pod kątem przechowywania ogromnych ilości danych bez struktury. Dane bez struktury są danymi, które nie są zgodne z żadnym modelem lub definicją danych, jak na przykład dane tekstowe lub binarne. Magazyn obiektów blob oferuje trzy typy zasobów:

* Konto magazynu
* Kontener na koncie magazynu
* Obiekt blob w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu obiektów blob](./media/storage-quickstart-blobs-dotnet/blob1.png)

Użyj następujących klas .NET, aby wchodzić w interakcje z tymi zasobami:

* [CloudStorageAccount:](/dotnet/api/microsoft.azure.storage.cloudstorageaccount) `CloudStorageAccount` klasa reprezentuje Twoje konto usługi Azure Storage. Użyj tej klasy, aby autoryzować dostęp do usługi Blob Storage przy użyciu kluczy dostępu do konta.
* [CloudBlobClient:](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)Klasa zapewnia punkt dostępu do Blob service `CloudBlobClient` w kodzie.
* [CloudBlobContainer:](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) `CloudBlobContainer` klasa reprezentuje kontener obiektów blob w kodzie.
* [CloudBlockBlob:](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob) `CloudBlockBlob` obiekt reprezentuje blokowy obiekt blob w kodzie. Blokowe obiekty blob składają się z bloków danych, którymi można zarządzać indywidualnie.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące czynności przy użyciu biblioteki klienta usługi Azure Blob Storage dla platformy .NET:

   * [Uwierzytelnianie klienta](#authenticate-the-client)
   * [Tworzenie kontenera](#create-a-container)
   * [Ustawianie uprawnień dla kontenera](#set-permissions-on-a-container)
   * [Przekazywanie obiektów blob do kontenera](#upload-blobs-to-a-container)
   * [Wyświetlanie listy obiektów blob w kontenerze](#list-the-blobs-in-a-container)
   * [Pobieranie obiektów blob](#download-blobs)
   * [Usuwanie kontenera](#delete-a-container)

### <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Poniższy kod sprawdza, czy zmienna środowiskowa zawiera ciąg połączenia, które mogą być analizowane w celu utworzenia obiektu [CloudStorageAccount,](/dotnet/api/microsoft.azure.storage.cloudstorageaccount) który wskaże konto magazynu. Aby sprawdzić, czy parametry połączenia są prawidłowe, należy użyć metody [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse). Jeśli `TryParse` to się powiedzie, inicjuje `storageAccount` zmienną i zwraca wartość `true` .

Dodaj ten kod wewnątrz `ProcessAsync` metody :

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");

// Check whether the connection string can be parsed.
CloudStorageAccount storageAccount;
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob
    // storage here.
    // ADD OTHER OPERATIONS HERE
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'AZURE_STORAGE_CONNECTION_STRING' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> Aby wykonać pozostałe operacje w tym artykule, zastąp w powyższym kodzie fragmentami `// ADD OTHER OPERATIONS HERE` kodu w poniższych sekcjach.

### <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener, najpierw utwórz wystąpienie obiektu [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient), które wskazuje na magazynu obiektów blob na koncie magazynu. Następnie utwórz wystąpienie obiektu [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) i utwórz kontener.

W takim przypadku kod wywołuje metodę [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) w celu utworzenia kontenera. Wartość identyfikatora GUID jest dołączana do nazwy kontenera w celu zapewnienia, że jest ona unikatowa. W środowisku produkcyjnym często lepiej jest użyć metody [CreateIfNotExistsAsync,](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) aby utworzyć kontener tylko wtedy, gdy jeszcze nie istnieje.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

```csharp
// Create the CloudBlobClient that represents the 
// Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and 
// append a GUID value to it to make the name unique.
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs" + 
        Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();
```

### <a name="set-permissions-on-a-container"></a>Ustawianie uprawnień do kontenera

Ustaw uprawnienia w kontenerze tak, aby wszystkie obiekty blob w kontenerze są publiczne. Jeśli obiekt blob jest publiczny, dowolny klient może uzyskiwać do niego anonimowy dostęp.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów blob do kontenera

Poniższy fragment kodu pobiera odwołanie do obiektu przez wywołanie metody `CloudBlockBlob` [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) w kontenerze utworzonym w poprzedniej sekcji. Następnie przekaże wybrany plik lokalny do obiektu blob, wywołując metodę [UploadFromFileAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) Jeśli obiekt blob nie istnieje, zostanie utworzony przez metodę. W przeciwnym razie zostanie zastąpiony.

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Wylicz listę obiektów blob w kontenerze przy użyciu [metody ListBlobsSegmentedAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) W tym przypadku do kontenera został dodany tylko jeden obiekt blob, więc operacja na liście zwraca tylko ten jeden obiekt blob.

Jeśli istnieje zbyt wiele obiektów blob do zwrócenia w jednym wywołaniu (domyślnie więcej niż 5000), metoda zwraca segment całkowitego zestawu wyników i `ListBlobsSegmentedAsync` token kontynuacji. Aby pobrać następny segment obiektów blob, udostępnij token kontynuacji zwrócony przez poprzednie wywołanie i tak dalej, aż token kontynuacji będzie mieć wartość null. Token kontynuacji o wartości null wskazuje, że wszystkie obiekty blob zostały pobrane. W kodzie pokazano, jak używać tokenu kontynuacji w celu zapewnienia najlepszych rozwiązań.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null); // Loop while the continuation token is not null.

```

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz utworzony wcześniej obiekt blob do lokalnego systemu plików przy użyciu [metody DownloadToFileAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) Przykładowy kod dodaje sufiks "_DOWNLOADED" do nazwy obiektu blob, dzięki czemu można zobaczyć oba pliki w lokalnym systemie plików.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie całego kontenera przy użyciu [funkcji CloudBlobContainer.DeleteAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync) Jeśli chcesz, możesz także usunąć pliki lokalne.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the example files, " +
    "example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja utworzy plik testowy w lokalnym folderze *MyDocuments* i przekaże go do usługi Blob Storage. Następnie przykład wyświetla listę obiektów blob w kontenerze i pobiera plik z nową nazwą, aby można było porównać stare i nowe pliki.

Przejdź do katalogu aplikacji, a następnie skompilować i uruchomić aplikację.

```console
dotnet build
```

```console
dotnet run
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

```output
Azure Blob storage - .NET Quickstart example

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-96
97c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f
4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-
958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926
-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the example files and example container.
```

Po naciśnięciu klawisza **Enter** aplikacja usunie kontener magazynu i pliki. Przed ich usunięciem sprawdź, czy folder *Moje dokumenty* zawiera dwa pliki. Możesz je otworzyć i sprawdzić, czy są identyczne. Skopiuj adres URL obiektu blob z okna konsoli i wklej go do przeglądarki, aby wyświetlić zawartość obiektu blob.

Po zweryfikowaniu plików naciśnij dowolny klawisz, aby zakończyć demonstrację i usunąć pliki testowe.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano następujące czynności: przekazywanie, pobieranie i wyświetlanie listy obiektów blob przy użyciu platformy .NET.

Aby dowiedzieć się, jak utworzyć aplikację internetową, która przekaże obraz do usługi Blob Storage, przejdź do:

> [!div class="nextstepaction"]
> [Przekazywanie i przetwarzanie obrazu](storage-upload-process-images.md)

* Aby dowiedzieć się więcej na temat platformy .NET Core, zobacz [Get started with .NET in 10 minutes (Rozpoczynanie pracy z platformą .NET w 10 minut)](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro).
* Aby zapoznać się z przykładową aplikacją, którą można wdrożyć z programu Visual Studio dla systemu Windows, zobacz [.NET Photo Gallery Web Application Sample with Azure Blob Storage (Przykład internetowej aplikacji platformy .NET z galerią zdjęć i z usługą Azure Blob Storage)](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).