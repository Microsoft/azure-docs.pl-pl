---
title: Programowanie dla Azure Files przy użyciu języka Java | Microsoft Docs
description: Dowiedz się, jak opracowywać aplikacje i usługi Java, które używają Azure Files do przechowywania danych plików.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 25baa278961b93b04e60f2e997b98753cb6cf3ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95024113"
---
# <a name="develop-for-azure-files-with-java"></a>Develop for Azure Files with Java (Tworzenie oprogramowania dla usługi Azure Files przy użyciu języka Java)

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Zapoznaj się z podstawą tworzenia aplikacji w języku Java, które używają Azure Files do przechowywania danych. Utwórz aplikację konsolową i uzyskaj podstawowe działania przy użyciu Azure Files interfejsów API:

- Tworzenie i usuwanie udziałów plików platformy Azure
- Tworzenie i Usuwanie katalogów
- Wyliczanie plików i katalogów w udziale plików platformy Azure
- Przekazywanie, pobieranie i usuwanie pliku

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Tworzenie aplikacji Java

Aby skompilować przykłady, potrzebny będzie zestaw Java Development Kit (JDK) i [zestaw SDK usługi Azure Storage dla języka Java](https://github.com/azure/azure-sdk-for-java). Należy również utworzyć konto usługi Azure Storage.

## <a name="set-up-your-application-to-use-azure-files"></a>Skonfiguruj aplikację do używania Azure Files

Aby skorzystać z interfejsów API Azure Files, Dodaj następujący kod na początku pliku Java z lokalizacji, w której ma być uzyskiwany dostęp Azure Files.

# <a name="java-v12"></a>[V12 Java](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure Storage

Aby korzystać z Azure Files, musisz nawiązać połączenie z kontem usługi Azure Storage. Skonfiguruj parametry połączenia i użyj go do nawiązania połączenia z kontem magazynu. Zdefiniuj statyczną zmienną do przechowywania parametrów połączenia.

# <a name="java-v12"></a>[V12 Java](#tab/java)

Zamień *\<storage_account_name\>* i *\<storage_account_key\>* na wartości rzeczywiste dla konta magazynu.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Zastąp *your_storage_account_name* i *your_storage_account_key* wartościami rzeczywistymi dla konta magazynu.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-azure-files-storage"></a>Magazyn Azure Files dostępu

# <a name="java-v12"></a>[V12 Java](#tab/java)

Aby uzyskać dostęp do Azure Files, Utwórz obiekt [ShareClient](/java/api/com.azure.storage.file.share.shareclient) . Użyj klasy [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) , aby utworzyć nowy obiekt **ShareClient** .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Aby uzyskać dostęp do konta magazynu, użyj obiektu **CloudStorageAccount** , przekazując parametry połączenia do metody **analizy** .

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount. Parse** zgłasza InvalidKeyException, więc należy umieścić go wewnątrz bloku try/catch.

---

## <a name="create-a-file-share"></a>Tworzenie udziału plików

Wszystkie pliki i katalogi w Azure Files są przechowywane w kontenerze o nazwie udziału.

# <a name="java-v12"></a>[V12 Java](#tab/java)

Metoda [ShareClient. Create](/java/api/com.azure.storage.file.share.shareclient.create) zgłasza wyjątek, jeśli udział już istnieje. Należy umieścić wywołanie do **utworzenia** w `try/catch` bloku i obsłużyć wyjątek.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Aby uzyskać dostęp do udziału i jego zawartości, Utwórz klienta Azure Files.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Za pomocą klienta Azure Files można uzyskać odwołanie do udziału.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Aby faktycznie utworzyć udział, użyj metody **metodę createifnotexists** obiektu **CloudFileShare** .

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

W tym momencie **udział** zawiera odwołanie do udziału o nazwie **przykład udziału**.

---

## <a name="delete-a-file-share"></a>Usuwanie udziału plików

Poniższy przykładowy kod usuwa udział plików.

# <a name="java-v12"></a>[V12 Java](#tab/java)

Usuń udział, wywołując metodę [ShareClient. Delete](/java/api/com.azure.storage.file.share.shareclient.delete) .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Usuń udział, wywołując metodę **deleteIfExists** dla obiektu **CloudFileShare** .

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

---

## <a name="create-a-directory"></a>Tworzenie katalogu

Organizuj magazyn, umieszczając pliki w podkatalogach zamiast ich wszystkich w katalogu głównym.

# <a name="java-v12"></a>[V12 Java](#tab/java)

Poniższy kod tworzy katalog przez wywołanie [ShareDirectoryClient. Create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create). Przykładowa Metoda zwraca `Boolean` wartość wskazującą, czy pomyślnie utworzył katalog.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Poniższy kod tworzy podkatalog o nazwie **sampledir** w katalogu głównym.

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

---

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuwanie katalogu jest prostym zadaniem. Nie można usunąć katalogu, który nadal zawiera pliki lub podkatalogi.

# <a name="java-v12"></a>[V12 Java](#tab/java)

Metoda [ShareDirectoryClient. Delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) zgłasza wyjątek, jeśli katalog nie istnieje lub nie jest pusty. Umieść wywołanie do **usunięcia** w `try/catch` bloku i obsłuż wyjątek.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

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

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Wyliczanie plików i katalogów w udziale plików platformy Azure

# <a name="java-v12"></a>[V12 Java](#tab/java)

Pobierz listę plików i katalogów, wywołując [ShareDirectoryClient. listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories). Metoda zwraca listę obiektów [ShareFileItem](/java/api/com.azure.storage.file.share.models.sharefileitem) , na których można wykonać iterację. Poniższy kod zawiera listę plików i katalogów w katalogu określonym przez parametr *dirname* .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Pobierz listę plików i katalogów, wywołując **listFilesAndDirectories** na odwołaniu **CloudFileDirectory** . Metoda zwraca listę obiektów **ListFileItem** , na których można wykonać iterację. Poniższy kod zawiera listę plików i katalogów w katalogu głównym.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>Przekazywanie pliku

Dowiedz się, jak przekazać plik z magazynu lokalnego.

# <a name="java-v12"></a>[V12 Java](#tab/java)

Poniższy kod przekazuje plik lokalny do usługi Azure File Storage, wywołując metodę [ShareFileClient. uploadFromFile](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) . Poniższa przykładowa Metoda zwraca `Boolean` wartość wskazującą, czy pomyślnie przekazano określony plik.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Pobierz odwołanie do katalogu, w którym plik zostanie przekazany przez wywołanie metody **getRootDirectoryReference** w obiekcie Share.

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

---

## <a name="download-a-file"></a>Pobieranie pliku

Jedną z wielu częstych operacji jest pobieranie plików z magazynu Azure Files.

# <a name="java-v12"></a>[V12 Java](#tab/java)

Poniższy przykład pobiera określony plik do katalogu lokalnego określonego w parametrze *destDir* . Przykładowa metoda powoduje, że pobrana nazwa pliku jest unikatowa przez zaczekanie na datę i godzinę.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Poniższy przykład pobiera SampleFile.txt i wyświetla jego zawartość.

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

---

## <a name="delete-a-file"></a>Usuwanie pliku

Inną typową operacją Azure Files jest usunięcie pliku.

# <a name="java-v12"></a>[V12 Java](#tab/java)

Poniższy kod usuwa określony określony plik. Najpierw przykład tworzy [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient) na podstawie parametru *dirname* . Następnie kod pobiera [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient) z klienta katalogu na podstawie parametru *filename* . Na koniec Przykładowa metoda wywołuje [ShareFileClient. Delete](/java/api/com.azure.storage.file.share.sharefileclient.delete) , aby usunąć plik.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Poniższy kod usuwa plik o nazwie SampleFile.txt przechowywany w katalogu o nazwie **sampledir**.

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

---

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej na temat innych interfejsów API usługi Azure Storage, Skorzystaj z poniższych linków.

- [Platforma Azure dla deweloperów języka Java](/azure/developer/java)
- [Zestaw Azure SDK dla języka Java](https://github.com/azure/azure-sdk-for-java)
- [Zestaw Azure SDK dla systemu Android](https://github.com/azure/azure-sdk-for-android)
- [Dokumentacja biblioteki klienckiej udziału plików platformy Azure dla języka Java SDK](/java/api/overview/azure/storage-file-share-readme)
- [Interfejs API REST usług Azure Storage](/rest/api/storageservices/)
- [Blog zespołu usługi Azure Storage](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../common/storage-use-azcopy-v10.md)
- [Troubleshooting Azure Files problems - Windows (Rozwiązywanie problemów z usługą Azure Files — Windows)](storage-troubleshoot-windows-file-connection-problems.md)
