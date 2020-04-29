---
title: Azure Key Vault zarządzane konto magazynu — wersja programu PowerShell
description: Funkcja zarządzanego konta magazynu zapewnia bezproblemową integrację między Azure Key Vault i kontem usługi Azure Storage.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 7307741e56c7fc912f60d0496979243eb4be77a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431269"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Pobieranie tokenów sygnatury dostępu współdzielonego

Kontem magazynu można zarządzać przy użyciu [tokenów sygnatury dostępu współdzielonego](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) w magazynie kluczy. W tym artykule przedstawiono przykłady kodu w języku C#, który pobiera token sygnatury dostępu współdzielonego i wykonuje do niego operacje.  Aby uzyskać informacje na temat tworzenia i przechowywania tokenów SAS, zobacz [Zarządzanie kluczami konta magazynu przy użyciu Key Vault i interfejsu wiersza polecenia platformy Azure](overview-storage-keys.md) lub [Zarządzanie kluczami konta magazynu za pomocą Key Vault i Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Przykłady kodu

W tym przykładzie kod pobiera token sygnatury dostępu współdzielonego z magazynu kluczy, używa go do utworzenia nowego konta magazynu i tworzy nowy klient Blob service.  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Jeśli token sygnatury dostępu współdzielonego niedługo wygaśnie, możesz pobrać token sygnatury dostępu współdzielonego z magazynu kluczy i zaktualizować kod.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [zarządzać kluczami konta magazynu za pomocą Key Vault i interfejsu wiersza polecenia platformy Azure](overview-storage-keys.md) lub [Azure PowerShell](overview-storage-keys-powershell.md).
- Zobacz [przykłady kluczy zarządzanego konta magazynu](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Dokumentacja programu Key Vault PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
