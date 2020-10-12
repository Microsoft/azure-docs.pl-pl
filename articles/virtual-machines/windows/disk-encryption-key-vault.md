---
title: Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption
description: Ten artykuł zawiera instrukcje dotyczące tworzenia i konfigurowania magazynu kluczy do użycia z Azure Disk Encryption na maszynie wirtualnej z systemem Windows.
ms.service: virtual-machines
ms.subservice: security
ms.topic: how-to
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 9d9d3d8456e0623ea3f1ef17c5f9f7acb28d0ecd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90977908"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption

Azure Disk Encryption używa Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi.  Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz Wprowadzenie do [Azure Key Vault](../../key-vault/general/overview.md) i [Zabezpieczanie magazynu kluczy](../../key-vault/general/secure-your-key-vault.md). 

> [!WARNING]
> - Jeśli usługa Azure AD została wcześniej Azure Disk Encryption użyta w celu zaszyfrowania maszyny wirtualnej, należy użyć tej opcji, aby zaszyfrować maszynę wirtualną. Aby uzyskać szczegółowe informacje [, zobacz Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption w usłudze Azure AD (w poprzedniej wersji)](disk-encryption-key-vault-aad.md) .

Tworzenie i Konfigurowanie magazynu kluczy do użycia z Azure Disk Encryption obejmuje trzy kroki:

> [!Note]
> Aby włączyć dostęp do Azure Disk Encryption szyfrowania woluminów, należy wybrać opcję w ustawieniach zasad dostępu Azure Key Vault. Jeśli włączono Zaporę w magazynie kluczy, należy przejść do karty sieć w magazynie kluczy i umożliwić dostęp do zaufanych usług firmy Microsoft. 

1. Tworzenie grupy zasobów, w razie konieczności.
2. Tworzenie magazynu kluczy. 
3. Ustawianie zaawansowanych zasad dostępu magazynu kluczy.

Te kroki przedstawiono w następujących przewodnikach szybki start:

- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli-quickstart.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu programu Azure PowerShell](disk-encryption-powershell-quickstart.md)

Możesz również, jeśli chcesz, wygenerować lub zaimportować klucz szyfrowania klucza (KEK).

> [!Note]
> Kroki opisane w tym artykule są zautomatyzowane w [Azure Disk Encryption skrypt interfejsu wiersza polecenia wymagania wstępne](https://github.com/ejarvi/ade-cli-getting-started) i [Azure Disk Encryption skrypt programu PowerShell dotyczące wymagań wstępnych](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i nawiązywanie połączenia z platformą Azure

Kroki opisane w tym artykule można wykonać przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/), [Azure PowerShell Az module](/powershell/azure/)lub [Azure Portal](https://portal.azure.com).

Gdy Portal jest dostępny za pomocą przeglądarki, interfejs wiersza polecenia platformy Azure i Azure PowerShell wymagają instalacji lokalnej; Zobacz [Azure Disk Encryption dla systemu Windows: Zainstaluj narzędzia](disk-encryption-windows.md#install-tools-and-connect-to-azure) , aby uzyskać szczegółowe informacje.

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Przed rozpoczęciem korzystania z interfejsu wiersza polecenia platformy Azure lub Azure PowerShell należy najpierw nawiązać połączenie z subskrypcją platformy Azure. Można to zrobić, [logując się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [logując się przy użyciu programu Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)lub dostarczając poświadczenia do Azure Portal po wyświetleniu monitu.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Następne kroki

- [Skrypt interfejsu wiersza polecenia Azure Disk Encryption preinstalacji](https://github.com/ejarvi/ade-cli-getting-started)
- [Skrypt programu PowerShell dla Azure Disk Encryption wymagań wstępnych](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Uczenie się [Azure Disk Encryption scenariuszy na maszynach wirtualnych z systemem Windows](disk-encryption-windows.md)
- Dowiedz się, jak [rozwiązywać problemy Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Przeczytaj [Azure Disk Encryption przykładowe skrypty](disk-encryption-sample-scripts.md)
