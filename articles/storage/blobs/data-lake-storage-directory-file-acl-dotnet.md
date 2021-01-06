---
title: Azure Data Lake Storage Gen2 zestawu SDK platformy .NET dla plików & listy ACL
description: Biblioteka klienta usługi Azure Storage umożliwia zarządzanie katalogami oraz list kontroli dostępu do plików i katalogów (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
author: normesta
ms.service: storage
ms.date: 08/26/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 5af1c656699a7c60ad4f93beb43b603bdc6e3be7
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935108"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Użyj programu .NET do zarządzania katalogami, plikami i listami ACL w Azure Data Lake Storage Gen2

W tym artykule przedstawiono sposób korzystania z platformy .NET do tworzenia katalogów, plików i uprawnień w ramach kont magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS), oraz do zarządzania nimi. 

[Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [Dokumentacja](/dotnet/api/azure.storage.files.datalake)  |  interfejsu API Mapowanie Gen1 do [Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Przekaż opinię](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](../common/storage-account-create.md) instrukcjami, aby je utworzyć.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Łączenie przy użyciu Azure Active Directory (AD)

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

## <a name="manage-access-control-lists-acls"></a>Zarządzanie listami kontroli dostępu (ACL)

Uprawnienia dostępu do katalogów i plików można uzyskiwać, ustawiać i aktualizować.

> [!NOTE]
> Jeśli używasz usługi Azure Active Directory (Azure AD) do autoryzacji dostępu, upewnij się, że podmiot zabezpieczeń ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [Kontrola dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-a-directory-acl"></a>Zarządzanie listą ACL katalogów

Pobierz listę kontroli dostępu (ACL) katalogu, wywołując metodę [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) i ustawiając listę ACL przez wywołanie metody [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzacji dostępu ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [Kontrola dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md). 

Ten przykład pobiera i ustawia listę ACL katalogu o nazwie `my-directory` . Ciąg `user::rwx,group::r-x,other::rw-` przyznaje uprawnienia Odczyt, zapis i wykonywanie przez użytkownika, który ma grupę będącą właścicielem, ma uprawnienia tylko do odczytu i wykonania, i daje wszystkim innym uprawnienie do odczytu i zapisu.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

Możesz również uzyskać i ustawić listę kontroli dostępu dla katalogu głównego kontenera. Aby uzyskać katalog główny, należy przekazać pusty ciąg ( `""` ) do metody [DataLakeFileSystemClient. GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) .

### <a name="manage-a-file-acl"></a>Zarządzanie listą ACL plików

Pobieranie listy kontroli dostępu (ACL) pliku przez wywołanie metody [DataLakeFileClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) i ustawienie listy ACL przez wywołanie metody [DataLakeFileClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) .

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzacji dostępu ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [Kontrola dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md). 

Ten przykład pobiera i ustawia listę ACL pliku o nazwie `my-file.txt` . Ciąg `user::rwx,group::r-x,other::rw-` przyznaje uprawnienia Odczyt, zapis i wykonywanie przez użytkownika, który ma grupę będącą właścicielem, ma uprawnienia tylko do odczytu i wykonania, i daje wszystkim innym uprawnienie do odczytu i zapisu.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-an-acl-recursively"></a>Ustawianie listy ACL rekursywnie

Można dodawać, aktualizować i usuwać listy ACL cyklicznie na istniejących elementach podrzędnych katalogu nadrzędnego bez konieczności wprowadzania tych zmian indywidualnie dla każdego elementu podrzędnego. Więcej informacji można znaleźć w sekcji [Ustawianie list kontroli dostępu (ACL) rekursywnie dla Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Zobacz też

* [Dokumentacja referencyjna interfejsu API](/dotnet/api/azure.storage.files.datalake)
* [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Przekaż opinię](https://github.com/Azure/azure-sdk-for-net/issues)