---
title: Zarządzanie danymi w Azure Data Lake Storage Gen2 przy użyciu platformy .NET
description: Biblioteka klienta usługi Azure Storage dla platformy .NET umożliwia zarządzanie katalogami i plikami na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 52e993a22a512a94c8b5b8b050205db0c4ce0b1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650324"
---
# <a name="use-net-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Zarządzanie katalogami i plikami w programie Azure Data Lake Storage Gen2 przy użyciu platformy .NET

W tym artykule pokazano, jak używać programu .NET do tworzenia katalogów i plików oraz zarządzania nimi na kontach magazynu, które mają hierarchiczną przestrzeń nazw.

Aby dowiedzieć się, jak pobierać, ustawiać i aktualizować listy kontroli dostępu (ACL) katalogów i plików, zobacz [Używanie platformy .NET do zarządzania listami ACL w Azure Data Lake Storage Gen2](data-lake-storage-acl-dotnet.md).

[Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [Dokumentacja](/dotnet/api/azure.storage.files.datalake)  |  interfejsu API Mapowanie Gen1 do [Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Przekaż opinię](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

- Konto magazynu, dla którego włączono hierarchiczną przestrzeń nazw. Postępuj zgodnie z [tymi](create-data-lake-storage-account.md) instrukcjami, aby je utworzyć.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Aby rozpocząć, zainstaluj pakiet NuGet [Azure. Storage. Files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

Aby uzyskać więcej informacji o instalowaniu pakietów NuGet, zobacz [Instalowanie i zarządzanie pakietami w programie Visual Studio przy użyciu Menedżera pakietów NuGet](/nuget/consume-packages/install-use-packages-visual-studio).

Następnie Dodaj te instrukcje using na początku pliku kodu.

```csharp
using Azure;
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;

```

## <a name="connect-to-the-account"></a>Połącz z kontem

Aby użyć fragmentów kodu w tym artykule, należy utworzyć wystąpienie [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) reprezentujące konto magazynu. 

### <a name="connect-by-using-an-account-key"></a>Nawiązywanie połączenia przy użyciu klucza konta

Jest to najprostszy sposób nawiązywania połączenia z kontem. 

Ten przykład tworzy wystąpienie [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) przy użyciu klucza konta.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Nawiązywanie połączenia przy użyciu Azure Active Directory (Azure AD)

Za pomocą [biblioteki klienta tożsamości platformy Azure dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) można uwierzytelniać aplikację w usłudze Azure AD.

Ten przykład tworzy wystąpienie [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) przy użyciu identyfikatora klienta, klucza tajnego klienta i identyfikatora dzierżawy.  Aby uzyskać te wartości, zobacz [pozyskiwanie tokenu z usługi Azure AD w celu autoryzowania żądań z aplikacji klienckiej](../common/storage-auth-aad-app.md).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz dokumentację [biblioteki klienta tożsamości platformy Azure dla programu .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

## <a name="create-a-container"></a>Tworzenie kontenera

Kontener działa jako system plików dla plików. Można go utworzyć, wywołując metodę [DataLakeServiceClient. isfilesystem](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) .

Ten przykład tworzy kontener o nazwie `my-file-system` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu, wywołując metodę [DataLakeFileSystemClient. CreateDirectoryAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) .

Ten przykład dodaje katalog o nazwie `my-directory` do kontenera, a następnie dodaje podkatalog o nazwie `my-subdirectory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub Przenieś katalog, wywołując metodę [DataLakeDirectoryClient. RenameAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) . Przekaż ścieżkę do żądanego katalogu jako parametr. 

Ten przykład zmienia nazwę podkatalogu na nazwę `my-subdirectory-renamed` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_RenameDirectory":::

Ten przykład przenosi katalog o nazwie `my-subdirectory-renamed` do podkatalogu o nazwie `my-directory-2` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog, wywołując metodę [DataLakeDirectoryClient. Delete](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) .

Ten przykład usuwa katalog o nazwie `my-directory` .  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Przekaż plik do katalogu

Najpierw Utwórz odwołanie do pliku w katalogu docelowym, tworząc wystąpienie klasy [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) . Przekaż plik, wywołując metodę [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) . Upewnij się, że ukończono przekazywanie, wywołując metodę [DataLakeFileClient. FlushAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) .

Ten przykład przekazuje plik tekstowy do katalogu o nazwie `my-directory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFile":::

> [!TIP]
> Jeśli rozmiar pliku jest duży, kod będzie musiał wykonać wiele wywołań do [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Rozważ użycie metody [DataLakeFileClient. UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) . Dzięki temu można przekazać cały plik w jednym wywołaniu.
>
> Zobacz następną sekcję, aby zapoznać się z przykładem.

## <a name="upload-a-large-file-to-a-directory"></a>Przekaż duży plik do katalogu

Użyj metody [DataLakeFileClient. UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) , aby przekazać duże pliki bez konieczności wykonywania wielu wywołań metody [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Pobierz z katalogu 

Najpierw Utwórz wystąpienie [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) reprezentujące plik, który chcesz pobrać. Użyj metody [DataLakeFileClient. ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync)  i Przeanalizuj wartość zwracaną, aby uzyskać obiekt [strumienia](/dotnet/api/system.io.stream) . Użyj dowolnego interfejsu API przetwarzania plików platformy .NET, aby zaoszczędzić bajty ze strumienia do pliku. 

Ten przykład używa [BinaryReader](/dotnet/api/system.io.binaryreader) i [FILESTREAM](/dotnet/api/system.io.filestream) do zapisywania bajtów do pliku. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DownloadBinaryFromDirectory":::

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Wyświetlanie zawartości katalogu przez wywołanie metody [FileSystemClient. GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) , a następnie Wyliczenie przez wyniki.

W tym przykładzie program drukuje nazwy poszczególnych plików znajdujących się w katalogu o nazwie `my-directory` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Zobacz też

- [Dokumentacja referencyjna interfejsu API](/dotnet/api/azure.storage.files.datalake)
- [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Przekaż opinię](https://github.com/Azure/azure-sdk-for-net/issues)