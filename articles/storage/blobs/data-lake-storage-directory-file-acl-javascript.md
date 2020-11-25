---
title: Użyj języka JavaScript dla plików & list ACL w Azure Data Lake Storage Gen2
description: Biblioteka klienta usługi Azure Storage Data Lake dla języka JavaScript umożliwia zarządzanie katalogami oraz list kontroli dostępu do plików i katalogów (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: a929fcbc87a1ce11b226e9def46354c24a151a0c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913372"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Używanie języka JavaScript do zarządzania katalogami, plikami i listami ACL w Azure Data Lake Storage Gen2

W tym artykule pokazano, jak używać języka JavaScript do tworzenia katalogów, plików i uprawnień w ramach kont magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS) i zarządzać nimi. 

[Pakiet (Menedżer pakietów węzła)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Przekaż opinię](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](../common/storage-account-create.md) instrukcjami, aby je utworzyć.
> * Jeśli używasz tego pakietu w aplikacji Node.js, musisz mieć Node.js 8.0.0 lub wyższy.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Zainstaluj Data Lake bibliotekę kliencką dla języka JavaScript, otwierając okno terminalu, a następnie wpisując następujące polecenie.

```javascript
npm install @azure/storage-file-datalake
```

Zaimportuj `storage-file-datalake` pakiet, umieszczając tę instrukcję w górnej części pliku kodu. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
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
> Ta metoda autoryzacji działa tylko w przypadku aplikacji Node.js. Jeśli planujesz uruchamianie kodu w przeglądarce, możesz autoryzować się przy użyciu Azure Active Directory (AD). 

### <a name="connect-by-using-azure-active-directory-ad"></a>Łączenie przy użyciu Azure Active Directory (AD)

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

## <a name="manage-access-control-lists-acls"></a>Zarządzanie listami kontroli dostępu (ACL)

Uprawnienia dostępu do katalogów i plików można uzyskiwać, ustawiać i aktualizować.

> [!NOTE]
> Jeśli używasz usługi Azure Active Directory (Azure AD) do autoryzacji dostępu, upewnij się, że podmiot zabezpieczeń ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [Kontrola dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-a-directory-acl"></a>Zarządzanie listą ACL katalogów

Ten przykład pobiera i ustawia listę ACL katalogu o nazwie `my-directory` . Ten przykład daje właścicielowi uprawnień Odczyt, zapis i wykonywanie, daje grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania, a ponadto daje wszystkim innym osobom dostęp do odczytu.

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzacji dostępu ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [Kontrola dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

Możesz również uzyskać i ustawić listę kontroli dostępu dla katalogu głównego kontenera. Aby uzyskać katalog główny, należy przekazać pusty ciąg ( `/` ) do metody **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="manage-a-file-acl"></a>Zarządzanie listą ACL plików

Ten przykład pobiera i ustawia listę ACL pliku o nazwie `upload-file.txt` . Ten przykład daje właścicielowi uprawnień Odczyt, zapis i wykonywanie, daje grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania, a ponadto daje wszystkim innym osobom dostęp do odczytu.

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzacji dostępu ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [Kontrola dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="see-also"></a>Zobacz też

* [Pakiet (Menedżer pakietów węzła)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Przekaż opinię](https://github.com/Azure/azure-sdk-for-java/issues)