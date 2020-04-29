---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73412994"
---
**Wychodzące transfery danych**: [wychodzące transfery danych](https://azure.microsoft.com/pricing/details/bandwidth/) (dane przesyłane z centrów danych platformy Azure) powodują naliczanie opłat za użycie przepustowości.

**Transakcje**: opłaty są naliczane za liczbę transakcji wykonywanych na dysku zarządzanym w warstwie Standardowa. W przypadku standardowej dysków SSD każda operacja we/wy mniejsza lub równa 256 KiB przepływności jest uznawana za pojedynczą operację we/wy. Operacje we/wy o rozmiarze większym niż 256 KiB przepływności są uważane za wiele systemów I/OS o rozmiarach 256 KiB. W przypadku standardowej HDD każda operacja we/wy jest uznawana za pojedynczą transakcję, niezależnie od rozmiaru wejścia/wyjścia.

Aby uzyskać szczegółowe informacje na temat cen Managed Disks, w tym kosztów transakcji, zobacz [Managed disks cennika](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Opłata rezerwacji na maszynę wirtualną Ultra Disk

Maszyny wirtualne platformy Azure mają możliwość wskazywania, czy są one zgodne z Ultra Disks. Maszyna wirtualna zgodne z dyskami jest przydzielona dedykowanej przepustowości między wystąpieniem maszyny wirtualnej obliczeniowej a jednostką skali magazynu blokowego w celu zoptymalizowania wydajności i skrócenia opóźnień. Dodanie tej funkcji na maszynie wirtualnej spowoduje naliczenie opłaty za rezerwację, która jest nałożona tylko wtedy, gdy na maszynie wirtualnej jest włączona funkcja Ultra Disk bez dołączania do niej dysku. Gdy dysk jest podłączony do maszyny wirtualnej zgodnej z dyskiem Ultra, ta opłata nie zostanie zastosowana. Ta opłata jest naliczana za vCPU na maszynie wirtualnej. 

> [!Note]
> W przypadku [ograniczonych rozmiarów maszyn wirtualnych](../articles/virtual-machines/linux/constrained-vcpu.md)opłata za rezerwację jest oparta na rzeczywistej liczbie procesorów wirtualnych vCPU, a nie w ograniczonych rdzeniach. W przypadku Standard_E32-8s_v3 opłata za rezerwację będzie oparta na 32 rdzeniach. 

Zapoznaj się ze [stroną cennika usługi Azure disks](https://azure.microsoft.com/pricing/details/managed-disks/) , aby uzyskać szczegółowe informacje o cenach.

### <a name="azure-disk-reservation"></a>Rezerwacja dysku platformy Azure

Rezerwacja dysku to opcja zakupu jednego roku magazynu dyskowego z góry z rabatem, co zmniejsza całkowity koszt. Podczas kupowania rezerwacji dysku wybierana jest określona jednostka SKU dysku w regionie docelowym, na przykład 10 P30 (1TiB) Premium dysków SSD w regionie Wschodnie stany USA 2 przez okres jednego roku. Środowisko rezerwacji jest podobne do wystąpień zarezerwowanych maszyn wirtualnych. W celu zmaksymalizowania oszczędności można powiązać rezerwacje maszyn wirtualnych i dysków. Na razie rezerwacja Azure disks oferuje roczny plan zobowiązania dla jednostek SKU SSD w warstwie Premium z P30 (1TiB) do P80 (32 TiB) we wszystkich regionach produkcyjnych. Aby uzyskać więcej informacji na temat cen dysków zarezerwowanych, zobacz [stronę cennika usługi Azure disks](https://azure.microsoft.com/pricing/details/managed-disks/).