---
title: dołączanie pliku
description: dołączanie pliku
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b8073240bdda38757a5e4feee66c9f54746966c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80632044"
---
### <a name="portal"></a>Portal

Skonfigurowanie kluczy zarządzanych przez klienta dla dysków będzie wymagało utworzenia zasobów w określonej kolejności, jeśli jest to wykonywane po raz pierwszy. Najpierw należy utworzyć i skonfigurować Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>Konfigurowanie Azure Key Vault

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wyszukaj i wybierz pozycję **magazyny kluczy**.

    [![sse-key-vault-portal-search.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Aby wdrożenie zakończyło się pomyślnie, w tym samym regionie i w ramach subskrypcji musi znajdować się Twój Magazyn kluczy platformy Azure, zestaw szyfrowania dysków, maszyna wirtualna, dyski i migawki.

1. Wybierz pozycję **+ Dodaj** , aby utworzyć nowy Key Vault.
1. Utwórz nową grupę zasobów.
1. Wprowadź nazwę magazynu kluczy, wybierz region, a następnie wybierz warstwę cenową.
1. Wybierz pozycję **Recenzja + Utwórz**, sprawdź wybrane opcje, a następnie wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający środowisko tworzenia Azure Key Vault. Wyświetlanie określonych wartości, które tworzysz](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Po zakończeniu wdrażania magazynu kluczy wybierz go.
1. Wybierz pozycję **klucze** w obszarze **Ustawienia**.
1. Wybierz pozycję **Generuj/Importuj**.

    ![Zrzut ekranu przedstawiający okienko ustawień zasobów Key Vault. Pokazuje przycisk Generuj/Importuj wewnątrz ustawień.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Pozostaw **Typ klucza** ustawiony na wartość **RSA** i **rozmiar klucza RSA** ustawiony na **2048**.
1. Wypełnij pozostałe wybrane opcje, a następnie wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający blok Utwórz klucz, który pojawia się po wybraniu przycisku Generuj/Importuj](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Konfigurowanie zestawu szyfrowania dysków

1. Wyszukaj **zestawy szyfrowania dysków** i wybierz je.
1. W bloku **zestawy szyfrowania dysków** wybierz pozycję **+ Dodaj**.

    ![Zrzut ekranu przedstawiający ekran główny portalu szyfrowania dysku. Wyróżnianie przycisku Dodaj](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Wybierz grupę zasobów, Nazwij zestaw szyfrowania i wybierz ten sam region, w którym znajduje się Twój Magazyn kluczy.
1. Wybierz pozycję **Magazyn kluczy i klucz**.
1. Wybierz utworzony wcześniej Magazyn kluczy i klucz, a także wersję.
1. Naciśnij **pozycję Wybierz**.
1. Wybierz kolejno pozycje **Przegląd + Utwórz** i **Utwórz**.

    ![Zrzut ekranu przedstawiający blok tworzenia szyfrowania dysku. Wyświetlanie subskrypcji, grupy zasobów, nazwy zestawu szyfrowania dysku, regionu i selektora kluczy + Key.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Otwórz zestaw szyfrowanie dysków po zakończeniu tworzenia i wybierz alert, który zostanie wystawiony.

    ![Zrzut ekranu przedstawiający okno podręczne alertu: ' aby skojarzyć dysk, obraz lub migawkę z zestawem szyfrowania dysków, należy przyznać uprawnienia do magazynu kluczy. Wybierz ten alert, aby kontynuować](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Dwie powiadomienia powinny wyskakujące i zakończyć się pomyślnie. Pozwoli to na użycie zestawu szyfrowanie dysków w magazynie kluczy.

![Zrzut ekranu przedstawiający pomyślne uprawnienie i przypisanie roli dla magazynu kluczy.](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Wdrażanie maszyny wirtualnej

Po utworzeniu i skonfigurowaniu magazynu kluczy oraz zestawu szyfrowania dysków można wdrożyć maszynę wirtualną przy użyciu szyfrowania.
Proces wdrażania maszyn wirtualnych jest podobny do standardowego procesu wdrażania, ale jedyną różnicą jest to, że należy wdrożyć maszynę wirtualną w tym samym regionie, co inne zasoby, i wybrać klucz zarządzany przez klienta.

1. Wyszukaj **Virtual Machines** a następnie wybierz pozycję **+ Dodaj** , aby utworzyć maszynę wirtualną.
1. Na karcie **podstawowa** wybierz ten sam region, w którym ustawiono szyfrowanie dysków i Azure Key Vault.
1. Wypełnij pozostałe wartości na karcie **podstawowa** .

    ![Zrzut ekranu przedstawiający środowisko tworzenia maszyn wirtualnych z wyróżnioną wartością region.](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Na karcie **dyski** wybierz pozycję **szyfrowanie w spoczynku z kluczem zarządzanym przez klienta**.
1. Wybierz pozycję Szyfrowanie dysków na liście rozwijanej **zestaw szyfrowania dysków** .
1. Wprowadź pozostałe wybrane opcje.

    ![Zrzut ekranu przedstawiający środowisko tworzenia maszyn wirtualnych, blok dysków. Z wyróżnioną listą rozwijaną ustawienia szyfrowania dysku.](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Włącz na istniejącym dysku

> [!CAUTION]
> Włączenie szyfrowania dysków na wszystkich dyskach dołączonych do maszyny wirtualnej wymaga zatrzymania maszyny wirtualnej.
    
1. Przejdź do maszyny wirtualnej, która znajduje się w tym samym regionie co jeden z zestawów szyfrowania dysków.
1. Otwórz maszynę wirtualną i wybierz pozycję **Zatrzymaj**.

    ![Zrzut ekranu przedstawiający główną nakładkę dla przykładowej maszyny wirtualnej. Z wyróżnionym przyciskiem Zatrzymaj](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. Po zakończeniu zatrzymywania maszyny wirtualnej wybierz pozycję **dyski** , a następnie wybierz dysk, który chcesz zaszyfrować.

    ![Zrzut ekranu przedstawiający przykładową maszynę wirtualną z otwartym blokiem dysków. Dysk systemu operacyjnego jest wyróżniony jako przykładowy dysk do wybrania.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Wybierz pozycję **szyfrowanie** i wybierz pozycję **szyfrowanie w spoczynku z kluczem zarządzanym przez klienta** , a następnie na liście rozwijanej wybierz zestaw szyfrowanie dysków.
1. Wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający przykładowy dysk systemu operacyjnego. Zostanie otwarty blok szyfrowanie, szyfrowanie w stanie spoczynku z kluczem zarządzanym przez klienta oraz przykład Azure Key Vault. Po dokonaniu wyboru należy zaznaczyć przycisk Zapisz.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Powtórz ten proces dla wszystkich innych dysków dołączonych do maszyny wirtualnej, którą chcesz zaszyfrować.
1. Po zakończeniu przełączania dysków na klucze zarządzane przez klienta, jeśli nie ma żadnych innych dołączonych dysków, które chcesz zaszyfrować, możesz uruchomić maszynę wirtualną.
