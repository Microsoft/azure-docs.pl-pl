---
title: 'Szybki Start: Biblioteka usługi Azure Blob Storage V12 — JavaScript'
description: W tym przewodniku szybki start dowiesz się, jak używać biblioteki klienckiej usługi Azure Blob Storage w wersji 12 dla języka JavaScript w celu utworzenia kontenera i obiektu BLOB w magazynie obiektów BLOB (Object). Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać listę wszystkich obiektów blob w kontenerze.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 0883fd1546ac3b55ec4211e5048569556858de29
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179922"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK języka JavaScript V12 w Node.js

W tym przewodniku szybki start nauczysz się zarządzać obiektami BLOB przy użyciu Node.js. Obiekty blob są obiektami, które mogą przechowywać duże ilości danych tekstowych lub binarnych, w tym obrazy, dokumenty, multimedia strumieniowe i dane archiwalne. Będziesz przekazywać, pobierać i wyświetlać listy obiektów blob, a następnie tworzyć i usuwać kontenery.

Dodatkowe zasoby:

* [Dokumentacja referencyjna interfejsu API](/javascript/api/@azure/storage-blob)
* [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [Pakiet (Menedżer pakietów węzła)](https://www.npmjs.com/package/@azure/storage-blob)
* [Samples](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Konto usługi Azure Storage. [Utwórz konto magazynu](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji omówiono przygotowanie projektu do pracy z biblioteką klienta usługi Azure Blob Storage V12 dla języka JavaScript.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację JavaScript o nazwie *BLOB-Start-V12*.

1. W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla projektu.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Przejdź do nowo utworzonego katalogu *obiektów BLOB — szybki start-V12* .

    ```console
    cd blob-quickstart-v12
    ```

1. Utwórz nowy plik tekstowy o nazwie *package.json*. Ten plik definiuje projekt Node.js. Zapisz ten plik w katalogu *BLOB-Start-V12* . Poniżej znajduje się zawartość pliku:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Możesz umieścić własną nazwę w `author` polu dla pola, jeśli chcesz.

### <a name="install-the-package"></a>Zainstaluj pakiet

Mimo że w katalogu *obiektów BLOB-szybkiego startu V12* Zainstaluj bibliotekę klienta usługi Azure Blob Storage dla pakietu JavaScript za pomocą `npm install` polecenia. To polecenie odczytuje *package.jsw* pliku i instaluje bibliotekę klienta usługi Azure Blob Storage V12 dla pakietu JavaScript i wszystkie biblioteki, w których jest ona zależna.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwórz inny nowy plik tekstowy w edytorze kodu
1. Dodawanie `require` wywołań do ładowania modułów platformy Azure i Node.js
1. Utwórz strukturę dla programu, w tym podstawową obsługę wyjątków

    Oto kod:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const { v1: uuidv1} = require('uuid');

    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }

    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Zapisz nowy plik jako *blob-quickstart-v12.js* w katalogu *BLOB-Start-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektów

Usługa Azure Blob Storage jest zoptymalizowana pod kątem przechowywania dużych ilości danych bez struktury. Dane bez struktury są danymi, które nie są zgodne z żadnym modelem lub definicją danych, jak na przykład dane tekstowe lub binarne. Magazyn obiektów blob oferuje trzy typy zasobów:

* Konto magazynu
* Kontener na koncie magazynu
* Obiekt BLOB w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu obiektów blob](./media/storage-blobs-introduction/blob1.png)

Użyj następujących klas języka JavaScript do korzystania z tych zasobów:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): `BlobServiceClient` Klasa umożliwia manipulowanie zasobami usługi Azure Storage i kontenerami obiektów BLOB.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): `ContainerClient` Klasa umożliwia manipulowanie kontenerami usługi Azure Storage i ich obiektami BLOB.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): `BlobClient` Klasa umożliwia manipulowanie obiektami BLOB usługi Azure Storage.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące czynności z biblioteką klienta usługi Azure Blob Storage dla języka JavaScript:

