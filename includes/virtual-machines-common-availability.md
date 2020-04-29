---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4860dcac666f790fed199536338e50a967113c20
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76749004"
---
Ten artykuł zawiera omówienie funkcji dostępności maszyn wirtualnych platformy Azure.

## <a name="high-availability"></a>Wysoka dostępność

Obciążenia są zwykle rozpraszane na różnych maszynach wirtualnych w celu uzyskania dużej przepływności, wydajności i tworzenia nadmiarowości na wypadek, gdyby maszyna wirtualna miała wpływ na aktualizację lub inne zdarzenie. 

Istnieje kilka opcji zapewniających wysoką dostępność systemu Azure. Najpierw porozmawiamy o podstawowych konstrukcjach. 

### <a name="availability-zones"></a>Strefy dostępności

[Strefy dostępności](../articles/availability-zones/az-overview.md) rozszerzają poziom kontroli, aby zachować dostępność aplikacji i danych na maszynach wirtualnych. Strefa dostępności to fizycznie oddzielona strefa w regionie świadczenia usługi Azure. Istnieją trzy Strefy dostępności na obsługiwany region platformy Azure. 

Każda strefa dostępności ma oddzielne źródło zasilania, sieć i chłodzenie. Dzięki zaprojektowaniu rozwiązań do korzystania z replikowanych maszyn wirtualnych w strefach można chronić aplikacje i dane przed utratą centrum danych. W przypadku naruszenia zabezpieczeń jednej strefy zreplikowane aplikacje i dane są natychmiast dostępne w innej strefie. 

