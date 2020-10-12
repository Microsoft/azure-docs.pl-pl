---
title: Wymagaj bezpiecznego transferu w celu zapewnienia bezpiecznych połączeń
titleSuffix: Azure Storage
description: Dowiedz się, jak wymagać bezpiecznego transferu dla żądań do usługi Azure Storage. Gdy wymagany jest bezpieczny transfer dla konta magazynu, wszystkie żądania pochodzące z niezabezpieczonego połączenia są odrzucane.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 22e012c36f5c2c6f195a7e3b21afe9001a4cad0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89077963"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Wymagaj bezpiecznego transferu w celu zapewnienia bezpiecznych połączeń

Konto magazynu można skonfigurować tak, aby akceptowało żądania z bezpiecznych połączeń tylko przez ustawienie właściwości **wymagany bezpieczny transfer** dla konta magazynu. W przypadku konieczności bezpiecznego transferu wszystkie żądania pochodzące z niezabezpieczonego połączenia są odrzucane. Firma Microsoft zaleca, aby zawsze wymagać bezpiecznego transferu dla wszystkich kont magazynu.

Gdy wymagany jest bezpieczny transfer, wywołanie operacji interfejsu API REST usługi Azure Storage musi być nawiązywane za pośrednictwem protokołu HTTPS. Wszystkie żądania wysłane za pośrednictwem protokołu HTTP zostały odrzucone.

Nawiązywanie połączenia z udziałem plików platformy Azure za pośrednictwem protokołu SMB bez szyfrowania kończy się niepowodzeniem, gdy wymagany jest bezpieczny transfer dla konta magazynu. Przykładami niezabezpieczonych połączeń są te, które zostały wykonane za pośrednictwem protokołu SMB 2,1, SMB 3,0 bez szyfrowania lub niektórych wersji klienta SMB systemu Linux.

Domyślnie właściwość **Required Secure transfer** jest włączona podczas tworzenia konta magazynu.

> [!NOTE]
> Ponieważ usługa Azure Storage nie obsługuje protokołu HTTPS dla niestandardowych nazw domen, ta opcja nie jest stosowana, jeśli używasz niestandardowej nazwy domeny. I klasyczne konta magazynu nie są obsługiwane.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Wymagaj bezpiecznego transferu w Azure Portal

Właściwość **Required Secure transfer** można włączyć podczas tworzenia konta magazynu w [Azure Portal](https://portal.azure.com). Można ją również włączyć dla istniejących kont magazynu.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Wymagaj bezpiecznego transferu dla nowego konta magazynu

1. Otwórz okienko **Tworzenie konta magazynu** w Azure Portal.
1. W obszarze **wymagany bezpieczny transfer**wybierz pozycję **włączone**.

   ![Utwórz blok konta magazynu](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Wymagaj bezpiecznego transferu dla istniejącego konta magazynu

1. Wybierz istniejące konto magazynu w Azure Portal.
1. W okienku menu konto magazynu w obszarze **Ustawienia**wybierz pozycję **Konfiguracja**.
1. W obszarze **wymagany bezpieczny transfer**wybierz pozycję **włączone**.

   ![Okienko menu konta magazynu](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Wymagaj bezpiecznego transferu z kodu

Aby wymagać bezpiecznego transferu programowo, należy ustawić właściwość _enableHttpsTrafficOnly_ na _wartość true_ na koncie magazynu. Tę właściwość można ustawić za pomocą interfejsu API REST dostawcy zasobów usługi Storage, bibliotek klienckich lub narzędzi:

* [Interfejs API REST](/rest/api/storagerp/storageaccounts)
* [Program PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [Interfejs wiersza polecenia](/cli/azure/storage/account)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/)
* [Zestaw SDK platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Zestaw SDK dla języka Python](https://pypi.org/project/azure-mgmt-storage)
* [Zestaw SDK dla języka Ruby](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Wymagaj bezpiecznego transferu przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ten przykład wymaga modułu Azure PowerShell AZ w wersji 0,7 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

Uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenia z platformą Azure.

 Aby sprawdzić ustawienie, użyj następującego wiersza polecenia:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Aby włączyć ustawienie, użyj następującego wiersza polecenia:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Wymagaj bezpiecznego transferu przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Aby sprawdzić ustawienie, użyj następującego polecenia:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Aby włączyć ustawienie, użyj następującego polecenia:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Następne kroki

[Zalecenia dotyczące zabezpieczeń usługi BLOB Storage](../blobs/security-recommendations.md)
