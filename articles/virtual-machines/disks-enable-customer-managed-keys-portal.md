---
title: Azure Portal — Włącz klucze zarządzane przez klienta przy użyciu dysków zarządzanych przez funkcję SSE
description: Włącz klucze zarządzane przez klienta na dyskach zarządzanych za pomocą Azure Portal.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 5452b1e2ffc3ca8a11f3acb591c6288806445e1b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499394"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>Użyj Azure Portal, aby włączyć szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta dla dysków zarządzanych

Azure Disk Storage pozwala zarządzać własnymi kluczami podczas korzystania z szyfrowania po stronie serwera (SSE) dla dysków zarządzanych, jeśli wybierzesz opcję. Aby uzyskać informacje koncepcyjne na temat instrukcji SSE z kluczami zarządzanymi przez klienta, a także innych typów szyfrowania dysków zarządzanych, zobacz sekcję **klucze zarządzane przez klienta** w artykule dotyczącym szyfrowania dysku:

- System Linux: [klucze zarządzane przez klienta](./disk-encryption.md#customer-managed-keys)
- Dla systemu Windows: [klucze zarządzane przez klienta](./disk-encryption.md#customer-managed-keys)

## <a name="restrictions"></a>Ograniczenia

Na razie klucze zarządzane przez klienta mają następujące ograniczenia:

- Jeśli ta funkcja jest włączona dla danego dysku, nie można jej wyłączyć.
    Jeśli zachodzi potrzeba obejścia tego problemu, należy skopiować wszystkie dane na całkowicie inny dysk zarządzany, który nie korzysta z kluczy zarządzanych przez klienta:

    - System Linux: [Kopiowanie dysku zarządzanego](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - Dla systemu Windows: [Kopiuj dysk zarządzany](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

- Obsługiwane są tylko [klucze RSA oprogramowania i modułu HSM](../key-vault/keys/about-keys.md) o rozmiarze 2 048-bitowym, 3 072-bitowym i 4 096-bitowym, brak innych kluczy ani rozmiarów.
    - Klucze [HSM](../key-vault/keys/hsm-protected-keys.md) wymagają warstwy **Premium** magazynów kluczy platformy Azure.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

W poniższych sekcjach opisano sposób włączania i używania kluczy zarządzanych przez klienta w przypadku dysków zarządzanych:

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Wdrażanie maszyny wirtualnej

Po utworzeniu i skonfigurowaniu magazynu kluczy oraz zestawu szyfrowania dysków można wdrożyć maszynę wirtualną przy użyciu szyfrowania.
Proces wdrażania maszyn wirtualnych jest podobny do standardowego procesu wdrażania, ale jedyną różnicą jest to, że należy wdrożyć maszynę wirtualną w tym samym regionie, co inne zasoby, i wybrać klucz zarządzany przez klienta.

1. Wyszukaj **Virtual Machines** a następnie wybierz pozycję **+ Dodaj** , aby utworzyć maszynę wirtualną.
1. W bloku **podstawowa** wybierz ten sam region, w którym ustawiono szyfrowanie dysków i Azure Key Vault.
1. Wypełnij pozostałe wartości w bloku **podstawowa** .

    ![Zrzut ekranu przedstawiający środowisko tworzenia maszyn wirtualnych z wyróżnioną wartością region.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. W bloku **dyski** wybierz pozycję **szyfrowanie w spoczynku z kluczem zarządzanym przez klienta**.
1. Wybierz pozycję Szyfrowanie dysków na liście rozwijanej **zestaw szyfrowania dysków** .
1. Wprowadź pozostałe wybrane opcje.

    ![Zrzut ekranu przedstawiający środowisko tworzenia maszyn wirtualnych, blok dysków. Z wyróżnioną listą rozwijaną ustawienia szyfrowania dysku.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Włącz na istniejącym dysku

> [!CAUTION]
> Włączenie szyfrowania dysków na wszystkich dyskach dołączonych do maszyny wirtualnej wymaga zatrzymania maszyny wirtualnej.
    
1. Przejdź do maszyny wirtualnej, która znajduje się w tym samym regionie co jeden z zestawów szyfrowania dysków.
1. Otwórz maszynę wirtualną i wybierz pozycję **Zatrzymaj**.

    ![Zrzut ekranu przedstawiający główną nakładkę dla przykładowej maszyny wirtualnej z wyróżnionym przyciskiem Zatrzymaj.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Po zakończeniu zatrzymywania maszyny wirtualnej wybierz pozycję **dyski** , a następnie wybierz dysk, który chcesz zaszyfrować.

    ![Zrzut ekranu przedstawiający przykładową maszynę wirtualną z otwartym blokiem dysków. Dysk systemu operacyjnego jest wyróżniony jako przykładowy dysk do wybrania.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Wybierz pozycję **szyfrowanie** i wybierz pozycję **szyfrowanie w spoczynku z kluczem zarządzanym przez klienta** , a następnie na liście rozwijanej wybierz zestaw szyfrowanie dysków.
1. Wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający przykładowy dysk systemu operacyjnego. Zostanie otwarty blok szyfrowanie, szyfrowanie w stanie spoczynku z kluczem zarządzanym przez klienta oraz przykład Azure Key Vault. Po dokonaniu wyboru należy zaznaczyć przycisk Zapisz.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Powtórz ten proces dla wszystkich innych dysków dołączonych do maszyny wirtualnej, którą chcesz zaszyfrować.
1. Po zakończeniu przełączania dysków na klucze zarządzane przez klienta, jeśli nie ma żadnych innych dołączonych dysków, które chcesz zaszyfrować, możesz uruchomić maszynę wirtualną.

> [!IMPORTANT]
> Klucze zarządzane przez klienta korzystają z zarządzanych tożsamości dla zasobów platformy Azure, funkcji Azure Active Directory (Azure AD). W przypadku konfigurowania kluczy zarządzanych przez klienta tożsamość zarządzana jest automatycznie przypisywana do zasobów w ramach okładek. Jeśli później przeniesiesz subskrypcję, grupę zasobów lub dysk zarządzany z jednego katalogu usługi Azure AD do innego, zarządzana tożsamość skojarzona z dyskami zarządzanymi nie zostanie przetransferowana do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz [transfer subskrypcji między katalogami usługi Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Następne kroki

- [Poznaj szablony Azure Resource Manager tworzenia szyfrowanych dysków przy użyciu kluczy zarządzanych przez klienta](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Co to jest usługa Azure Key Vault?](../key-vault/general/overview.md)
- [Replikowanie maszyn z włączonymi kluczami zarządzanymi przez klienta](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Skonfiguruj odzyskiwanie po awarii maszyn wirtualnych VMware na platformę Azure za pomocą programu PowerShell](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Skonfiguruj odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych funkcji Hyper-V przy użyciu programu PowerShell i Azure Resource Manager](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)