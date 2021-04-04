---
title: Rozwiązanie VMware firmy Azure przez CloudSimple — korzystanie z chmury prywatnej jako lokacji awaryjnej dla obciążeń lokalnych
description: Opis sposobu konfigurowania prywatnej chmury CloudSimple jako lokacji odzyskiwania po awarii dla lokalnych obciążeń programu VMware
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5dd1f157984cdb300cc3b375d71654e5da54d1ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898134"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Skonfiguruj chmurę prywatną CloudSimple jako lokację odzyskiwania po awarii dla lokalnych obciążeń programu VMware

Chmurę prywatną CloudSimple można skonfigurować jako lokację odzyskiwania dla aplikacji lokalnych, aby zapewnić ciągłość działania w przypadku awarii. Rozwiązanie odzyskiwania bazuje na replikacji wirtualnej Zerto jako platforma replikacji i aranżacji. Krytyczne infrastruktury i maszyny wirtualne aplikacji mogą być replikowane w sposób ciągły z lokalnego programu vCenter do chmury prywatnej. Możesz użyć chmury prywatnej do testowania pracy w trybie failover i zapewnić dostępność aplikacji podczas awarii. Podobne podejście można wykonać, aby skonfigurować chmurę prywatną jako lokację główną, która jest chroniona przez lokację odzyskiwania w innej lokalizacji.

> [!NOTE]
> Zapoznaj się z [zagadnieniami dotyczącymi ustalania rozmiarów dokumentu Zerto w przypadku replikacji wirtualnej Zerto](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) w celu uzyskania wskazówek dotyczących określania rozmiarów środowiska odzyskiwania po awarii.

Rozwiązanie CloudSimple:

* Eliminuje konieczność skonfigurowania centrum danych przeznaczonego do odzyskiwania po awarii (DR).
* Umożliwia korzystanie z lokalizacji platformy Azure, w których wdrożono CloudSimple na całym świecie.
* Zapewnia możliwość zmniejszenia kosztów wdrożenia i łącznego kosztu posiadania odzyskiwania po awarii.

Rozwiązanie wymaga:

* Instalowanie, Konfigurowanie i zarządzanie Zerto w chmurze prywatnej.
* Udostępnianie własnych licencji dla usługi Zerto, gdy Chmura prywatna jest chroniona. Można sparować Zerto uruchomione w witrynie CloudSimple z lokacją lokalną na potrzeby licencjonowania.

Na poniższej ilustracji przedstawiono architekturę rozwiązania Zerto.

![Architektura](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Jak wdrożyć rozwiązanie

W poniższych sekcjach opisano, jak wdrożyć rozwiązanie DR przy użyciu replikacji wirtualnej Zerto w chmurze prywatnej.

1. [Wymagania wstępne](#prerequisites)
2. [Opcjonalna konfiguracja w chmurze prywatnej CloudSimple](#optional-configuration-on-your-private-cloud)
3. [Konfigurowanie ZVM i VRA w chmurze prywatnej CloudSimple](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Konfigurowanie wirtualnej grupy ochrony Zerto](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć replikację wirtualną Zerto z poziomu środowiska lokalnego do chmury prywatnej, należy spełnić następujące wymagania wstępne.

1. [Skonfiguruj połączenie sieci VPN typu lokacja-lokacja między siecią lokalną a chmurą prywatną usługi CloudSimple](set-up-vpn.md).
2. [Skonfiguruj wyszukiwanie DNS w taki sposób, aby składniki zarządzania chmurą prywatną były przekazywane do serwerów DNS w chmurze prywatnej](on-premises-dns-setup.md).  Aby włączyć przekazywanie wyszukiwania DNS, Utwórz wpis strefy przekazywania na lokalnym serwerze DNS dla `*.cloudsimple.io` CloudSimple serwerów DNS.
3. Skonfiguruj wyszukiwanie DNS w taki sposób, że lokalne składniki programu vCenter są przekazywane do lokalnych serwerów DNS.  Serwery DNS muszą być dostępne z chmury prywatnej CloudSimple za pośrednictwem sieci VPN typu lokacja-lokacja. Aby uzyskać pomoc, Prześlij [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), dostarczając poniższe informacje.  

    * Lokalna nazwa domeny DNS
    * Adresy IP lokalnego serwera DNS

4. Zainstaluj system Windows Server w chmurze prywatnej. Serwer służy do instalowania Zerto Virtual Manager.
5. [Eskalacja uprawnień CloudSimple](escalate-private-cloud-privileges.md).
6. Utwórz nowego użytkownika w chmurze prywatnej vCenter z rolą administracyjną, która będzie używana jako konto usługi dla Menedżera wirtualnego Zerto.

### <a name="optional-configuration-on-your-private-cloud"></a>Opcjonalna konfiguracja w chmurze prywatnej

1. Utwórz co najmniej jedną pulę zasobów w chmurze prywatnej vCenter, która będzie używana jako pula zasobów docelowych dla maszyn wirtualnych w środowisku lokalnym.
2. Utwórz co najmniej jeden folder w chmurze prywatnej vCenter do użycia jako foldery docelowe dla maszyn wirtualnych w środowisku lokalnym.
3. Utwórz sieć VLAN dla sieci trybu failover i skonfiguruj reguły zapory. Otwórz [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) , aby uzyskać pomoc.
4. Tworzenie rozproszonych grup portów dla sieci trybu failover i sieci testowej w celu testowania pracy w trybie failover maszyn wirtualnych.
5. Zainstaluj [serwery DHCP i DNS](dns-dhcp-setup.md) lub użyj Active Directory kontrolera domeny w środowisku chmury prywatnej.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>Konfigurowanie ZVM i VRA w chmurze prywatnej

1. Zainstaluj program Zerto Virtual Manager (ZVM) w systemie Windows Server w chmurze prywatnej.
2. Zaloguj się do ZVM przy użyciu konta usługi utworzonego w poprzednich krokach.
3. Konfigurowanie licencjonowania dla Zerto Virtual Manager.
4. Zainstaluj urządzenie Zerto Virtual Replication (VRA) na hostach ESXi w chmurze prywatnej.
5. Parowanie prywatnej chmury ZVM z lokalnym ZVMem.

### <a name="set-up-zerto-virtual-protection-group"></a>Konfigurowanie wirtualnej grupy ochrony Zerto

1. Utwórz nową wirtualną grupę ochrony (VPG) i określ priorytet dla VPG.
2. Wybierz Maszyny wirtualne wymagające ochrony ciągłości działania i w razie potrzeby dostosuj kolejność rozruchu.
3. Wybierz lokację odzyskiwania jako chmurę prywatną i domyślny serwer odzyskiwania jako klaster chmury prywatnej lub utworzoną grupę zasobów. Wybierz pozycję **vsanDatastore** dla magazynu danych odzyskiwania w chmurze prywatnej.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Można dostosować opcję hosta dla poszczególnych maszyn wirtualnych w opcji ustawienia maszyny wirtualnej.

4. Dostosuj opcje magazynu zgodnie z potrzebami.
5. Określ sieci odzyskiwania, które mają być używane dla sieci trybu failover i sieci testowej trybu failover jako rozproszone grupy portów utworzone wcześniej, i Dostosuj skrypty odzyskiwania zgodnie z wymaganiami.
6. W razie potrzeby dostosuj ustawienia sieci dla poszczególnych maszyn wirtualnych i Utwórz VPG.
7. Test pracy w trybie failover po zakończeniu replikacji.

## <a name="reference"></a>Odwołanie

[Dokumentacja Zerto](https://www.zerto.com/myzerto/technical-documentation/)
