---
title: Migrowanie z usługi AWS i innych platform do dysków zarządzanych na platformie Azure
description: Tworzenie maszyn wirtualnych na platformie Azure przy użyciu dysków wirtualnych przekazanych z innych chmur, takich jak AWS lub innych platform wirtualizacji i korzystać z dysków zarządzanych platformy Azure.
author: roygara
manager: twooley
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dc283d1845926b79fb541d8ccb011fe853f50484
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870278"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migracja z usług Amazon Web Services (AWS) i innych platform na dyski zarządzane na platformie Azure

Można przekazać pliki VHD z usług AWS lub lokalnych rozwiązań wirtualizacji na platformę Azure, aby utworzyć maszyny wirtualne korzystające z dysków zarządzanych. Dyski zarządzane platformy Azure usuwa potrzebę zarządzania kontami magazynu dla maszyn wirtualnych usługi Azure IaaS. Należy określić tylko typ (Premium lub Standard) i rozmiar dysku, którego potrzebujesz, a platforma Azure tworzy dysk i zarządza nim. 

Można przesłać uogólnione i wyspecjalizowane veds. 
- **Uogólniony VHD** - miał wszystkie dane osobowe usunięte za pomocą Sysprep. 
- **Specialized VHD** - przechowuje konta użytkowników, aplikacje i inne dane o stanie z oryginalnej maszyny Wirtualnej. 

