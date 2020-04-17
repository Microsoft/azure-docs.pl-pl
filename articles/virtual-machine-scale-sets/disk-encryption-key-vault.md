---
title: Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption
description: Ten artykuł zawiera kroki dotyczące tworzenia i konfigurowania magazynu kluczy do użycia z szyfrowaniem dysku azure
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 30fa6b910c0241621c2b2cdae9bb9a164f27cedb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454547"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption

Usługa Azure Disk Encryption używa usługi Azure Key Vault do kontrolowania kluczy i wpisów tajnych szyfrowania dysków i zarządzania nimi.  Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz [Wprowadzenie do usługi Azure Key Vault](../key-vault/key-vault-get-started.md) i [zabezpieczanie magazynu kluczy](../key-vault/general/secure-your-key-vault.md).

Tworzenie i konfigurowanie magazynu kluczy do użycia za pomocą szyfrowania dysków platformy Azure obejmuje trzy kroki:

1. W razie potrzeby tworzenie grupy zasobów.
2. Tworzenie magazynu kluczy. 
3. Ustawianie zaawansowanych zasad dostępu magazynu kluczy.

Te kroki są zilustrowane w następujących przewodnikach Szybki start:

Możesz również, jeśli chcesz, wygenerować lub zaimportować klucz szyfrowania klucza (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i łączenie się z platformą Azure

Kroki opisane w tym artykule można wykonać za pomocą [interfejsu wiersza polecenia platformy Azure,](/cli/azure/) [modułu Azure PowerShell Az](/powershell/azure/overview)lub [portalu Azure.](https://portal.azure.com)

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Przed użyciem interfejsu wiersza polecenia platformy Azure lub usługi Azure PowerShell należy najpierw połączyć się z subskrypcją platformy Azure. Można to zrobić, [logując się za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Logując się za pomocą usługi Azure Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)lub podajesz poświadczenia do witryny Azure portal po wyświetleniu monitu.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Następne kroki

- [Omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md)
- [Szyfruj zestawy skalowania maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli.md)
- [Szyfrowanie zestawów skalowania maszyny wirtualnej przy użyciu programu Azure PowerShell](disk-encryption-powershell.md)
