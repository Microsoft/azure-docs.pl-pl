---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f0a706cc51027bc503da9ee3bc637216e9e52853
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171376"
---
## <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Obsługiwane regiony

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Obsługiwane rozmiary maszyn wirtualnych

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było używać szyfrowania na hoście dla maszyn wirtualnych lub zestawów skalowania maszyn wirtualnych, należy włączyć tę funkcję w ramach subskrypcji. Wyślij wiadomość e-mail na adres encryptionAtHost@microsoft. com z identyfikatorami subskrypcji w celu włączenia funkcji dla subskrypcji.

Zaloguj się do Azure Portal przy użyciu [podanego linku](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Musisz użyć [podanego linku](https://aka.ms/diskencryptionupdates) , aby uzyskać dostęp do Azure Portal. Szyfrowanie na hoście nie jest obecnie widoczne w publicznej Azure Portal bez użycia linku.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Tworzenie zestawu Azure Key Vault i szyfrowania dysków

Po włączeniu tej funkcji należy skonfigurować Azure Key Vault i zestaw szyfrowania dysku, jeśli jeszcze tego nie zrobiono.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Wdrażanie maszyny wirtualnej

Aby włączyć szyfrowanie na hoście, należy wdrożyć nową maszynę wirtualną. nie można jej włączyć na istniejących maszynach wirtualnych.

1. Wyszukaj **Virtual Machines** a następnie wybierz pozycję **+ Dodaj** , aby utworzyć maszynę wirtualną.
1. Utwórz nową maszynę wirtualną, wybierz odpowiedni region i rozmiar obsługiwanej maszyny wirtualnej.
1. Wypełnij pozostałe wartości w bloku **podstawowe** , a następnie wybierz pozycję **dyski** w bloku.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Zrzut ekranu przedstawiający blok podstawy tworzenia maszyn wirtualnych, rozmiar regionu i rozmiaru V M są wyróżnione.":::

1. W bloku **dyski** wybierz opcję **tak** , aby włączyć **szyfrowanie na hoście**.
1. Wprowadź pozostałe wybrane opcje.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="Zrzut ekranu przedstawiający blok dysków tworzenia maszyn wirtualnych, szyfrowanie na hoście jest wyróżnione.":::

1. Zakończ proces wdrażania maszyny wirtualnej, wybierz odpowiednie opcje, które pasują do Twojego środowiska.

Maszyna wirtualna została wdrożona z szyfrowaniem na hoście włączonym, wszystkie skojarzone dyski zostaną zaszyfrowane przy użyciu szyfrowania na hoście.