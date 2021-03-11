---
title: Migrowanie z AWS i innych platform do Managed Disks na platformie Azure
description: Twórz maszyny wirtualne na platformie Azure przy użyciu wirtualnych dysków twardych, takich jak AWS lub inne platformy wirtualizacji, i korzystaj z Managed Disks platformy Azure.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.subervice: disks
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c817528ff26dd10112eaf69c7ad20f8fb5813695
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550403"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrowanie z Amazon Web Services (AWS) i innych platform do Managed Disks na platformie Azure

Możesz przekazać pliki VHD z AWS lub lokalnych rozwiązań do wirtualizacji na platformę Azure, aby utworzyć maszyny wirtualne wykorzystujące Managed Disks. Usługa Azure Managed Disks eliminuje konieczność zarządzania kontami magazynu dla maszyn wirtualnych usługi Azure IaaS. Należy określić tylko typ (Premium lub standard) i wymagany rozmiar dysku, a platforma Azure utworzy dysk i będzie nim zarządzać. 

Można przekazać uogólnione i wyspecjalizowane dyski VHD. 
- **Uogólniony wirtualny dysk twardy** — wszystkie informacje o koncie osobistym zostały usunięte przy użyciu programu Sysprep. 
- **Wyspecjalizowany wirtualny dysk twardy** — obsługuje konta użytkowników, aplikacje i inne dane stanu z oryginalnej maszyny wirtualnej. 

> [!IMPORTANT]
> Przed przekazaniem dowolnego wirtualnego dysku twardego na platformę Azure należy wykonać następujące czynności [Przygotuj plik VHD lub VHDX systemu Windows w celu przekazania go do platformy Azure](prepare-for-upload-vhd-image.md)
>
>


