---
title: dołączanie pliku
description: dołączanie pliku
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa9327bd0ba6763aa4e89630611aabb3c5195655
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85448768"
---
Na razie Ultra dyski mają dodatkowe ograniczenia, są następujące:

Jedyne opcje nadmiarowości infrastruktury obecnie dostępne dla Ultra disks to strefy dostępności. Maszyny wirtualne używające innych opcji nadmiarowości nie mogą dołączać dysku Ultra Disk.

W poniższej tabeli przedstawiono regiony Ultra disks, a także odpowiadające im opcje dostępności:

> [!NOTE]
> Jeśli region na poniższej liście nie ma stref dostępności obsługujących dysk, należy wdrożyć maszyny wirtualne w tym regionie bez żadnych opcji nadmiarowości infrastruktury w celu dołączenia dysku Ultra.

|Regiony  |Liczba stref dostępności obsługujących Ultra disks  |
|---------|---------|
|US Gov Wirginia     |Brak         |
|Południowo-środkowe stany USA     |Brak         |
|Środkowe stany USA     |Trzy strefy         |
|Zachodnie stany USA     |Brak         |
|Zachodnie stany USA 2    |Trzy strefy         |
|Wschodnie stany USA     |Trzy strefy         |
|Wschodnie stany USA 2     |Dwie strefy         |
|Azja Południowo-Wschodnia     |Trzy strefy         |
|Europa Północna     |Trzy strefy          |
|Europa Zachodnia     |Trzy strefy          |
|Południowe Zjednoczone Królestwo     |Trzy strefy          |
|Japonia Wschodnia     |Dwie strefy         |
|Francja Środkowa    |Dwie strefy        |


- Są obsługiwane tylko przez następującą serię maszyn wirtualnych:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nie każdy rozmiar maszyny wirtualnej jest dostępny w każdym obsługiwanym regionie za pomocą Ultra disks
- Są dostępne tylko jako dyski danych i obsługują tylko rozmiar sektora fizycznego 4 KB. Ze względu na rozmiar sektora macierzystego 4K dla Ultra dysku istnieją aplikacje, które nie będą zgodne z Ultra Disks. Przykładem może być Oracle Database, który wymaga wersji 12,2 lub nowszej, aby można było obsługiwać Ultra Disks.  
- Można utworzyć tylko jako puste dyski  
- Obecnie nie obsługuje migawek dysków, obrazów maszyn wirtualnych, zestawów dostępności, dedykowanych hostów platformy Azure ani usługi Azure Disk Encryption
- Program nie obsługuje obecnie integracji z Azure Backup ani Azure Site Recovery
- Obsługuje tylko odczyty z pamięci podręcznej i zapisywanie w pamięci podręcznej
- Bieżący maksymalny limit liczby operacji we/wy na maszynach wirtualnych "GA" to 80 000.

Usługa Azure Ultra disks domyślnie oferuje do 16 TiB na region dla każdej subskrypcji, ale niezwykle wysokie pojemności są obsługiwane przez żądanie. Aby zażądać zwiększenia wydajności, skontaktuj się z pomocą techniczną platformy Azure.