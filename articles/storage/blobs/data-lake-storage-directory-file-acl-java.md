---
title: Azure Data Lake Storage Gen2 zestawu SDK Java dla plików & list ACL
description: Biblioteki usługi Azure Storage dla języka Java umożliwiają zarządzanie katalogami oraz list kontroli dostępu do plików i katalogów (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
author: normesta
ms.service: storage
ms.date: 09/10/2020
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: f6e8219f744a91628f9860f0af133c07eddb4253
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913389"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Używanie języka Java do zarządzania katalogami, plikami i listami ACL w Azure Data Lake Storage Gen2

W tym artykule pokazano, jak używać języka Java do tworzenia katalogów, plików i uprawnień w ramach kont magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS) i zarządzać nimi. 

[Pakiet (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [Dokumentacja](/java/api/overview/azure/storage-file-datalake-readme)  |  interfejsu API Mapowanie Gen1 do [Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Przekaż opinię](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](../common/storage-account-create.md) instrukcjami, aby je utworzyć.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Aby rozpocząć, Otwórz [Tę stronę](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) i Znajdź najnowszą wersję biblioteki języka Java. Następnie otwórz plik *pom.xml* w edytorze tekstów. Dodaj element zależności, który odwołuje się do tej wersji.

Jeśli planujesz uwierzytelnienie aplikacji klienckiej za pomocą Azure Active Directory (AD), a następnie Dodaj zależność do biblioteki klienta usługi Azure Secret. Zobacz  [Dodawanie pakietu biblioteki tajnego klienta do projektu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Następnie Dodaj te instrukcje Imports do pliku kodu.

```java
import com.azure.core.credential.TokenCredential;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>Połącz z kontem 

Aby użyć fragmentów kodu w tym artykule, należy utworzyć wystąpienie **DataLakeServiceClient** reprezentujące konto magazynu. 

### <a name="connect-by-using-an-account-key"></a>Nawiązywanie połączenia przy użyciu klucza konta

Jest to najprostszy sposób nawiązywania połączenia z kontem. 

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu klucza konta.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Nawiązywanie połączenia przy użyciu Azure Active Directory (Azure AD)

Aby uwierzytelnić aplikację w usłudze Azure AD, możesz użyć [biblioteki klienta tożsamości platformy Azure dla języka Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu identyfikatora klienta, klucza tajnego klienta i identyfikatora dzierżawy.  Aby uzyskać te wartości, zobacz [pozyskiwanie tokenu z usługi Azure AD w celu autoryzowania żądań z aplikacji klienckiej](../common/storage-auth-aad-app.md).

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz dokumentację [usługi Azure Identity Client Library for Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .


## <a name="create-a-container"></a>Tworzenie kontenera

Kontener działa jako system plików dla plików. Można go utworzyć, wywołując metodę **DataLakeServiceClient. isfilesystem** .

Ten przykład tworzy kontener o nazwie `my-file-system` . 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu, wywołując metodę **DataLakeFileSystemClient. setdirectory** .

Ten przykład dodaje katalog o nazwie `my-directory` do kontenera, a następnie dodaje podkatalog o nazwie `my-subdirectory` . 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub Przenieś katalog, wywołując metodę **DataLakeDirectoryClient. Rename** . Przekaż ścieżkę do żądanego katalogu jako parametr. 

Ten przykład zmienia nazwę podkatalogu na nazwę `my-subdirectory-renamed` .

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

Ten przykład przenosi katalog o nazwie `my-subdirectory-renamed` do podkatalogu o nazwie `my-directory-2` . 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog, wywołując metodę **DataLakeDirectoryClient. deleteWithResponse** .

Ten przykład usuwa katalog o nazwie `my-directory` .   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="upload-a-file-to-a-directory"></a>Przekaż plik do katalogu

Najpierw Utwórz odwołanie do pliku w katalogu docelowym, tworząc wystąpienie klasy **DataLakeFileClient** . Przekaż plik, wywołując metodę **DataLakeFileClient. Append** . Upewnij się, że ukończono przekazywanie, wywołując metodę **DataLakeFileClient. FlushAsync** .

Ten przykład przekazuje plik tekstowy do katalogu o nazwie `my-directory` .

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new BufferedInputStream(new FileInputStream(file));

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

> [!TIP]
> Jeśli rozmiar pliku jest duży, kod będzie musiał wykonać wiele wywołań metody **DataLakeFileClient. Append** . Rozważ użycie metody **DataLakeFileClient. uploadFromFile** . Dzięki temu można przekazać cały plik w jednym wywołaniu. 
>
> Zobacz następną sekcję, aby zapoznać się z przykładem.

## <a name="upload-a-large-file-to-a-directory"></a>Przekaż duży plik do katalogu

Użyj metody **DataLakeFileClient. uploadFromFile** , aby przekazać duże pliki bez konieczności wykonywania wielu wywołań metody **DataLakeFileClient. Append** .

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```

## <a name="download-from-a-directory"></a>Pobierz z katalogu

Najpierw Utwórz wystąpienie **DataLakeFileClient** reprezentujące plik, który chcesz pobrać. Użyj metody **DataLakeFileClient. Read** , aby odczytać plik. Użyj dowolnego interfejsu API przetwarzania plików platformy .NET, aby zaoszczędzić bajty ze strumienia do pliku. 

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

W tym przykładzie program drukuje nazwy poszczególnych plików znajdujących się w katalogu o nazwie `my-directory` .

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="manage-access-control-lists-acls"></a>Zarządzanie listami kontroli dostępu (ACL)

Uprawnienia dostępu do katalogów i plików można uzyskiwać, ustawiać i aktualizować.

> [!NOTE]
> Jeśli używasz usługi Azure Active Directory (Azure AD) do autoryzacji dostępu, upewnij się, że podmiot zabezpieczeń ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [Kontrola dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-a-directory-acl"></a>Zarządzanie listą ACL katalogów

Ten przykład pobiera i ustawia listę ACL katalogu o nazwie `my-directory` . Ten przykład daje właścicielowi uprawnień Odczyt, zapis i wykonywanie, daje grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania, a ponadto daje wszystkim innym osobom dostęp do odczytu.

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzacji dostępu ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [Kontrola dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

Możesz również uzyskać i ustawić listę kontroli dostępu dla katalogu głównego kontenera. Aby uzyskać katalog główny, należy przekazać pusty ciąg ( `""` ) do metody **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="manage-a-file-acl"></a>Zarządzanie listą ACL plików

Ten przykład pobiera i ustawia listę ACL pliku o nazwie `upload-file.txt` . Ten przykład daje właścicielowi uprawnień Odczyt, zapis i wykonywanie, daje grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania, a ponadto daje wszystkim innym osobom dostęp do odczytu.

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzacji dostępu ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [Kontrola dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

### <a name="set-an-acl-recursively"></a>Ustawianie listy ACL rekursywnie

Można dodawać, aktualizować i usuwać listy ACL cyklicznie na istniejących elementach podrzędnych katalogu nadrzędnego bez konieczności wprowadzania tych zmian indywidualnie dla każdego elementu podrzędnego. Więcej informacji można znaleźć w sekcji [Ustawianie list kontroli dostępu (ACL) rekursywnie dla Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Zobacz też

* [Dokumentacja referencyjna interfejsu API](/java/api/overview/azure/storage-file-datalake-readme)
* [Pakiet (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Przekaż opinię](https://github.com/Azure/azure-sdk-for-java/issues)