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
ms.openlocfilehash: a967777b65c06cf23239a47e8e691fb3a29231b4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "88815474"
---
Skonfigurowanie kluczy zarządzanych przez klienta dla dysków będzie wymagało utworzenia zasobów w określonej kolejności, jeśli jest to wykonywane po raz pierwszy. Najpierw należy utworzyć i skonfigurować Azure Key Vault.

## <a name="set-up-your-azure-key-vault"></a>Skonfiguruj Azure Key Vault

1. Zaloguj się do [Azure Portal](https://aka.ms/diskencryptionupdates).
1. Wyszukaj i wybierz pozycję **magazyny kluczy**.

    [![Zrzut ekranu przedstawiający Azure Portal przy użyciu okna dialogowego Wyszukiwanie rozwinięte.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Aby wdrożenie zakończyło się pomyślnie, w tym samym regionie i w ramach subskrypcji musi znajdować się Twój Magazyn kluczy platformy Azure, zestaw szyfrowania dysków, maszyna wirtualna, dyski i migawki.

1. Wybierz pozycję **+ Dodaj** , aby utworzyć nowy Key Vault.
1. Tworzenie nowej grupy zasobów
1. Wprowadź nazwę magazynu kluczy, wybierz region, a następnie wybierz warstwę cenową.

    > [!NOTE]
    > Podczas tworzenia wystąpienia Key Vault należy włączyć opcję trwałe usuwanie i przeczyszczanie ochrony. Usuwanie nietrwałe gwarantuje, że Key Vault przechowuje usunięty klucz dla danego okresu przechowywania (wartość domyślna 90). Ochrona przed przeczyszczeniem gwarantuje, że usunięty klucz nie może zostać trwale usunięty, dopóki nie upłynie okres przechowywania. Te ustawienia chronią przed utratą danych z powodu przypadkowego usunięcia. Te ustawienia są obowiązkowe w przypadku używania Key Vault do szyfrowania dysków zarządzanych.

1. Wybierz pozycję **Recenzja + Utwórz**, sprawdź wybrane opcje, a następnie wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający środowisko tworzenia Azure Key Vault. Wyświetlanie określonych wartości, które tworzysz](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. Po zakończeniu wdrażania magazynu kluczy wybierz go.
1. Wybierz pozycję **klucze** w obszarze **Ustawienia**.
1. Wybierz pozycję **Generuj/Importuj**.

    ![Zrzut ekranu przedstawiający okienko ustawień zasobów Key Vault. Pokazuje przycisk Generuj/Importuj wewnątrz ustawień.](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. Pozostaw **Typ klucza** ustawiony na wartość **RSA** i **rozmiar klucza RSA** ustawiony na **2048**.
1. Wypełnij pozostałe wybrane opcje, a następnie wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający blok Utwórz klucz, który pojawia się po wybraniu przycisku Generuj/Importuj](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>Konfigurowanie zestawu szyfrowania dysków

1. Wyszukaj **zestawy szyfrowania dysków** i wybierz je.
1. W bloku **zestawy szyfrowania dysków** wybierz pozycję **+ Dodaj**.

    ![Zrzut ekranu przedstawiający ekran główny portalu szyfrowania dysku. Wyróżnianie przycisku Dodaj](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. Wybierz grupę zasobów, Nazwij zestaw szyfrowania i wybierz ten sam region, w którym znajduje się Twój Magazyn kluczy.
1. W obszarze **typ szyfrowania** wybierz pozycję **szyfrowanie przy użyciu klucza zarządzanego przez klienta**.

    > [!NOTE]
    > Po utworzeniu zestawu szyfrowania dysku z określonym typem szyfrowania nie można go zmienić. Jeśli chcesz użyć innego typu szyfrowania, musisz utworzyć nowy zestaw szyfrowania dysków.

1. Wybierz **pozycję kliknij, aby wybrać klucz**.
1. Wybierz utworzony wcześniej Magazyn kluczy i klucz, a także wersję.
1. Naciśnij **pozycję Wybierz**.
1. Wybierz pozycję **Przeglądanie + tworzenie**, a następnie pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający blok tworzenia szyfrowania dysku. Wyświetlanie subskrypcji, grupy zasobów, nazwy zestawu szyfrowania dysku, regionu i selektora kluczy + Key.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)

1. Otwórz zestaw szyfrowanie dysków po zakończeniu tworzenia i wybierz alert, który zostanie wystawiony.

    ![Zrzut ekranu przedstawiający okno podręczne alertu: ' aby skojarzyć dysk, obraz lub migawkę z zestawem szyfrowania dysków, należy przyznać uprawnienia do magazynu kluczy. Wybierz ten alert, aby kontynuować](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

    Dwie powiadomienia powinny wyskakujące i zakończyć się pomyślnie. Dzięki temu można używać zestawu szyfrowanie dysków z magazynem kluczy.

    ![Zrzut ekranu przedstawiający pomyślne uprawnienie i przypisanie roli dla magazynu kluczy.](./media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)