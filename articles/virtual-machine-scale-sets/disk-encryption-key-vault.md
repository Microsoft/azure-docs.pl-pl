---
title: Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption
description: Ten artykuł zawiera instrukcje tworzenia i konfigurowania magazynu kluczy do użycia z Azure Disk Encryption
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: c9706176e537ec926a5335b54d667081773d76b1
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817363"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption"></a>Tworzenie i konfigurowanie magazynu kluczy dla Azure Disk Encryption

Azure Disk Encryption używa Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi.  Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz [Wprowadzenie](../key-vault/general/overview.md) do Azure Key Vault i [Azure Key Vault zabezpieczeń](../key-vault/general/security-features.md).

Tworzenie i konfigurowanie magazynu kluczy do użycia z Azure Disk Encryption obejmuje trzy kroki:

1. Tworzenie grupy zasobów, jeśli to konieczne.
2. Tworzenie magazynu kluczy. 
3. Ustawianie zaawansowanych zasad dostępu magazynu kluczy.

Te kroki zostały zilustrowane w następujących przewodnikach Szybki start:

Jeśli chcesz, możesz również wygenerować lub zaimportować klucz szyfrowania klucza (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i nawiązywanie połączenia z platformą Azure

Kroki opisane w tym artykule można wykonać za pomocą interfejsu wiersza polecenia platformy [Azure,](/cli/azure/)modułu [Azure PowerShell Az](/powershell/azure/)lub Azure Portal [.](https://portal.azure.com)

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Przed rozpoczęciem korzystania z interfejsu wiersza polecenia Azure PowerShell platformy Azure należy najpierw nawiązać połączenie z subskrypcją platformy Azure. W tym celu należy zalogować się przy użyciu interfejsu wiersza polecenia platformy [Azure,](/cli/azure/authenticate-azure-cli) [zalogować](/powershell/azure/authenticate-azureps)się przy użyciu usługi Azure PowerShell lub podać poświadczenia do Azure Portal po wyświetleniu monitu.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Następne kroki

- [Azure Disk Encryption omówienie](disk-encryption-overview.md)
- [Szyfrowanie zestawów skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli.md)
- [Szyfrowanie zestawów skalowania maszyn wirtualnych przy użyciu Azure PowerShell](disk-encryption-powershell.md)
