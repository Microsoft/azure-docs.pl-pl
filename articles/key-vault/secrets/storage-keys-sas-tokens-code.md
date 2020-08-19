---
title: Pobieranie tokenów sygnatury dostępu współdzielonego w kodzie | Azure Key Vault
description: Funkcja zarządzanego konta magazynu zapewnia bezproblemową integrację między Azure Key Vault i kontem usługi Azure Storage.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 6530434e36f7c9a9a60a9782bcf2dce7ba447dab
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584917"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Pobieranie tokenów sygnatury dostępu współdzielonego

Kontem magazynu można zarządzać za pomocą tokenów sygnatury dostępu współdzielonego (SAS) przechowywanych w magazynie kluczy. Aby uzyskać więcej informacji, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego](../../storage/common/storage-sas-overview.md).

W tym artykule przedstawiono przykłady kodu platformy .NET, który pobiera token SAS i wykonuje do niego operacje. Aby uzyskać informacje na temat tworzenia i przechowywania tokenów SAS, zobacz [Zarządzanie kluczami konta magazynu przy użyciu Key Vault i interfejsu wiersza polecenia platformy Azure](overview-storage-keys.md) lub [Zarządzanie kluczami konta magazynu za pomocą Key Vault i Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Przykłady kodu

W tym przykładzie kod pobiera token sygnatury dostępu współdzielonego z magazynu kluczy, używa go do utworzenia nowego konta magazynu i tworzy nowy klient Blob service.

```cs
// The shared access signature is stored as a secret in keyvault. 
// After you get a security token, create a new SecretClient with vault credentials and the key vault URI.
// The format for the key vault URI (kvuri) is https://<YourKeyVaultName>.vault.azure.net

var kv = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

// Now retrive your storage SAS token from Key Vault using the name of the secret (secretName).

KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;

// Create new storage credentials using the SAS token.
StorageCredentials accountSAS = new StorageCredentials(sasToken);

// Use these credentials and your storage account name to create a Blob service client.
CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();
```

Jeśli token sygnatury dostępu współdzielonego niedługo wygaśnie, możesz pobrać token sygnatury dostępu współdzielonego z magazynu kluczy i zaktualizować kod.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [udzielić ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego](../../storage/common/storage-sas-overview.md).
- Dowiedz się, jak [zarządzać kluczami konta magazynu za pomocą Key Vault i interfejsu wiersza polecenia platformy Azure](overview-storage-keys.md) lub [Azure PowerShell](overview-storage-keys-powershell.md).
- Zobacz [przykłady kluczy zarządzanego konta magazynu](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
