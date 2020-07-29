---
title: Często zadawane pytania dotyczące migracji Azure Migrate serwera
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące korzystania z migracji Azure Migrate serwera w celu migrowania maszyn.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: af40aecaa1614542074cf87ce95eb81492233bdc
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321228"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migracja serwera Azure Migrate: typowe pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure Migrate: serwerowego narzędzia migracji. Jeśli masz inne pytania, zapoznaj się z następującymi zasobami:

- [Ogólne pytania](resources-faq.md) dotyczące Azure Migrate
- Pytania dotyczące [urządzenia Azure Migrate](common-questions-appliance.md)
- Pytania dotyczące [odnajdywania, oceny i wizualizacji zależności](common-questions-discovery-assessment.md)
- Uzyskaj odpowiedzi na pytania na [forum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Jakie lokalizacje geograficzne są obsługiwane w przypadku migracji z Azure Migrate?

Przejrzyj obsługiwane lokalizacje geograficzne [chmur publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [chmur dla instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-does-agentless-vmware-replication-work"></a>Jak działa replikacja VMware bez agenta?

Metoda replikacji bez agenta dla oprogramowania VMware używa migawek VMware i śledzenia bloków zmienionych przez program VMware (CBT).

Oto proces:

1. Po uruchomieniu replikacji jest zaplanowana początkowa cykl replikacji. W cyklu początkowym tworzona jest migawka maszyny wirtualnej. Migawka służy do replikowania maszyn wirtualnych VMDK (disks). 
2. Po zakończeniu cyklu replikacji początkowej cykle replikacji różnicowej są planowane okresowo.
    - Podczas replikacji różnicowej jest wykonywana migawka, a bloki danych, które uległy zmianie od czasu ostatniego cyklu replikacji, są replikowane.
    - Program VMware CBT służy do określania bloków, które uległy zmianie od ostatniego cyklu.
    - Częstotliwość okresowych cykli replikacji jest automatycznie zarządzana przez Azure Migrate i zależy od tego, jak wiele innych maszyn wirtualnych i dysków jest jednocześnie replikowana z tego samego magazynu danych. W idealnych warunkach replikacja ostatecznie wywoła jeden cykl na godzinę dla każdej maszyny wirtualnej.

Podczas migracji w celu przechwycenia wszystkich pozostałych danych na komputerze zaplanowano cykl replikacji na żądanie. Aby zapewnić zero utraty danych i spójność aplikacji, można wyłączyć maszynę podczas migracji.

## <a name="why-isnt-resynchronization-exposed"></a>Dlaczego nie jest dostępna ponowna synchronizacja?

Podczas migracji bez wykorzystania agentów w każdym cyklu różnicowym jest zapisywana różnica między bieżącą migawką a wcześniej zrobioną migawką. Zawsze jest to różnica między migawkami, składania danych w programie. Jeśli określony sektor jest zapisywana *N* razy między migawkami, tylko ostatni zapis musi być transferowany, ponieważ interesuje tylko ostatnią synchronizację. Proces różni się od replikacji opartej na agentach, podczas którego śledzimy i stosujemy każdy zapis. W tym procesie każdy cykl różnicowy jest ponowną synchronizacją. Tak więc nie jest dostępna opcja ponownej synchronizacji. Jeśli dyski kiedykolwiek nie są zsynchronizowane ze względu na awarię, zostanie ona rozwiązana w następnym cyklu. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Jak współczynnik zmian wpływa na replikację bezagentową?

Ponieważ replikacja bez agentów jest składana w danych, *wzorzec* zmian jest ważniejszy niż *współczynnik*zmian. Gdy plik zostanie ponownie zapisany i ponownie, szybkość nie ma znacznie wpływu. Jednak wzorzec, w którym każdy inny sektor jest zapisywana, powoduje duże zmiany w następnym cyklu. Ze względu na to, że minimalizujemy ilość przesyłanych danych, będziemy mogli złożyć dane tak dużo, jak to możliwe, przed zaplanowaniem następnego cyklu.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Jak często zaplanowano cykl replikacji?

Formuła do zaplanowania następnego cyklu replikacji to (czas poprzedniego cyklu/2) lub jedna godzina, w zależności od tego, co jest wyższe.

Na przykład jeśli maszyna wirtualna zajmuje cztery godziny cyklu różnicowego, kolejny cykl jest zaplanowany w ciągu dwóch godzin, a nie w ciągu następnej godziny. Proces jest inny od razu po replikacji początkowej, gdy pierwszy cykl różnicowy jest zaplanowany natychmiast.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>W jaki sposób replikacja bez agentów ma wpływ na serwery VMware?

Replikacja bez wykorzystania agentów powoduje pewien wpływ na wydajność na hostach VMware vCenter Server i VMware ESXi. Ponieważ replikacja bezagentowa używa migawek, zużywa ona operacje we/wy na sekundę, więc wymagana jest pewna przepustowość magazynu IOPS. Nie zalecamy korzystania z replikacji bez wykorzystania agentów, jeśli istnieją ograniczenia dotyczące magazynu lub operacji we/wy na sekundę w danym środowisku.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Czy można przeprowadzić migrację maszyn wirtualnych UEFI do usługi Azure Gen 2 bez wykorzystania agentów?

Nie. Użyj Azure Site Recovery do migrowania tych maszyn wirtualnych do maszyn wirtualnych platformy Azure w generacji 2. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Czy mogę przypiąć maszyny wirtualne do Strefy dostępności platformy Azure podczas migracji?

Nie. Strefy dostępności platformy Azure nie są obsługiwane w przypadku migracji Azure Migrate.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Jakiego protokołu transportowego Azure Migrate używać podczas replikacji?

Azure Migrate używa protokołu urządzenia bloku sieciowego (w przypadku szyfrowania TLS).

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>Jak dane są przesyłane ze środowiska Premium do platformy Azure? Czy jest szyfrowana przed przesłaniem? 
Urządzenie Azure Migrate w przypadku replikacji bez agenta kompresuje dane i szyfruje przed przekazaniem. Dane są przesyłane przy użyciu bezpiecznego kanału komunikacyjnego za pośrednictwem protokołu HTTPS i korzystają z protokołu TLS 1,2 lub nowszego. Ponadto usługa Azure Storage automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze (szyfrowanie w trybie REST).  

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Co to jest minimalna wersja vCenter Server wymagana do migracji?

Musisz mieć co najmniej vCenter Server 5,5 i vSphere ESXi w wersji 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Czy klienci mogą migrować maszyny wirtualne do dysków niezarządzanych?

Nie. Azure Migrate obsługuje migrację tylko do dysków zarządzanych (HDD w warstwie Standardowa, SSD w warstwie Premium).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Ile maszyn wirtualnych można replikować jednocześnie za pomocą migracji bez wykorzystania agentów?

Obecnie można migrować maszyny wirtualne 300 na wystąpienie vCenter Server jednocześnie. Migrowanie w partiach 10 maszyn wirtualnych.

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Jak mogęnie przepustowości w przypadku używania urządzenia Azure Migrate do replikacji VMware bez agentów?  

Można ograniczyć użycie NetQosPolicy. Na przykład:

AppNamePrefix do użycia w NetQosPolicy to "GatewayWindowsService.exe". Można utworzyć zasady na urządzeniu Azure Migrate, aby ograniczyć ruch związany z replikacją z urządzenia przez utworzenie zasad, takich jak:
 
New-NetQosPolicy-Name "ThrottleReplication"-AppPathNameMatchCondition "GatewayWindowsService.exe"-ThrottleRateActionBitsPerSecond 1 MB

## <a name="can-i-migrate-vms-that-are-already-being-replicated-to-azure"></a>Czy mogę migrować maszyny wirtualne, które są już replikowane na platformę Azure? 

Jeśli maszyny wirtualne są już replikowane na platformę Azure, nie można migrować tych maszyn jako maszyn wirtualnych za pomocą migracji Azure Migrate Server. Jako obejście można traktować maszyny wirtualne jako serwery fizyczne i migrować je zgodnie z [obsługiwaną migracją serwera fizycznego](migrate-support-matrix-physical-migration.md).

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>Kiedy należy migrować maszyny jako serwery fizyczne?

Migrowanie maszyn przez traktowanie ich jako serwerów fizycznych jest przydatne w wielu scenariuszach:

- Podczas migrowania lokalnych serwerów fizycznych.
- W przypadku migrowania maszyn wirtualnych zwirtualizowanych przez platformy takie jak Xen, KVM.
- Aby przeprowadzić migrację maszyn wirtualnych funkcji Hyper-V lub oprogramowania VMware, jeśli z jakiegoś powodu nie można użyć standardowego procesu migracji dla [funkcji Hyper-V](tutorial-migrate-hyper-v.md)lub migracji [VMware](server-migrate-overview.md) . Jeśli na przykład nie korzystasz z programu VMware vCenter i są używane tylko hosty ESXi.
- Aby migrować maszyny wirtualne, które są aktualnie uruchomione w chmurach prywatnych na platformie Azure
- Jeśli chcesz przeprowadzić migrację maszyn wirtualnych działających w chmurach publicznych, takich jak Amazon Web Services (AWS) lub Google Cloud Platform (GCP), na platformę Azure.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>Wdrożono co najmniej dwa urządzenia do odnajdywania maszyn wirtualnych w vCenter Server. Jednak podczas próby migrowania maszyn wirtualnych są wyświetlane tylko maszyny wirtualne odpowiadające jednemu urządzeniu.

Jeśli skonfigurowano wiele urządzeń, jest wymagane, aby nie nakładać się na maszyny wirtualne na kontach programu vCenter. Odnajdywanie z takim nakładaniem jest nieobsługiwanym scenariuszem.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Czy do migrowania maszyn wirtualnych VMware jest potrzebny program VMware vCenter?
Aby [przeprowadzić migrację maszyn wirtualnych VMware](server-migrate-overview.md) przy użyciu migracji opartej na agencie VMware lub bez agentów, hosty ESXi, na których znajdują się maszyny wirtualne, muszą być zarządzane przez vCenter Server. Jeśli nie masz vCenter Server, możesz migrować maszyny wirtualne VMware przez migrowanie ich jako serwerów fizycznych. [Dowiedz się więcej](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [omówieniem Azure Migrate](migrate-services-overview.md).
