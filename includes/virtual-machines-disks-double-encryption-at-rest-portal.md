---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8313259d5375bb19e7caa08a01a658707453d133
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177046"
---
## <a name="supported-regions"></a>Obsługiwane regiony

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>Wprowadzenie

1. Zaloguj się w witrynie [Azure Portal](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > Musisz użyć [podanego linku](https://aka.ms/diskencryptionupdates) , aby uzyskać dostęp do Azure Portal. Podwójne szyfrowanie w czasie spoczynku nie jest obecnie widoczne w publicznej Azure Portal bez użycia linku.

1. Wyszukaj i wybierz pozycję **zestawy szyfrowania dysków**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Zrzut ekranu przedstawiający główne Azure Portal zestawy szyfrowania dysków są wyróżnione na pasku wyszukiwania.":::

1. Wybierz pozycję **+ Dodaj**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Zrzut ekranu przedstawiający blok zestawu szyfrowania dysku + Dodaj jest wyróżniony.":::

1. Wybierz jeden z obsługiwanych regionów.
1. W obszarze **typ szyfrowania**wybierz pozycję **szyfrowanie podwójne przy użyciu kluczy zarządzanych przez platformę i klienta**.

    > [!NOTE]
    > Po utworzeniu zestawu szyfrowania dysku z określonym typem szyfrowania nie można go zmienić. Jeśli chcesz użyć innego typu szyfrowania, musisz utworzyć nowy zestaw szyfrowania dysków.

1. Wypełnij pozostałe informacje.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="Zrzut ekranu przedstawiający blok tworzenia zestawu szyfrowania dysku, regiony i podwójne szyfrowanie z kluczami zarządzanymi przez platformę i zarządzane przez klienta.":::

1. Wybierz Azure Key Vault i klucz lub Utwórz nowy, jeśli jest to konieczne.

    > [!NOTE]
    > Jeśli utworzysz wystąpienie Key Vault, musisz włączyć ochronę nietrwałego usuwania i przeczyszczania. Te ustawienia są obowiązkowe w przypadku używania Key Vault do szyfrowania dysków zarządzanych i ochrony przed utratą danych z powodu przypadkowego usunięcia.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Zrzut ekranu przedstawiający blok tworzenia Key Vault.":::

1. Wybierz przycisk **Utwórz**.
1. Przejdź do utworzonego zestawu szyfrowania dysków i wybierz wyświetlany błąd. Spowoduje to skonfigurowanie ustawienia szyfrowania dysku.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="Zrzut ekranu przedstawiający wyświetlony błąd, tekst błędu: Aby skojarzyć dysk, obraz lub migawkę z tym zestawem szyfrowania dysków, należy przyznać uprawnienia do magazynu kluczy.":::

    Powiadomienie powinno się wyskakujące i zakończyć pomyślnie. Pozwoli to na użycie zestawu szyfrowanie dysków w magazynie kluczy.
    
    ![Zrzut ekranu przedstawiający pomyślne uprawnienie i przypisanie roli dla magazynu kluczy.](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. Przejdź do dysku.
1. Wybierz pozycję **szyfrowanie**.
1. W obszarze **typ szyfrowania**wybierz pozycję **szyfrowanie podwójne przy użyciu kluczy zarządzanych przez platformę i klienta**.
1. Wybierz zestaw szyfrowania dysków.
1. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Zrzut ekranu przedstawiający blok szyfrowania dla dysku zarządzanego, wyróżniony wcześniej typ szyfrowania.":::

Teraz włączono podwójne szyfrowanie na dysku zarządzanym.
