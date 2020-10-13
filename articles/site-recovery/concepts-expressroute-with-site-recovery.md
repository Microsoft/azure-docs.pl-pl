---
title: Informacje o korzystaniu z programu ExpressRoute z Azure Site Recovery
description: Opisuje sposób korzystania z usługi Azure ExpressRoute z usługą Azure Site Recovery na potrzeby odzyskiwania po awarii i migracji.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: 68671a98c7703514af11de2f8c505110129fe713
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91398650"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Usługa Azure ExpressRoute z Azure Site Recovery

Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Dzięki usłudze ExpressRoute możesz ustanowić połączenia z usługami firmy Microsoft w chmurze, np. Microsoft Azure, Microsoft 365 i Dynamics 365.

W tym artykule opisano, jak można użyć usługi Azure ExpressRoute z Azure Site Recovery na potrzeby odzyskiwania po awarii i migracji.

## <a name="expressroute-circuits"></a>Obwody usługi ExpressRoute

Obwód usługi ExpressRoute reprezentuje połączenie logiczne między infrastrukturą lokalną i usługami w chmurze firmy Microsoft przez dostawcę połączenia. Można zamówić wiele obwodów usługi ExpressRoute. Każdy obwód może znajdować się w tym samym lub różnych regionach i może być połączony z Twoim lokalem za pośrednictwem różnych dostawców łączności. Więcej informacji na temat obwodów usługi ExpressRoute znajduje się [tutaj](../expressroute/expressroute-circuit-peerings.md).

