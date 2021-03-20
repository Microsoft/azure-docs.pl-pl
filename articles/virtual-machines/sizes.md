---
title: Rozmiary maszyn wirtualnych
description: Wyświetla różne rozmiary dostępne dla maszyn wirtualnych na platformie Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 07/21/2020
ms.author: jushiman
ms.openlocfilehash: d9377ba22f1461762e53b1004dfe5f06c2d7b972
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89420221"
---
# <a name="sizes-for-virtual-machines-in-azure"></a>Rozmiary maszyn wirtualnych na platformie Azure

W tym artykule opisano dostępne rozmiary i opcje dla maszyn wirtualnych platformy Azure, których można użyć do uruchamiania aplikacji i obciążeń. Zawiera również zagadnienia dotyczące wdrażania, które należy wziąć pod uwagę w przypadku planowania użycia tych zasobów. 

| Typ | Rozmiary | Opis |
|------|-------|-------------|
| [Ogólnego przeznaczenia](sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, Dv4, Dsv4, Ddv4, Ddsv4  | Zrównoważony współczynnik mocy procesora CPU w stosunku do pamięci. Idealne rozwiązanie na potrzeby testowania i wdrażania, małych i średnich baz danych oraz serwerów internetowych o małym lub średnim ruchu. |
| [Optymalizacja pod kątem obliczeń](sizes-compute.md) | F, FS, Fsv2 | Duży współczynnik mocy procesora CPU w stosunku do pamięci. Dobre dla serwerów sieci Web o średnim ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji. |
| [Optymalizacja pod kątem pamięci](sizes-memory.md) | Esv3, EV3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Duży współczynnik pamięci w stosunku do mocy procesora CPU. Opcja bardzo dobra w przypadku serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.                 |
| [Optymalizacja pod kątem magazynu](sizes-storage.md) | Lsv2 | Wysoka przepływność dysku i doskonałe operacje we/wy dla danych Big Data, baz danych SQL, NoSQL, magazynowania danych i dużych transakcyjnych baz danych.  |
| [Procesor GPU](sizes-gpu.md) | NC, NCv2, Seria NCV3, NCasT4_v3 (wersja zapoznawcza), ND, NDv2 (wersja zapoznawcza), NV, NVv3, NVv4 | Wyspecjalizowane maszyny wirtualne przeznaczone do intensywnego renderowania grafiki i edytowania wideo, a także szkolenia modelowe i inferencing (ND) z głębokie uczenie. Dostępne z pojedynczym lub wieloma procesorami GPU. |
| [Obliczenia o wysokiej wydajności](sizes-hpc.md) | HB, HBv2, HC, H | Nasze najszybsze i najbardziej wydajne maszyny wirtualne procesora z opcjonalnymi interfejsami sieciowymi o wysokiej przepływności (RDMA). |

- Aby uzyskać informacje o cenach różnych rozmiarów, zobacz strony cen dla systemu [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux) lub [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/Windows/#Windows).
- Aby uzyskać dostęp do rozmiarów maszyn wirtualnych w regionach platformy Azure, zobacz temat [produkty dostępne według regionów](https://azure.microsoft.com/regions/services/).
- Aby wyświetlić ogólne limity dotyczące maszyn wirtualnych platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Aby uzyskać więcej informacji na temat nazw maszyn wirtualnych platformy Azure, zobacz [konwencje nazewnictwa maszyn wirtualnych platformy Azure](./vm-naming-conventions.md).

## <a name="rest-api"></a>Interfejs API REST

Aby uzyskać informacje na temat używania interfejsu API REST do wysyłania zapytań o rozmiary maszyn wirtualnych, zobacz następujące tematy:

- [Wyświetl dostępne rozmiary maszyn wirtualnych na potrzeby zmiany rozmiaru](/rest/api/compute/virtualmachines/listavailablesizes)
- [Wyświetlanie listy dostępnych rozmiarów maszyn wirtualnych dla subskrypcji](/rest/api/compute/resourceskus/list)
- [Wyświetlanie listy dostępnych rozmiarów maszyn wirtualnych w zestawie dostępności](/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.

## <a name="benchmark-scores"></a>Wyniki testów porównawczych

Dowiedz się więcej o wydajności obliczeń dla maszyn wirtualnych z systemem Linux przy użyciu [wyników testów porównawczych](./linux/compute-benchmark-scores.md).

Dowiedz się więcej o wydajności obliczeń dla maszyn wirtualnych z systemem Windows za pomocą [wyników testów porównawczych SPECInt](./windows/compute-benchmark-scores.md).

## <a name="manage-costs"></a>Zarządzanie kosztami

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o różnych dostępnych rozmiarach maszyn wirtualnych:

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Procesor GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- Sprawdź [poprzednią generację](sizes-previous-gen.md) strony, aby zapoznać się ze standardem, Dv1 (D1-4 i D11-14 v1) oraz seriami A8-A11
