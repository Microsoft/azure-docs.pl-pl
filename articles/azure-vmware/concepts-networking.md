---
title: Pojęcia — łączność sieciowa
description: Dowiedz się więcej na temat kluczowych aspektów i przypadków użycia sieci i połączeń w Azure VMware Solution.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a6c86c0a9eb8e07a91a094ddf5d6fb77b0eddf67
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871691"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware Solution dotyczące sieci i połączeń

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Istnieją dwa sposoby łączenia w chmurze Azure VMware Solution prywatnej:

- [**Podstawowa łączność tylko na platformie**](#azure-virtual-network-interconnectivity) Azure umożliwia zarządzanie chmurą prywatną i używanie jej tylko z jedną siecią wirtualną na platformie Azure. Ta implementacja jest najbardziej odpowiednia dla Azure VMware Solution lub implementacji, które nie wymagają dostępu ze środowisk lokalnych.

- [**Pełna łączność między**](#on-premises-interconnectivity) środowiskiem lokalnym i chmurą prywatną rozszerza podstawową implementację tylko na platformie Azure, tak aby uwzględniała łączność między chmurami lokalnymi i Azure VMware Solution prywatnymi.
 
W tym artykule ojmiemy kluczowe koncepcje, które ustanowią łączność i sieć, w tym wymagania i ograniczenia. Ten artykuł zawiera informacje, które należy znać, aby skonfigurować sieć do pracy z Azure VMware Solution.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Azure VMware Solution przypadków użycia chmury prywatnej

Przypadki użycia chmur prywatnych Azure VMware Solution obejmują:
- Nowe obciążenia maszyn wirtualnych VMware w chmurze
- Obciążenie maszyny wirtualnej przesłonięciem do chmury (tylko w środowisku lokalnym Azure VMware Solution)
- Migracja obciążenia maszyny wirtualnej do chmury (tylko lokalnie do Azure VMware Solution)
- Odzyskiwanie po awarii (Azure VMware Solution do Azure VMware Solution lub lokalne do Azure VMware Solution)
- Zużycie usług platformy Azure

> [!TIP]
> Wszystkie przypadki użycia usługi Azure VMware Solution są włączone z łącznością chmury lokalnej z chmurą prywatną.

## <a name="azure-virtual-network-interconnectivity"></a>Łączność między sieciami wirtualnymi platformy Azure

Sieć wirtualną platformy Azure można połączyć z implementacją chmury Azure VMware Solution prywatnej. Możesz zarządzać chmurą Azure VMware Solution prywatnej, korzystać z obciążeń w chmurze prywatnej i uzyskać dostęp do innych usług platformy Azure.

Na poniższym diagramie przedstawiono podstawową łączność sieciową ustanowioną w czasie wdrażania chmury prywatnej. Pokazuje on sieć logiczną między siecią wirtualną na platformie Azure a chmurą prywatną. Ta łączność jest nawiązana za pośrednictwem usługi ExpressRoute zaplecza, która jest Azure VMware Solution usługi. Łączność spełnia następujące podstawowe przypadki użycia:

- Dostęp przychodzący do serwera vCenter i menedżera NSX-T, który jest dostępny z maszyn wirtualnych w ramach subskrypcji platformy Azure.
- Dostęp wychodzący z maszyn wirtualnych w chmurze prywatnej do usług platformy Azure.
- Dostęp przychodzący dla obciążeń uruchomionych w chmurze prywatnej.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Podstawowa łączność między siecią wirtualną a chmurą prywatną" border="false":::

## <a name="on-premises-interconnectivity"></a>Łączność lokalna

W scenariuszu, który jest w pełni połączony, dostęp do Azure VMware Solution z sieci wirtualnych platformy Azure i lokalnych. Ta implementacja jest rozszerzeniem podstawowej implementacji opisanej w poprzedniej sekcji. Obwód usługi ExpressRoute jest wymagany do nawiązania połączenia ze środowisk lokalnych do chmury Azure VMware Solution prywatnej na platformie Azure.

Na poniższym diagramie przedstawiono łączność między środowiskami lokalnymi i chmurą prywatną, co umożliwia korzystanie z następujących przypadków użycia:

- Gorąca/zimna wersja vCenter vMotion między środowiskiem lokalnym i Azure VMware Solution.
- Lokalne do zarządzania Azure VMware Solution chmury prywatnej.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Sieć wirtualna i lokalna pełna łączność z chmurą prywatną" border="false":::

Aby uzyskać pełną łączność z chmurą prywatną, należy włączyć usługę ExpressRoute Global Reach, a następnie zażądać klucza autoryzacji i identyfikatora prywatnej komunikacji równorzędnej dla Global Reach w Azure Portal. Klucz autoryzacji i identyfikator komunikacji równorzędnej są używane do ustanawiania Global Reach między obwodem usługi ExpressRoute w subskrypcji a obwodem usługi ExpressRoute dla chmury prywatnej. Po nawiązanych połączeniach dwa obwody usługi ExpressRoute przekierują ruch sieciowy między środowiskami lokalnymi do chmury prywatnej. Aby uzyskać więcej informacji na temat procedur, zobacz samouczek dotyczący tworzenia usługi ExpressRoute Global Reach komunikacji równorzędnej [z chmurą prywatną.](tutorial-expressroute-global-reach-private-cloud.md)

## <a name="limitations"></a>Ograniczenia
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Następne kroki 

Teraz, gdy zostały już Azure VMware Solution pojęcia dotyczące sieci i połączeń, możesz dowiedzieć się więcej na temat:

- [Azure VMware Solution pojęcia dotyczące magazynu](concepts-storage.md)
- [Azure VMware Solution pojęcia dotyczące tożsamości](concepts-identity.md)
- [Jak włączyć Azure VMware Solution zasobów](enable-azure-vmware-solution.md)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
