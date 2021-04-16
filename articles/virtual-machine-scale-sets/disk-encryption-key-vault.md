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
ms.openlocfilehash: e05940cecd8ebdc4e61281406aafd5edc266cabc
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483133"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption"></a>Tworzenie i konfigurowanie magazynu kluczy dla Azure Disk Encryption

Azure Disk Encryption używa Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi.  Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz [Get started with Azure Key Vault](../key-vault/general/overview.md) and Secure your key vault (Rozpoczynanie pracy z magazynem kluczy i [zabezpieczanie magazynu kluczy).](../key-vault/general/secure-your-key-vault.md)

Tworzenie i konfigurowanie magazynu kluczy do użycia z Azure Disk Encryption obejmuje trzy kroki:

1. Tworzenie grupy zasobów, jeśli jest to konieczne.
2. Tworzenie magazynu kluczy. 
3. Ustawianie zaawansowanych zasad dostępu magazynu kluczy.

Te kroki zostały zilustrowane w następujących przewodnikach Szybki start:

Jeśli chcesz, możesz również wygenerować lub zaimportować klucz szyfrowania klucza (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i nawiązywanie połączenia z platformą Azure

Kroki opisane w tym artykule można wykonać przy użyciu interfejsu wiersza polecenia platformy [Azure,](/cli/azure/)modułu [Azure PowerShell Az](/powershell/azure/)lub Azure Portal [.](https://portal.azure.com)

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Przed rozpoczęciem korzystania z interfejsu wiersza polecenia Azure PowerShell platformy Azure należy najpierw nawiązać połączenie z subskrypcją platformy Azure. Możesz to zrobić, logując się za pomocą interfejsu wiersza polecenia platformy [Azure,](/cli/azure/authenticate-azure-cli)logując się Azure PowerShell usłudze [Azure PowerShell](/powershell/azure/authenticate-azureps), lub po po wyświetleniu monitu Azure Portal poświadczenia.

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
