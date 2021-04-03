---
title: Wymagania dotyczące urządzenie Data Box magazynu obiektów BLOB Microsoft Azure | Microsoft Docs
description: Poznaj obsługiwane wersje interfejsów API, zestawów SDK i bibliotek klienckich dla Azure Data Box magazynu obiektów BLOB
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/05/2020
ms.author: alkohli
ms.openlocfilehash: ac5f2de383066d6ee399dac3b0ad8c365b2e72bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91744110"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure Data Box wymagania dotyczące magazynu obiektów BLOB

W tym artykule wymieniono wersje interfejsów API platformy Azure, biblioteki klienckie platformy Azure i narzędzia obsługiwane przez urządzenie Data Box magazynu obiektów BLOB. Urządzenie Data Box Storage BLOB oferuje funkcje zarządzania obiektami BLOB z semantyką spójną na platformie Azure. Ten artykuł podsumowuje także znane Azure Data Box różnice w magazynie obiektów blob z usług Azure Storage.

Zalecamy dokładne zapoznanie się z informacjami przed nawiązaniem połączenia z magazynem urządzenie Data Box obiektów blob, a następnie odwoływanie się do niego w razie potrzeby.


## <a name="storage-differences"></a>Różnice w magazynie

|     Cecha                                             |     Azure Storage                                     |     Magazyn obiektów blob usługi Data Box |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Obsługiwane udziały plików SMB oparte na chmurze              |    Nieobsługiwane      |
|    Szyfrowanie usługi dla danych magazynowanych                  |    256-bitowe szyfrowanie AES                             |    256-bitowe szyfrowanie AES |
|    Typ konta magazynu                                 |    Konta ogólnego przeznaczenia i usługi Azure Blob Storage    |    Tylko do celu ogólnego przeznaczenia|
|    Nazwa obiektu blob                                            |    1 024 znaków (2 048 bajtów)                     |    880 znaków (1 760 bajtów)|
|    Maksymalny rozmiar bloku obiektów BLOB                              |    4,75 TB (100 MB X 50 000 bloków)                   |    4,75 TB (100 MB x 50 000 bloków) dla Azure Data Box v 3,0 lub nowszego.|
|    Maksymalny rozmiar obiektu BLOB strony                               |    8 TB                                               |    1 TB                   |
|    Rozmiar strony strony BLOB                                  |    512 bajtów                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Obsługiwane wersje interfejsu API

Następujące wersje interfejsów API usługi Azure Storage są obsługiwane przez urządzenie Data Box magazynu obiektów BLOB.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3,0 do wewnątrz

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Obsługiwane biblioteki klienckie platformy Azure

W przypadku usługi urządzenie Data Box BLOB Storage istnieją określone biblioteki klienta i określone wymagania dotyczące sufiksu punktu końcowego. Punkty końcowe magazynu obiektów BLOB urządzenie Data Box nie mają pełnej zgodności z najnowszą wersją interfejsu API REST platformy Azure Blob Storage. Zapoznaj się z [obsługiwanymi wersjami Azure Data Box 3,0](#supported-api-versions). W przypadku bibliotek klienta magazynu należy mieć świadomość wersji zgodnej z interfejsem API REST.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3,0 do wewnątrz

Obsługiwane są następujące wersje biblioteki klienta platformy Azure dla urządzenie Data Box magazynu obiektów BLOB.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-php-client-via-composer---current"></a>Zainstaluj klienta PHP przez układacz-Current

Aby zainstalować za pośrednictwem programu Composer: (Zrób obiekt BLOB jako przykład).
1. Utwórz plik o nazwie composer.jsw folderze głównym projektu o następującym kodzie:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Pobierz `composer.phar` do katalogu głównego projektu.

3. Uruchom: PHAR języka PHP.

### <a name="endpoint-declaration"></a>Deklaracja punktu końcowego

W urządzenie Data Box zestaw SDK magazynu obiektów BLOB — sufiks punktu końcowego — `<device serial number>.microsoftdatabox.com` identyfikuje domenę urządzenie Data Box. Aby uzyskać więcej informacji o punkcie końcowym usługi BLOB Service, przejdź do pozycji [Połącz za pośrednictwem urządzenie Data Box BLOB Storage](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Przykłady

### <a name="net"></a>.NET

W przypadku urządzenie Data Box obiektu BLOB Storage sufiks punktu końcowego jest określony w `app.config` pliku:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

W przypadku urządzenie Data Box BLOB Storage sufiks punktu końcowego jest określony w instalatorze parametrów połączenia:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

W przypadku urządzenie Data Box obiektu BLOB Storage sufiks punktu końcowego jest określony w wystąpieniu deklaracji:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

W przypadku urządzenie Data Box BLOB Storage sufiks punktu końcowego jest określony w instalatorze parametrów połączenia:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

W przypadku urządzenie Data Box BLOB Storage sufiks punktu końcowego jest określony w instalatorze parametrów połączenia:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

W przypadku urządzenie Data Box obiektu BLOB Storage sufiks punktu końcowego jest określony w wystąpieniu deklaracji:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

W przypadku urządzenie Data Box BLOB Storage sufiks punktu końcowego jest określony w instalatorze parametrów połączenia:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie Azure Data Box](data-box-deploy-ordered.md)
