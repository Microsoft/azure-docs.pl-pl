---
title: Używanie języka JavaScript (Node.js) do zarządzania danymi w programie Azure Data Lake Storage Gen2
description: Biblioteka klienta usługi Azure Storage Data Lake dla języka JavaScript umożliwia zarządzanie katalogami i plikami na kontach magazynu z włączoną hierarchiczną przestrzenią nazw.
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 678af3e2fb4111593ece0cc2cdf3811cf0e793a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774766"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Używanie zestawu SDK języka JavaScript w Node.js do zarządzania katalogami i plikami w programie Azure Data Lake Storage Gen2

W tym artykule przedstawiono sposób użycia Node.js do tworzenia katalogów i plików oraz zarządzania nimi na kontach magazynu, które mają hierarchiczną przestrzeń nazw.

Aby dowiedzieć się, jak pobierać, ustawiać i aktualizować listy kontroli dostępu (ACL) katalogów i plików, zobacz [Używanie zestawu SDK języka JavaScript w Node.js do zarządzania listami ACL w programie Azure Data Lake Storage Gen2](data-lake-storage-acl-javascript.md).

[Pakiet (Menedżer pakietów węzła)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Przekaż opinię](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

- Konto magazynu, dla którego włączono hierarchiczną przestrzeń nazw. Postępuj zgodnie z [tymi](create-data-lake-storage-account.md) instrukcjami, aby je utworzyć.

- Jeśli używasz tego pakietu w aplikacji Node.js, musisz mieć Node.js 8.0.0 lub wyższy.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Zainstaluj Data Lake bibliotekę kliencką dla języka JavaScript, otwierając okno terminalu, a następnie wpisując następujące polecenie.

```javascript
npm install @azure/storage-file-datalake
```

Zaimportuj `storage-file-datalake` pakiet, umieszczając tę instrukcję w górnej części pliku kodu. 

```javascript
const {
AzureStorageDataLake,
DataLakeServiceClient,
StorageSharedKeyCredential
} = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Połącz z kontem 

Aby użyć fragmentów kodu w tym artykule, należy utworzyć wystąpienie **DataLakeServiceClient** reprezentujące konto magazynu. 

### <a name="connect-by-using-an-account-key"></a>Nawiązywanie połączenia przy użyciu klucza konta

Jest to najprostszy sposób nawiązywania połączenia z kontem. 

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu klucza konta.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```

> [!NOTE]
> Ta metoda autoryzacji działa tylko w przypadku aplikacji Node.js. Jeśli planujesz uruchamianie kodu w przeglądarce, możesz autoryzować się przy użyciu Azure Active Directory (Azure AD).

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Nawiązywanie połączenia przy użyciu Azure Active Directory (Azure AD)

Za pomocą [biblioteki Azure Identity Client Library for js](https://www.npmjs.com/package/@azure/identity) można uwierzytelniać aplikację w usłudze Azure AD.

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu identyfikatora klienta, klucza tajnego klienta i identyfikatora dzierżawy.  Aby uzyskać te wartości, zobacz [pozyskiwanie tokenu z usługi Azure AD w celu autoryzowania żądań z aplikacji klienckiej](../common/storage-auth-aad-app.md).

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz dokumentację [biblioteki klienta usługi Azure Identity for js](https://www.npmjs.com/package/@azure/identity) .

## <a name="create-a-container"></a>Tworzenie kontenera

Kontener działa jako system plików dla plików. Można go utworzyć, pobierając wystąpienie **FileSystemClient** , a następnie wywołując metodę **FileSystemClient. Create** .

Ten przykład tworzy kontener o nazwie `my-file-system` . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu, pobierając wystąpienie **DirectoryClient** , a następnie wywołując metodę **DirectoryClient. Create** .

Ten przykład dodaje katalog o nazwie `my-directory` do kontenera. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub Przenieś katalog, wywołując metodę **DirectoryClient. Rename** . Przekaż ścieżkę do żądanego katalogu jako parametr. 

Ten przykład zmienia nazwę podkatalogu na nazwę `my-directory-renamed` .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Ten przykład przenosi katalog o nazwie `my-directory-renamed` do podkatalogu o nazwie `my-directory-2` . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog, wywołując metodę **DirectoryClient. Delete** .

Ten przykład usuwa katalog o nazwie `my-directory` .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="upload-a-file-to-a-directory"></a>Przekaż plik do katalogu

Najpierw Odczytaj plik. Ten przykład używa modułu Node.js `fs` . Następnie Utwórz odwołanie do pliku w katalogu docelowym, tworząc wystąpienie **FileClient** , a następnie wywołując metodę **FileClient. Create** . Przekaż plik, wywołując metodę **FileClient. Append** . Upewnij się, że ukończono przekazywanie, wywołując metodę **FileClient. Flush** .

Ten przykład przekazuje plik tekstowy do katalogu o nazwie `my-directory` .

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="download-from-a-directory"></a>Pobierz z katalogu

Najpierw Utwórz wystąpienie **FileSystemClient** reprezentujące plik, który chcesz pobrać. Użyj metody **FileSystemClient. Read** , aby odczytać plik. Następnie napisz plik. Ten przykład używa modułu Node.js, `fs` Aby to zrobić. 

> [!NOTE]
> Ta metoda pobierania pliku działa tylko w przypadku aplikacji Node.js. Jeśli planujesz uruchamianie kodu w przeglądarce, zapoznaj się z artykułem " [usługa Azure Storage file Data Lake Client Library for JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) plik Readme, aby zapoznać się z przykładem, jak to zrobić w przeglądarce.

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
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
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

W tym przykładzie program drukuje nazwy poszczególnych katalogów i plików znajdujących się w katalogu o nazwie `my-directory` .

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Zobacz też

- [Pakiet (Menedżer pakietów węzła)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [Samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Przekaż opinię](https://github.com/Azure/azure-sdk-for-java/issues)