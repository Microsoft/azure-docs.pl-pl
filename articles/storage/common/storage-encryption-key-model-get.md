---
title: Określanie, który model klucza szyfrowania jest w użyciu dla konta magazynu
titleSuffix: Azure Storage
description: Użyj Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby sprawdzić, jak klucze szyfrowania są zarządzane dla konta magazynu. Klucze mogą być zarządzane przez firmę Microsoft (ustawienie domyślne) lub przez klienta. Klucze zarządzane przez klienta muszą być przechowywane w Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 08bc36500bbd95633d1cb1d02bf10a7397401aa4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780123"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Określanie, który model klucza szyfrowania usługi Azure Storage jest w użyciu dla konta magazynu

Dane na koncie magazynu są automatycznie szyfrowane przez usługę Azure Storage. Szyfrowanie w usłudze Azure Storage oferuje dwie opcje zarządzania kluczami szyfrowania na poziomie konta magazynu:

- **Klucze zarządzane przez firmę Microsoft.** Domyślnie firma Microsoft zarządza kluczami używanymi do szyfrowania konta magazynu.
- **Klucze zarządzane przez klienta.** Opcjonalnie możesz zarządzać kluczami szyfrowania dla konta magazynu. Klucze zarządzane przez klienta muszą być przechowywane w Azure Key Vault.

Ponadto można podać klucz szyfrowania na poziomie pojedynczego żądania dla niektórych operacji usługi Blob Storage. Jeśli w żądaniu zostanie określony klucz szyfrowania, zastąpi on klucz szyfrowania aktywny na koncie magazynu. Aby uzyskać więcej informacji, [zobacz Temat Specify a customer-provided key on a request to Blob storage](../blobs/storage-blob-customer-provided-key.md)(Określanie klucza dostarczonego przez klienta w żądaniu do usługi Blob Storage).

Aby uzyskać więcej informacji na temat kluczy szyfrowania, zobacz Szyfrowanie danych magazynowych w [usłudze Azure Storage.](storage-service-encryption.md)

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Sprawdzanie modelu klucza szyfrowania dla konta magazynu

Aby określić, czy konto magazynu używa kluczy zarządzanych przez firmę Microsoft, czy kluczy zarządzanych przez klienta do szyfrowania, użyj jednej z następujących metod.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby sprawdzić model szyfrowania dla konta magazynu przy użyciu Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. Wybierz ustawienie **Szyfrowanie** i zanotuj to ustawienie.

Na poniższej ilustracji przedstawiono konto magazynu zaszyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft:

![Wyświetlanie konta zaszyfrowanego przy użyciu kluczy zarządzanych przez firmę Microsoft](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

Na poniższej ilustracji przedstawiono konto magazynu szyfrowane przy użyciu kluczy zarządzanych przez klienta:

![Zrzut ekranu przedstawiający ustawienie klucza szyfrowania w Azure Portal](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić model szyfrowania dla konta magazynu przy użyciu programu PowerShell, wywołaj polecenie [Get-AzStorageAccount,](/powershell/module/az.storage/get-azstorageaccount) a następnie sprawdź właściwość **KeySource** dla konta.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Jeśli wartość właściwości **KeySource** to , konto jest szyfrowane `Microsoft.Storage` przy użyciu kluczy zarządzanych przez firmę Microsoft. Jeśli wartość właściwości **KeySource** to , konto jest szyfrowane `Microsoft.Keyvault` przy użyciu kluczy zarządzanych przez klienta.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Aby sprawdzić model szyfrowania dla konta magazynu przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj [polecenie az storage account show,](/cli/azure/storage/account#az_storage_account_show) a następnie sprawdź właściwość **keySource** dla konta.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Jeśli wartość właściwości **keySource** to , konto jest szyfrowane `Microsoft.Storage` przy użyciu kluczy zarządzanych przez firmę Microsoft. Jeśli wartość właściwości **keySource** to , konto jest szyfrowane `Microsoft.Keyvault` przy użyciu kluczy zarządzanych przez klienta.

---

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](storage-service-encryption.md)
- [Klucze zarządzane przez klienta na potrzeby szyfrowania w usłudze Azure Storage](customer-managed-keys-overview.md)
