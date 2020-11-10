---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: eeda43b972e30c573743c8c59f75cfae61d7ee85
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413148"
---
Na razie Ultra dyski mają dodatkowe ograniczenia, są następujące:

Jedyne opcje nadmiarowości infrastruktury obecnie dostępne dla Ultra disks to strefy dostępności. Maszyny wirtualne używające innych opcji nadmiarowości nie mogą dołączać dysku Ultra Disk.

W poniższej tabeli przedstawiono regiony Ultra disks, a także odpowiadające im opcje dostępności:

> [!NOTE]
> Jeśli region na poniższej liście nie ma stref dostępności obsługujących dysk, należy wdrożyć maszyny wirtualne w tym regionie bez żadnych opcji nadmiarowości infrastruktury w celu dołączenia dysku Ultra.

|Regiony  |Opcje nadmiarowości  |
|---------|---------|
|Brazil South     |Tylko pojedyncze maszyny wirtualne (zbiory dostępności i zestawy skalowania maszyn wirtualnych nie są obsługiwane)|
|Indie Środkowe     |Tylko pojedyncze maszyny wirtualne (zbiory dostępności i zestawy skalowania maszyn wirtualnych nie są obsługiwane)|
|Azja Wschodnia     |Tylko pojedyncze maszyny wirtualne (zbiory dostępności i zestawy skalowania maszyn wirtualnych nie są obsługiwane)|
|Niemcy Środkowo-Zachodnie     |Tylko pojedyncze maszyny wirtualne (zbiory dostępności i zestawy skalowania maszyn wirtualnych nie są obsługiwane)|
|Korea Środkowa     |Tylko pojedyncze maszyny wirtualne (zbiory dostępności i zestawy skalowania maszyn wirtualnych nie są obsługiwane)|
|South Central US    |Tylko pojedyncze maszyny wirtualne (zbiory dostępności i zestawy skalowania maszyn wirtualnych nie są obsługiwane)|
|US Gov Arizona     |Tylko pojedyncze maszyny wirtualne (zbiory dostępności i zestawy skalowania maszyn wirtualnych nie są obsługiwane)|
|US Gov Wirginia     |Tylko pojedyncze maszyny wirtualne (zbiory dostępności i zestawy skalowania maszyn wirtualnych nie są obsługiwane)|
|US Gov Teksas     |Tylko pojedyncze maszyny wirtualne (zbiory dostępności i zestawy skalowania maszyn wirtualnych nie są obsługiwane)|
|Zachodnie stany USA     |Tylko pojedyncze maszyny wirtualne (zbiory dostępności i zestawy skalowania maszyn wirtualnych nie są obsługiwane)        |
|Australia Środkowa    |Tylko pojedyncze maszyny wirtualne (zbiory dostępności i zestawy skalowania maszyn wirtualnych nie są obsługiwane)|
|Australia Wschodnia     |Trzy strefy dostępności         |
|Azja Południowo-Wschodnia    |Trzy strefy dostępności        |
|Kanada środkowa *     |Trzy strefy dostępności          |
|Środkowe stany USA     |Trzy strefy dostępności          |
|East US     |Trzy strefy dostępności          |
|Wschodnie stany USA 2     |Trzy strefy dostępności         |
|Francja Środkowa    |Dwie strefy dostępności        |
|Japan East    |Trzy strefy dostępności        |
|Europa Północna    |Trzy strefy dostępności        |
|Południowe Zjednoczone Królestwo    |Trzy strefy dostępności        |
|Europa Zachodnia    | Trzy strefy dostępności|
|Zachodnie stany USA 2    |Trzy strefy dostępności|

\* Skontaktuj się z pomocą techniczną platformy Azure, aby uzyskać dostęp do Strefy dostępności w tym regionie.

- Są obsługiwane tylko przez następującą serię maszyn wirtualnych:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)
    - [Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)
    - [Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nie każdy rozmiar maszyny wirtualnej jest dostępny w każdym obsługiwanym regionie przy użyciu Ultra Disks.
- Są dostępne tylko jako dyski danych. 
- Domyślnie Obsługuj rozmiar sektora fizycznego 4 KB. rozmiar sektora 512E jest dostępny jako oferta ogólnie dostępna, ale należy [utworzyć konto](https://aka.ms/ultradisk512e). Większość aplikacji jest zgodna z rozmiarami sektorów 4K, ale niektóre wymagają 512 bajtów sektorów. Przykładem może być Oracle Database, który wymaga wersji 12,2 lub nowszej w celu obsługi dysków natywnych 4K. W przypadku starszych wersji Oracle DB wymagany jest rozmiar sektora bajtów 512.
- Można utworzyć tylko jako puste dyski.
- Obecnie nie obsługuje migawek dysków, obrazów maszyn wirtualnych, zestawów dostępności, dedykowanych hostów platformy Azure ani usługi Azure Disk Encryption.
- Program nie obsługuje obecnie integracji z Azure Backup ani Azure Site Recovery.
- Obsługuje tylko odczyty z pamięci podręcznej i zapisywanie w pamięci podręcznej.
- Bieżący maksymalny limit liczby operacji we/wy na maszynach wirtualnych "GA" to 80 000.

Usługa Azure Ultra disks domyślnie oferuje do 16 TiB na region dla każdej subskrypcji, ale niezwykle wysokie pojemności są obsługiwane przez żądanie. Aby zażądać zwiększenia wydajności, skontaktuj się z pomocą techniczną platformy Azure.