* [Pobieranie parametrów połączenia](#get-the-connection-string)
* [Tworzenie kontenera](#create-a-container)
* [Przekazywanie obiektów BLOB do kontenera](#upload-blobs-to-a-container)
* [Wyświetlanie listy obiektów blob w kontenerze](#list-the-blobs-in-a-container)
* [Pobieranie obiektów blob](#download-blobs)
* [Usuwanie kontenera](#delete-a-container)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu ze zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie parametrów połączenia magazynu](#configure-your-storage-connection-string) .

Dodaj ten kod wewnątrz `main` funkcji:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>Tworzenie kontenera

Określ nazwę nowego kontenera. Poniższy kod dołącza wartość identyfikatora UUID do nazwy kontenera, aby upewnić się, że jest ona unikatowa.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Utwórz wystąpienie klasy [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) przez wywołanie metody [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) . Następnie Wywołaj metodę [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) , aby uzyskać odwołanie do kontenera. Na koniec Wywołaj polecenie [Utwórz](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) , aby utworzyć kontener na koncie magazynu.

Dodaj ten kod na końcu `main` funkcji:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów BLOB do kontenera

Następujący fragment kodu:

1. Tworzy ciąg tekstowy do przekazania do obiektu BLOB.
1. Pobiera odwołanie do obiektu [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) przez wywołanie metody [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) na [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) z sekcji [Create a Container](#create-a-container) .
1. Przekazuje dane ciągu tekstowego do obiektu BLOB przez wywołanie metody [przekazywania](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) .

Dodaj ten kod na końcu `main` funkcji:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Utwórz listę obiektów BLOB w kontenerze, wywołując metodę [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) . W takim przypadku tylko jeden obiekt BLOB został dodany do kontenera, więc operacja tworzenia listy zwraca tylko jeden obiekt BLOB.

Dodaj ten kod na końcu `main` funkcji:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz utworzony wcześniej obiekt BLOB, wywołując metodę [pobierania](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) . Przykładowy kod zawiera funkcję pomocnika `streamToString` , która jest używana do odczytywania Node.jsgo do odczytu strumienia w postaci ciągu.

Dodaj ten kod na końcu `main` funkcji:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Dodaj tę funkcję pomocnika *po* `main` funkcji:

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie całego kontenera przy użyciu metody [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) . Możesz również usunąć pliki lokalne, jeśli chcesz.

Dodaj ten kod na końcu `main` funkcji:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy ciąg tekstowy i przekazuje go do magazynu obiektów BLOB. W przykładzie zostanie wyświetlona lista obiektów BLOB w kontenerze, program pobierze obiekt BLOB i wyświetli pobrane dane.

W wierszu konsoli przejdź do katalogu zawierającego plik *blob-quickstart-v12.js* , a następnie wykonaj następujące `node` polecenie, aby uruchomić aplikację.

```console
node blob-quickstart-v12.js
```

Dane wyjściowe aplikacji są podobne do następujących:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Przechodzenie przez kod w debugerze i sprawdzanie [Azure Portal](https://portal.azure.com) w trakcie całego procesu. Sprawdź, czy kontener jest tworzony. Możesz otworzyć obiekt BLOB wewnątrz kontenera i wyświetlić jego zawartość.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób przekazywania, pobierania i wyświetlania listy obiektów BLOB za pomocą języka JavaScript.

Samouczki, przykłady, Przewodniki Szybki Start i inne dokumenty można znaleźć w temacie:

> [!div class="nextstepaction"]
> [Azure dla Centrum deweloperów języka JavaScript](/azure/developer/javascript/)

* Aby dowiedzieć się, jak wdrożyć aplikację sieci Web korzystającą z usługi Azure Blob Storage, zobacz [Samouczek: przekazywanie danych obrazu w chmurze za pomocą usługi Azure Storage](./storage-upload-process-images.md?preserve-view=true&tabs=javascript)
* Aby wyświetlić przykładowe aplikacje magazynu obiektów blob, przejdź do [biblioteki klienta usługi Azure Blob Storage V12 Samples JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).
* Aby dowiedzieć się więcej, zobacz [Biblioteka klienta usługi Azure Blob Storage dla języka JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
