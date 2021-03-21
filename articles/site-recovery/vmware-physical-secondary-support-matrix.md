---
title: Obsługa oprogramowania VMware/fizycznego odzyskiwania po awarii w lokacji dodatkowej z Azure Site Recovery
description: Podsumowuje obsługę odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych w lokacji dodatkowej z Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: ac67e3cf8f057738b76b0de7cbcb821ef290e0cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98757580"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Macierz obsługi odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych do lokacji dodatkowej

W tym artykule opisano, co jest obsługiwane w przypadku korzystania z usługi [Azure Site Recovery](site-recovery-overview.md) na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware lub serwerów fizycznych z systemem Windows/Linux do pomocniczej lokacji programu VMware.

- Jeśli chcesz replikować maszyny wirtualne VMware lub serwery fizyczne do platformy Azure, zapoznaj się z [tą matrycą pomocy technicznej](vmware-physical-azure-support-matrix.md).
- Jeśli chcesz replikować maszyny wirtualne funkcji Hyper-V do lokacji dodatkowej, zapoznaj się z [tą matrycą obsługi](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replikacja lokalnych maszyn wirtualnych programu VMware i serwerów fizycznych jest udostępniana przez program InMage Scout. Usługa InMage Scout jest dołączona do subskrypcji usługi Azure Site Recovery.

## <a name="end-of-support-announcement"></a>Ogłoszenie końca wsparcia
Site Recovery scenariusz replikacji między lokalnymi centrami danych programu VMware lub fizycznymi centrami danych zbliża się do końca wsparcia.

- Od sierpnia 2018 scenariusza nie można skonfigurować w magazynie Recovery Services i nie można pobrać oprogramowania InMage Scout z magazynu. Istniejące wdrożenia będą obsługiwane.
- - W grudniu 31 2020 scenariusz nie będzie obsługiwany.
Istniejący partnerzy mogą dołączać nowych klientów do scenariusza do momentu zakończenia pomocy technicznej.
- W okresie 2018 i 2019 zostaną wydane dwie aktualizacje:

    - Update 7: Rozwiązywanie problemów z konfiguracją sieci i zgodnością oraz zapewnia obsługę protokołu TLS 1,2.
    - Update 8: dodaje obsługę systemów operacyjnych Linux RHEL/CentOS 7.3/7.4/7.5 i dla SUSE 12
    - Po aktualizacji 8 nie zostaną wydane żadne dalsze aktualizacje. Zostanie ograniczona obsługa poprawek dla systemów operacyjnych dodanych w Update 8 i poprawki błędów na podstawie najlepszego nakładu pracy.

## <a name="host-servers"></a>Serwery hosta

**System operacyjny** | **Szczegóły**
--- | ---
Serwer vCenter | vCenter 5,5, 6,0 i 6,5<br/><br/> W przypadku uruchomienia 6,0 lub 6,5 należy zauważyć, że obsługiwane są tylko funkcje 5,5.


## <a name="replicated-vm-support"></a>Obsługa zreplikowanej maszyny wirtualnej

Poniższa tabela zawiera podsumowanie obsługi systemu operacyjnego dla maszyn replikowanych za pomocą Site Recovery. Każde obciążenie może być uruchomione w obsługiwanym systemie operacyjnym.

**System operacyjny** | **Szczegóły**
--- | ---
Windows Server | 64-bitowy system Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z co najmniej dodatkiem SP1.
Linux | Red Hat Enterprise Linux 6,7, 6,8, 6,9, 7,1, 7,2 <br/><br/> CentOS 6,5, 6,6, 6,7, 6,8, 6,9, 7,0, 7,1, 7,2 <br/><br/> Oracle Enterprise Linux 6,4, 6,5, 6,8, uruchamianie jądra zgodnego z systemem Red Hat lub nieprzerwane wydanie jądra 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 z dodatkiem SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Magazyn maszyny z systemem Linux

Można replikować tylko maszyny z systemem Linux z następującym magazynem:

- System plików (EXT3, ETX4, ReiserFS, XFS).
- Wielościeżkowe oprogramowanie — mapowanie urządzeń.
- Menedżer woluminów (LVM2).
- Serwery fizyczne z magazynem w usłudze HP CCISS Controller nie są obsługiwane.
- System plików ReiserFS jest obsługiwany tylko w systemie SUSE Linux Enterprise Server 11 z dodatkiem SP3.

## <a name="network-configuration---hostguest-vm"></a>Konfiguracja sieci-Host/maszyna wirtualna gościa

**Konfiguracja** | **Obsługiwane**  
--- | --- 
Hostowanie — Tworzenie zespołu kart interfejsu sieciowego | Tak 
Host-sieć VLAN | Tak 
Host-IPv4 | Tak 
Host-IPv6 | Nie 
Maszyna wirtualna gościa — Tworzenie zespołu kart interfejsu sieciowego | Nie
Maszyna wirtualna gościa — IPv4 | Tak
Maszyna wirtualna gościa — IPv6 | Nie
Maszyna wirtualna gościa — system Windows/Linux — statyczny adres IP | Tak
Maszyna wirtualna gościa — wiele kart sieciowych | Yes


## <a name="storage"></a>Magazyn

### <a name="host-storage"></a>Magazyn hosta

**Magazyn (Host)** | **Obsługiwane** 
--- | --- 
NFS | Tak 
SMB 3.0 | Nie dotyczy 
SIEĆ SAN (ISCSI) | Tak 
Wiele ścieżek (MPIO) | Tak 

### <a name="guest-or-physical-server-storage"></a>Magazyn Gości lub serwer fizyczny

**Konfiguracja** | **Obsługiwane** 
--- | --- 
VMDK | Tak 
DYSK VHD/VHDX | Nie dotyczy 
Maszyna wirtualna generacji 2 | Nie dotyczy 
Udostępniony dysk klastra | Tak 
Zaszyfrowany dysk | Nie 
UEFI| Tak 
NFS | Nie 
SMB 3.0 | Nie 
RDM | Tak 
Dysk > 1 TB | Tak 
Wolumin z dyskiem rozłożonym > 1 TB<br/><br/> LVM | Tak 
Miejsca do magazynowania | Nie 
Gorące Dodawanie/usuwanie dysku | Tak 
Wykluczanie dysku | Tak 
Wiele ścieżek (MPIO) | Nie dotyczy 

## <a name="vaults"></a>Magazyny

**Akcja** | **Obsługiwane** 
--- | --- 
Przenoszenie magazynów między grupami zasobów (w ramach subskrypcji lub między subskrypcjami) | Nie 
Przenoszenie magazynu, sieci, maszyn wirtualnych platformy Azure między grupami zasobów (w ramach subskrypcji lub między subskrypcjami) | Nie 

## <a name="mobility-service-and-updates"></a>Usługa mobilności i aktualizacje

Usługa mobilności koordynuje replikację między lokalnymi serwerami VMware lub serwerami fizycznymi oraz lokacją dodatkową. Podczas konfigurowania replikacji należy upewnić się, że masz najnowszą wersję usługi mobilności oraz inne składniki.

| **Aktualizowanie** | **Szczegóły** |
| --- | --- |
|Aktualizacje Scout | Aktualizacje Scout są zbiorcze. <br/><br/> [Uzyskaj informacje o](vmware-physical-secondary-disaster-recovery.md#updates) najnowszych aktualizacjach programu Scout i Pobierz je |
|Aktualizacje składników | Aktualizacje Scout obejmują aktualizacje wszystkich składników, w tym serwer RX, serwer konfiguracji, proces i główne serwery docelowe, serwery vContinuum i serwery źródłowe, które mają być chronione.<br/><br/> [Dowiedz się więcej](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Następne kroki

Pobierz [Podręcznik użytkownika programu InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Replikowanie maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM do lokacji dodatkowej](./hyper-v-vmm-disaster-recovery.md)
- [Replikacja maszyn wirtualnych VMware i serwerów fizycznych do lokacji dodatkowej](./vmware-physical-secondary-disaster-recovery.md)
