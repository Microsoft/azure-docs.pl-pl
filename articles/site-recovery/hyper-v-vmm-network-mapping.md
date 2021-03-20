---
title: Mapowanie sieci funkcji Hyper-V (z programem VMM) za pomocą Site Recovery
description: Opisuje sposób konfigurowania mapowania sieci na potrzeby odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V (zarządzanych w chmurach programu VMM) na platformie Azure przy użyciu Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 6b68b4c943ec96620427978c2309f27e1fb1f217
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74082555"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Przygotowanie mapowania sieci na potrzeby odzyskiwania po awarii maszyny wirtualnej funkcji Hyper-V na platformie Azure


Ten artykuł ułatwia zrozumienie i przygotowanie mapowania sieci w przypadku replikacji maszyn wirtualnych funkcji Hyper-V w chmurach System Center Virtual Machine Manager (VMM) do platformy Azure lub lokacji dodatkowej przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) .


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Przygotowanie mapowania sieci na potrzeby replikacji na platformę Azure

W przypadku replikacji na platformę Azure mapowanie sieci jest mapowane między sieciami maszyn wirtualnych na źródłowym serwerze programu VMM i docelowymi sieciami wirtualnymi platformy Azure. Mapowanie wykonuje następujące czynności:
-  **Połączenie sieciowe**— zapewnia, że zreplikowane maszyny wirtualne platformy Azure są połączone z zamapowanej sieci. Wszystkie maszyny, które przechodzą w tryb failover w tej samej sieci, mogą łączyć się ze sobą, nawet jeśli przechodzą w tryb failover w różnych planach odzyskiwania.
- **Brama sieci**— Jeśli Brama sieci została skonfigurowana w docelowej sieci platformy Azure, maszyny wirtualne mogą łączyć się z innymi lokalnymi maszynami wirtualnymi.

Mapowanie sieci działa w następujący sposób:

- Źródłową sieć VMNETWORK programu VMM można mapować do sieci wirtualnej platformy Azure.
- Po przejściu do trybu failover maszyny wirtualne platformy Azure w sieci źródłowej zostaną połączone z zamapowanej docelowej sieci wirtualnej.
- Nowe maszyny wirtualne dodane do źródłowej sieci maszyn wirtualnych są połączone z zamapowanej sieci platformy Azure w przypadku wystąpienia replikacji.
- Jeśli sieć docelowa ma wiele podsieci i jedna z tych podsieci ma taką samą nazwę, jak podsieć, w której znajduje się źródłowa maszyna wirtualna, replika maszyny wirtualnej jest łączona z tą docelową podsiecią po przejściu do trybu failover.
- Jeśli nie istnieje docelowa podsieć o takiej samej nazwie, maszyna wirtualna jest łączona z pierwszą podsiecią w sieci.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Przygotowanie mapowania sieci na potrzeby replikacji do lokacji dodatkowej

W przypadku replikowania do lokacji dodatkowej mapowanie sieci jest mapowane między sieciami maszyn wirtualnych na źródłowym serwerze programu VMM i sieciami maszyn wirtualnych na docelowym serwerze programu VMM. Mapowanie wykonuje następujące czynności:

- **Połączenie sieciowe**— nawiązuje połączenie maszyn wirtualnych z odpowiednimi sieciami po przejściu do trybu failover. Replika maszyny wirtualnej zostanie podłączona do sieci docelowej zamapowanej do sieci źródłowej.
- **Optymalne umieszczanie maszyn wirtualnych**— najlepiej umieszcza maszyny wirtualne repliki na serwerach hostów funkcji Hyper-V. Maszyny wirtualne repliki są umieszczane na hostach, które mogą uzyskać dostęp do mapowanych sieci maszyn wirtualnych.
- **Brak mapowania sieci**— Jeśli mapowanie sieci nie zostanie skonfigurowane, maszyny wirtualne repliki nie będą połączone z żadną siecią maszyny wirtualnej po przejściu w tryb failover.

Mapowanie sieci działa w następujący sposób:

- Mapowanie sieci można skonfigurować między sieciami maszyn wirtualnych na dwóch serwerach programu VMM lub na pojedynczym serwerze programu VMM, jeśli dwie lokacje są zarządzane przez ten sam serwer.
- Gdy mapowanie jest prawidłowo skonfigurowane i replikacja jest włączona, maszyna wirtualna w lokalizacji podstawowej zostanie podłączona do sieci, a jej replika w lokalizacji docelowej zostanie połączona z zamapowanej sieci.
- Po wybraniu docelowej sieci maszyn wirtualnych podczas mapowania sieci w Site Recovery, zostaną wyświetlone chmury źródłowe programu VMM używające źródłowej sieci maszyn wirtualnych wraz z dostępnymi docelowymi sieciami maszyn wirtualnych w chmurach docelowych, które są używane do ochrony.
- Jeśli Sieć docelowa ma wiele podsieci i jedna z tych podsieci ma taką samą nazwę jak podsieć, w której znajduje się źródłowa maszyna wirtualna, replika maszyny wirtualnej zostanie podłączona do tej podsieci docelowej po przejściu do trybu failover. Jeśli nie istnieje docelowa podsieć o pasującej nazwie, maszyna wirtualna zostanie połączona z pierwszą podsiecią w sieci.