![Strefy dostępności](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Dowiedz się więcej o wdrażaniu maszyny wirtualnej z [systemem Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) lub [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) w strefie dostępności.


### <a name="fault-domains"></a>Domeny błędów

Domena błędów to logiczne grupowanie odpowiednich elementów sprzętu, które współdzielą źródła zasilania i przełącznik sieciowy, podobnie jak w przypadku regału w lokalnym centrum danych. 

### <a name="update-domains"></a>Domeny aktualizacji

Domena aktualizacji to logiczne grupowanie odpowiednich elementów sprzętu, które mogą być w tym samym czasie poddawane konserwacji lub ponownie uruchamiane. 

To podejście zapewnia, że zawsze działa co najmniej jedno wystąpienie aplikacji, gdy platforma Azure jest poddawana okresowej konserwacji. Kolejność domen aktualizacji, które są ponownie uruchamiane, może nie przebiegać sekwencyjnie podczas konserwacji, ale tylko jedna domena aktualizacji jest uruchamiana ponownie.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines zestawy skalowania 

Zestawy skalowania maszyn wirtualnych platformy Azure umożliwiają tworzenie i Zarządzanie grupą maszyn wirtualnych o zrównoważonym obciążeniu. Liczba wystąpień maszyn wirtualnych może automatycznie zwiększać lub zmniejszać się w reakcji na zapotrzebowanie lub zdefiniowany harmonogram. Zestawy skalowania zapewniają wysoką dostępność aplikacji i umożliwiają centralne zarządzanie, Konfigurowanie i aktualizowanie wielu maszyn wirtualnych. Zalecamy, aby co najmniej dwie maszyny wirtualne zostały utworzone w ramach zestawu skalowania, aby zapewnić wysoką dostępność aplikacji i spełnić warunki [umowy SLA na 99,95%](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Nie ma kosztu dla samego zestawu skalowania, płacisz tylko za każde utworzone wystąpienie maszyny wirtualnej. Jeśli jedna maszyna wirtualna korzysta z [usługi Azure Premium dysków SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd), umowa SLA platformy Azure ma zastosowanie do nieplanowanych zdarzeń konserwacji. Maszyny wirtualne w zestawie skalowania można wdrożyć w wielu domenach aktualizacji i domenach błędów w celu zmaksymalizowania dostępności i odporności na awarie z powodu przestoju centrum danych oraz planowanych lub nieplanowanych zdarzeń konserwacji. Maszyny wirtualne w zestawie skalowania można również wdrożyć w pojedynczej strefie dostępności lub w regionalnie. Opcje wdrożenia strefy dostępności mogą się różnić w zależności od trybu aranżacji.

### <a name="preview-orchestration-mode-preview"></a>Wersja zapoznawcza: tryb aranżacji — wersja zapoznawcza
Zestawy skalowania maszyn wirtualnych umożliwiają określenie trybu aranżacji.  Za pomocą trybu aranżacji zestawu skalowania maszyn wirtualnych (wersja zapoznawcza) można teraz wybrać, czy zestaw skalowania ma organizować maszyny wirtualne, które są tworzone jawnie poza modelem konfiguracji zestawu skalowania, lub wystąpieniami maszyn wirtualnych utworzonymi niejawnie na podstawie modelu konfiguracji. Wybierz tryb aranżacji, który model aranżacji maszyny wirtualnej pozwala grupować jawnie zdefiniowane Virtual Machines razem w regionie lub w strefie dostępności. Maszyny wirtualne wdrożone w strefie dostępności zapewniają izolację strefową do maszyn wirtualnych, są one powiązane ze granicą strefy dostępności i nie są objęte żadnymi błędami, które mogą wystąpić w innych strefach dostępności w regionie. 

|   | "orchestrationMode": "VM" (VirtualMachine)| "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| Model konfiguracji maszyny wirtualnej| Brak. VirtualMachineProfile jest niezdefiniowany w modelu zestawu skalowania. | Wymagany. VirtualMachineProfile jest wypełniany w modelu zestawu skalowania. |
| Dodawanie nowej maszyny wirtualnej do zestawu skalowania| Maszyny wirtualne są jawnie dodawane do zestawu skalowania podczas tworzenia maszyny wirtualnej. | Maszyny wirtualne są niejawnie tworzone i dodawane do zestawu skalowania na podstawie modelu konfiguracji maszyny wirtualnej, liczby wystąpień i reguł skalowania automatycznego. |
| Strefy dostępności| Obsługuje wdrożenie regionalne lub maszyny wirtualne w jednej strefie dostępności| Obsługuje wdrożenie regionalne lub wiele Strefy dostępności; Może definiować strategię równoważenia strefy |
| Domeny błędów| Można zdefiniować liczbę domen błędów. 2 lub 3 w oparciu o obsługę regionalną i 5 dla strefy dostępności. Domena błędów przypisanej maszyny wirtualnej będzie trwała z cyklem życia maszyny wirtualnej, w tym cofnięciem alokacji i ponownym uruchomieniu. | Można zdefiniować 1, 2 lub 3 domeny błędów dla wdrożeń nienależących do stref i 5 dla wdrożeń stref dostępności. Przypisana domena błędów maszyny wirtualnej nie utrzymuje cyklu życia maszyny wirtualnej, a maszyny wirtualne są przypisane do domeny błędów w czasie przydziału. |
| Domeny aktualizacji| Nie dotyczy. Domeny aktualizacji są automatycznie mapowane na domeny błędów| Nie dotyczy. Domeny aktualizacji są automatycznie mapowane na domeny błędów |

**Domeny błędów i domeny aktualizacji**

Zestawy skalowania maszyn wirtualnych upraszczają projektowanie pod kątem wysokiej dostępności przez wyrównywanie domen błędów i aktualizowanie domen. W przypadku zestawu skalowania będzie konieczne tylko zdefiniowanie domen błędów. Liczba domen błędów dostępnych dla zestawów skalowania może różnić się w zależności od regionu. Zobacz [Zarządzanie dostępnością maszyn wirtualnych na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).


## <a name="availability-sets"></a>Zestawy dostępności
Zestaw dostępności to logiczna Grupa maszyn wirtualnych w centrum danych, która pozwala platformie Azure zrozumieć, w jaki sposób aplikacja została skompilowana w celu zapewnienia nadmiarowości i dostępności. Zalecamy, aby co najmniej dwie maszyny wirtualne zostały utworzone w ramach zestawu dostępności, aby zapewnić wysoką dostępność aplikacji i spełnić warunki [umowy SLA na 99,95%](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Dla samego zestawu dostępności opłaty są naliczane tylko za każde utworzone wystąpienie maszyny wirtualnej. Jeśli jedna maszyna wirtualna korzysta z [usługi Azure Premium dysków SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd), umowa SLA platformy Azure ma zastosowanie do nieplanowanych zdarzeń konserwacji.

W zestawie dostępności maszyny wirtualne są automatycznie dystrybuowane w tych domenach błędów. To podejście ogranicza wpływ potencjalnych awarii sprzętu fizycznego, przestojów sieci lub przerw w dostawie prądu.

Maszyny wirtualne korzystające z usługi [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) są przydzielane do domen błędów dysków zarządzanych w przypadku korzystania z zarządzanego zestawu dostępności. Dzięki takiemu dopasowaniu wszystkie dyski zarządzane dołączone do maszyny wirtualnej działają w tej samej domenie błędów dysku zarządzanego. 

W zarządzanym zestawie dostępności można tworzyć tylko maszyny wirtualne z użyciem dysków zarządzanych. Liczba domen błędów dysku zarządzanego zależy od regionu — dwie lub trzy domeny błędów dysku zarządzanego na region. Więcej informacji o tych domenach błędów dysków zarządzanych można znaleźć na [maszynach wirtualnych systemu Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) lub [maszynach wirtualnych z systemem Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Zarządzany zestaw dostępności](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Maszyny wirtualne w zestawie dostępności są również automatycznie dystrybuowane w różnych domenach aktualizacji. 

![Zestawy dostępności](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Następne kroki
Możesz teraz rozpocząć korzystanie z tych funkcji dostępności i nadmiarowości podczas kompilowania środowiska platformy Azure. Aby uzyskać informacje o najlepszych rozwiązaniach, zobacz [Azure availability best practices](/azure/architecture/checklist/resiliency-per-service) (Najlepsze rozwiązania dotyczące dostępności platformy Azure).

