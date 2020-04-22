---
title: 'Szybki start: biblioteka magazynu obiektów Blob platformy Azure w wersji 12 — Java'
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć kontener i obiektowy magazyn za pomocą biblioteki klienta magazynu obiektów Blob platformy Azure w wersji 12 dla języka Java. Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać listę wszystkich obiektów blob w kontenerze.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/27/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 15e0eaaeb407ad22220faa7d085eea805fe62679
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731346"
---
# <a name="quickstart-manage-blobs-with-java-v12-sdk"></a>Szybki start: zarządzanie obiektami blob za pomocą sdk Java w wersji 12

W tym przewodniku Szybki start nauczysz się zarządzać obiektami blob za pomocą języka Java. Obiekty blob to obiekty, które mogą zawierać duże ilości danych tekstowych lub binarnych, w tym obrazy, dokumenty, multimedia strumieniowe i dane archiwum. Będziesz przesyłać, pobierać i wystawiać obiekty blob, a także tworzyć i usuwać kontenery.

[Dokumentacja](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/index.html) | referencyjna interfejsu API[Przykłady pakietu kodu źródłowego](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob) | (Maven) kod[źródłowy](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples) [(Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob?repo=jcenter) | 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Konto usługi Azure Storage. [Utwórz konto magazynu](../common/storage-account-create.md).
- [Zestaw Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) w wersji 8 lub wyższej.
- [Apache Maven](https://maven.apache.org/download.cgi).

> [!NOTE]
> Aby rozpocząć pracę z poprzednią wersją SDK, zobacz [Szybki start: Zarządzanie obiektami blob za pomocą pliku Java v8 SDK](storage-quickstart-blobs-java-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji przeprowadzi Cię proces przygotowania projektu do pracy z biblioteką klienta magazynu obiektów Blob platformy Azure w wersji 12 dla oprogramowania Java.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację Java o nazwie *blob-szybki start-v12*.

1. W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj maven, aby utworzyć nową aplikację konsoli o nazwie *blob-szybki start-v12*. Wpisz następujące polecenie **mvn,** aby utworzyć "Hello world!" projektu Java.

   ```console
   mvn archetype:generate -DgroupId=com.blobs.quickstart \
                          -DartifactId=blob-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. Dane wyjściowe z generowania projektu powinny wyglądać mniej więcej tak:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/blobs/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\QuickStarts\blob-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  7.056 s
    [INFO] Finished at: 2019-10-23T11:09:21-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *blob-quickstart-v12* folder.

   ```console
   cd blob-quickstart-v12
   ```

1. Z boku katalogu *blob-szybki start-v12* należy utworzyć inny katalog o nazwie *data*. W tym miejscu zostaną utworzone i przechowywane pliki danych obiektów blob.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Otwórz plik *pom.xml* w edytorze tekstu. Dodaj następujący element zależności do grupy zależności.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Przejdź do *katalogu /src/main/java/com/blobs/quickstart*
1. Otwieranie pliku *App.java* w edytorze
1. Usuwanie `System.out.println("Hello world!");` instrukcji
1. Dodawanie `import` dyrektyw

Oto kod:

```java
package com.blobs.quickstart;

/**
 * Azure blob storage v12 SDK quickstart
 */
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektu

Usługa Azure Blob storage jest zoptymalizowana pod kątem przechowywania ogromnych ilości nieustrukturyzowanych danych. Dane bez struktury są danymi, które nie są zgodne z żadnym modelem lub definicją danych, jak na przykład dane tekstowe lub binarne. Magazyn obiektów blob oferuje trzy typy zasobów:

* Konto magazynu
* Kontener na koncie magazynu
* Obiekt blob w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu obiektów blob](./media/storage-blobs-introduction/blob1.png)

Użyj następujących klas Java do interakcji z tymi zasobami:

* [BlobServiceClient:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClient.html) `BlobServiceClient` Klasa umożliwia manipulowanie zasobami usługi Azure Storage i kontenerami obiektów blob. Konto magazynu udostępnia obszar nazw najwyższego poziomu dla usługi obiektów Blob.
* [BlobServiceClientBuilder:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClientBuilder.html) `BlobServiceClientBuilder` Klasa zapewnia płynny interfejs API konstruktora, aby `BlobServiceClient` ułatwić konfigurację i tworzenie wystąpienia obiektów.
* [BlobContainerClient:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) `BlobContainerClient` Klasa umożliwia manipulowanie kontenerami usługi Azure Storage i ich obiektów blob.
* [BlobClient:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html) `BlobClient` Klasa umożliwia manipulowanie obiektów blob usługi Azure Storage.
* [BlobItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/models/BlobItem.html): `BlobItem` Klasa reprezentuje poszczególne obiekty blob zwrócone z wywołania do `listBlobsFlat`.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta magazynu obiektów Blob platformy Azure dla środowiska Java:

* [Pobieranie parametrów połączenia](#get-the-connection-string)
* [Tworzenie kontenera](#create-a-container)
* [Przekazywanie obiektów blob do kontenera](#upload-blobs-to-a-container)
* [Wyświetlanie listy obiektów blob w kontenerze](#list-the-blobs-in-a-container)
* [Pobieranie obiektów blob](#download-blobs)
* [Usuwanie kontenera](#delete-a-container)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu ze zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie ciągu połączenia magazynu.](#configure-your-storage-connection-string)

Dodaj ten kod `Main` wewnątrz metody:

```java
System.out.println("Azure Blob storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Tworzenie kontenera

Zdecyduj o nazwie nowego kontenera. Poniższy kod dołącza wartość UUID do nazwy kontenera, aby upewnić się, że jest unikatowa.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Następnie należy utworzyć wystąpienie [klasy BlobContainerClient,](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) a następnie wywołać metodę [create,](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#create--) aby faktycznie utworzyć kontener na koncie magazynu.

Dodaj ten kod na `Main` końcu metody:

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów blob do kontenera

Fragment kodu w brzmieniu:

1. Tworzy plik tekstowy w lokalnym katalogu *danych.*
1. Pobiera odwołanie do [obiektu BlobClient,](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html) wywołując [getBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#getBlobClient-java.lang.String-) metody w kontenerze z [Create sekcji kontenera.](#create-a-container)
1. Przekazuje lokalny plik tekstowy do obiektu blob, wywołując [metodę uploadFromFile.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html#uploadFromFile-java.lang.String-) Ta metoda tworzy obiekt blob, jeśli jeszcze nie istnieje, ale nie zastąpi go, jeśli tak.

Dodaj ten kod na `Main` końcu metody:

```java
// Create a local file in the ./data/ directory for uploading and downloading
String localPath = "./data/";
String fileName = "quickstart" + java.util.UUID.randomUUID() + ".txt";
File localFile = new File(localPath + fileName);

// Write text to the file
FileWriter writer = new FileWriter(localPath + fileName, true);
writer.write("Hello, World!");
writer.close();

// Get a reference to a blob
BlobClient blobClient = containerClient.getBlobClient(fileName);

System.out.println("\nUploading to Blob storage as blob:\n\t" + blobClient.getBlobUrl());

// Upload the blob
blobClient.uploadFromFile(localPath + fileName);
```

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Lista obiektów blob w kontenerze, wywołując [listBlobs](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#listBlobs--) metody. W takim przypadku tylko jeden obiekt blob został dodany do kontenera, więc operacja listy zwraca tylko ten jeden obiekt blob.

Dodaj ten kod na `Main` końcu metody:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz poprzednio utworzony obiekt blob, wywołując [metodę downloadToFile.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/specialized/BlobClientBase.html#downloadToFile-java.lang.String-) Przykładowy kod dodaje sufiks "POBIERZ" do nazwy pliku, dzięki czemu można zobaczyć oba pliki w lokalnym systemie plików.

Dodaj ten kod na `Main` końcu metody:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby utworzone przez aplikację, usuwając cały kontener przy użyciu metody [usuwania.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#delete--) Usuwa również pliki lokalne utworzone przez aplikację.

Aplikacja wstrzymuje wprowadzanie danych przez użytkownika, wywołując `System.console().readLine()` przed usunięciem obiektów blob, kontenera i plików lokalnych. Jest to dobra okazja, aby sprawdzić, czy zasoby zostały utworzone poprawnie, zanim zostaną usunięte.

Dodaj ten kod na `Main` końcu metody:

```java
// Clean up
System.out.println("\nPress the Enter key to begin clean up");
System.console().readLine();

System.out.println("Deleting blob container...");
containerClient.delete();

System.out.println("Deleting the local source and downloaded files...");
localFile.delete();
downloadedFile.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy plik testowy w folderze lokalnym i przekazuje go do magazynu obiektów Blob. W przykładzie następnie wyświetla listę obiektów blob w kontenerze i pobiera plik o nowej nazwie, dzięki czemu można porównać stare i nowe pliki.

Przejdź do katalogu zawierającego plik *pom.xml* i skompiluj projekt za pomocą następującego `mvn` polecenia.

```console
mvn compile
```

Następnie skompiluj pakiet.

```console
mvn package
```

Uruchom następujące `mvn` polecenie, aby wykonać aplikację.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

```output
Azure Blob storage v12 - Java quickstart sample

Uploading to Blob storage as blob:
        https://mystorageacct.blob.core.windows.net/quickstartblobsf9aa68a5-260e-47e6-bea2-2dcfcfa1fd9a/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Listing blobs...
        quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Downloading blob to
        ./data/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Przed rozpoczęciem procesu oczyszczania sprawdź folder *danych* pod kątem dwóch plików. Możesz je otworzyć i sprawdzić, czy są identyczne.

Po zweryfikowaniu plików naciśnij klawisz **Enter,** aby usunąć pliki testowe i zakończyć prezentację.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak przesyłać, pobierać i wystawiać obiekty blob za pomocą oprogramowania Java.

Aby wyświetlić przykładowe aplikacje magazynu obiektów Blob, przejdź do:

> [!div class="nextstepaction"]
> [Przykłady środowiska Azure Blob Storage SDK w wersji 12 Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* Aby dowiedzieć się więcej, zobacz [zestaw SDK platformy Azure dla języka Java](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md).
* Aby uzyskać samouczki, przykłady, przewodniki Szybki start i inną dokumentację, odwiedź witrynę [Azure for Java cloud developers](/azure/developer/java/).
