---
title: Opcje dostępności dla Virtual Machines platformy Azure
description: Informacje o opcjach dostępności dla uruchomionych maszyn wirtualnych na platformie Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 1ea87d40430dbf3edabd557b80ab1456b49f4605
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102507878"
---
# <a name="availability-options-for-azure-virtual-machines"></a>Opcje dostępności dla Virtual Machines platformy Azure
Ten artykuł zawiera omówienie opcji dostępności dla maszyn wirtualnych platformy Azure.

## <a name="availability-zones"></a>Strefy dostępności

[Strefy dostępności](../availability-zones/az-overview.md?context=/azure/virtual-machines/context/context) rozszerzają poziom kontroli, aby zachować dostępność aplikacji i danych na maszynach wirtualnych. Strefa dostępności to fizycznie oddzielona strefa w regionie świadczenia usługi Azure. Istnieją trzy Strefy dostępności na obsługiwany region platformy Azure. 

Każda strefa dostępności ma oddzielne źródło zasilania, sieć i chłodzenie. Projektując rozwiązania do korzystania z replikowanych maszyn wirtualnych w strefach, możesz chronić aplikacje i dane przed utratą centrum danych. W przypadku naruszenia zabezpieczeń jednej strefy zreplikowane aplikacje i dane są natychmiast dostępne w innej strefie. 

## <a name="availability-sets"></a>Zestawy dostępności
[Zestaw dostępności](availability-set-overview.md) to logiczna Grupa maszyn wirtualnych, dzięki której platforma Azure może zrozumieć, w jaki sposób aplikacja została utworzona w celu zapewnienia nadmiarowości i dostępności. Zalecamy, aby co najmniej dwie maszyny wirtualne zostały utworzone w ramach zestawu dostępności, aby zapewnić wysoką dostępność aplikacji i spełnić warunki [umowy SLA na 99,95%](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Dla samego zestawu dostępności opłaty są naliczane tylko za każde utworzone wystąpienie maszyny wirtualnej.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines zestawy skalowania 

[Zestawy skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/overview.md?context=/azure/virtual-machines/context/context) umożliwiają tworzenie i Zarządzanie grupą maszyn wirtualnych o zrównoważonym obciążeniu. Liczba wystąpień maszyn wirtualnych może automatycznie zwiększać się lub zmniejszać w reakcji na zapotrzebowanie lub według zdefiniowanego harmonogramu. Zestawy skalowania zapewniają wysoką dostępność aplikacji i umożliwiają centralne zarządzanie, Konfigurowanie i aktualizowanie wielu maszyn wirtualnych. Zalecamy, aby co najmniej dwie maszyny wirtualne zostały utworzone w ramach zestawu skalowania, aby zapewnić wysoką dostępność aplikacji i spełnić warunki [umowy SLA na 99,95%](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Nie ma kosztu dla samego zestawu skalowania, płacisz tylko za każde utworzone wystąpienie maszyny wirtualnej.

Maszyny wirtualne w zestawie skalowania można również wdrożyć w pojedynczej strefie dostępności lub w regionalnie. Opcje wdrożenia strefy dostępności mogą się różnić w zależności od [trybu aranżacji](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md?context=/azure/virtual-machines/context/context).

## <a name="load-balancer"></a>Moduł równoważenia obciążenia
Połącz [Azure Load Balancer](../load-balancer/load-balancer-overview.md) ze strefą dostępności lub zestawem dostępności w celu uzyskania większości odporności aplikacji. Usługa Azure Load Balancer dystrybuuje ruch między wieloma maszynami wirtualnymi. W przypadku naszych maszyn wirtualnych w warstwie Standardowa usługa Azure Load Balancer jest uwzględniana. Nie wszystkie warstwy maszyn wirtualnych obejmują usługę Azure Load Balancer. Aby uzyskać więcej informacji na temat równoważenia obciążenia maszyn wirtualnych, zobacz **równoważenie obciążenia maszyn wirtualnych** dla systemu [Linux](linux/tutorial-load-balancer.md) lub [Windows](windows/tutorial-load-balancer.md).


## <a name="azure-storage-redundancy"></a>Nadmiarowość usługi Azure Storage
Usługa Azure Storage zawsze przechowuje wiele kopii danych w taki sposób, aby była chroniona przed planowanymi i nieplanowanymi zdarzeniami, w tym przejściowymi awariami sprzętowymi, siecią lub przestojem, a także ogromnymi klęskami żywiołowymi. Nadmiarowość gwarantuje, że Twoje konto magazynu spełnia jego dostępność i cele trwałości nawet w przypadku awarii.

Decydując o tym, która opcja nadmiarowości jest Najlepsza dla danego scenariusza, należy wziąć pod uwagę kompromisy między niższymi kosztami i wyższą dostępnością. Czynniki pomagające w ustaleniu, która opcja nadmiarowości należy wybrać:
- Jak dane są replikowane w regionie podstawowym
- Bez względu na to, czy dane są replikowane do drugiego regionu, który jest geograficznie odległy do regionu podstawowego, aby chronić przed awariami regionalnymi
- Czy aplikacja wymaga dostępu do odczytu replikowanych danych w regionie pomocniczym, jeśli region podstawowy stał się niedostępny z dowolnego powodu

Aby uzyskać więcej informacji, zobacz [nadmiarowość usługi Azure Storage](../storage/common/storage-redundancy.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Jako organizacja należy wdrożyć strategię ciągłości działania i odzyskiwania po awarii (BCDR), która zapewnia bezpieczeństwo danych, a Twoje aplikacje i obciążenia są dostępne w trybie online, gdy wystąpią planowane i nieplanowane przestoje.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) pomaga zapewnić ciągłość działania, dzięki czemu aplikacje biznesowe i obciążenia działają w trakcie awarii. Usługa Site Recovery replikuje obciążenia uruchomione na maszynach fizycznych i wirtualnych z lokacji podstawowej do lokacji dodatkowej. W przypadku awarii w lokacji głównej ma miejsce przełączenie w tryb failover do lokacji dodatkowej, z poziomu której możliwy jest dostęp do aplikacji. Po ponownym uruchomieniu lokacji głównej możliwy jest do niej powrót po awarii.

Usługa Site Recovery może zarządzać replikacją dla:
- Maszyn wirtualnych platformy Azure replikowanych między regionami świadczenia usługi Azure.
- Lokalne maszyny wirtualne, Azure Stack maszyny wirtualne i serwery fizyczne.

## <a name="next-steps"></a>Następne kroki
- [Tworzenie maszyny wirtualnej w strefie dostępności](/linux/create-cli-availability-zone.md)
- [Tworzenie maszyny wirtualnej w zestawie dostępności](/linux/tutorial-availability.md)
- [Tworzenie zestawu skalowania maszyn wirtualnych](../virtual-machine-scale-sets/quick-create-portal.md)