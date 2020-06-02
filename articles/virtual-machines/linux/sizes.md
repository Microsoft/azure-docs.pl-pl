---
title: Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure
description: Wyświetla listę różnych rozmiarów dostępnych dla maszyn wirtualnych z systemem Linux na platformie Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 6d3f4750a795611de05c610c32c24c3ebd0fc84b
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84249131"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure

W tym artykule opisano dostępne rozmiary i opcje dla maszyn wirtualnych platformy Azure, za pomocą których można uruchamiać aplikacje i obciążenia systemu Linux. Zawiera również zagadnienia dotyczące wdrażania, które należy wziąć pod uwagę w przypadku planowania użycia tych zasobów. Ten artykuł jest również dostępny dla [maszyn wirtualnych z systemem Windows](../windows/sizes.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json).

| Typ | Rozmiary | Opis |
|------|-------|-------------|
| [Zastosowania ogólne](../sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, Ddv4, Ddsv4  | Zrównoważony współczynnik procesora CPU do pamięci. Idealne rozwiązanie na potrzeby testowania i wdrażania, małych i średnich baz danych oraz serwerów internetowych o małym lub średnim ruchu. |
| [Optymalizacja pod kątem obliczeń](../sizes-compute.md) | Fsv2 | Wysoki współczynnik procesora CPU do pamięci. Dobre dla serwerów sieci Web o średnim ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji. |
| [Optymalizacja pod kątem pamięci](../sizes-memory.md) | Esv3, EV3, Easv4, Eav4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Duże proporcje pamięci i procesora CPU. Opcja bardzo dobra w przypadku serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.                 |
| [Optymalizacja pod kątem magazynu](../sizes-storage.md) | Lsv2 | Wysoka przepływność dysku i doskonałe operacje we/wy dla danych Big Data, baz danych SQL, NoSQL, magazynowania danych i dużych transakcyjnych baz danych.  |
| [Procesor GPU](../sizes-gpu.md) | NC, NCv2, Seria NCV3, ND, NDv2 (wersja zapoznawcza), NV, NVv3, NVv4 | Wyspecjalizowane maszyny wirtualne przeznaczone do intensywnego renderowania grafiki i edytowania wideo, a także szkolenia modelowe i inferencing (ND) z głębokie uczenie. Dostępne z pojedynczym lub wieloma procesorami GPU. |
| [Obliczenia o wysokiej wydajności](../sizes-hpc.md) | HB, HBv2, HC, H | Nasze najszybsze i najbardziej wydajne maszyny wirtualne procesora z opcjonalnymi interfejsami sieciowymi o wysokiej przepływności (RDMA). |

- Aby uzyskać informacje o cenach różnych rozmiarów, zobacz [Virtual Machines Cennik](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Aby uzyskać dostęp do rozmiarów maszyn wirtualnych w regionach platformy Azure, zobacz temat [produkty dostępne według regionów](https://azure.microsoft.com/regions/services/).
- Aby wyświetlić ogólne limity dotyczące maszyn wirtualnych platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../../azure-subscription-service-limits.md).
- Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](../acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.

## <a name="rest-api"></a>Interfejs API REST

Aby uzyskać informacje na temat używania interfejsu API REST do wysyłania zapytań o rozmiary maszyn wirtualnych, zobacz następujące tematy:

- [Wyświetl dostępne rozmiary maszyn wirtualnych na potrzeby zmiany rozmiaru](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Wyświetlanie listy dostępnych rozmiarów maszyn wirtualnych dla subskrypcji](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Wyświetlanie listy dostępnych rozmiarów maszyn wirtualnych w zestawie dostępności](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](../acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.

## <a name="benchmark-scores"></a>Wyniki testów porównawczych

Dowiedz się więcej o wydajności obliczeń dla maszyn wirtualnych z systemem Linux przy użyciu [wyników testów porównawczych](compute-benchmark-scores.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o różnych dostępnych rozmiarach maszyn wirtualnych:

- [Zastosowania ogólne](../sizes-general.md)
- [Optymalizacja pod kątem obliczeń](../sizes-compute.md)
- [Optymalizacja pod kątem pamięci](../sizes-memory.md)
- [Optymalizacja pod kątem magazynu](../sizes-storage.md)
- [Procesor GPU](../sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](../sizes-hpc.md)
- Sprawdź [poprzednią generację](../sizes-previous-gen.md) strony, aby zapoznać się ze standardem, Dv1 (D1-4 i D11-14 v1) oraz seriami A8-A11
