---
title: Wymagania dotyczące urządzenie Data Box magazynu obiektów BLOB Microsoft Azure | Microsoft Docs
description: Poznaj obsługiwane wersje interfejsów API, zestawów SDK i bibliotek klienckich dla Azure Data Box magazynu obiektów BLOB
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "61436498"
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
|    Maksymalny rozmiar bloku obiektów BLOB                              |    4,75 TB (100 MB X 50 000 bloków)                   |    4,75 TB (100 MB x 50 000 bloków) dla Azure Data Box v 1,8 lub nowszego.|
|    Maksymalny rozmiar obiektu BLOB strony                               |    8 TB                                               |    1 TB                   |
|    Rozmiar strony strony BLOB                                  |    512 bajtów                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Obsługiwane wersje interfejsu API

Następujące wersje interfejsów API usługi Azure Storage są obsługiwane z użyciem urządzenie Data Box magazynu obiektów blob:

Azure Data Box 1,8 do wewnątrz

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [17.04.2017](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)|
## <a name="supported-azure-client-libraries"></a>Obsługiwane biblioteki klienckie platformy Azure

W przypadku usługi urządzenie Data Box BLOB Storage istnieją określone biblioteki klienta i określone wymagania dotyczące sufiksu punktu końcowego. Punkty końcowe magazynu obiektów BLOB urządzenie Data Box nie mają pełnej zgodności z najnowszą wersją interfejsu API REST platformy Azure Blob Storage, zobacz [obsługiwane wersje Azure Data Box 1,8](#supported-api-versions). W przypadku bibliotek klienta magazynu należy mieć świadomość wersji zgodnej z interfejsem API REST.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1,8 do wewnątrz

| Biblioteka kliencka     |urządzenie Data Box obsługiwana wersja magazynu obiektów BLOB     | Link   |     Specyfikacja punktu końcowego      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Pakiet NuGet:https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>Wersja usługi GitHub:https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    Plik app.config                 |
|    Java                |    7.0.0                                           |    Pakiet Maven:https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>Wersja usługi GitHub:https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Konfiguracja parametrów połączenia         |
|    Node.js             |    2.8.3                                           |    Link NPM: https://www.npmjs.com/package/azure-storage (Run: `npm install azure-storage@2.7.0` )   <br>Wersja usługi GitHub:https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Deklaracja wystąpienia usługi    |
|    C++                 |    5.2.0                                           |    Pakiet NuGet:https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>Wersja usługi GitHub:https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Konfiguracja parametrów połączenia         |
|    PHP                 |    1.2.0                                           |    Wersja usługi GitHub:<br>Wspólnahttps://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Tworzeniahttps://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Zainstaluj za pośrednictwem programu Composer (aby dowiedzieć się więcej, zobacz szczegóły poniżej).                                                                                                             |    Konfiguracja parametrów połączenia         |
|    Python              |    1.1.0                                           |    Wersja usługi GitHub:<br>Wspólnahttps://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Tworzeniahttps://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Deklaracja wystąpienia usługi    |
|    Ruby                |    1.0.1                                           |    Pakiet RubyGems:<br>Wspólnahttps://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Tworzeniahttps://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>Wersja usługi GitHub:<br>Wspólnahttps://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Tworzeniahttps://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Konfiguracja parametrów połączenia         |



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

Punkt końcowy magazynu obiektów Blob Azure Data Box obejmuje dwie części: nazwę regionu i domenę urządzenie Data Box. W zestawie SDK urządzenie Data Box BLOB Storage domyślny punkt końcowy to `\<serial no. of the device>.microsoftdatabox.com` .  Aby uzyskać więcej informacji o punkcie końcowym usługi BLOB Service, przejdź do pozycji [Połącz za pośrednictwem urządzenie Data Box BLOB Storage](data-box-deploy-copy-data-via-rest.md).
 
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
