---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 950fd82e14902c3a7a94fddb44d50ac372ebe119
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177047"
---
[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

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
