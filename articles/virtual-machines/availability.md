---
title: Opcje dostępności
description: Dowiedz się więcej o funkcjach dostępności do uruchamiania maszyn wirtualnych na platformie Azure
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: a0ea9a0871dd57088473999b28553258ff210038
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628768"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Opcje dostępności maszyn wirtualnych na platformie Azure

Ten artykuł zawiera omówienie funkcji dostępności maszyn wirtualnych platformy Azure.

## <a name="high-availability"></a>Wysoka dostępność

Obciążenia są zwykle rozpraszane na różnych maszynach wirtualnych w celu uzyskania dużej przepływności, wydajności i tworzenia nadmiarowości na wypadek, gdyby maszyna wirtualna miała wpływ na aktualizację lub inne zdarzenie. 

Istnieje kilka opcji zapewniających wysoką dostępność systemu Azure. Najpierw porozmawiamy o podstawowych konstrukcjach. 

### <a name="availability-zones"></a>Strefy dostępności

[Strefy dostępności](../availability-zones/az-overview.md) rozszerzają poziom kontroli, aby zachować dostępność aplikacji i danych na maszynach wirtualnych. Strefa dostępności to fizycznie oddzielona strefa w regionie świadczenia usługi Azure. Istnieją trzy Strefy dostępności na obsługiwany region platformy Azure. 

Każda strefa dostępności ma oddzielne źródło zasilania, sieć i chłodzenie. Dzięki zaprojektowaniu rozwiązań do korzystania z replikowanych maszyn wirtualnych w strefach można chronić aplikacje i dane przed utratą centrum danych. W przypadku naruszenia zabezpieczeń jednej strefy zreplikowane aplikacje i dane są natychmiast dostępne w innej strefie. 

![Strefy dostępności](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Dowiedz się więcej o wdrażaniu maszyny wirtualnej z [systemem Windows](./windows/create-powershell-availability-zone.md) lub [Linux](./linux/create-cli-availability-zone.md) w strefie dostępności.


### <a name="fault-domains"></a>Domeny błędów

Domena błędów to logiczne grupowanie odpowiednich elementów sprzętu, które współdzielą źródła zasilania i przełącznik sieciowy, podobnie jak w przypadku regału w lokalnym centrum danych. 

### <a name="update-domains"></a>Domeny aktualizacji

Domena aktualizacji to logiczne grupowanie odpowiednich elementów sprzętu, które mogą być w tym samym czasie poddawane konserwacji lub ponownie uruchamiane. 

To podejście zapewnia, że zawsze działa co najmniej jedno wystąpienie aplikacji, gdy platforma Azure jest poddawana okresowej konserwacji. Kolejność domen aktualizacji, które są ponownie uruchamiane, może nie przebiegać sekwencyjnie podczas konserwacji, ale tylko jedna domena aktualizacji jest uruchamiana ponownie.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines zestawy skalowania 

Zestawy skalowania maszyn wirtualnych platformy Azure umożliwiają tworzenie i Zarządzanie grupą maszyn wirtualnych o zrównoważonym obciążeniu. Liczba wystąpień maszyn wirtualnych może automatycznie zwiększać się lub zmniejszać w reakcji na zapotrzebowanie lub według zdefiniowanego harmonogramu. Zestawy skalowania zapewniają wysoką dostępność aplikacji i umożliwiają centralne zarządzanie, Konfigurowanie i aktualizowanie wielu maszyn wirtualnych. Zalecamy, aby co najmniej dwie maszyny wirtualne zostały utworzone w ramach zestawu skalowania, aby zapewnić wysoką dostępność aplikacji i spełnić warunki [umowy SLA na 99,95%](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Nie ma kosztu dla samego zestawu skalowania, płacisz tylko za każde utworzone wystąpienie maszyny wirtualnej. Jeśli jedna maszyna wirtualna korzysta z [usługi Azure Premium dysków SSD](./disks-types.md#premium-ssd), umowa SLA platformy Azure ma zastosowanie do nieplanowanych zdarzeń konserwacji. Maszyny wirtualne w zestawie skalowania można wdrożyć w wielu domenach aktualizacji i domenach błędów w celu zmaksymalizowania dostępności i odporności na awarie z powodu przestoju centrum danych oraz planowanych lub nieplanowanych zdarzeń konserwacji. Maszyny wirtualne w zestawie skalowania można również wdrożyć w pojedynczej strefie dostępności lub w regionalnie. Opcje wdrożenia strefy dostępności mogą się różnić w zależności od trybu aranżacji.

**Domeny błędów i domeny aktualizacji**

Zestawy skalowania maszyn wirtualnych upraszczają projektowanie pod kątem wysokiej dostępności przez wyrównywanie domen błędów i aktualizowanie domen. W przypadku zestawu skalowania będzie konieczne tylko zdefiniowanie domen błędów. Liczba domen błędów dostępnych dla zestawów skalowania może różnić się w zależności od regionu. Zobacz [Zarządzanie dostępnością maszyn wirtualnych na platformie Azure](./manage-availability.md).


## <a name="availability-sets"></a>Zestawy dostępności
Zestaw dostępności to logiczne grupowanie maszyn wirtualnych, które umożliwia platformie Azure zrozumienie sposobu kompilacji aplikacji w celu zapewnienia nadmiarowości i dostępności. Zalecamy, aby co najmniej dwie maszyny wirtualne zostały utworzone w ramach zestawu dostępności, aby zapewnić wysoką dostępność aplikacji i spełnić warunki [umowy SLA na 99,95%](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Dla samego zestawu dostępności opłaty są naliczane tylko za każde utworzone wystąpienie maszyny wirtualnej. Jeśli jedna maszyna wirtualna korzysta z [usługi Azure Premium dysków SSD](./disks-types.md#premium-ssd), umowa SLA platformy Azure ma zastosowanie do nieplanowanych zdarzeń konserwacji.

W zestawie dostępności maszyny wirtualne są automatycznie dystrybuowane w tych domenach błędów. To podejście ogranicza wpływ potencjalnych awarii sprzętu fizycznego, przestojów sieci lub przerw w dostawie prądu.

Maszyny wirtualne korzystające z usługi [Azure Managed Disks](./faq-for-disks.md) są przydzielane do domen błędów dysków zarządzanych w przypadku korzystania z zarządzanego zestawu dostępności. Dzięki takiemu dopasowaniu wszystkie dyski zarządzane dołączone do maszyny wirtualnej działają w tej samej domenie błędów dysku zarządzanego. 

W zarządzanym zestawie dostępności można tworzyć tylko maszyny wirtualne z użyciem dysków zarządzanych. Liczba domen błędów dysku zarządzanego zależy od regionu — dwie lub trzy domeny błędów dysku zarządzanego na region. Więcej informacji o tych domenach błędów dysków zarządzanych można znaleźć na [maszynach wirtualnych systemu Linux](./manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) lub [maszynach wirtualnych z systemem Windows](./manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

![Zarządzany zestaw dostępności](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Maszyny wirtualne w zestawie dostępności są również automatycznie dystrybuowane w różnych domenach aktualizacji. 

![Zestawy dostępności](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Następne kroki
Możesz teraz rozpocząć korzystanie z tych funkcji dostępności i nadmiarowości podczas kompilowania środowiska platformy Azure. Aby uzyskać informacje o najlepszych rozwiązaniach, zobacz [Azure availability best practices](/azure/architecture/checklist/resiliency-per-service) (Najlepsze rozwiązania dotyczące dostępności platformy Azure).
