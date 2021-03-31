---
title: Sieciowe grupy zabezpieczeń z Azure Site Recovery | Microsoft Docs
description: Opisuje sposób używania sieciowych grup zabezpieczeń z Azure Site Recovery na potrzeby odzyskiwania po awarii i migracji
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: harshacs
ms.openlocfilehash: 367aba09f84da1e227c08721077aa1b2132a62bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92367978"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Sieciowe grupy zabezpieczeń z usługą Azure Site Recovery

Sieciowe grupy zabezpieczeń służą do ograniczania ruchu sieciowego do zasobów w sieci wirtualnej. [Sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń)](../virtual-network/network-security-groups-overview.md#network-security-groups) zawiera listę reguł zabezpieczeń, które zezwalają na ruch przychodzący lub zablokowany ruchu sieciowego w sieci lub na podstawie źródłowego lub docelowego adresu IP, portu i protokołu.

W modelu wdrażania Menedżer zasobów sieciowych grup zabezpieczeń można kojarzyć z podsieciami lub poszczególnymi interfejsami sieciowymi. Jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, te reguły są stosowane do wszystkich zasobów połączonych z tą podsiecią. Ruch można dodatkowo ograniczyć przez skojarzenie sieciowej grupy zabezpieczeń z poszczególnymi interfejsami sieciowymi w podsieci, która ma już skojarzone sieciowej grupy zabezpieczeń.

W tym artykule opisano, jak można użyć sieciowych grup zabezpieczeń z Azure Site Recovery.

## <a name="using-network-security-groups"></a>Korzystanie z sieciowych grup zabezpieczeń

Pojedyncza podsieć może mieć wartość zero lub jedną skojarzoną sieciowej grupy zabezpieczeń. Pojedynczy interfejs sieciowy może również mieć wartość zero lub jeden skojarzony sieciowej grupy zabezpieczeń. W związku z tym można efektywnie mieć dwa ograniczenia ruchu dla maszyny wirtualnej, kojarząc najpierw sieciowej grupy zabezpieczeń z podsiecią, a następnie inną sieciowej grupy zabezpieczeń do interfejsu sieciowego maszyny wirtualnej. Stosowanie reguł sieciowej grupy zabezpieczeń w tym przypadku zależy od kierunku ruchu i priorytetów stosowanych reguł zabezpieczeń.

Rozważmy prosty przykład z jedną maszyną wirtualną w następujący sposób:
-    Maszyna wirtualna jest umieszczana w **podsieci contoso**.
-    **Podsieć contoso** jest skojarzona z **podsiecią sieciowej grupy zabezpieczeń**.
-    Interfejs sieciowy VM również jest skojarzony z **maszyną wirtualną sieciowej grupy zabezpieczeń**.

![SIECIOWEJ grupy zabezpieczeń z Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

W tym przykładzie w przypadku ruchu przychodzącego najpierw sieciowej grupy zabezpieczeń podsieć jest szacowana. Każdy ruch dozwolony za pomocą podsieci sieciowej grupy zabezpieczeń jest następnie oceniany przez sieciowej grupy zabezpieczeń maszyny wirtualnej. Odwrócenie jest stosowane dla ruchu wychodzącego, a sieciowej grupy zabezpieczeń maszyny wirtualnej jest najpierw oceniane. Każdy ruch dozwolony za pomocą maszyny wirtualnej sieciowej grupy zabezpieczeń jest następnie oceniany przez sieciowej grupy zabezpieczeń podsieci.

Pozwala to na stosowanie szczegółowych reguł zabezpieczeń. Na przykład możesz chcieć zezwolić na dostęp do przychodzącego Internetu do kilku maszyn wirtualnych aplikacji (takich jak maszyny wirtualne frontonu) w podsieci, ale ograniczyć dostęp do Internetu na innych maszynach wirtualnych (takich jak baza danych i inne maszyny wirtualne zaplecza). W takim przypadku można mieć bardziej lenientą regułę w podsieci sieciowej grupy zabezpieczeń, zezwalająca na ruch internetowy i ograniczyć dostęp do określonych maszyn wirtualnych przez odmowę dostępu do maszyny wirtualnej sieciowej grupy zabezpieczeń. Tę samą wartość można zastosować dla ruchu wychodzącego.

Podczas konfigurowania takich konfiguracji sieciowej grupy zabezpieczeń upewnij się, że odpowiednie priorytety są stosowane do [reguł zabezpieczeń](../virtual-network/network-security-groups-overview.md#security-rules). Reguły są przetwarzane w kolejności priorytetów. Im niższy numer, tym wyższy priorytet, więc te o niższych numerach są przetwarzane przed tymi o wyższych numerach. Kiedy ruch jest zgodny z regułą, przetwarzanie zostaje zatrzymane. W związku z tym żadne istniejące reguły o niższych priorytetach (wyższych numerach), które mają takie same atrybuty jak reguły o wyższych priorytetach, nie będą przetwarzane.

Możesz czasami nie wiedzieć, że grupy zabezpieczeń sieci są stosowane do interfejsu sieciowego i podsieci. Aby sprawdzić reguły agregacji zastosowane do interfejsu sieciowego, można wyświetlić [obowiązujące reguły zabezpieczeń](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) interfejsu sieciowego. Możesz również użyć możliwości [weryfikacji przepływu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) w [usłudze Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) , aby określić, czy komunikacja może być lub z interfejsu sieciowego. Narzędzie informuje, czy komunikacja jest dozwolona, oraz która reguła zabezpieczeń sieci zezwala lub nie zezwala na ruch.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Replikacja lokalna do platformy Azure za pomocą sieciowej grupy zabezpieczeń

Azure Site Recovery umożliwia odzyskiwanie po awarii i migrację na platformę Azure dla lokalnych [maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-architecture.md), [maszyn wirtualnych VMware](vmware-azure-architecture.md)i [serwerów fizycznych](physical-azure-architecture.md). W przypadku wszystkich scenariuszy lokalnych z platformą Azure dane replikacji są wysyłane do konta usługi Azure Storage i przechowywane na nim. Podczas replikacji nie są naliczane żadne opłaty za maszyny wirtualne. Po uruchomieniu trybu failover na platformie Azure Site Recovery automatycznie tworzy maszyny wirtualne IaaS platformy Azure.

Po utworzeniu maszyn wirtualnych po przejściu do trybu failover na platformie Azure sieciowych grup zabezpieczeń może służyć do ograniczania ruchu sieciowego do sieci wirtualnej i maszyn wirtualnych. Site Recovery nie tworzy sieciowych grup zabezpieczeń w ramach operacji przejścia w tryb failover. Zalecamy utworzenie wymaganej usługi Azure sieciowych grup zabezpieczeń przed zainicjowaniem pracy w trybie failover. Następnie można skojarzyć sieciowych grup zabezpieczeń z maszynami wirtualnymi, które przełączone do trybu failover, przy użyciu skryptów automatyzacji z zaawansowanymi [planami odzyskiwania](site-recovery-create-recovery-plans.md)Site Recovery.

Na przykład jeśli konfiguracja maszyny wirtualnej po zakończeniu pracy w trybie failover jest podobna do [przykładowego](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) szczegółowo szczegółowego scenariusza:
-    W celu zaplanowania odzyskiwania po awarii w docelowym regionie platformy Azure można utworzyć **Sieć** **wirtualną firmy Contoso** i jej część.
-    Można również utworzyć i skonfigurować zarówno **sieciowej grupy zabezpieczeń podsieci** , jak i **sieciowej grupy zabezpieczeń maszyny wirtualnej** w ramach tego samego planu odzyskiwania po awarii.
-    **Sieciowej grupy zabezpieczeń podsieci** można następnie natychmiast skojarzyć z **podsiecią contoso**, ponieważ zarówno sieciowej grupy zabezpieczeń, jak i podsieć są już dostępne.
-    **Sieciowej grupy zabezpieczeń maszyny wirtualnej** można skojarzyć z maszynami wirtualnymi podczas pracy w trybie failover przy użyciu planów odzyskiwania.

Po utworzeniu i skonfigurowaniu sieciowych grup zabezpieczeń zalecamy uruchomienie [testowej pracy w trybie failover](site-recovery-test-failover-to-azure.md) w celu zweryfikowania skojarzeń sieciowej grupy zabezpieczeń z skryptami i łączności maszyny wirtualnej po awarii.

## <a name="azure-to-azure-replication-with-nsg"></a>Replikacja z platformy Azure do platformy Azure za pomocą sieciowej grupy zabezpieczeń

Azure Site Recovery włącza odzyskiwanie po awarii [maszyn wirtualnych platformy Azure](azure-to-azure-architecture.md). Podczas włączania replikacji dla maszyn wirtualnych platformy Azure Site Recovery można utworzyć sieci wirtualne repliki (w tym podsieci i podsieci bramy) w regionie docelowym i utworzyć wymagane mapowania między źródłową i docelową siecią wirtualną. Można również wstępnie utworzyć docelowe sieci i podsieci po stronie docelowej oraz korzystać z tego samego programu podczas włączania replikacji. Site Recovery nie tworzy żadnych maszyn wirtualnych w docelowym regionie platformy Azure przed [przejściem do trybu failover](azure-to-azure-tutorial-failover-failback.md).

W przypadku replikacji maszyny wirtualnej platformy Azure upewnij się, że reguły sieciowej grupy zabezpieczeń w źródłowym regionie platformy Azure zezwalają na [łączność wychodzącą](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) dla ruchu związanego z replikacją. Możesz również testować i weryfikować te wymagane reguły, korzystając z tej [przykładowej konfiguracji sieciowej grupy zabezpieczeń](azure-to-azure-about-networking.md#example-nsg-configuration).

Site Recovery nie tworzy ani nie replikuje sieciowych grup zabezpieczeń w ramach operacji przejścia w tryb failover. Przed zainicjowaniem trybu failover zalecamy utworzenie wymaganego sieciowych grup zabezpieczeń w docelowym regionie platformy Azure. Następnie można skojarzyć sieciowych grup zabezpieczeń z maszynami wirtualnymi, które przełączone do trybu failover, przy użyciu skryptów automatyzacji z zaawansowanymi [planami odzyskiwania](site-recovery-create-recovery-plans.md)Site Recovery.

Rozważamy [Przykładowy scenariusz](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) opisany wcześniej:
-    Site Recovery może tworzyć repliki sieci **wirtualnej firmy Contoso** i **podsieci firmy Contoso** w docelowym regionie platformy Azure, gdy replikacja jest włączona dla maszyny wirtualnej.
-    W docelowym regionie platformy Azure można utworzyć żądane repliki **podsieci sieciowej grupy zabezpieczeń** i **VM sieciowej grupy zabezpieczeń** (odpowiednio: sieciowej grupy zabezpieczeń i Target **Subnet** **sieciowej grupy zabezpieczeń**), co pozwala na zastosowanie wszelkich dodatkowych reguł wymaganych w regionie docelowym.
-    **Sieciowej grupy zabezpieczeń podsieci docelowej** można następnie natychmiast skojarzyć z podsiecią regionu docelowego, ponieważ zarówno sieciowej grupy zabezpieczeń, jak i podsieć są już dostępne.
-    **Docelowa sieciowej grupy zabezpieczeń maszyny wirtualnej** można skojarzyć z maszynami wirtualnymi podczas pracy w trybie failover przy użyciu planów odzyskiwania.

Po utworzeniu i skonfigurowaniu sieciowych grup zabezpieczeń zalecamy uruchomienie [testowej pracy w trybie failover](azure-to-azure-tutorial-dr-drill.md) w celu zweryfikowania skojarzeń sieciowej grupy zabezpieczeń z skryptami i łączności maszyny wirtualnej po awarii.

## <a name="next-steps"></a>Następne kroki
-    Dowiedz się więcej na temat [sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md#network-security-groups).
-    Dowiedz się więcej o [regułach zabezpieczeń](../virtual-network/network-security-groups-overview.md#security-rules)sieciowej grupy zabezpieczeń.
-    Dowiedz się więcej o obowiązujących [regułach zabezpieczeń](../virtual-network/diagnose-network-traffic-filter-problem.md) dla sieciowej grupy zabezpieczeń.
-    Dowiedz się więcej o [planach odzyskiwania](site-recovery-create-recovery-plans.md) w celu zautomatyzowania pracy awaryjnej aplikacji.