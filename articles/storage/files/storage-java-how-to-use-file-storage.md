---
title: Programowanie dla Azure Files przy użyciu języka Java | Microsoft Docs
description: Dowiedz się, jak opracowywać aplikacje i usługi Java, które używają Azure Files do przechowywania danych plików.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/19/2017
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 977777aff4aa32bf6876e1d573970d71ec71584e
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629771"
---
# <a name="develop-for-azure-files-with-java"></a>Develop for Azure Files with Java (Tworzenie oprogramowania dla usługi Azure Files przy użyciu języka Java)
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Informacje o tym samouczku
W tym samouczku przedstawiono podstawy używania języka Java do tworzenia aplikacji lub usług, które używają Azure Files do przechowywania danych plików. W tym samouczku utworzymy aplikację konsolową i pokażemy, jak wykonywać podstawowe działania za pomocą języka Java i Azure Files:

* Tworzenie i usuwanie udziałów plików platformy Azure
* Tworzenie i Usuwanie katalogów
* Wyliczanie plików i katalogów w udziale plików platformy Azure
* Przekazywanie, pobieranie i usuwanie pliku

> [!Note]  
> Ponieważ do Azure Files można uzyskać dostęp za pośrednictwem protokołu SMB, można napisać aplikacje, które uzyskują dostęp do udziału plików platformy Azure przy użyciu standardowych klas we/wy języka Java. W tym artykule opisano sposób pisania aplikacji, które używają zestawu SDK Java usługi Azure Storage, który używa [interfejsu API REST Azure Files](/rest/api/storageservices/file-service-rest-api) , aby komunikować się z Azure Files.

## <a name="create-a-java-application"></a>Tworzenie aplikacji Java
Aby skompilować przykłady, potrzebny będzie zestaw Java Development Kit (JDK) i [zestaw SDK usługi Azure Storage dla języka Java](https://github.com/Azure/azure-storage-java). Należy również utworzyć konto usługi Azure Storage.

## <a name="set-up-your-application-to-use-azure-files"></a>Skonfiguruj aplikację do używania Azure Files
Aby skorzystać z interfejsów API usługi Azure Storage, Dodaj następującą instrukcję na początku pliku Java, w którym chcesz uzyskać dostęp do usługi Storage.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure Storage
Aby korzystać z Azure Files, musisz nawiązać połączenie z kontem usługi Azure Storage. Pierwszym krokiem jest skonfigurowanie parametrów połączenia, które zostaną użyte do nawiązania połączenia z kontem magazynu. Zdefiniujmy zmienną statyczną, aby to zrobić.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Zastąp your_storage_account_name i your_storage_account_key wartościami rzeczywistymi dla konta magazynu.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Nawiązywanie połączenia z kontem usługi Azure Storage
Aby nawiązać połączenie z kontem magazynu, należy użyć obiektu **CloudStorageAccount** , przekazując parametry połączenia do metody **analizy** .

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount. Parse** zgłasza InvalidKeyException, więc należy umieścić go wewnątrz bloku try/catch.

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
Wszystkie pliki i katalogi w Azure Files znajdują się w kontenerze nazywanym **udziałem**. Twoje konto magazynu może mieć tyle udziałów, ile wynosi pojemność konta. Aby uzyskać dostęp do udziału i jego zawartości, musisz użyć klienta Azure Files.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Za pomocą klienta Azure Files można uzyskać odwołanie do udziału.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Aby faktycznie utworzyć udział, użyj metody **metodę createifnotexists** obiektu CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

W tym momencie **udział** przechowuje odwołanie do udziału o nazwie **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Usuwanie udziału plików platformy Azure
Usuwanie udziału odbywa się przez wywołanie metody **deleteIfExists** w obiekcie CloudFileShare. Oto przykładowy kod, który robi.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="create-a-directory"></a>Tworzenie katalogu
Magazyn można również organizować, umieszczając pliki w podkatalogach zamiast ich wszystkich w katalogu głównym. Azure Files umożliwia utworzenie tylu katalogów, ile zezwoli Twoje konto. Poniższy kod utworzy podkatalog o nazwie **sampledir** w katalogu głównym.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>Usuwanie katalogu
Usuwanie katalogu to proste zadanie, chociaż należy zauważyć, że nie można usunąć katalogu, który nadal zawiera pliki lub inne katalogi.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Wyliczanie plików i katalogów w udziale plików platformy Azure
Aby uzyskać listę plików i katalogów w ramach udziału, można łatwo wykonać instrukcje **listFilesAndDirectories** na odwołaniach CloudFileDirectory. Metoda zwraca listę obiektów ListFileItem, na których można wykonać iterację. Na przykład poniższy kod wyświetla listę plików i katalogów w katalogu głównym.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Przekazywanie pliku
W tej sekcji dowiesz się, jak przekazać plik z magazynu lokalnego do katalogu głównego udziału.

Pierwszym krokiem przekazywania pliku jest uzyskanie odwołania do katalogu, w którym powinien się znajdować. W tym celu należy wywołać metodę **getRootDirectoryReference** obiektu Share.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Teraz, gdy masz odwołanie do katalogu głównego udziału, możesz przekazać do niego plik przy użyciu następującego kodu.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Pobieranie pliku
Jedną z kilku częstych operacji wykonywanych względem Azure Files jest pobranie plików. W poniższym przykładzie kod pobiera SampleFile.txt i wyświetla jego zawartość.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>Usuwanie pliku
Inną typową operacją Azure Files jest usunięcie pliku. Poniższy kod usuwa plik o nazwie SampleFile.txt przechowywany w katalogu o nazwie **sampledir**.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="next-steps"></a>Następne kroki
Jeśli chcesz dowiedzieć się więcej na temat innych interfejsów API usługi Azure Storage, Skorzystaj z poniższych linków.

* [Platforma Azure dla deweloperów języka Java](/java/azure)/)
* [Zestaw SDK usługi Azure Storage dla języka Java](https://github.com/azure/azure-storage-java)
* [Zestaw SDK usługi Azure Storage dla systemu Android](https://github.com/azure/azure-storage-android)
* [Dokumentacja zestawu SDK klienta usługi Azure Storage](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [Interfejs API REST usług Azure Storage](/rest/api/storageservices/)
* [Blog zespołu usługi Azure Storage](/archive/blogs/windowsazurestorage/)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../common/storage-use-azcopy-v10.md)
* [Troubleshooting Azure Files problems - Windows (Rozwiązywanie problemów z usługą Azure Files — Windows)](storage-troubleshoot-windows-file-connection-problems.md)