## <a name="example"></a>Przykład

Oto przykład ilustrujący ten mechanizm. Przyjrzyjmy się organizacji z dwiema lokalizacjami w Nowym Jorku i Chicago.

**Lokalizacja** | **Serwer VMM** | **Sieci VMNETWORK** | **Zamapowane na**
---|---|---|---
Nowy Jork | VMM-NewYork| VMNetwork1-NewYork | Mapowany do VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Niezamapowane
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mapowany do VMNetwork1-NewYork
 | | VMNetwork2-Chicago | Niezamapowane

W tym przykładzie:

- Po utworzeniu maszyny wirtualnej repliki dla dowolnej maszyny wirtualnej, która jest połączona z usługą VMNetwork1-NewYork, zostanie ona połączona z VMNetwork1-Chicago.
- Po utworzeniu maszyny wirtualnej repliki dla VMNetwork2-NewYork lub VMNetwork2-Chicago nie będzie ona połączona z żadną siecią.

Poniżej przedstawiono sposób konfigurowania chmur programu VMM w naszej przykładowej organizacji oraz w sieciach logicznych skojarzonych z chmurami.

### <a name="cloud-protection-settings"></a>Ustawienia ochrony chmury

**Chroniona chmura** | **Ochrona chmury** | **Sieć logiczna (Nowy Jork)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Ustawienia sieci logicznej i maszyny wirtualnej

**Lokalizacja** | **Sieć logiczna** | **Skojarzona sieć maszyny wirtualnej**
---|---|---
Nowy Jork | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Ustawienia sieci docelowej

W oparciu o te ustawienia, po wybraniu docelowej sieci maszyn wirtualnych w poniższej tabeli przedstawiono dostępne opcje.

**Wybierz** | **Chroniona chmura** | **Ochrona chmury** | **Dostępna sieć docelowa**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Dostępne
 | GoldCloud1 | GoldCloud2 | Dostępne
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Niedostępne
 | GoldCloud1 | GoldCloud2 | Dostępne


Jeśli Sieć docelowa ma wiele podsieci i jedna z tych podsieci ma taką samą nazwę jak podsieć, w której znajduje się źródłowa maszyna wirtualna, po przejściu w tryb failover maszyna wirtualna repliki zostanie podłączona do tej podsieci docelowej. Jeśli nie istnieje docelowa podsieć o takiej samej nazwie, maszyna wirtualna zostanie podłączona do pierwszej podsieci w sieci.


### <a name="failback-behavior"></a>Zachowanie powrotu po awarii

Aby zobaczyć, co się dzieje w przypadku powrotu po awarii (replikacja odwrotna), Załóżmy, że VMNetwork1-NewYork jest mapowany do VMNetwork1-Chicago z poniższymi ustawieniami.


**VM** | **Połączono z siecią maszyny wirtualnej**
---|---
Maszyna wirtualna 1 | VMNetwork1-Network
VM2 (replika VM1) | VMNetwork1-Chicago

Korzystając z tych ustawień, zobaczmy, co się dzieje w kilku możliwych scenariuszach.

**Scenariusz** | **Wynikiem**
---|---
Nie zmieniono właściwości sieci maszyny wirtualnej-2 po przejściu w tryb failover. | Maszyna wirtualna — 1 jest połączona z siecią źródłową.
Właściwości sieci maszyny wirtualnej-2 są zmieniane po przejściu w tryb failover i są odłączone. | Maszyna wirtualna — 1 została odłączona.
Właściwości sieci maszyny wirtualnej — 2 są zmieniane po przejściu w tryb failover i są połączone z VMNetwork2-Chicago. | Jeśli VMNetwork2-Chicago nie jest zamapowana, maszyna wirtualna-1 zostanie odłączona.
Mapowanie sieci VMNetwork1-Chicago zostało zmienione. | Maszyna wirtualna-1 zostanie połączona z siecią zamapowana do VMNetwork1-Chicago.



## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o](hyper-v-vmm-networking.md) Adresowanie IP po przełączeniu w tryb failover do pomocniczej lokacji programu VMM.
- [Dowiedz się więcej o](concepts-on-premises-to-azure-networking.md) Adresowanie IP po przejściu do trybu failover na platformie Azure.
