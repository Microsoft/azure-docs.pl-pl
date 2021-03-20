---
title: Architektura — odzyskiwanie po awarii funkcji Hyper-V do lokacji dodatkowej z Azure Site Recovery
description: Ten artykuł zawiera omówienie architektury odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V do lokacji pomocniczej programu System Center VMM z Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 703a6afdc12c8a9863ff0f480ec7a577ec31ef77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87496002"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Architektura — replikacja funkcji Hyper-V do lokacji dodatkowej

Ten artykuł zawiera opis składników i procesów związanych z replikacją lokalnych maszyn wirtualnych funkcji Hyper-V w chmurach programu System Center Virtual Machine Manager (VMM) do lokacji dodatkowej z programem VMM za pomocą usługi [Azure Site Recovery](site-recovery-overview.md) w witrynie Azure Portal.
a

## <a name="architectural-components"></a>Składniki architektury

Poniższa tabela i ilustracja przedstawiają ogólny widok składników służących do replikacji funkcji Hyper-V do lokacji dodatkowej.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure | Magazyn usługi Recovery Services tworzy się w subskrypcji platformy Azure, aby organizować replikację między lokalizacjami z programem VMM i nią zarządzać.
**Serwer VMM** | Potrzebujesz podstawowej i dodatkowej lokalizacji z programem VMM. | Zalecamy, aby jeden serwer VMM znajdował się w lokacji głównej, a jeden w lokacji dodatkowej.
**Serwer funkcji Hyper-V** |  Co najmniej jeden serwer hosta funkcji Hyper-V w głównych i dodatkowych chmurach programu VMM. | Dane są replikowane między głównymi i dodatkowymi serwerami hosta funkcji Hyper-V za pośrednictwem sieci LAN albo sieci VPN korzystającej z protokołu Kerberos lub uwierzytelniania certyfikatu.  
**Maszyny wirtualne funkcji Hyper-V** | Na serwerze hosta funkcji Hyper-V. | Źródłowy serwer hosta powinien mieć co najmniej jedną maszynę wirtualną, która ma być replikowana.

**Lokalna z architekturą lokalną**

![Diagram przedstawiający ochronę lokalną w przypadku ochrony lokalnej.](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Proces replikacji

1. Gdy replikacja początkowa jest wyzwalana, wykonywana jest migawka [migawki maszyny wirtualnej funkcji Hyper-V](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560637(v=ws.10)) .
2. Wirtualne dyski twarde w maszynie wirtualnej są replikowane jednokrotnie do lokalizacji dodatkowej.
3. Jeśli podczas początkowej replikacji wystąpią zmiany dysku, śledzenie replikacji funkcji Hyper-V Replica śledzi zmiany w postaci dzienników replikacji funkcji Hyper-V (. HRL). Te pliki dzienników znajdują się w tym samym folderze co dyski. Każdy dysk ma skojarzony plik. HRL, który jest wysyłany do lokalizacji pomocniczej. Pliki migawki i dziennika zużywają zasoby dysku w trakcie replikacji początkowej.
4. Po zakończeniu replikacji początkowej migawka maszyny wirtualnej zostanie usunięta i rozpocznie się replikacja różnicowa.
5. Różnicowe zmiany dysku w dzienniku są synchronizowane i scalane z dyskiem nadrzędnym.


## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

- W tryb failover można przełączyć pojedynczą maszynę lub można utworzyć plany odzyskiwania, aby zaaranżować tryb failover na wielu maszynach.
- Planowane lub nieplanowane przejście w tryb failover można uruchomić między lokacjami lokalnymi. Jeśli zostanie uruchomione planowane przejście w tryb failover, źródłowe maszyny wirtualne zostaną wyłączone w celu zapewnienia, że nie będzie miała miejsca utrata danych.
    - W przypadku wykonywania nieplanowanego przejścia w tryb failover do lokacji dodatkowej, gdy maszyny trybu failover w lokalizacji dodatkowej nie są chronione.
    - Jeśli dokonano planowanego przejścia w tryb failover, po przejściu w tryb failover maszyny znajdujące się w lokalizacji dodatkowej są chronione.
- Po wstępnym uruchomieniu trybu failover należy go zatwierdzić, aby rozpocząć uzyskiwanie dostępu do obciążenia z maszyny wirtualnej repliki.
- Po ponownym udostępnieniu lokalizacji podstawowej można wrócić po awarii.
    - Należy zainicjować replikację odwrotną, aby rozpocząć replikację z lokacji dodatkowej do podstawowego. Replikacja odwrotna powoduje przełączenie maszyn wirtualnych do stanu chronionego, ale aktywną lokalizacją jest nadal dodatkowe centrum danych.
    - Aby lokacja główna stała się z powrotem lokalizacją aktywną, należy zainicjować planowane przejście w tryb failover z lokacji dodatkowej do głównej, a następnie przeprowadzić kolejną replikację odwrotną.



## <a name="next-steps"></a>Następne kroki


Postępuj zgodnie z [tym samouczkiem](hyper-v-vmm-disaster-recovery.md) , aby włączyć replikację funkcji Hyper-V między CHMURAMI programu VMM.
