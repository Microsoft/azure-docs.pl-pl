---
title: Konfigurowanie parametrów połączenia
titleSuffix: Azure Storage
description: Skonfiguruj parametry połączenia dla konta usługi Azure Storage. Parametry połączenia zawierają informacje potrzebne do autoryzacji dostępu do konta magazynu z aplikacji w czasie wykonywania przy użyciu autoryzacji klucza współużytkowanego.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d7ca1707c89f03683960822591065143d3f8aa4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92783882"
---
# <a name="configure-azure-storage-connection-strings"></a>Konfiguracja parametrów połączenia usługi Azure Storage

Parametry połączenia zawierają informacje o autoryzacji wymagane przez aplikację do uzyskiwania dostępu do danych na koncie usługi Azure Storage w środowisku uruchomieniowym przy użyciu autoryzacji klucza współużytkowanego. Parametry połączenia można skonfigurować w taki sposób, aby:

* Nawiąż połączenie z emulatorem magazynu azurite.
* Uzyskaj dostęp do konta magazynu na platformie Azure.
* Dostęp do określonych zasobów na platformie Azure za pośrednictwem sygnatury dostępu współdzielonego (SAS).

Aby dowiedzieć się, jak wyświetlać klucze dostępu do konta i kopiować parametry połączenia, zobacz [Zarządzanie kluczami dostępu do konta magazynu](storage-account-keys-manage.md).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="store-a-connection-string"></a>Przechowywanie parametrów połączenia

Aplikacja musi uzyskać dostęp do parametrów połączenia w czasie wykonywania, aby autoryzować żądania kierowane do usługi Azure Storage. Istnieje kilka opcji przechowywania parametrów połączenia:

* Parametry połączenia można przechowywać w zmiennej środowiskowej.
* Aplikacja działająca na komputerze stacjonarnym lub na urządzeniu może przechowywać parametry połączenia w pliku **app.config** lub **web.config** . Dodaj parametry połączenia do sekcji **AppSettings** w tych plikach.
* W aplikacji uruchomionej w usłudze w chmurze platformy Azure można przechowywać parametry połączenia w [pliku schematu konfiguracji usługi platformy Azure (cscfg)](/previous-versions/azure/reference/ee758710(v=azure.100)). Dodaj parametry połączenia do sekcji **ConfigurationSettings** w pliku konfiguracji usługi.

Przechowywanie parametrów połączenia w pliku konfiguracji ułatwia aktualizowanie parametrów połączenia w celu przełączania się między [emulatorem magazynu azurite](../common/storage-use-azurite.md) a kontem usługi Azure Storage w chmurze. Wystarczy edytować parametry połączenia, aby wskazywały na środowisko docelowe.

Możesz użyć [Configuration Manager Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) , aby uzyskać dostęp do parametrów połączenia w czasie wykonywania, niezależnie od miejsca, w którym działa aplikacja.

## <a name="configure-a-connection-string-for-azurite"></a>Konfigurowanie parametrów połączenia dla azurite

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Aby uzyskać więcej informacji na temat azurite, zobacz [Używanie emulatora azurite na potrzeby tworzenia lokalnych magazynów platformy Azure](../common/storage-use-azurite.md).

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Konfigurowanie parametrów połączenia dla konta usługi Azure Storage

Aby utworzyć parametry połączenia dla konta usługi Azure Storage, użyj następującego formatu. Wskaż, czy chcesz nawiązać połączenie z kontem magazynu za pośrednictwem protokołu HTTPS (zalecane) lub HTTP, Zastąp ciąg `myAccountName` nazwą konta magazynu i Zastąp ciąg `myAccountKey` kluczem dostępu do konta:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Na przykład parametry połączenia mogą wyglądać podobnie do następujących:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Mimo że usługa Azure Storage obsługuje zarówno protokół HTTP, jak i HTTPS w parametrach połączenia, *zdecydowanie zaleca się użycie protokołu HTTPS*.

> [!TIP]
> Parametry połączenia konta magazynu można znaleźć w [Azure Portal](https://portal.azure.com). Przejdź do **ustawień**  >  **klucze dostępu** w bloku menu konta magazynu, aby wyświetlić parametry połączenia dla podstawowych i pomocniczych kluczy dostępu.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Tworzenie parametrów połączenia przy użyciu sygnatury dostępu współdzielonego

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Utwórz parametry połączenia dla jawnego punktu końcowego magazynu

Możesz określić jawne punkty końcowe usługi w parametrach połączenia zamiast korzystać z domyślnych punktów końcowych. Aby utworzyć parametry połączenia określające jawny punkt końcowy, określ kompletny punkt końcowy usługi dla każdej usługi, w tym specyfikację protokołu (HTTPS (zalecane) lub HTTP), w następującym formacie:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Jednym z scenariuszy, w którym można określić jawny punkt końcowy, jest zamapowanie punktu końcowego magazynu obiektów BLOB na [domenę niestandardową](../blobs/storage-custom-domain-name.md). W takim przypadku można określić niestandardowy punkt końcowy dla magazynu obiektów BLOB w parametrach połączenia. Opcjonalnie można określić domyślne punkty końcowe dla innych usług, jeśli są one używane przez aplikację.

Oto przykład parametrów połączenia, które określają jawny punkt końcowy dla Blob service:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

W tym przykładzie określono jawne punkty końcowe dla wszystkich usług, w tym domenę niestandardową dla Blob service:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

Wartości punktów końcowych w parametrach połączenia są używane do konstruowania identyfikatorów URI żądań do usług magazynu, a także do podyktowania formy identyfikatorów URI zwracanych do kodu.

Jeśli punkt końcowy magazynu został zamapowany na domenę niestandardową i pominięto ten punkt końcowy z parametrów połączenia, nie będzie można używać tych parametrów połączenia do uzyskiwania dostępu do danych w tej usłudze z kodu.

Aby uzyskać więcej informacji na temat konfigurowania domeny niestandardowej dla usługi Azure Storage, zobacz [Mapowanie domeny niestandardowej na punkt końcowy usługi azure BLOB Storage](../blobs/storage-custom-domain-name.md).

> [!IMPORTANT]
> Wartości punktu końcowego usługi w parametrach połączenia muszą mieć poprawnie sformułowane identyfikatory URI, w tym `https://` (zalecane) lub `http://` .

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Tworzenie parametrów połączenia przy użyciu sufiksu punktu końcowego

Aby utworzyć parametry połączenia dla usługi magazynu w regionach lub wystąpieniach z różnymi sufiksami punktów końcowych, takimi jak Azure Chiny 21Vianet lub Azure Government, użyj następującego formatu parametrów połączenia. Wskaż, czy chcesz nawiązać połączenie z kontem magazynu za pośrednictwem protokołu HTTPS (zalecane) lub protokołu HTTP, Zastąp ciąg `myAccountName` nazwą konta magazynu, Zastąp ciąg `myAccountKey` kluczem dostępu do konta i Zastąp ciąg `mySuffix` identyfikatorem URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Oto przykładowe parametry połączenia dla usług magazynu na platformie Azure (Chiny):

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analizowanie parametrów połączenia

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z emulatora azurite na potrzeby tworzenia lokalnych magazynów platformy Azure](../common/storage-use-azurite.md)
* [Eksploratory usługi Azure Storage](storage-explorers.md)
* [Używanie sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)