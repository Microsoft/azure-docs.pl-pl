---
title: Pojęcia — łączność z siecią
description: Dowiedz się więcej na temat kluczowych aspektów i przypadków użycia sieci i połączeń z platformą Azure VMware (Automatyczna synchronizacja)
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: c0416da9c745ccf92970ff39f623a782d5784983
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87062836"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware (wersja zapoznawcza) — Omówienie rozwiązań sieciowych i międzyłącznościowych

Łączność sieciową między chmurami prywatnymi (Automatyczna synchronizacja) i środowiskami lokalnymi lub sieciami wirtualnymi na platformie Azure umożliwia dostęp do chmury prywatnej oraz korzystanie z niej. W tym artykule omówiono kilka najważniejszych koncepcji, które określają podstawę sieci i międzyłączności.

Przydatną perspektywą dotyczącą międzyłączności jest uwzględnienie dwóch typów implementacji chmury prywatnej na potrzeby automatycznej synchronizacji:

1. [**Podstawowa platforma Azure — tylko łączność międzyfirmowa**](#azure-virtual-network-interconnectivity) umożliwia zarządzanie chmurą prywatną i korzystanie z niej tylko przy użyciu jednej sieci wirtualnej na platformie Azure. Ta implementacja najlepiej nadaje się do oceny lub implementacji automatycznej synchronizacji, które nie wymagają dostępu ze środowisk lokalnych.

1. Funkcja międzyusługowa [**w chmurze z obsługą chmury prywatnej**](#on-premises-interconnectivity) obejmuje podstawową implementację wyłącznie platformy Azure w celu uwzględnienia wzajemnej łączności między chmurami prywatnymi lokalnymi i automatyczna.
 
Więcej informacji na temat wymagań i dwa typy implementacji międzyłączności chmury prywatnej można znaleźć w poniższych sekcjach.

## <a name="avs-private-cloud-use-cases"></a>Automatyczna synchronizacja przypadków użycia chmury prywatnej

W przypadku użycia w przypadku chmur prywatnych w ramach automatycznej synchronizacji należą:
- nowe obciążenia maszyn wirtualnych VMware w chmurze
- Obciążenie maszyny wirtualnej w chmurze (tylko w środowisku lokalnym do automatycznej synchronizacji)
- Migracja obciążenia maszyny wirtualnej do chmury (tylko w środowisku lokalnym do automatycznej synchronizacji)
- odzyskiwanie po awarii (Automatyczna synchronizacja z automatyczna i automatyczna synchronizacja)
- Użycie usług platformy Azure

 Wszystkie przypadki użycia usługi automatycznej synchronizacji są włączone z łącznością lokalną z chmurą prywatną. 

## <a name="virtual-network-and-expressroute-circuit-requirements"></a>Wymagania dotyczące obwodów sieci wirtualnych i ExpressRoute
 
Po utworzeniu połączenia z sieci wirtualnej w ramach subskrypcji obwód usługi ExpressRoute jest ustanawiany za pomocą komunikacji równorzędnej, korzysta z klucza autoryzacji i identyfikatora komunikacji równorzędnej, który jest żądany w Azure Portal. Komunikacja równorzędna to połączenie prywatne, jedno-do-jednego między chmurą prywatną i siecią wirtualną.

> [!NOTE] 
> Obwód usługi ExpressRoute nie jest częścią wdrożenia chmury prywatnej. Lokalny obwód ExpressRoute wykracza poza zakres tego dokumentu. Jeśli potrzebujesz łączności lokalnej z chmurą prywatną, możesz użyć jednego z istniejących obwodów usługi ExpressRoute lub kupić jeden z nich w Azure Portal.

Podczas wdrażania chmury prywatnej otrzymujesz adresy IP dla Menedżera vCenter i NSX-T. Aby uzyskać dostęp do tych interfejsów zarządzania, należy utworzyć dodatkowe zasoby w sieci wirtualnej w ramach subskrypcji. Procedury tworzenia tych zasobów i ustanawiania prywatnej komunikacji równorzędnej ExpressRoute można znaleźć w samouczkach.

Sieci logiczne w chmurze prywatnej są dostarczane ze wstępnie zainicjowaną NSX-T. Bramy warstwy 0 i bramy warstwy 1 są wstępnie udostępniane. Można utworzyć segment i dołączyć go do istniejącej bramy warstwy 1 lub dołączyć do nowej zdefiniowanej bramy warstwy 1. Logiczne składniki sieci NSX zapewniają łączność południowo-zachodnią między obciążeniami, a także zapewniają łączność północ-południe z Internetem i usługami platformy Azure. 

## <a name="routing-and-subnet-requirements"></a>Wymagania dotyczące routingu i podsieci

Routing jest oparty na Border Gateway Protocol (BGP), który jest automatycznie inicjowany i domyślnie włączony dla każdego wdrożenia chmury prywatnej. W przypadku chmur prywatnych w celu przeprowadzania automatycznej synchronizacji wymagane jest zaplanowanie przestrzeni adresowej sieci chmury prywatnej z co najmniej/22 blokami adresów sieciowych CIDR dla podsieci, które przedstawiono w poniższej tabeli. Blok adresów nie powinien nakładać się na bloki adresów używane w innych sieciach wirtualnych, które znajdują się w ramach subskrypcji i sieci lokalnych. W ramach tego bloku adresów, zarządzanie, aprowizacji i sieci vMotion są obsługiwane automatycznie.

Przykładowy `/22` blok adresów sieciowych CIDR:`10.10.0.0/22`

Podsieci:

| Użycie sieci             | Podsieć | Przykład        |
| ------------------------- | ------ | -------------- |
| Zarządzanie chmurą prywatną  | `/24`  | `10.10.0.0/24` |
| Sieć vMotion           | `/24`  | `10.10.1.0/24` |
| Obciążenia maszyn wirtualnych              | `/24`  | `10.10.2.0/24` |
| Komunikacja równorzędna ExpressRoute      | `/24`  | `10.10.3.8/30` |


## <a name="azure-virtual-network-interconnectivity"></a>Łączność z siecią wirtualną platformy Azure

W ramach implementacji chmury prywatnej w sieci wirtualnej możesz zarządzać chmurą prywatną, korzystać z obciążeń w chmurze prywatnej i uzyskiwać dostęp do usług platformy Azure za pośrednictwem połączenia ExpressRoute. 

Na poniższym diagramie przedstawiono podstawową komunikację międzysieciową w czasie wdrażania chmury prywatnej. Przedstawia ona logiczne, ExpressRoute sieci między siecią wirtualną na platformie Azure a chmurą prywatną. Międzyłączność spełnia trzy podstawowe przypadki użycia:
* Dostęp przychodzący do programu vCenter Server i Menedżera NSX-T, który jest dostępny z maszyn wirtualnych w ramach subskrypcji platformy Azure, a nie z systemów lokalnych. 
* Dostęp wychodzący z maszyn wirtualnych do usług platformy Azure. 
* Dostęp przychodzący i użycie obciążeń z uruchomioną chmurą prywatną.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Podstawowa Sieć wirtualna do łączności z chmurą prywatną" border="false":::

## <a name="on-premises-interconnectivity"></a>Międzyfirmowa łączność lokalna

W sieci wirtualnej i w środowisku lokalnym do pełnej implementacji chmury prywatnej możesz uzyskać dostęp do chmur prywatnych w ramach automatycznej synchronizacji ze środowisk lokalnych. Ta implementacja stanowi rozszerzenie podstawowej implementacji opisanej w poprzedniej sekcji. Podobnie jak w przypadku podstawowej implementacji, obwód usługi ExpressRoute jest wymagany, ale w tej implementacji jest używany do nawiązywania połączeń ze środowisk lokalnych z chmurą prywatną na platformie Azure. 

Na poniższym diagramie przedstawiono międzyfirmowe łączenie z chmurą prywatną, co umożliwia wykonywanie następujących przypadków użycia:
* Gorąca/zimna vMotion — vCenter
* Lokalna do automatycznej synchronizacji dostępu do zarządzania chmurą prywatną

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Sieć wirtualna i lokalna pełna łączność z chmurą prywatną" border="false":::

W celu uzyskania pełnej łączności z chmurą prywatną należy włączyć ExpressRoute Global Reach a następnie zażądać klucza autoryzacji i prywatnego identyfikatora komunikacji równorzędnej dla Global Reach w Azure Portal. Klucz autoryzacji i identyfikator komunikacji równorzędnej są używane do ustanawiania Global Reach między obwodem usługi ExpressRoute w ramach subskrypcji i obwodem usługi ExpressRoute dla nowej chmury prywatnej. Po połączeniu te dwa obwody usługi ExpressRoute kierują ruchem sieciowym między środowiskami lokalnymi a chmurą prywatną.  Zapoznaj się z [samouczkiem dotyczącym tworzenia ExpressRoute Global REACH komunikacji równorzędnej z chmurą prywatną](tutorial-expressroute-global-reach-private-cloud.md) w celu uzyskania procedur żądania i używania klucza autoryzacji i identyfikatora komunikacji równorzędnej.


## <a name="next-steps"></a>Następne kroki 

Następnym krokiem jest zapoznanie się z [pojęciami dotyczącymi magazynu w chmurze prywatnej](concepts-storage.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
