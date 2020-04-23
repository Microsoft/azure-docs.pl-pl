---
title: Wymagaj bezpiecznego transferu, aby zapewnić bezpieczne połączenia
titleSuffix: Azure Storage
description: Dowiedz się, jak wymagać bezpiecznego transferu żądań do usługi Azure Storage. Jeśli potrzebujesz bezpiecznego transferu dla konta magazynu, wszystkie żądania pochodzące z niezabezpieczone połączenie są odrzucane.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 125f4188ed3f12f366c619af9efe3aa203987c19
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870514"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Wymagaj bezpiecznego transferu, aby zapewnić bezpieczne połączenia

Konto magazynu można skonfigurować tak, aby akceptować żądania z bezpiecznych połączeń tylko przez ustawienie właściwości **Wymagane przeniesienie bezpiecznego** dla konta magazynu. Jeśli potrzebujesz bezpiecznego transferu, wszystkie żądania pochodzące z niezabezpieczone połączenie są odrzucane. Firma Microsoft zaleca, aby zawsze wymagać bezpiecznego transferu dla wszystkich kont magazynu.

Gdy wymagany jest bezpieczny transfer, wywołanie operacji interfejsu API rest usługi Azure Storage musi być wykonane za pośrednictwem protokołu HTTPS. Każde żądanie złożone za pośrednictwem protokołu HTTP zostanie odrzucone.

Łączenie się z udziałem pliku usługi Azure za pomocą usługi SMB bez szyfrowania kończy się niepowodzeniem, gdy dla konta magazynu wymagany jest bezpieczny transfer. Przykłady niezabezpieczonych połączeń obejmują połączenia wykonane za 2.1, SMB 3.0 bez szyfrowania lub niektóre wersje klienta SMB systemu Linux.

Domyślnie właściwość **Wymagane przeniesienie bezpieczeństwa** jest włączona podczas tworzenia konta magazynu.

> [!NOTE]
> Ponieważ usługa Azure Storage nie obsługuje protokołu HTTPS dla niestandardowych nazw domen, ta opcja nie jest stosowana podczas korzystania z niestandardowej nazwy domeny. A klasyczne konta magazynu nie są obsługiwane.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Wymagaj bezpiecznego transferu w witrynie Azure portal

Właściwość Wymagane **przeniesienie bezpieczeństwa** można włączyć podczas tworzenia konta magazynu w [witrynie Azure portal](https://portal.azure.com). Można również włączyć go dla istniejących kont magazynu.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Wymagaj bezpiecznego transferu dla nowego konta magazynu

1. Otwórz okienko **Tworzenie konta magazynu** w witrynie Azure portal.
1. W **obszarze Wymagany bezpieczny transfer**wybierz pozycję **Włączone**.

   ![Tworzenie bloku konta magazynu](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Wymagaj bezpiecznego transferu dla istniejącego konta magazynu

1. Wybierz istniejące konto magazynu w witrynie Azure portal.
1. W okienku menu konta magazynu w obszarze **USTAWIENIA**wybierz pozycję **Konfiguracja**.
1. W **obszarze Wymagany bezpieczny transfer**wybierz pozycję **Włączone**.

   ![Okienko menu konta magazynu](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Wymagaj bezpiecznego transferu z kodu

Aby wymagać bezpiecznego transferu programowo, ustaw _właściwość httpstrafficOnly_ na koncie magazynu. Tę właściwość można ustawić za pomocą interfejsu API REST dostawcy zasobów magazynu, bibliotek klienta lub narzędzi:

* [Interfejs API REST](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [Interfejs wiersza polecenia](/cli/azure/storage/account)
* [Nodejs](https://www.npmjs.com/package/azure-arm-storage/)
* [Zestaw SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK](https://pypi.org/project/azure-mgmt-storage)
* [Zestaw SDK dla języka Ruby](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Wymagaj bezpiecznego transferu za pomocą programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ten przykład wymaga modułu Azure PowerShell Az w wersji 0.7 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

Uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenia z platformą Azure.

 Aby sprawdzić to ustawienie, użyj następującego wiersza polecenia:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Użyj następującego wiersza polecenia, aby włączyć ustawienie:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Wymagaj bezpiecznego transferu za pomocą interfejsu wiersza polecenia platformy Azure

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Aby sprawdzić to ustawienie, użyj następującego polecenia:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Aby włączyć to ustawienie, użyj następującego polecenia:

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

[Zalecenia dotyczące zabezpieczeń dla magazynu obiektów Blob](../blobs/security-recommendations.md)
