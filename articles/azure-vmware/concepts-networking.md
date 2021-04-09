---
title: Pojęcia — łączność z siecią
description: Dowiedz się więcej na temat kluczowych aspektów i przypadków użycia sieci i połączeń z platformą Azure VMware.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 4c964151c49e2fea56031dd24bacf4655753a18d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491813"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware rozwiązanie dotyczące sieci i międzyłączności

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Istnieją dwa sposoby łączenia się z chmurą prywatną rozwiązania Azure VMware:

- [**Podstawowa platforma Azure — tylko łączność międzyfirmowa**](#azure-virtual-network-interconnectivity) umożliwia zarządzanie chmurą prywatną i korzystanie z niej tylko przy użyciu jednej sieci wirtualnej na platformie Azure. Ta implementacja najlepiej nadaje się w przypadku ocen lub implementacji rozwiązań VMware platformy Azure, które nie wymagają dostępu ze środowisk lokalnych.

- Funkcja międzyusługowa [**w chmurze z chmurą prywatną pozwala**](#on-premises-interconnectivity) rozszerzyć podstawową implementację wyłącznie platformy Azure w celu uwzględnienia wzajemnej łączności między chmurami prywatnymi i rozwiązaniami VMware platformy Azure.
 
W tym artykule omówiono kluczowe koncepcje, które nawiązywać połączenia sieciowe i międzysieciowe, w tym wymagania i ograniczenia. Ten artykuł zawiera informacje, które należy znać, aby skonfigurować sieć do pracy z rozwiązaniem VMware platformy Azure.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Scenariusze użycia chmury prywatnej dla rozwiązań VMware platformy Azure

Przypadki użycia dla chmur prywatnych rozwiązania Azure VMware obejmują:
- Nowe obciążenia maszyn wirtualnych VMware w chmurze
- Obciążenie pracą maszyny wirtualnej w chmurze (tylko rozwiązanie firmy Microsoft do platformy Azure VMware)
- Migracja obciążenia maszyn wirtualnych do chmury (tylko rozwiązanie do oprogramowania VMware na platformie Azure)
- Odzyskiwanie po awarii (rozwiązanie VMware platformy Azure do rozwiązania VMware platformy Azure lub w środowisku lokalnym do rozwiązania Azure VMware)
- Użycie usług platformy Azure

> [!TIP]
> Wszystkie przypadki użycia usługi Azure VMware Solution są włączane z łącznością lokalną z chmurą prywatną.

## <a name="azure-virtual-network-interconnectivity"></a>Łączność z siecią wirtualną platformy Azure

Możesz łączyć się z siecią wirtualną platformy Azure za pomocą implementacji chmury prywatnej rozwiązania VMware platformy Azure. Możesz zarządzać chmurą prywatną rozwiązania Azure VMware, korzystać z obciążeń w chmurze prywatnej i uzyskiwać dostęp do innych usług platformy Azure.

Na poniższym diagramie przedstawiono podstawową komunikację międzysieciową w czasie wdrażania chmury prywatnej. Pokazuje sieć logiczną między siecią wirtualną na platformie Azure a chmurą prywatną. To połączenie jest nawiązywane za pośrednictwem ExpressRoute zaplecza, który jest częścią usługi rozwiązań VMware platformy Azure. Międzyłączność spełnia następujące podstawowe przypadki użycia:

- Dostęp przychodzący do Menedżera vCenter Server i NSX-T, który jest dostępny z maszyn wirtualnych w ramach subskrypcji platformy Azure.
- Dostęp wychodzący z maszyn wirtualnych w chmurze prywatnej do usług platformy Azure.
- Dostęp przychodzący do obciążeń uruchomionych w chmurze prywatnej.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Podstawowa Sieć wirtualna do łączności z chmurą prywatną" border="false":::

## <a name="on-premises-interconnectivity"></a>Międzyfirmowa łączność lokalna

W pełni połączony scenariusz możesz uzyskać dostęp do rozwiązania VMware platformy Azure z sieci wirtualnych platformy Azure i lokalnie. Ta implementacja stanowi rozszerzenie podstawowej implementacji opisanej w poprzedniej sekcji. Obwód usługi ExpressRoute jest wymagany do nawiązywania połączenia ze swojej lokalizacji lokalnej z chmurą prywatną rozwiązania Azure VMware na platformie Azure.

Na poniższym diagramie przedstawiono międzyfirmowe łączenie z chmurą prywatną, co umożliwia wykonywanie następujących przypadków użycia:

- Hot/zimny serwer vCenter vMotion między środowiskiem lokalnym i rozwiązaniem VMware platformy Azure.
- Rozwiązanie do zarządzania chmurą prywatną w usłudze Azure VMware.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Sieć wirtualna i lokalna pełna łączność z chmurą prywatną" border="false":::

Aby zapewnić pełną łączność z chmurą prywatną, należy włączyć ExpressRoute Global Reach a następnie zażądać klucza autoryzacji i prywatnego identyfikatora komunikacji równorzędnej dla Global Reach w Azure Portal. Klucz autoryzacji i identyfikator komunikacji równorzędnej są używane do ustanawiania Global Reach między obwodem usługi ExpressRoute w ramach subskrypcji i obwodem usługi ExpressRoute w chmurze prywatnej. Po połączeniu te dwa obwody usługi ExpressRoute kierują ruchem sieciowym między środowiskami lokalnymi a chmurą prywatną. Aby uzyskać więcej informacji na temat tych procedur, zobacz Samouczek dotyczący [tworzenia komunikacji równorzędnej ExpressRoute Global REACH w chmurze prywatnej](tutorial-expressroute-global-reach-private-cloud.md).

## <a name="limitations"></a>Ograniczenia
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Następne kroki 

Teraz, gdy została objęta usługą Azure VMware rozwiązanie dotyczące sieci i międzyłączności, warto zapoznać się z tematem:

- [Pojęcia dotyczące magazynu rozwiązań VMware platformy Azure](concepts-storage.md).
- [Pojęcia dotyczące tożsamości rozwiązań VMware platformy Azure](concepts-identity.md).
- [Jak włączyć zasób rozwiązania VMware dla platformy Azure](enable-azure-vmware-solution.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