> [!IMPORTANT]
> Przed przekazaniem dowolnego dysku VHD na platformę Azure należy postępować zgodnie z postępującą po [przygotowaniu dysku VHD lub VHDX systemu Windows do przesłania na platformę Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Scenariusz                                                                                                                         | Dokumentacja                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Masz istniejące wystąpienia AWS EC2, które chcesz przeprowadzić migrację do maszyn wirtualnych platformy Azure przy użyciu dysków zarządzanych                              | [Przenoszenie maszyny Wirtualnej z usług Amazon Web Services (AWS) na platformę Azure](aws-to-azure.md)                           |
| Masz maszynę wirtualną z innej platformy wirtualizacji, która ma być używana jako obraz do tworzenia wielu maszyn wirtualnych platformy Azure. | [Przekaż uogólniony dysk VHD i użyj go do utworzenia nowej maszyny Wirtualnej na platformie Azure](upload-generalized-managed.md) |
| Masz unikatowo dostosowaną maszynę wirtualną, którą chcesz odtworzyć na platformie Azure.                                                      | [Przekazywanie specjalistycznego dysku twardego na platformę Azure i tworzenie nowej maszyny Wirtualnej](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Omówienie dysków zarządzanych

Dyski zarządzane platformy Azure upraszczają zarządzanie maszynami wirtualnymi, usuwając konieczność zarządzania kontami magazynu. Dyski zarządzane również korzystać z lepszej niezawodności maszyn wirtualnych w zestawie dostępności. Zapewnia, że dyski różnych maszyn wirtualnych w zestawie dostępności są wystarczająco odizolowane od siebie, aby uniknąć pojedynczego punktu awarii. Automatycznie umieszcza dyski różnych maszyn wirtualnych w zestawie dostępności w różnych jednostkach skalowania magazynu (sygnaturach), co ogranicza wpływ awarii pojedynczych jednostek skalowania magazynu spowodowanych awariami sprzętu i oprogramowania.
W zależności od potrzeb możesz wybrać jeden z czterech typów opcji pamięci masowej. Aby dowiedzieć się więcej o dostępnych typach dysków, zobacz nasz artykuł [Wybieranie typu dysku](disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Planowanie migracji do dysków zarządzanych

Ta sekcja pomaga w podejmowaniu najlepszych decyzji dotyczących maszyn wirtualnych i typów dysków.

Jeśli planujesz migrację z dysków niezarządzanych do dysków zarządzanych, należy pamiętać, że użytkownicy z rolą [współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nie będą mogli zmienić rozmiaru maszyny wirtualnej (ponieważ mogą wstępnie przeprowadzić konwersję). Dzieje się tak, ponieważ maszyny wirtualne z dyskami zarządzanymi wymagają od użytkownika uprawnienia Microsoft.Compute/disks/write na dyskach systemu operacyjnego.

### <a name="location"></a>Lokalizacja

Wybierz lokalizację, w której są dostępne dyski zarządzane platformy Azure. Jeśli przeprowadzasz migrację do dysków zarządzanych w układce, upewnij się również, że magazyn w wersji Premium jest dostępny w regionie, do którego planujesz przeprowadzić migrację. Aby uzyskać aktualne informacje o dostępnych lokalizacjach, zobacz Usługi platformy Azure według [regionów.](https://azure.microsoft.com/regions/#services)

### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych

W przypadku migracji do dysków zarządzanych w wersji Premium należy zaktualizować rozmiar maszyny Wirtualnej do magazynu w wersji Premium dostępny w regionie, w którym znajduje się maszyna wirtualna. Przejrzyj rozmiary maszyn wirtualnych, które są w stanie magazynu w wersji Premium. Specyfikacje rozmiaru maszyny wirtualnej platformy Azure są wymienione w [rozmiary dla maszyn wirtualnych](sizes.md).
Przejrzyj charakterystykę wydajności maszyn wirtualnych, które współpracują z magazynem w wersji Premium i wybierz najbardziej odpowiedni rozmiar maszyny wirtualnej, który najlepiej odpowiada twojemu obciążeniu. Upewnij się, że na maszynie wirtualnej jest dostępna wystarczająca przepustowość, aby zwiększyć ruch na dysku.

### <a name="disk-sizes"></a>Rozmiary dysków

**Dyski zarządzane w wersji Premium**

Istnieje siedem typów dysków zarządzanych w wersji premium, które mogą być używane z maszyną wirtualną, a każdy z nich ma określone IOP i limity przepływności. Należy wziąć pod uwagę te limity przy wyborze typu dysku premium dla maszyny Wirtualnej na podstawie potrzeb aplikacji pod względem pojemności, wydajności, skalowalności i obciążenia szczytowego.

| Typ dysków premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Rozmiar dysku           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Przepływność na dysk | 25 MB na sekundę  | 50 MB na sekundę  | 100 MB na sekundę | 125 MB na sekundę |150 MB na sekundę | 200 MB na sekundę | 250 MB na sekundę | 250 MB na sekundę |

**Standardowe dyski zarządzane**

Istnieje siedem typów standardowych dysków zarządzanych, które mogą być używane z maszyną wirtualną. Każdy z nich ma inną pojemność, ale mają te same limity we/wy i przepływność. Wybierz typ standardowych dysków zarządzanych na podstawie potrzeb aplikacji w zakresie pojemności.

| Typ dysku standardowego  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Rozmiar dysku           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Przepływność na dysk | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę |60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 

### <a name="disk-caching-policy"></a>Zasady buforowania dysków 

**Dyski zarządzane w wersji Premium**

Domyślnie zasady buforowania dysków to *Tylko do odczytu* dla wszystkich dysków z danymi w układzie premium i *odczyt i zapis* dla dysku systemu operacyjnego Premium podłączonego do maszyny Wirtualnej. To ustawienie konfiguracji jest zalecane w celu osiągnięcia optymalnej wydajności dla aplikacji we/wy. W przypadku dysków danych z dużą wydajnością lub tylko do zapisu (takich jak pliki dziennika programu SQL Server) należy wyłączyć buforowanie dysków, aby uzyskać lepszą wydajność aplikacji.

### <a name="pricing"></a>Cennik

Przejrzyj [ceny dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/). Ceny dysków zarządzanych w wersji Premium są takie same jak dyski niezarządzane w wersji Premium. Ale ceny dla standardowych dysków zarządzanych różni się od standardowych dysków niezarządzanych.


## <a name="next-steps"></a>Następne kroki

- Przed przekazaniem dowolnego dysku VHD na platformę Azure należy postępować zgodnie z postępującą po [przygotowaniu dysku VHD lub VHDX systemu Windows do przesłania na platformę Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
