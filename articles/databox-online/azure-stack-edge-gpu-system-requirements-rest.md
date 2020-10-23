---
title: Microsoft Azure Stack Edge — wymagania dotyczące magazynu obiektów BLOB | Microsoft Docs
description: Poznaj obsługiwane wersje interfejsów API, zestawów SDK i bibliotek klienckich dla Azure Stack Edge magazynu obiektów BLOB
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: f99a3110880626b3a809e6bab5edc02398094547
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426214"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Azure Stack Edge — wymagania dotyczące magazynu obiektów BLOB

W tym artykule wymieniono wersje interfejsów API platformy Azure, biblioteki klienckie platformy Azure i narzędzia obsługiwane za pomocą Azure Stack Edge magazynu obiektów BLOB. Azure Stack Edge usługa BLOB Storage zapewnia funkcjonalność zarządzania obiektami BLOB z użyciem semantyki spójnej na platformie Azure. Ten artykuł podsumowuje także znane Azure Stack brzegowych obiektów BLOB Storage z usług Azure Storage.

Zalecamy dokładne zapoznanie się z informacjami przed nawiązaniem połączenia z magazynem obiektów Blob Azure Stack Edge, a następnie odwoływanie się do niego w razie potrzeby.

## <a name="storage-differences"></a>Różnice w magazynie

|     Promowanie                                             |     Azure Storage                                     |     Azure Stack Edge — magazyn obiektów BLOB |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Obsługiwane udziały plików SMB oparte na chmurze              |    Nieobsługiwane      |
|    Typ konta magazynu                                 |    Konta ogólnego przeznaczenia i usługi Azure Blob Storage    |    Tylko do celu ogólnego przeznaczenia|
|    Nazwa obiektu blob                                            |    1 024 znaków (2 048 bajtów)                     |    880 znaków (1 760 bajtów)|
|    Maksymalny rozmiar bloku obiektów BLOB                              |    4,75 TB (100 MB X 50 000 bloków)                   |    4,75 TB (100 MB x 50 000 bloków) dla Azure Stack Edge|
|    Maksymalny rozmiar obiektu BLOB strony                               |    8 TB                                               |    1 TB                   |
|    Rozmiar strony strony BLOB                                  |    512 bajtów                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Obsługiwane wersje interfejsu API

Następujące wersje interfejsów API usługi Azure Storage są obsługiwane przez Azure Stack brzegowych obiektów BLOB Storage.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack krawędź 2.1.1377.2170 na zewnątrz

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Obsługiwane biblioteki klienckie platformy Azure

W przypadku Azure Stack Edge magazynu obiektów BLOB istnieją określone biblioteki klienta i określone wymagania dotyczące sufiksu punktu końcowego. Punkty końcowe magazynu obiektów Blob Azure Stack Edge nie mają pełnej zgodności z najnowszą wersją interfejsu API REST platformy Azure Blob Storage. Zapoznaj się z [obsługiwanymi wersjami interfejsu API dla Azure Stack Edge](#supported-api-versions). W przypadku bibliotek klienta magazynu należy mieć świadomość wersji zgodnej z interfejsem API REST.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack krawędź 2.1.1377.2170 na zewnątrz

Następujące wersje biblioteki klienckiej platformy Azure są obsługiwane w przypadku Azure Stack Edge magazynu obiektów BLOB.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-the-php-client-via-composer---current"></a>Zainstaluj klienta PHP przez układacz-Current

Aby zainstalować klienta PHP za pośrednictwem programu Composer:

1. Utwórz plik o nazwie composer.jsw folderze głównym projektu o następującym kodzie (przykład używa usługi Azure Storage Blob Service).

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Pobierz `composer.phar` do katalogu głównego projektu.

3. Uruchom: PHAR języka PHP.


## <a name="endpoint-declaration"></a>Deklaracja punktu końcowego

W Azure Stack Edge do zestawu SDK magazynu obiektów blob, sufiks punktu końcowego — `<device serial number>.microsoftdatabox.com` określa Azure Stack domeny brzegowej. Aby uzyskać więcej informacji na temat punktu końcowego usługi BLOB Service, przejdź do obszaru [transfer danych za pomocą kont magazynu za pomocą Azure Stack EDGE Pro GPU](azure-stack-edge-j-series-deploy-add-storage-accounts.md).


## <a name="examples"></a>Przykłady

### <a name="net"></a>.NET

W przypadku Azure Stack Edge magazynu obiektów BLOB sufiks punktu końcowego jest określony w `app.config` pliku:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

W przypadku Azure Stack Edge magazynu obiektów BLOB sufiks punktu końcowego jest określony w instalatorze parametrów połączenia:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

W przypadku Azure Stack Edge magazynu obiektów BLOB sufiks punktu końcowego jest określony w wystąpieniu deklaracji:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

W przypadku Azure Stack Edge magazynu obiektów BLOB sufiks punktu końcowego jest określony w instalatorze parametrów połączenia:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

W przypadku Azure Stack Edge magazynu obiektów BLOB sufiks punktu końcowego jest określony w instalatorze parametrów połączenia:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

W przypadku Azure Stack Edge magazynu obiektów BLOB sufiks punktu końcowego jest określony w wystąpieniu deklaracji:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

W przypadku Azure Stack Edge magazynu obiektów BLOB sufiks punktu końcowego jest określony w instalatorze parametrów połączenia:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Następne kroki

* [Przygotowanie do wdrożenia Azure Stack EDGE Pro z procesorem GPU](azure-stack-edge-gpu-deploy-prep.md)
