---
title: Tworzenie aplikacji dla Azure Files przy użyciu języka Java | Microsoft Docs
description: Dowiedz się, jak opracowywać aplikacje i usługi Języka Java, które Azure Files do przechowywania danych plików.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 115c55a5833906aa0dcc616a5b1b659468647282
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814554"
---
# <a name="develop-for-azure-files-with-java"></a>Develop for Azure Files with Java (Tworzenie oprogramowania dla usługi Azure Files przy użyciu języka Java)

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Poznaj podstawy tworzenia aplikacji Java, które używają Azure Files do przechowywania danych. Tworzenie aplikacji konsolowej i nauka podstawowych akcji przy użyciu Azure Files API:

- Tworzenie i usuwanie udziałów plików platformy Azure
- Tworzenie i usuwanie katalogów
- Wyliczanie plików i katalogów w udziałach plików platformy Azure
- Przekazywanie, pobieranie i usuwanie pliku

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Tworzenie aplikacji Java

Do skompilowania przykładów potrzebny jest zestaw Java Development Kit (JDK) i zestaw [SDK usługi Azure Storage dla języka Java.](https://github.com/azure/azure-sdk-for-java) Należy również utworzyć konto usługi Azure Storage.

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurowanie aplikacji do korzystania z Azure Files

Aby użyć Azure Files API, dodaj następujący kod na początku pliku Java, z którego zamierzasz uzyskać dostęp do Azure Files.

# <a name="azure-java-sdk-v12"></a>[Zestaw Sdk Java platformy Azure w wersji 12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="azure-java-sdk-v11"></a>[Zestaw Sdk Java platformy Azure w wersji 11](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure Storage

Aby użyć Azure Files, musisz nawiązać połączenie z kontem usługi Azure Storage. Skonfiguruj parametrów połączenia i użyj ich do nawiązania połączenia z kontem magazynu. Zdefiniuj zmienną statyczną do przechowywania parametrów połączenia.

# <a name="azure-java-sdk-v12"></a>[Zestaw Sdk Java platformy Azure w wersji 12](#tab/java)

Zastąp *\<storage_account_name\>* wartości *\<storage_account_key\>* i rzeczywistymi wartościami dla konta magazynu.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="azure-java-sdk-v11"></a>[Zestaw Sdk Java platformy Azure w wersji 11](#tab/java11)

Zastąp *your_storage_account_name* i *your_storage_account_key* rzeczywistymi wartościami konta magazynu.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-an-azure-file-share"></a>Uzyskiwanie dostępu do udziału plików platformy Azure

# <a name="azure-java-sdk-v12"></a>[Zestaw Sdk Java platformy Azure w wersji 12](#tab/java)

Aby uzyskać Azure Files, utwórz [obiekt ShareClient.](/java/api/com.azure.storage.file.share.shareclient) Użyj klasy [ShareClientBuilder,](/java/api/com.azure.storage.file.share.shareclientbuilder) aby skompilować nowy **obiekt ShareClient.**

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="azure-java-sdk-v11"></a>[Zestaw Sdk Java platformy Azure w wersji 11](#tab/java11)

Aby uzyskać dostęp do konta magazynu, użyj obiektu **CloudStorageAccount,** przekazując ciąg połączenia do jego **metody analizy.**

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse zgłasza** wyjątek InvalidKeyException, więc należy umieścić go wewnątrz bloku try/catch.

---

## <a name="create-a-file-share"></a>Tworzenie udziału plików

Wszystkie pliki i katalogi w Azure Files są przechowywane w kontenerze nazywanym udziałem.

# <a name="azure-java-sdk-v12"></a>[Zestaw Sdk Java platformy Azure w wersji 12](#tab/java)

Metoda [ShareClient.create](/java/api/com.azure.storage.file.share.shareclient.create) zgłasza wyjątek, jeśli udział już istnieje. Umieść wywołanie , **aby utworzyć** w bloku `try/catch` i obsłużyć wyjątek.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="azure-java-sdk-v11"></a>[Zestaw Sdk Java platformy Azure w wersji 11](#tab/java11)

Aby uzyskać dostęp do udziału i jego zawartości, utwórz Azure Files klienta.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Przy użyciu Azure Files można uzyskać odwołanie do udziału.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Aby faktycznie utworzyć udział, użyj metody **createIfNotExists** obiektu **CloudFileShare.**

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

W tym momencie **udział zawiera** odwołanie do udziału o nazwie **przykładowy udział**.

---

## <a name="delete-a-file-share"></a>Usuwanie udziału plików

Poniższy przykładowy kod usuwa udział plików.

# <a name="azure-java-sdk-v12"></a>[Zestaw Sdk Java platformy Azure w wersji 12](#tab/java)

Usuń udział, wywołując [metodę ShareClient.delete.](/java/api/com.azure.storage.file.share.shareclient.delete)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="azure-java-sdk-v11"></a>[Zestaw Sdk Java platformy Azure w wersji 11](#tab/java11)

Usuń udział, wywołując **metodę deleteIfExists** dla **obiektu CloudFileShare.**

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

Organizowanie magazynu przez umieszczenie plików wewnątrz podkatalogów zamiast umieszczania ich wszystkich w katalogu głównym.

# <a name="azure-java-sdk-v12"></a>[Zestaw Sdk Java platformy Azure w wersji 12](#tab/java)

Poniższy kod tworzy katalog, wywołując [plik ShareDirectoryClient.create.](/java/api/com.azure.storage.file.share.sharedirectoryclient.create) Przykładowa metoda zwraca `Boolean` wartość wskazującą, czy katalog został pomyślnie utworzony.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="azure-java-sdk-v11"></a>[Zestaw Sdk Java platformy Azure w wersji 11](#tab/java11)

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

# <a name="azure-java-sdk-v12"></a>[Zestaw Sdk Java platformy Azure w wersji 12](#tab/java)

Metoda [ShareDirectoryClient.delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) zgłasza wyjątek, jeśli katalog nie istnieje lub nie jest pusty. Umieść wywołanie usunięcia **w** bloku i `try/catch` obsłuż wyjątek.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="azure-java-sdk-v11"></a>[Zestaw Sdk Java platformy Azure w wersji 11](#tab/java11)

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Wyliczanie plików i katalogów w udziałach plików platformy Azure

# <a name="azure-java-sdk-v12"></a>[Zestaw Sdk Java platformy Azure w wersji 12](#tab/java)

Pobierz listę plików i katalogów, wywołując [plik ShareDirectoryClient.listFilesAndDirectories.](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories) Metoda zwraca listę obiektów [ShareFileItem,](/java/api/com.azure.storage.file.share.models.sharefileitem) na których można iterować. Poniższy kod wyświetla listę plików i katalogów wewnątrz katalogu określonego przez *dirName parametru.*

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="azure-java-sdk-v11"></a>[Zestaw Sdk Java platformy Azure w wersji 11](#tab/java11)

Pobierz listę plików i katalogów, wywołując **listFilesAndDirectories w** **odwołaniu CloudFileDirectory.** Metoda zwraca listę obiektów **ListFileItem,** na których można iterować. Poniższy kod zawiera listę plików i katalogów w katalogu głównym.

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

# <a name="azure-java-sdk-v12"></a>[Zestaw Sdk Java platformy Azure w wersji 12](#tab/java)

Poniższy kod służy do przekazywania pliku lokalnego do usługi Azure File Storage przez wywołanie metody [ShareFileClient.uploadFromFile.](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) Następująca przykładowa metoda zwraca `Boolean` wartość wskazującą, czy plik został pomyślnie przekazany.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="azure-java-sdk-v11"></a>[Zestaw Sdk Java platformy Azure w wersji 11](#tab/java11)

Pobierz odwołanie do katalogu, w którym plik zostanie przekazany, wywołując metodę **getRootDirectoryReference** w obiekcie udziału.

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

Jedną z częstszych operacji jest pobieranie plików z udziału plików platformy Azure.

# <a name="azure-java-sdk-v12"></a>[Zestaw Sdk Java platformy Azure w wersji 12](#tab/java)

Poniższy przykład pobiera określony plik do katalogu lokalnego określonego w *destDir* parametru. Przykładowa metoda sprawia, że pobrana nazwa pliku jest unikatowa, dołączając datę i godzina.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="azure-java-sdk-v11"></a>[Zestaw Sdk Java platformy Azure w wersji 11](#tab/java11)

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

Inną często Azure Files operacją jest usunięcie pliku.

# <a name="azure-java-sdk-v12"></a>[Zestaw Sdk Java platformy Azure w wersji 12](#tab/java)

Poniższy kod usuwa określony plik. Najpierw przykład tworzy [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient) na podstawie *parametru dirName.* Następnie kod pobiera plik [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient) z klienta katalogu na podstawie *parametru fileName.* Na koniec przykładowa metoda wywołuje [metodę ShareFileClient.delete,](/java/api/com.azure.storage.file.share.sharefileclient.delete) aby usunąć plik.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="azure-java-sdk-v11"></a>[Zestaw Sdk Java platformy Azure w wersji 11](#tab/java11)

Poniższy kod usuwa plik o nazwie SampleFile.txt przechowywany wewnątrz katalogu o nazwie **sampledir.**

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

Jeśli chcesz dowiedzieć się więcej o innych interfejsach API usługi Azure Storage, skorzystaj z poniższych linków.

- [Platforma Azure dla deweloperów języka Java](/azure/developer/java)
- [Zestaw Azure SDK dla języka Java](https://github.com/azure/azure-sdk-for-java)
- [Zestaw Azure SDK dla systemu Android](https://github.com/azure/azure-sdk-for-android)
- [Biblioteka klienta udziału plików platformy Azure dla zestawu JAVA SDK](/java/api/overview/azure/storage-file-share-readme)
- [Interfejs API REST usług Azure Storage](/rest/api/storageservices/)
- [Blog zespołu usługi Azure Storage](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../common/storage-use-azcopy-v10.md)
- [Troubleshooting Azure Files problems - Windows (Rozwiązywanie problemów z usługą Azure Files — Windows)](storage-troubleshoot-windows-file-connection-problems.md)
