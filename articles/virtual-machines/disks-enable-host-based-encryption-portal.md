---
title: Włącz kompleksowe szyfrowanie przy użyciu szyfrowania na dyskach zarządzanych hosta Azure Portal
description: Użyj szyfrowania na hoście, aby umożliwić kompleksowe szyfrowanie na dyskach zarządzanych platformy Azure — Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4498e78b408f64ab5bc00b9f8730559b90c95d57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88817659"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Użyj Azure Portal, aby włączyć szyfrowanie kompleksowe przy użyciu szyfrowania na hoście

Po włączeniu szyfrowania na hoście dane przechowywane na hoście maszyny wirtualnej są szyfrowane w stanie spoczynku i są zaszyfrowane w usłudze Storage. Aby uzyskać informacje koncepcyjne na temat szyfrowania na hoście, a także inne typy szyfrowania dysków zarządzanych, zobacz:

* Linux: [szyfrowanie na hoście-end-to-end dla danych maszyny wirtualnej](./linux/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

* Windows: [szyfrowanie na hoście-end-to-end dla danych maszyny wirtualnej](./windows/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Obsługiwane regiony

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Obsługiwane rozmiary maszyn wirtualnych

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było używać szyfrowania na hoście dla maszyn wirtualnych lub zestawów skalowania maszyn wirtualnych, należy włączyć tę funkcję w ramach subskrypcji. Wyślij wiadomość e-mail na adres encryptionAtHost@microsoft. com z identyfikatorami subskrypcji w celu włączenia funkcji dla subskrypcji.

Zaloguj się do Azure Portal przy użyciu [podanego linku](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Musisz użyć [podanego linku](https://aka.ms/diskencryptionupdates) , aby uzyskać dostęp do Azure Portal. Szyfrowanie na hoście nie jest obecnie widoczne w publicznej Azure Portal bez użycia linku.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Tworzenie zestawu Azure Key Vault i szyfrowania dysków

Po włączeniu tej funkcji należy skonfigurować Azure Key Vault i zestaw szyfrowania dysku, jeśli jeszcze tego nie zrobiono.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Wdrażanie maszyny wirtualnej

Aby włączyć szyfrowanie na hoście, należy wdrożyć nową maszynę wirtualną. nie można jej włączyć na istniejących maszynach wirtualnych.

1. Wyszukaj **Virtual Machines** a następnie wybierz pozycję **+ Dodaj** , aby utworzyć maszynę wirtualną.
1. Utwórz nową maszynę wirtualną, wybierz odpowiedni region i rozmiar obsługiwanej maszyny wirtualnej.
1. Wypełnij pozostałe wartości w bloku **podstawowe** , a następnie wybierz pozycję **dyski** w bloku.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Zrzut ekranu przedstawiający blok podstawy tworzenia maszyn wirtualnych, rozmiar regionu i rozmiaru V M są wyróżnione.":::

1. W bloku **dyski** wybierz opcję **tak** , aby włączyć **szyfrowanie na hoście**.
1. Wprowadź pozostałe wybrane opcje.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="Zrzut ekranu przedstawiający blok podstawy tworzenia maszyn wirtualnych, rozmiar regionu i rozmiaru V M są wyróżnione.":::

1. Zakończ proces wdrażania maszyny wirtualnej, wybierz odpowiednie opcje, które pasują do Twojego środowiska.

Maszyna wirtualna została wdrożona z szyfrowaniem na hoście włączonym, wszystkie skojarzone dyski zostaną zaszyfrowane przy użyciu szyfrowania na hoście.

## <a name="next-steps"></a>Następne kroki

[Przykłady szablonów usługi Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)