---
title: Tworzenie i konfigurowanie magazynu kluczy na Azure Disk Encryption wirtualnej z systemem Windows
description: Ten artykuł zawiera instrukcje dotyczące tworzenia i konfigurowania magazynu kluczy do użycia Azure Disk Encryption na maszynie wirtualnej z systemem Windows.
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5e45b242d85d761dcbe3593c59174942ef1a62a5
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749064"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption-on-a-windows-vm"></a>Tworzenie i konfigurowanie magazynu kluczy na Azure Disk Encryption wirtualnej z systemem Windows

Azure Disk Encryption używa Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi.  Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz [Get started with Azure Key Vault](../../key-vault/general/overview.md) and Secure your key vault (Rozpoczynanie pracy z magazynem kluczy i [zabezpieczanie magazynu kluczy).](../../key-vault/general/security-overview.md) 

> [!WARNING]
> - Jeśli wcześniej zaszyfrowano maszynę wirtualną Azure Disk Encryption usłudze Azure AD, należy nadal używać tej opcji do szyfrowania maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz Creating [and configuring a key vault for Azure Disk Encryption with Azure AD (previous release) (Tworzenie](disk-encryption-key-vault-aad.md) i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption azure AD w poprzedniej wersji).

Tworzenie i konfigurowanie magazynu kluczy do użycia z Azure Disk Encryption obejmuje trzy kroki:

> [!Note]
> Należy wybrać opcję w ustawieniach zasad dostępu Azure Key Vault, aby włączyć dostęp do Azure Disk Encryption szyfrowania woluminów. Jeśli zapora w magazynie kluczy jest włączona, musisz przejść na kartę Sieć w magazynie kluczy i włączyć dostęp do zaufanych usług firmy Microsoft. 

1. Tworzenie grupy zasobów, jeśli jest to konieczne.
2. Tworzenie magazynu kluczy. 
3. Ustawianie zaawansowanych zasad dostępu magazynu kluczy.

Te kroki zostały zilustrowane w następujących przewodnikach Szybki start:

- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli-quickstart.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu programu Azure PowerShell](disk-encryption-powershell-quickstart.md)

Jeśli chcesz, możesz również wygenerować lub zaimportować klucz szyfrowania klucza (KEK).

> [!Note]
> Kroki opisane w tym artykule są zautomatyzowane w skrypcie interfejsu wiersza [polecenia](https://github.com/ejarvi/ade-cli-getting-started) Azure Disk Encryption wymagań wstępnych i Azure Disk Encryption [wymagań wstępnych programu PowerShell.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i nawiązywanie połączenia z platformą Azure

Kroki opisane w tym artykule można wykonać przy użyciu interfejsu wiersza polecenia platformy [Azure,](/cli/azure/)modułu [Azure PowerShell Az](/powershell/azure/)lub Azure Portal [.](https://portal.azure.com)

Chociaż portal jest dostępny za pośrednictwem przeglądarki, interfejs wiersza polecenia platformy Azure Azure PowerShell wymaga instalacji lokalnej; Zobacz [Azure Disk Encryption dla systemu Windows: Instalowanie narzędzi, aby](disk-encryption-windows.md#install-tools-and-connect-to-azure) uzyskać szczegółowe informacje.

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Przed rozpoczęciem korzystania z interfejsu wiersza polecenia Azure PowerShell platformy Azure należy najpierw nawiązać połączenie z subskrypcją platformy Azure. W tym celu należy zalogować się przy użyciu interfejsu wiersza polecenia platformy [Azure,](/cli/azure/authenticate-azure-cli)zalogować się przy użyciu programu [Azure PowerShell](/powershell/azure/authenticate-azureps)lub podać poświadczenia do Azure Portal po wyświetleniu monitu.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Następne kroki

- [Azure Disk Encryption wymagań wstępnych interfejsu wiersza polecenia](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption wymagań wstępnych skryptu programu PowerShell](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Dowiedz [się Azure Disk Encryption scenariuszy na temat maszyn wirtualnych z systemem Windows](disk-encryption-windows.md)
- Dowiedz się, jak [rozwiązywać Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Przeczytaj [przykładowe Azure Disk Encryption skrypty](disk-encryption-sample-scripts.md)