| Scenariusz                                                                                                                         | Dokumentacja                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Masz istniejące wystąpienia AWS EC2, które chcesz migrować do maszyn wirtualnych platformy Azure przy użyciu usługi Managed disks                              | [Przenoszenie maszyny wirtualnej z Amazon Web Services (AWS) na platformę Azure](aws-to-azure.md)                           |
| Masz maszynę wirtualną z innej platformy wirtualizacji, która ma być używana jako obraz do tworzenia wielu maszyn wirtualnych platformy Azure. | [Przekaż uogólniony wirtualny dysk twardy i użyj go do utworzenia nowej maszyny wirtualnej na platformie Azure](upload-generalized-managed.md) |
| Masz unikatową dostosowaną maszynę wirtualną, którą chcesz odtworzyć na platformie Azure.                                                      | [Przekaż wyspecjalizowany wirtualny dysk twardy do platformy Azure i Utwórz nową maszynę wirtualną](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Omówienie Managed Disks

Usługa Azure Managed Disks upraszcza zarządzanie MASZYNami wirtualnymi, eliminując konieczność zarządzania kontami magazynu. Managed Disks również zwiększyć niezawodność maszyn wirtualnych w zestawie dostępności. Gwarantuje to, że dyski różnych maszyn wirtualnych w zestawie dostępności są wystarczająco odizolowane od siebie, aby uniknąć single point of failure. Automatycznie umieszcza dyski różnych maszyn wirtualnych w zestawie dostępności w różnych jednostkach skalowania magazynu (sygnatury), które ograniczają wpływ awarii jednostek skalowania pojedynczego magazynu spowodowanych awariami sprzętu i oprogramowania.
Na podstawie Twoich potrzeb można wybierać spośród czterech typów opcji magazynu. Aby dowiedzieć się więcej o dostępnych typach dysków, zapoznaj się z artykułem [Wybierz typ dysku](../disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Zaplanuj migrację do Managed Disks

Ta sekcja ułatwia podejmowanie najlepszej decyzji na temat maszyn wirtualnych i typów dysków.

Jeśli planujesz migrację z dysków niezarządzanych do usługi Managed disks, należy pamiętać, że użytkownicy z rolą [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nie będą mogli zmienić rozmiaru maszyny wirtualnej (ponieważ mogą one zostać przebudowane). Wynika to z faktu, że maszyny wirtualne z dyskami zarządzanymi wymagają, aby użytkownik miał uprawnienie Microsoft. COMPUTE/disks/Write na dyskach systemu operacyjnego.

### <a name="location"></a>Lokalizacja

Wybierz lokalizację, w której usługa Azure Managed Disks jest dostępna. W przypadku migrowania do Managed Disks Premium należy również upewnić się, że Usługa Premium Storage jest dostępna w regionie, w którym planujesz przeprowadzić migrację. Zobacz [usługi platformy Azure według regionów,](https://azure.microsoft.com/regions/#services) Aby uzyskać aktualne informacje dotyczące dostępnych lokalizacji.

### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych

W przypadku migrowania do Managed Disks w warstwie Premium należy zaktualizować rozmiar maszyny wirtualnej w celu Premium Storage dostępnej w regionie, w którym znajduje się maszyna wirtualna. Przejrzyj rozmiary maszyn wirtualnych, które są Premium Storage obsługują. Specyfikacje rozmiaru maszyny wirtualnej platformy Azure są wymienione w obszarze [rozmiary maszyn wirtualnych](../sizes.md).
Zapoznaj się z charakterystyką wydajności maszyn wirtualnych, które działają z Premium Storage i wybierz najbardziej odpowiedni rozmiar maszyny wirtualnej, który najlepiej odpowiada Twojemu obciążeniu. Upewnij się, że na maszynie wirtualnej jest dostępna wystarczająca przepustowość do obsługi ruchu na dysku.

### <a name="disk-sizes"></a>Rozmiary dysków

**Managed Disks Premium**

Istnieje siedem typów dysków zarządzanych w warstwie Premium, które mogą być używane z maszyną wirtualną, a każda z nich ma określone limity IOPs i przepływności. Należy wziąć pod uwagę te limity podczas wybierania typu dysku Premium dla maszyny wirtualnej na podstawie potrzeb aplikacji w zakresie pojemności, wydajności, skalowalności i szczytowych obciążeń.

| Typ dysków w warstwie Premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Rozmiar dysku           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Przepływność na dysk | 25 MB na sekundę  | 50 MB na sekundę  | 100 MB na sekundę | 125 MB na sekundę |150 MB na sekundę | 200 MB na sekundę | 250 MB na sekundę | 250 MB na sekundę |

**Standardowa Managed Disks**

Istnieje siedem typów dysków zarządzanych w warstwie Standardowa, które mogą być używane z maszyną wirtualną. Każdy z nich ma różną pojemność, ale ma te same limity IOPS i przepływności. Wybierz typ dysków zarządzanych w warstwie Standardowa na podstawie potrzeb związanych z pojemnością aplikacji.

| Typ dysku standardowego  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Rozmiar dysku           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Przepływność na dysk | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę |60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 

### <a name="disk-caching-policy"></a>Zasady buforowania dysku 

**Managed Disks Premium**

Domyślnie zasady buforowania dysku są tylko do *odczytu* dla wszystkich dysków danych w warstwie Premium oraz do *odczytu i zapisu* dla dysku systemu operacyjnego Premium dołączonego do maszyny wirtualnej. To ustawienie konfiguracji jest zalecane, aby osiągnąć optymalną wydajność aplikacji dla systemu IOs. W przypadku dysków z danymi zapisu lub zapisu (takich jak SQL Server plików dziennika) należy wyłączyć buforowanie dysków, aby zapewnić lepszą wydajność aplikacji.

### <a name="pricing"></a>Cennik

Zapoznaj się z [cennikiem Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/). Cennik Managed Disks w warstwie Premium jest taki sam jak w przypadku dysków niezarządzanych w warstwie Premium. Jednak Cennik Managed Disks standardowego różni się od standardowych dysków niezarządzanych.


## <a name="next-steps"></a>Następne kroki

- Przed przekazaniem dowolnego wirtualnego dysku twardego na platformę Azure należy wykonać następujące czynności [Przygotuj plik VHD lub VHDX systemu Windows w celu przekazania go do platformy Azure](prepare-for-upload-vhd-image.md)