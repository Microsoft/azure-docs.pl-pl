---
title: Zagadnienia dotyczące projektowania Virtual Machine Scale Sets platformy Azure
description: Poznaj zagadnienia dotyczące projektowania Virtual Machine Scale Sets platformy Azure. Porównaj funkcje zestawów skalowania z funkcjami maszyn wirtualnych.
keywords: Maszyna wirtualna z systemem Linux, zestawy skalowania maszyn wirtualnych
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 04446243ed827cca4972a4b606c4930e74a2c704
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774970"
---
# <a name="design-considerations-for-scale-sets"></a>Zagadnienia dotyczące projektowania zestawów skalowania
W tym artykule omówiono zagadnienia dotyczące projektowania Virtual Machine Scale Sets. Aby uzyskać informacje o tym, co Virtual Machine Scale Sets, zobacz [omówienie Virtual Machine Scale Sets](./overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Kiedy używać zestawów skalowania zamiast maszyn wirtualnych?
Ogólnie zestawy skalowania są przydatne do wdrażania infrastruktury o wysokiej dostępności, w której zestaw maszyn ma podobną konfigurację. Jednak niektóre funkcje są dostępne tylko w zestawach skalowania, a inne funkcje są dostępne tylko na maszynach wirtualnych. W celu podjęcia świadomej decyzji o tym, kiedy należy używać poszczególnych technologii, należy najpierw zapoznać się z najczęściej używanymi funkcjami, które są dostępne w zestawach skalowania, ale nie na maszynach wirtualnych:

### <a name="scale-set-specific-features"></a>Funkcje specyficzne dla zestawu skalowania

- Po określeniu konfiguracji zestawu skalowania można zaktualizować właściwość *pojemności* , aby wdrożyć więcej maszyn wirtualnych równolegle. Ten proces jest lepszy niż pisanie skryptu w celu organizowania wdrożenia wielu pojedynczych maszyn wirtualnych równolegle.
- Funkcja automatycznego [skalowania platformy Azure umożliwia automatyczne skalowanie zestawu skalowania,](./virtual-machine-scale-sets-autoscale-overview.md) ale nie poszczególnych maszyn wirtualnych.
- Możesz odtworzyć [maszyny wirtualne zestawu skalowania](/rest/api/compute/virtualmachinescalesets/reimage) , ale [nie poszczególne maszyny wirtualne](/rest/api/compute/virtualmachines).
- Za pośrednictwem maszyn wirtualnych z zestawem [skalowania](#overprovisioning) można nadmiernie zaalokować czas wdrażania. Nie można nadmiernie zainicjować obsługi administracyjnej poszczególnych maszyn wirtualnych, chyba że utworzysz kod niestandardowy w celu wykonania tej akcji.
- Można określić [zasady uaktualniania](./virtual-machine-scale-sets-upgrade-scale-set.md) , aby ułatwić wdrażanie uaktualnień między maszynami wirtualnymi w zestawie skalowania. W przypadku pojedynczych maszyn wirtualnych należy samodzielnie zorganizować aktualizacje.

### <a name="vm-specific-features"></a>Funkcje specyficzne dla maszyny wirtualnej

Niektóre funkcje są obecnie dostępne tylko na maszynach wirtualnych:

- Możesz przechwycić obraz z pojedynczej maszyny wirtualnej, ale nie z maszyny wirtualnej w zestawie skalowania.
- Pojedynczą maszynę wirtualną można migrować z dysków natywnych do usługi Managed disks, ale nie można migrować wystąpień maszyn wirtualnych w zestawie skalowania.
- Publiczne adresy IP można przypisać do poszczególnych kart interfejsów sieci wirtualnej (nic), ale nie można tego zrobić dla wystąpień maszyn wirtualnych w zestawie skalowania. Publiczne adresy IP można przypisać do modułów równoważenia obciążenia przed indywidualnymi maszynami wirtualnymi lub maszynami wirtualnymi zestawów skalowania.

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Zestawy skalowania przy użyciu usługi Azure Managed Disks
Zestawy skalowania można tworzyć przy użyciu [usługi azure Managed disks](../virtual-machines/managed-disks-overview.md) zamiast tradycyjnych kont usługi Azure Storage. Managed Disks zapewnić następujące korzyści:
- Nie trzeba wstępnie tworzyć zestawu kont usługi Azure Storage dla maszyn wirtualnych zestawu skalowania.
- Można zdefiniować [Dołączone dyski danych](virtual-machine-scale-sets-attached-disks.md) dla maszyn wirtualnych w zestawie skalowania.
- Zestawy skalowania można skonfigurować tak, aby [obsługiwały do 1 000 maszyn wirtualnych w zestawie](virtual-machine-scale-sets-placement-groups.md). 

Jeśli masz istniejący szablon, możesz również [zaktualizować szablon, aby użyć Managed disks](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Magazyn zarządzany przez użytkownika
Zestaw skalowania, który nie jest zdefiniowany za pomocą usługi Azure Managed Disks, jest oparty na kontach magazynu utworzonych przez użytkownika do przechowywania dysków systemu operacyjnego maszyn wirtualnych w zestawie. Zalecany jest współczynnik 20 maszyn wirtualnych na konto magazynu lub mniej, aby można było osiągnąć maksymalną operację we/wy i korzystać z nadmiernego _udostępniania_ (zobacz poniżej). Zalecane jest również rozłożenie początkowych znaków nazw kont magazynu w alfabecie. Takie działanie pomaga rozłożyć obciążenie między różne systemy wewnętrzne. 


## <a name="overprovisioning"></a>Celi
Zestawy skalowania są obecnie domyślne dla maszyn wirtualnych o nadmiernej aprowizacji. Dzięki włączeniu nadmiarowego udostępniania zestaw skalowania faktycznie zmniejsza liczbę maszyn wirtualnych, niż jest to wymagane, a następnie usuwa dodatkowe maszyny wirtualne po pomyślnym zainicjowaniu żądanej liczby maszyn wirtualnych. Nadmierne Inicjowanie obsługi administracyjnej zwiększa szybkość inicjowania obsługi i skraca czas wdrażania. Nie są naliczane opłaty za dodatkowe maszyny wirtualne i nie są one wliczane do limitów przydziałów.

Mimo że nadmierne Inicjowanie obsługi administracyjnej zwiększa częstotliwość powodzeń aprowizacji, może to spowodować mylące zachowanie aplikacji, która nie jest przeznaczona do obsługi dodatkowych maszyn wirtualnych, które są wyświetlane, a następnie znika. Aby wyłączyć nadmierne Inicjowanie obsługi administracyjnej, upewnij się, że w szablonie znajduje się następujący ciąg: `"overprovision": "false"` . Więcej szczegółów można znaleźć w [dokumentacji interfejsu API REST zestawu skalowania](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Jeśli zestaw skalowania korzysta z magazynu zarządzanego przez użytkownika i zostanie wyłączone przeprowadzenie nadmiernej aprowizacji, możesz mieć więcej niż 20 maszyn wirtualnych na konto magazynu, ale nie zaleca się więcej niż 40 z przyczyn związanych z wydajnością we/wy. 

## <a name="limits"></a>Limity
Zestaw skalowania zbudowany na obrazie witryny Marketplace (znany również jako obraz platformy) i skonfigurowany do korzystania z platformy Azure Managed Disks obsługuje pojemności do 1 000 maszyn wirtualnych. Jeśli skonfigurujesz zestaw skalowania do obsługi ponad 100 maszyn wirtualnych, nie wszystkie scenariusze działają tak samo (na przykład Równoważenie obciążenia). Aby uzyskać więcej informacji, zobacz [Praca z dużymi zestawami skalowania maszyn wirtualnych](virtual-machine-scale-sets-placement-groups.md). 

Zestaw skalowania skonfigurowany przy użyciu kont magazynu zarządzanych przez użytkownika jest obecnie ograniczony do 100 maszyn wirtualnych, a dla tej skali zaleca się 5 kont magazynu.

Zestaw skalowania zbudowany na obrazie niestandardowym (utworzonym przez Ciebie) może mieć pojemność do 600 maszyn wirtualnych po skonfigurowaniu usługi Azure Managed Disks. Jeśli zestaw skalowania jest skonfigurowany przy użyciu kont magazynu zarządzanych przez użytkownika, należy utworzyć wszystkie dyski VHD dysków systemu operacyjnego w ramach jednego konta magazynu. W związku z tym Maksymalna zalecana liczba maszyn wirtualnych w zestawie skalowania zbudowanym na obrazie niestandardowym i zarządzanym przez użytkownika magazynem wynosi 20. Jeśli wyłączysz opcję nadmiernej aprowizacji, możesz przejść do 40.

W przypadku większej liczby maszyn wirtualnych niż te limity Zezwalaj na wdrożenie wielu zestawów skalowania, jak pokazano w [tym szablonie](https://azure.microsoft.com/resources/templates/301-custom-images-at-scale/).
