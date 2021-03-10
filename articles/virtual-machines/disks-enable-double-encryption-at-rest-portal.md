---
title: Włącz podwójne szyfrowanie na dyskach zarządzanych w Azure Portal REST
description: Włącz podwójne szyfrowanie dla danych dysku zarządzanego przy użyciu Azure Portal.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b5f58b549fc9d879f6c2e5515c679d82c3ff44e4
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560365"
---
# <a name="use-the-azure-portal-to-enable-double-encryption-at-rest-for-managed-disks"></a>Użyj Azure Portal, aby włączyć podwójne szyfrowanie dla dysków zarządzanych

Azure Disk Storage obsługuje podwójne szyfrowanie dla dysków zarządzanych. Aby uzyskać informacje koncepcyjne na temat podwójnego szyfrowania w stanie spoczynku, a także innych typów szyfrowania dysków zarządzanych, zobacz sekcję [podwójne szyfrowanie w spoczynku](disk-encryption.md#double-encryption-at-rest) artykułu szyfrowanie dysków.

## <a name="getting-started"></a>Wprowadzenie

1. Zaloguj się w witrynie [Azure Portal](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > Musisz użyć [podanego linku](https://aka.ms/diskencryptionupdates) , aby uzyskać dostęp do Azure Portal. Podwójne szyfrowanie w czasie spoczynku nie jest obecnie widoczne w publicznej Azure Portal bez użycia linku.

1. Wyszukaj i wybierz pozycję **zestawy szyfrowania dysków**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Zrzut ekranu przedstawiający główne Azure Portal zestawy szyfrowania dysków są wyróżnione na pasku wyszukiwania.":::

1. Wybierz pozycję **+ Dodaj**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Zrzut ekranu przedstawiający blok zestawu szyfrowania dysku + Dodaj jest wyróżniony.":::

1. Wybierz jeden z obsługiwanych regionów.
1. W obszarze **typ szyfrowania** wybierz pozycję **szyfrowanie podwójne przy użyciu kluczy zarządzanych przez platformę i klienta**.

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
    
    ![Zrzut ekranu przedstawiający pomyślne uprawnienie i przypisanie roli dla magazynu kluczy.](media/virtual-machines-disks-double-encryption-at-rest-portal/disk-encryption-notification-success.png)

1. Przejdź do dysku.
1. Wybierz pozycję **szyfrowanie**.
1. W obszarze **typ szyfrowania** wybierz pozycję **szyfrowanie podwójne przy użyciu kluczy zarządzanych przez platformę i klienta**.
1. Wybierz zestaw szyfrowania dysków.
1. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Zrzut ekranu przedstawiający blok szyfrowania dla dysku zarządzanego, wyróżniony wcześniej typ szyfrowania.":::

Teraz włączono podwójne szyfrowanie na dysku zarządzanym.


## <a name="next-steps"></a>Następne kroki

- [Azure PowerShell — Włącz klucze zarządzane przez klienta za pomocą dysków zarządzanych przez szyfrowanie po stronie serwera](./windows/disks-enable-customer-managed-keys-powershell.md)
- [Przykłady szablonów usługi Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Włącz klucze zarządzane przez klienta przy użyciu szyfrowania po stronie serwera — przykłady](./linux/disks-enable-customer-managed-keys-cli.md#examples)