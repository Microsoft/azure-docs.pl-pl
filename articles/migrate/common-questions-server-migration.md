---
title: Często zadawane pytania dotyczące migracji serwera do migracji platformy Azure
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące migracji komputerów do migracji komputerów przy użyciu usługi Azure Migrate Server.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: bf33c276c721f3be16d75b964c2b223e90c2a560
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529806"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migracja serwera migracji platformy Azure: typowe pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące narzędzia Migracji platformy Azure: Migracja serwera. Jeśli masz inne pytania, sprawdź następujące zasoby:

- [Ogólne pytania](resources-faq.md) dotyczące migracji platformy Azure
- Pytania dotyczące [urządzenia migracji platformy Azure](common-questions-appliance.md)
- Pytania dotyczące [wizualizacji odnajdowania, oceny i zależności](common-questions-discovery-assessment.md)
- Odpowiedzi na pytania na [forum migracji platformy Azure](https://aka.ms/AzureMigrateForum)

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Jakie lokalizacje geograficzne są obsługiwane w przypadku migracji za pomocą usługi Azure Migrate?

Przejrzyj obsługiwane obszary geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-does-agentless-vmware-replication-work"></a>Jak działa replikacja VMware bez agenta?

Metoda replikacji bez agenta dla VMware używa migawek VMware i śledzenia bloków zmienionych przez VMware (CBT).

Oto proces:

1. Po uruchomieniu replikacji zaplanowano początkowy cykl replikacji. W początkowym cyklu jest pobierana migawka maszyny Wirtualnej. Migawka służy do replikowania VMDKs maszyn wirtualnych (dysków). 
2. Po zakończeniu początkowego cyklu replikacji cykle replikacji różnicowej są okresowo planowane.
    - Podczas replikacji różnicowej jest pobierana migawka i replikowane są bloki danych, które uległy zmianie od poprzedniego cyklu replikacji.
    - VMware CBT służy do określania bloków, które uległy zmianie od ostatniego cyklu.
    - Częstotliwość okresowych cykli replikacji jest automatycznie zarządzana przez usługę Azure Migrate i zależy od liczby innych maszyn wirtualnych i dysków, które są jednocześnie replikowane z tego samego magazynu danych. W idealnych warunkach replikacja ostatecznie zbiega się do jednego cyklu na godzinę dla każdej maszyny Wirtualnej.

Podczas migracji zaplanowano cykl replikacji na żądanie, aby urządzenie przechwyciło wszystkie pozostałe dane. Aby zapewnić zerową utratę danych i spójność aplikacji, można zamknąć komputer podczas migracji.

## <a name="why-isnt-resynchronization-exposed"></a>Dlaczego ponowna synchronizacja nie jest narażona?

Podczas migracji bez agenta w każdym cyklu delta jest zapisywana różnica między bieżącą migawką a poprzednio zrobiona migawka. Zawsze jest to różnica między migawkami, składaniem danych. Jeśli określony sektor jest zapisywany *N* razy między migawkami, tylko ostatni zapis musi zostać przeniesiony, ponieważ jesteśmy zainteresowani tylko w ostatniej synchronizacji. Proces różni się od replikacji opartej na agentach, podczas której śledzimy i stosujemy każdy zapis. W tym procesie każdy cykl delta jest ponowna synchronizacja. Tak, nie opcji ponownej synchronizyzacji narażone. Jeśli dyski kiedykolwiek nie są synchronizowane z powodu awarii, jest ustalona w następnym cyklu. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Jak współczynnik zmian wpływa na replikację bezagentową?

Ponieważ replikacja bez agenta składa się w danych, *wzorzec zmian* jest ważniejszy niż *współczynnik zmian*. Gdy plik jest zapisywany wielokrotnie, stawka nie ma większego wpływu. Jednak wzorzec, w którym co drugi sektor jest zapisywany powoduje wysoki współczynnik zmian w następnym cyklu. Ponieważ minimalizujemy ilość przesyłanych danych, zezwalamy na składanie danych w jak największej ilości danych, zanim zaplanujemy następny cykl.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Jak często jest zaplanowany cykl replikacji?

Formuła do zaplanowania następnego cyklu replikacji to (poprzedni czas cyklu / 2) lub jedna godzina, w zależności od tego, która z tych wartości jest wyższa.

Na przykład jeśli maszyna wirtualna trwa cztery godziny dla cyklu delta, następny cykl jest zaplanowane w ciągu dwóch godzin, a nie w następnej godzinie. Proces jest różny natychmiast po replikacji początkowej, gdy pierwszy cykl delta jest zaplanowane natychmiast.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Jak replikacja bez agenta wpływa na serwery VMware?

Replikacja bez agenta powoduje pewien wpływ na wydajność hostów VMware vCenter Server i VMware ESXi. Ponieważ replikacja bez agenta używa migawek, zużywa we/wy we/wy na magazynie, więc wymagana jest przepustowość magazynu we/wy. Nie zaleca się używania replikacji bez agenta, jeśli masz ograniczenia dotyczące magazynu lub we/wy w swoim środowisku.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Czy mogę wykonywać bezagentową migrację maszyn wirtualnych UEFI do platformy Azure Gen 2?

Nie. Użyj usługi Azure Site Recovery, aby migrować te maszyny wirtualne do maszyn wirtualnych platformy 2 platformy Azure. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Czy mogę przypiąć maszyny wirtualne do stref dostępności platformy Azure podczas migracji?

Nie. Strefy dostępności platformy Azure nie są obsługiwane w przypadku migracji migracji platformy Azure.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Jakiego protokołu transportu używa usługa Azure Migrate podczas replikacji?

Usługa Azure Migrate używa protokołu NBD (Network Block Device) z szyfrowaniem SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Jaka jest minimalna wersja serwera vCenter Server wymagana do migracji?

Musisz mieć co najmniej vCenter Server 5.5 i vSphere ESXi hosta w wersji 5.5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Czy klienci mogą migrować swoje maszyny wirtualne na dyski niezarządzane?

Nie. Usługa Azure Migrate obsługuje migrację tylko do dysków zarządzanych (standardowy dysk twardy, dysk SSD w wersji Premium).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Ile maszyn wirtualnych można replikować jednocześnie przy użyciu migracji bez agenta?

Obecnie można jednocześnie migrować 100 maszyn wirtualnych na wystąpienie serwera vCenter Server. Migrowanie w partiach 10 maszyn wirtualnych.

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Jak ograniczyć replikację przy użyciu urządzenia migracji platformy Azure do replikacji VMware bez agenta?  

Można przepustnicy za pomocą NetQosPolicy. Przykład:

AppNamePrefix do użycia w NetQosPolicy jest "GatewayWindowsService.exe". Można utworzyć zasady na urządzeniu migracji platformy Azure do ograniczania ruchu replikacji z urządzenia, tworząc zasady, takie jak ta:
 
New-NetQosPolicy -Name "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>Kiedy mogę migrować maszyny jako serwery fizyczne?

Migrowanie maszyn przez traktowanie ich jako serwerów fizycznych jest przydatne w wielu scenariuszach:

- Podczas migracji lokalnych serwerów fizycznych.
- Jeśli migrujesz maszyny wirtualne zwirtualizowane przez platformy takie jak Xen, KVM.
- Aby przeprowadzić migrację maszyn wirtualnych funkcji Hyper-V lub VMware, jeśli z jakiegoś powodu nie można użyć standardowego procesu migracji dla migracji [funkcji Hyper-V](tutorial-migrate-hyper-v.md)lub [VMware.](server-migrate-overview.md) Na przykład, jeśli nie używasz VMware vCenter i używasz tylko hostów ESXi.
- Aby przeprowadzić migrację maszyn wirtualnych, które są aktualnie uruchomione w chmurach prywatnych na platformę Azure
- Jeśli chcesz przeprowadzić migrację maszyn wirtualnych działających w chmurach publicznych, takich jak Amazon Web Services (AWS) lub Google Cloud Platform (GCP), na platformę Azure.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>Wdrożyłem dwa (lub więcej) urządzeń, aby odnajdować maszyny wirtualne w moim serwerze vCenter. Jednak podczas próby migracji maszyn wirtualnych, widzę tylko maszyny wirtualne odpowiadające jednemu z urządzeń.

Chociaż może to być dobry przypadek użycia, obecnie nie obsługujemy go. Wdrażanie dwóch (lub więcej) urządzeń w celu odnajdywania tego samego zestawu maszyn wirtualnych powoduje problem z usługą, w którym własność maszyny Wirtualnej utrzymuje przełączanie między dwoma urządzeniami. Jest to powód, dla którego widzisz maszyny wirtualne pojawiające się i znikające. W takich przypadkach, aby rozwiązać ten problem, należy usunąć jedno urządzenie i wykonać twarde odświeżenie.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Czy do migracji maszyn wirtualnych VMware potrzebny jest vMware vCenter?
Aby [przeprowadzić migrację maszyn wirtualnych VMware](server-migrate-overview.md) przy użyciu migracji opartej na agentach VMware lub bez agenta, hosty ESXi, na których znajdują się maszyny wirtualne, muszą być zarządzane przez serwer vCenter Server. Jeśli nie masz serwera vCenter, możesz migrować maszyny wirtualne VMware, migrując je jako serwery fizyczne. [Dowiedz się więcej](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>Następne kroki

Przeczytaj [omówienie migracji platformy Azure](migrate-services-overview.md).
