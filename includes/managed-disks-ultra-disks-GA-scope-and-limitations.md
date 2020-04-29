---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008649"
---
Na razie Ultra dyski mają dodatkowe ograniczenia, są następujące:

Jedyne opcje nadmiarowości infrastruktury obecnie dostępne dla Ultra disks to strefy dostępności. Maszyny wirtualne używające innych opcji nadmiarowości nie mogą dołączać dysku Ultra Disk.

W poniższej tabeli przedstawiono regiony Ultra disks, a także odpowiadające im opcje dostępności:

> [!NOTE]
> Niektóre strefy dostępności w tych regionach nie oferują bardzo dużo dysków.

|Regiony  |Brak nadmiarowości infrastruktury  |Strefy dostępności  |
|---------|---------|---------|
|Zachodnie stany USA     |Tak         |Nie         |
|Zachodnie stany USA 2    |Nie         |Tak         |
|Wschodnie stany USA     |Nie         |Tak         |
|Wschodnie stany USA 2     |Nie         |Tak         |
|Azja Południowo-Wschodnia     |Nie         |Tak         |
|Europa Północna     |Nie         |Tak         |
|Europa Zachodnia     |Nie         |Tak         |
|Południowe Zjednoczone Królestwo     |Nie         |Tak         |

- Są obsługiwane tylko przez następującą serię maszyn wirtualnych:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nie każdy rozmiar maszyny wirtualnej jest dostępny w każdym obsługiwanym regionie za pomocą Ultra disks
- Są dostępne tylko jako dyski danych i obsługują tylko rozmiar sektora fizycznego 4 KB. Ze względu na rozmiar sektora macierzystego 4K dla Ultra dysku istnieją aplikacje, które nie będą zgodne z Ultra Disks. Przykładem może być Oracle Database, który wymaga wersji 12,2 lub nowszej, aby można było obsługiwać Ultra Disks.  
- Można utworzyć tylko jako puste dyski  
- Obecnie nie obsługuje migawek dysków, obrazów maszyn wirtualnych, zestawów dostępności, dedykowanych hostów platformy Azure ani usługi Azure Disk Encryption
- Program nie obsługuje obecnie integracji z Azure Backup ani Azure Site Recovery
- Bieżący maksymalny limit liczby operacji we/wy na maszynach wirtualnych "GA" to 80 000.

Usługa Azure Ultra disks domyślnie oferuje do 16 TiB na region dla każdej subskrypcji, ale niezwykle wysokie pojemności są obsługiwane przez żądanie. Aby zażądać zwiększenia wydajności, skontaktuj się z pomocą techniczną platformy Azure.