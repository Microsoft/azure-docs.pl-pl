---
title: Rozmiary maszyn wirtualnych
description: Wyświetla listę różnych rozmiarów dostępnych dla maszyn wirtualnych na platformie Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 07/21/2020
ms.author: jushiman
ms.openlocfilehash: f2a4083e8cbbdbfbf971c441d8942b48e712c472
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740032"
---
# <a name="sizes-for-virtual-machines-in-azure"></a>Rozmiary maszyn wirtualnych na platformie Azure

W tym artykule opisano dostępne rozmiary i opcje dla maszyn wirtualnych platformy Azure, których można użyć do uruchamiania aplikacji i obciążeń. Przedstawiono w nim również zagadnienia dotyczące wdrażania, które należy wziąć pod uwagę podczas planowania korzystania z tych zasobów. 

:::image type="content" source="media/sizes/azurevmsthumb.jpg" alt-text="Wideo w serwisie YouTube dotyczące wybierania odpowiedniego rozmiaru maszyny wirtualnej." link="https://youtu.be/zOSvnJFd3ZM":::

| Typ | Rozmiary | Opis |
|------|-------|-------------|
| [Ogólnego przeznaczenia](sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, Dv4, Dsv4, Ddv4, Ddsv4  | Zrównoważony współczynnik mocy procesora CPU w stosunku do pamięci. Idealne rozwiązanie na potrzeby testowania i wdrażania, małych i średnich baz danych oraz serwerów internetowych o małym lub średnim ruchu. |
| [Optymalizacja pod kątem obliczeń](sizes-compute.md) | F, Fs, Fsv2 | Duży współczynnik mocy procesora CPU w stosunku do pamięci. Dobra w przypadku serwerów internetowych o średnim ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji. |
| [Optymalizacja pod kątem pamięci](sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Duży współczynnik pamięci w stosunku do mocy procesora CPU. Opcja bardzo dobra w przypadku serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.                 |
| [Optymalizacja pod kątem magazynu](sizes-storage.md) | Lsv2 | Wysoka przepływność dysku i liczba operacji we/wy idealne dla danych big data, baz danych SQL, NoSQL, magazynowania danych i dużych transakcyjnych baz danych.  |
| [Procesor GPU](sizes-gpu.md) | NC, NCv2, NCv3, NCasT4_v3, ND, NDv2, NV, NVv3, NVv4 | Wyspecjalizowane maszyny wirtualne, których celem jest intensywne renderowanie grafiki i edytowanie wideo, a także trenowanie modeli i wnioskowanie (ND) z uczeniem głębokim. Dostępne z pojedynczym lub wieloma procesorami GPU. |
| [Obliczenia o wysokiej wydajności](sizes-hpc.md) | HB, HBv2, HC, H | Maszyny wirtualne z najszybszymi i najbardziej wydajnymi procesorami CPU z opcjonalnymi interfejsami sieciowym o wysokiej przepływności (RDMA). |

- Aby uzyskać informacje o cenach różnych rozmiarów, zobacz strony cennika dla systemu [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux) lub [Windows.](https://azure.microsoft.com/pricing/details/virtual-machines/Windows/#Windows)
- Aby uzyskać informacje o dostępności rozmiarów maszyn wirtualnych w regionach świadczenia usługi Azure, [zobacz Dostępność produktów według regionów.](https://azure.microsoft.com/regions/services/)
- Aby wyświetlić ogólne limity dotyczące maszyn wirtualnych platformy Azure, zobacz [Azure subscription and service limits, quotas, and constraints](../azure-resource-manager/management/azure-subscription-service-limits.md)(Limity, przydziały i ograniczenia usług i subskrypcji platformy Azure).
- Aby uzyskać więcej informacji na temat sposobu, w jaki platforma Azure nazywa swoje maszyny wirtualne, zobacz Konwencje nazewnictwa rozmiarów maszyn [wirtualnych platformy Azure.](./vm-naming-conventions.md)

## <a name="rest-api"></a>Interfejs API REST

Aby uzyskać informacje na temat używania interfejsu API REST do wykonywania zapytań dotyczących rozmiarów maszyn wirtualnych, zobacz następujące informacje:

- [Lista dostępnych rozmiarów maszyn wirtualnych do zmiany rozmiaru](/rest/api/compute/virtualmachines/listavailablesizes)
- [Lista dostępnych rozmiarów maszyn wirtualnych dla subskrypcji](/rest/api/compute/resourceskus/list)
- [Lista dostępnych rozmiarów maszyn wirtualnych w zestawie dostępności](/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej między jednostkami SKU platformy Azure.

## <a name="benchmark-scores"></a>Wyniki testów porównawczych

Dowiedz się więcej o wydajności obliczeń dla maszyn wirtualnych z systemem Linux, korzystając z wyników testów [porównawczych coremark.](./linux/compute-benchmark-scores.md)

Dowiedz się więcej na temat wydajności obliczeniowej maszyn wirtualnych z systemem Windows przy użyciu wyników [testów porównawczych SPECInt.](./windows/compute-benchmark-scores.md)

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
- Sprawdź stronę [Poprzedniej generacji](sizes-previous-gen.md) dla serii A Standard, Dv1 (D1-4 i D11-14 v1) oraz A8-A11
