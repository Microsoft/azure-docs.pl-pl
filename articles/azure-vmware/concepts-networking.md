---
title: Pojęcia — łączność z siecią
description: Dowiedz się więcej na temat kluczowych aspektów i przypadków użycia sieci i połączeń z platformą Azure VMware.
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3420f6aa61ced7632175f3e12edda9de72639517
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750568"
---
# <a name="azure-vmware-solution-preview-networking-and-interconnectivity-concepts"></a>Rozwiązanie Azure VMware w wersji zapoznawczej — koncepcje dotyczące sieci i międzyłączności

Rozwiązanie VMware dla platformy Azure (Automatyczna synchronizacja) oferuje środowisko chmury prywatnej VMware dostępne dla użytkowników i aplikacji ze środowisk lokalnych i opartych na platformie Azure. Usługi, takie jak Azure ExpressRoute i połączenia sieci VPN, zapewniają łączność. Te usługi wymagają określonych zakresów adresów sieciowych i portów zapory do włączenia usług.  

Podczas wdrażania chmury prywatnej można utworzyć sieci prywatne do zarządzania, aprowizacji i vMotion. Są one używane do uzyskiwania dostępu do Menedżera vCenter i NSX-T oraz do vMotion lub wdrożenia maszyny wirtualnej. Wszystkie sieci prywatne są dostępne z sieci wirtualnej na platformie Azure lub w środowiskach lokalnych. ExpressRoute Global Reach służy do łączenia chmur prywatnych z środowiskami lokalnymi, a to połączenie wymaga sieci wirtualnej z obwodem usługi ExpressRoute w ramach subskrypcji.

Ponadto podczas wdrażania chmury prywatnej dostęp do Internetu i usług platformy Azure są obsługiwane i udostępniane, aby maszyny wirtualne w sieciach produkcyjnych mogły je wykorzystać.  Domyślnie dostęp do Internetu jest wyłączony dla nowych chmur prywatnych i w dowolnym momencie może być włączony lub wyłączony.

Przydatną perspektywą dotyczącą międzyłączności jest uwzględnienie dwóch typów implementacji chmury prywatnej na potrzeby automatycznej synchronizacji:

1. [**Podstawowa platforma Azure — tylko łączność międzyfirmowa**](#azure-virtual-network-interconnectivity) umożliwia zarządzanie chmurą prywatną i korzystanie z niej tylko przy użyciu jednej sieci wirtualnej na platformie Azure. Ta implementacja najlepiej nadaje się do oceny lub implementacji automatycznej synchronizacji, które nie wymagają dostępu ze środowisk lokalnych.

1. Funkcja międzyusługowa [**w chmurze z obsługą chmury prywatnej**](#on-premises-interconnectivity) obejmuje podstawową implementację wyłącznie platformy Azure w celu uwzględnienia wzajemnej łączności między chmurami prywatnymi lokalnymi i automatyczna.
 
W tym artykule omówiono kilka najważniejszych koncepcji, które nawiązują połączenie sieciowe i międzysieciowe, w tym wymagania i ograniczenia. Podajemy również więcej informacji na temat dwóch typów implementacji samołączności chmury prywatnej. Ten artykuł zawiera informacje potrzebne do poprawnego skonfigurowania sieci do pracy z programem automatycznej synchronizacji.

## <a name="avs-private-cloud-use-cases"></a>Automatyczna synchronizacja przypadków użycia chmury prywatnej

W przypadku użycia w przypadku chmur prywatnych w ramach automatycznej synchronizacji należą:
- Nowe obciążenia maszyn wirtualnych VMware w chmurze
- Obciążenie maszyny wirtualnej w chmurze (tylko w środowisku lokalnym do automatycznej synchronizacji)
- Migracja obciążenia maszyny wirtualnej do chmury (tylko w środowisku lokalnym do automatycznej synchronizacji)
- Odzyskiwanie po awarii (Automatyczna synchronizacja z automatyczna i automatyczna synchronizacja)
- Użycie usług platformy Azure

> [!TIP]
> Wszystkie przypadki użycia usługi automatycznej synchronizacji są włączone z łącznością lokalną z chmurą prywatną.

## <a name="azure-virtual-network-interconnectivity"></a>Łączność z siecią wirtualną platformy Azure

W ramach implementacji chmury prywatnej w sieci wirtualnej możesz zarządzać chmurą prywatną rozwiązania Azure VMware, korzystać z obciążeń w chmurze prywatnej i uzyskiwać dostęp do usług platformy Azure za pośrednictwem połączenia ExpressRoute. 

Na poniższym diagramie przedstawiono podstawową komunikację międzysieciową w czasie wdrażania chmury prywatnej. Przedstawia ona logiczne, ExpressRoute sieci między siecią wirtualną na platformie Azure a chmurą prywatną. Międzyłączność spełnia trzy podstawowe przypadki użycia:
* Dostęp przychodzący do programu vCenter Server i Menedżera NSX-T, który jest dostępny z maszyn wirtualnych w ramach subskrypcji platformy Azure, a nie z systemów lokalnych. 
* Dostęp wychodzący z maszyn wirtualnych do usług platformy Azure. 
* Dostęp przychodzący i użycie obciążeń z uruchomioną chmurą prywatną.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Podstawowa Sieć wirtualna do łączności z chmurą prywatną" border="false":::

## <a name="on-premises-interconnectivity"></a>Międzyfirmowa łączność lokalna

W sieci wirtualnej i w środowisku lokalnym do pełnej implementacji chmury prywatnej możesz uzyskać dostęp do chmur prywatnych rozwiązań VMware platformy Azure ze środowiska lokalnego. Ta implementacja stanowi rozszerzenie podstawowej implementacji opisanej w poprzedniej sekcji. Podobnie jak w przypadku podstawowej implementacji, obwód usługi ExpressRoute jest wymagany, ale w tej implementacji jest używany do nawiązywania połączeń ze środowisk lokalnych z chmurą prywatną na platformie Azure. 

Na poniższym diagramie przedstawiono międzyfirmowe łączenie z chmurą prywatną, co umożliwia wykonywanie następujących przypadków użycia:
* Gorąca/zimna vMotion — vCenter
* Dostęp do zarządzania chmurą prywatną w usłudze Azure VMware

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Sieć wirtualna i lokalna pełna łączność z chmurą prywatną" border="false":::

W celu uzyskania pełnej łączności z chmurą prywatną należy włączyć ExpressRoute Global Reach a następnie zażądać klucza autoryzacji i prywatnego identyfikatora komunikacji równorzędnej dla Global Reach w Azure Portal. Klucz autoryzacji i identyfikator komunikacji równorzędnej są używane do ustanawiania Global Reach między obwodem usługi ExpressRoute w ramach subskrypcji i obwodem usługi ExpressRoute dla nowej chmury prywatnej. Po połączeniu te dwa obwody usługi ExpressRoute kierują ruchem sieciowym między środowiskami lokalnymi a chmurą prywatną.  Zapoznaj się z [samouczkiem dotyczącym tworzenia ExpressRoute Global REACH komunikacji równorzędnej z chmurą prywatną](tutorial-expressroute-global-reach-private-cloud.md) w celu uzyskania procedur żądania i używania klucza autoryzacji i identyfikatora komunikacji równorzędnej.

## <a name="next-steps"></a>Następne kroki 

- Dowiedz się więcej o [zagadnieniach i wymaganiach dotyczących łączności sieciowej](tutorial-network-checklist.md). 
- Zapoznaj się z [pojęciami dotyczącymi magazynu w chmurze prywatnej](concepts-storage.md).


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

