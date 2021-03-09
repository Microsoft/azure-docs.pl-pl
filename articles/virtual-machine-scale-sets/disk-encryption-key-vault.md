---
title: Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption
description: Ten artykuł zawiera instrukcje dotyczące tworzenia i konfigurowania magazynu kluczy do użycia z usługą Azure Disk Encryption
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 3d30d506ae3b49b2e247ee77ef17d101f94854c7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501020"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption"></a>Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption

Azure Disk Encryption używa Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi.  Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz Wprowadzenie do [Azure Key Vault](../key-vault/general/overview.md) i [Zabezpieczanie magazynu kluczy](../key-vault/general/secure-your-key-vault.md).

Tworzenie i Konfigurowanie magazynu kluczy do użycia z Azure Disk Encryption obejmuje trzy kroki:

1. Tworzenie grupy zasobów, w razie konieczności.
2. Tworzenie magazynu kluczy. 
3. Ustawianie zaawansowanych zasad dostępu magazynu kluczy.

Te kroki przedstawiono w następujących przewodnikach szybki start:

Możesz również, jeśli chcesz, wygenerować lub zaimportować klucz szyfrowania klucza (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i nawiązywanie połączenia z platformą Azure

Kroki opisane w tym artykule można wykonać przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/), [Azure PowerShell Az module](/powershell/azure/)lub [Azure Portal](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Przed rozpoczęciem korzystania z interfejsu wiersza polecenia platformy Azure lub Azure PowerShell należy najpierw nawiązać połączenie z subskrypcją platformy Azure. Można to zrobić, [logując się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli), [logując się za pomocą Azure PowerShell](/powershell/azure/authenticate-azureps)lub podając poświadczenia do Azure Portal po wyświetleniu monitu.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Następne kroki

- [Przegląd Azure Disk Encryption](disk-encryption-overview.md)
- [Szyfrowanie zestawów skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli.md)
- [Szyfrowanie zestawów skalowania maszyn wirtualnych przy użyciu Azure PowerShell](disk-encryption-powershell.md)
