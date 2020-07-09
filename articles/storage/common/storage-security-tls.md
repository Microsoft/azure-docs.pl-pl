---
title: Włączanie zabezpieczeń protokołu TLS przy użyciu platformy .NET
titleSuffix: Azure Storage
description: Dowiedz się, jak włączyć protokół TLS 1,2 przy użyciu biblioteki klienckiej platformy .NET dla usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 81c9a8fe9513f1f8fc65ad64b34f0fb04383569b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75371806"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Włączanie bezpiecznego protokołu TLS dla klienta usługi Azure Storage

Transport Layer Security (TLS) i SSL (SSL) to protokoły kryptograficzne zapewniające bezpieczeństwo komunikacji w sieci komputerowej. Protokół SSL 1,0, 2,0 i 3,0 okazał się podatny na ataki. Są one zabronione przez RFC. Protokół TLS 1,0 jest niebezpieczny dla korzystania z szyfrowania bloku niezabezpieczonego (DES CBC i RC2 CBC) i szyfrowania strumienia (RC4). Rada PCI zaproponowała również migrację do wyższych wersji protokołu TLS. Aby uzyskać więcej informacji, zobacz [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Usługa Azure Storage zatrzymała protokół SSL 3,0 od 2015 i używa protokołu TLS 1,2 na publicznych punktach końcowych HTTPs, ale protokoły TLS 1,0 i TLS 1,1 nadal są obsługiwane w celu zapewnienia zgodności z poprzednimi

Aby zapewnić bezpieczne i zgodne połączenie z usługą Azure Storage, należy włączyć protokół TLS 1,2 lub nowszą wersję po stronie klienta przed wysłaniem żądań do działania usługi Azure Storage.

## <a name="enable-tls-12-in-net-client"></a>Włączanie protokołu TLS 1.2 w kliencie platformy .NET

Aby klient negocjował protokół TLS 1.2, system operacyjny i platforma .NET Framework muszą obsługiwać protokół TLS 1.2. Zobacz więcej szczegółów na temat [obsługi protokołu TLS 1,2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

Poniższy przykład przedstawia, jak włączyć protokół TLS 1.2 w kliencie platformy .NET.

```csharp

    static void EnableTls12()
    {
        // Enable TLS 1.2 before connecting to Azure Storage
        System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

        // Connect to Azure Storage
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName={yourstorageaccount};AccountKey={yourstorageaccountkey};EndpointSuffix=core.windows.net");
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        CloudBlobContainer container = blobClient.GetContainerReference("foo");
        container.CreateIfNotExists();
    }

```

## <a name="enable-tls-12-in-powershell-client"></a>Włączanie protokołu TLS 1.2 w kliencie programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)] 

Poniższy przykład pokazuje, jak włączyć protokół TLS 1,2 w kliencie programu PowerShell.

```powershell
# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGroupName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers
```

## <a name="verify-tls-12-connection"></a>Weryfikowanie połączenia TLS 1,2

Możesz użyć programu Fiddler, aby sprawdzić, czy protokół TLS 1,2 jest rzeczywiście używany. Otwórz programu Fiddler, aby rozpocząć przechwytywanie ruchu sieciowego klienta, a następnie wykonaj wyższą próbę. Następnie można znaleźć wersję protokołu TLS w połączeniu, które wykonuje przykład.

Poniższy zrzut ekranu przedstawia przykład weryfikacji.

![zrzut ekranu przedstawiający Sprawdzanie wersji protokołu TLS w programu Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Zobacz także

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [Zgodność ze standardem PCI w protokole TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Włączanie protokołu TLS w kliencie Java](https://www.java.com/en/configure_crypto.html)