Obwód usługi ExpressRoute ma skojarzone wiele domen routingu. Dowiedz się więcej o domenach routingu ExpressRoute i porównaj je [tutaj](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Replikacja lokalna do platformy Azure za pomocą ExpressRoute

Azure Site Recovery umożliwia odzyskiwanie po awarii i migrację na platformę Azure dla lokalnych [maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-architecture.md), [maszyn wirtualnych VMware](vmware-azure-architecture.md)i [serwerów fizycznych](physical-azure-architecture.md). W przypadku wszystkich scenariuszy lokalnych z platformą Azure dane replikacji są wysyłane do konta usługi Azure Storage i przechowywane na nim. Podczas replikacji nie są naliczane żadne opłaty za maszyny wirtualne. Po uruchomieniu trybu failover na platformie Azure Site Recovery automatycznie tworzy maszyny wirtualne IaaS platformy Azure.

Site Recovery replikuje dane do konta usługi Azure Storage lub dysku zarządzanego repliki w docelowym regionie platformy Azure za pośrednictwem publicznego punktu końcowego. Aby użyć ExpressRoute Site Recovery na potrzeby ruchu związanego z replikacją, można wykorzystać [komunikację równorzędną firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) lub istniejącą [publiczną komunikację równorzędną](../expressroute/about-public-peering.md) (przestarzałą dla nowych operacji tworzenia). Komunikacja równorzędna firmy Microsoft jest zalecaną domeną routingu na potrzeby replikacji. Należy zauważyć, że replikacja nie jest obsługiwana w trybie prywatnej komunikacji równorzędnej.

Upewnij się, że zostały spełnione także [wymagania dotyczące sieci](vmware-azure-configuration-server-requirements.md#network-requirements) dla serwera konfiguracji. Serwer konfiguracji wymaga połączenia z określonymi adresami URL w celu aranżacji replikacji Site Recovery. Nie można użyć ExpressRoute dla tej łączności. 

W przypadku korzystania z serwera proxy lokalnie i chcą korzystać z ExpressRoute na potrzeby ruchu związanego z replikacją należy skonfigurować listę pomijania proxy na serwerze konfiguracji i serwerach przetwarzania. Wykonaj następujące czynności:

- Pobierz narzędzie PsExec z tego [miejsca](https://aka.ms/PsExec) , aby uzyskać dostęp do kontekstu użytkownika systemu.
- Otwórz program Internet Explorer w kontekście użytkownika systemu, uruchamiając następujący wiersz polecenia PsExec-s-i "użycie ścieżki%ProgramFiles%\Internet Explorer\iexplore.exe"
- Dodawanie ustawień serwera proxy w programie IE
- Na liście obejście Dodaj adres URL usługi Azure Storage *. blob.core.windows.net

Zapewni to, że ruch związany z replikacją odbywa się za pomocą ExpressRoute, podczas gdy komunikacja będzie możliwa przez serwer proxy.

Gdy maszyny wirtualne lub serwery zostaną przełączone w tryb failover do sieci wirtualnej platformy Azure, możesz uzyskać do nich dostęp przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

Połączony scenariusz jest przedstawiony na poniższym diagramie: ![ lokalne na platformie Azure z ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replikacja z platformy Azure do platformy Azure za pomocą ExpressRoute

Azure Site Recovery włącza odzyskiwanie po awarii [maszyn wirtualnych platformy Azure](azure-to-azure-architecture.md). W zależności od tego, czy maszyny wirtualne platformy Azure korzystają z [usługi azure Managed disks](../virtual-machines/managed-disks-overview.md), dane replikacji są wysyłane do konta usługi Azure Storage lub dysku zarządzanego repliki w docelowym regionie platformy Azure. Mimo że punkty końcowe replikacji są publiczne, ruch związany z replikacją na potrzeby replikacji maszyny wirtualnej platformy Azure, domyślnie nie przechodzi przez Internet, niezależnie od regionu platformy Azure, w którym znajduje się źródłowa sieć wirtualna. Można zastąpić domyślną trasę systemową platformy Azure dla prefiksu adresu 0.0.0.0/0 z [trasą niestandardową](../virtual-network/virtual-networks-udr-overview.md#custom-routes) i przekierować ruch maszyny wirtualnej do lokalnego urządzenia sieciowego (urządzenie WUS), ale ta konfiguracja nie jest zalecana w przypadku replikacji Site Recovery. Jeśli używasz tras niestandardowych, należy [utworzyć punkt końcowy usługi sieci wirtualnej](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) w sieci wirtualnej dla elementu "Storage", aby ruch związany z replikacją nie opuszcza granicy platformy Azure.

W przypadku odzyskiwania po awarii maszyny wirtualnej platformy Azure domyślnie ExpressRoute nie jest wymagana do replikacji. Gdy maszyny wirtualne zostaną przełączone w tryb failover do docelowego regionu platformy Azure, możesz uzyskać do nich dostęp przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering). Należy pamiętać, że ceny transferu danych są stosowane niezależnie od trybu replikacji danych w regionach platformy Azure.

Jeśli używasz już ExpressRoute do nawiązywania połączenia z lokalnego centrum danych z maszynami wirtualnymi platformy Azure w regionie źródłowym, możesz zaplanować ponowne ustanowienie połączenia ExpressRoute w regionie docelowym trybu failover. Można użyć tego samego obwodu usługi ExpressRoute, aby połączyć się z regionem docelowym za pomocą nowego połączenia sieci wirtualnej lub użyć oddzielnego obwodu usługi ExpressRoute i połączenia na potrzeby odzyskiwania po awarii. W [tym miejscu](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)opisano różne możliwe scenariusze.

Maszyny wirtualne platformy Azure można replikować do dowolnego regionu świadczenia usługi Azure w tym samym klastrze geograficznym, co [opisano tutaj](../site-recovery/azure-to-azure-support-matrix.md#region-support). Jeśli wybrany docelowy region platformy Azure nie znajduje się w tym samym regionie geopolitycznym co źródło, może być konieczne włączenie opcji ExpressRoute Premium. Aby uzyskać więcej informacji, sprawdź [lokalizacje ExpressRoute](../expressroute/expressroute-locations.md) i [Cennik ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [obwodów usługi ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Dowiedz się więcej o [domenach routingu ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Dowiedz się więcej o [lokalizacjach ExpressRoute](../expressroute/expressroute-locations.md).
- Dowiedz się więcej na temat odzyskiwania po awarii [maszyn wirtualnych platformy Azure przy użyciu usługi ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
