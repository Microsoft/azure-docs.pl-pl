---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/09/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e53dda2c6cb293a9204f344d152daa1937aa38b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008358"
---
W wersji zapoznawczej włączenie dysków udostępnionych jest możliwe tylko dla podzbioru typów dysków. Obecnie tylko Ultra disks i Premium dysków SSD mogą włączać dyski udostępnione. Każdy dysk zarządzany z włączonymi dyskami udostępnionymi podlega następującym ograniczeniom zorganizowanym według typu dysku:

### <a name="ultra-disks"></a>Dyski w warstwie Ultra

Niezwykle dyski mają własną listę ograniczeń, niezwiązanych z dyskami udostępnionymi. Ograniczenia dotyczące programu Ultra Disk można znaleźć w tematach [Korzystanie z usługi Azure Ultra disks](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

Podczas udostępniania Ultra disks obowiązują następujące dodatkowe ograniczenia:

- Obecnie obsługiwane tylko w regionie zachodnie stany USA.
- Obecnie ograniczona do Azure Resource Manager lub obsługi zestawu SDK.
- Tylko dyski podstawowe mogą być używane z niektórymi wersjami klastra trybu failover systemu Windows Server, aby uzyskać szczegółowe informacje, zobacz [wymagania sprzętowe klastra trybu failover i opcje magazynu](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>Dyski SSD w warstwie Premium

- Obecnie obsługiwane tylko w regionie zachodnie stany USA.
- Wszystkie maszyny wirtualne, które udostępniają dysk, muszą być wdrożone w tych samych [grupach umieszczania sąsiedztwa](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Można ją włączyć tylko na dyskach danych, a nie na dyskach systemu operacyjnego.
- Tylko dyski podstawowe mogą być używane z niektórymi wersjami klastra trybu failover systemu Windows Server, aby uzyskać szczegółowe informacje, zobacz [wymagania sprzętowe klastra trybu failover i opcje magazynu](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Buforowanie hosta tylko do odczytu nie jest dostępne dla dysków SSD `maxShares>1`Premium z.
- Zestawy dostępności i zestawy skalowania maszyn wirtualnych mogą być używane tylko `FaultDomainCount` z ustawioną opcją 1.
- Obsługa Azure Backup i Azure Site Recovery nie jest jeszcze dostępna.

Jeśli chcesz próbować korzystać z dysków udostępnionych, [Utwórz konto w wersji zapoznawczej](https://aka.ms/AzureSharedDiskPreviewSignUp).
