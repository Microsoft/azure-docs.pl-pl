---
title: Konfigurowanie Transport Layer Security (TLS) dla aplikacji klienckiej
titleSuffix: Azure Storage
description: Skonfiguruj aplikację kliencką do komunikacji z usługą Azure Storage przy użyciu minimalnej wersji Transport Layer Security (TLS).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 37b8c79df5b208feea185292fa09c323b64fa27d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89001811"
---
# <a name="configure-transport-layer-security-tls-for-a-client-application"></a>Konfigurowanie Transport Layer Security (TLS) dla aplikacji klienckiej

Ze względów bezpieczeństwa konto usługi Azure Storage może wymagać, aby klienci używali minimalnej wersji Transport Layer Security (TLS) do wysyłania żądań. Wywołania usługi Azure Storage zakończą się niepowodzeniem, jeśli klient korzysta z wersji TLS, która jest niższa niż minimalna wymagana wersja. Na przykład jeśli konto magazynu wymaga protokołu TLS 1,2, żądanie wysyłane przez klienta, który korzysta z protokołu TLS 1,1, zakończy się niepowodzeniem.

W tym artykule opisano sposób konfigurowania aplikacji klienckiej do korzystania z określonej wersji protokołu TLS. Aby uzyskać informacje dotyczące sposobu konfigurowania minimalnej wymaganej wersji protokołu TLS dla konta usługi Azure Storage, zobacz [Konfigurowanie minimalnej wymaganej wersji Transport Layer Security (TLS) dla konta magazynu](transport-layer-security-configure-minimum-version.md).

## <a name="configure-the-client-tls-version"></a>Skonfiguruj wersję protokołu TLS klienta

Aby klient mógł wysyłać żądanie z określoną wersją protokołu TLS, system operacyjny musi obsługiwać tę wersję.

W poniższych przykładach pokazano, jak ustawić wersję protokołu TLS klienta na 1,2 przy użyciu programu PowerShell lub programu .NET. .NET Framework używany przez klienta musi obsługiwać protokół TLS 1,2. Aby uzyskać więcej informacji, zobacz [Obsługa protokołu TLS 1,2](/dotnet/framework/network-programming/tls#support-for-tls-12).

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Poniższy przykład pokazuje, jak włączyć protokół TLS 1,2 w kliencie programu PowerShell:

```powershell
# Set the TLS version used by the PowerShell client to TLS 1.2.
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

# Create a new container.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context
New-AzStorageContainer -Name "sample-container" -Context $ctx
```

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Poniższy przykład pokazuje, jak włączyć protokół TLS 1,2 w kliencie .NET przy użyciu wersji 12 biblioteki klienta usługi Azure Storage:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Networking.cs" id="Snippet_ConfigureTls12":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Poniższy przykład pokazuje, jak włączyć protokół TLS 1,2 w kliencie .NET przy użyciu wersji 11 biblioteki klienta usługi Azure Storage:

```csharp
static void EnableTls12()
{
    // Enable TLS 1.2 before connecting to Azure Storage
    System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

    // Add your connection string here.
    string connectionString = "";

    // Connect to Azure Storage and create a new container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
}
```

---

## <a name="verify-the-tls-version-used-by-a-client"></a>Weryfikowanie wersji protokołu TLS używanej przez klienta

Aby sprawdzić, czy określona wersja protokołu TLS była używana przez klienta do wysyłania żądania, można użyć [programu Fiddler](https://www.telerik.com/fiddler) lub podobnego narzędzia. Otwórz programu Fiddler, aby rozpocząć przechwytywanie ruchu sieciowego klienta, a następnie wykonaj jedną z przykładów w poprzedniej sekcji. Spójrz na ślad programu Fiddler, aby upewnić się, że do wysłania żądania użyto odpowiedniej wersji protokołu TLS, jak pokazano na poniższej ilustracji.

:::image type="content" source="media/transport-layer-security-configure-client-version/fiddler-trace-tls-version.png" alt-text="Zrzut ekranu przedstawiający śledzenie programu Fiddler wskazujące używaną wersję protokołu TLS na żądanie":::

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj minimalną wymaganą wersję Transport Layer Security (TLS) dla konta magazynu](transport-layer-security-configure-minimum-version.md)
- [Zalecenia dotyczące zabezpieczeń usługi BLOB Storage](../blobs/security-recommendations.md)
