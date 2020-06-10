---
title: Pojęcia — łączność z siecią
description: Dowiedz się więcej na temat kluczowych aspektów i przypadków użycia sieci i połączeń z platformą Azure VMware (Automatyczna synchronizacja)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 35d886fe0f6a68e522d7f2cf20b450b5d9afc199
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629209"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware (wersja zapoznawcza) — Omówienie rozwiązań sieciowych i międzyłącznościowych

Łączność sieciową między chmurami prywatnymi (Automatyczna synchronizacja) i środowiskami lokalnymi lub sieciami wirtualnymi na platformie Azure umożliwia dostęp do chmury prywatnej oraz korzystanie z niej. W tym artykule opisano kilka najważniejszych koncepcji dotyczących sieci i międzyłączności, które stanowią podstawę międzyłączności.

Przydatną perspektywą dotyczącą międzyłączności jest rozważenie dwóch typów implementacji chmury prywatnej na potrzeby automatycznej synchronizacji: implementacji z podstawowymi połączeniami i niestandardowymi rozwiązaniami opartymi na platformie Azure z pełnymi połączeniami lokalnymi z chmurą prywatną.

W przypadku użycia w przypadku chmur prywatnych w ramach automatycznej synchronizacji należą:
- nowe obciążenia maszyn wirtualnych VMware w chmurze
- Obciążenie pracą maszyny wirtualnej w chmurze
- Migracja obciążenia maszyny wirtualnej do chmury
- odzyskiwanie po awarii
- Użycie usług platformy Azure

 Wszystkie przypadki użycia usługi automatycznej synchronizacji są włączone z łącznością lokalną z chmurą prywatną. Model podstawowej łączności wzajemnej jest najlepiej dostosowany do oceny lub implementacji automatycznej synchronizacji, które nie wymagają dostępu ze środowisk lokalnych.

W poniższych sekcjach opisano dwa typy synchronizacji prywatnej z chmurą prywatną.  Najbardziej podstawową międzyłącznością jest "łączność sieci wirtualnej platformy Azure"; Umożliwia ona zarządzanie chmurą prywatną i korzystanie z niej tylko przy użyciu jednej sieci wirtualnej na platformie Azure. Wzajemne połączenia opisane w temacie "łączność lokalna" rozszerzają łączność sieci wirtualnej w celu uwzględnienia połączenia między środowiskami lokalnymi i automatyczna synchronizacja chmur prywatnych.

## <a name="azure-virtual-network-interconnectivity"></a>Łączność z siecią wirtualną platformy Azure

Podstawowa łączność międzysieciowa, która jest określana w czasie wdrażania chmury prywatnej, jest pokazana na poniższym diagramie. Przedstawia ona logiczne, ExpressRoute sieci między siecią wirtualną na platformie Azure a chmurą prywatną. Międzyłączność spełnia trzy podstawowe przypadki użycia:
- Dostęp przychodzący do sieci zarządzania, w których znajdują się serwery vCenter Server i NSX-T.
    - Dostępne z maszyn wirtualnych w ramach subskrypcji platformy Azure, a nie z systemów lokalnych.
- Dostęp wychodzący z maszyn wirtualnych do usług platformy Azure.
- Dostęp przychodzący i użycie obciążeń z uruchomioną chmurą prywatną.

![Podstawowa łączność z chmurą wirtualną](./media/concepts/adjacency-overview-drawing-single.png)

Obwód usługi ExpressRoute w ramach scenariusza chmury prywatnej w tej sieci wirtualnej jest ustanawiany podczas tworzenia połączenia z sieci wirtualnej w ramach subskrypcji do obwodu ExpressRoute w chmurze prywatnej. Komunikacja równorzędna używa klucza autoryzacji i identyfikatora obwodu, który jest żądany w Azure Portal. Połączenie ExpressRoute, które jest nawiązywane za pomocą komunikacji równorzędnej, jest połączeniem prywatnym, jeden-do-jednego między chmurą prywatną i siecią wirtualną. Możesz zarządzać chmurą prywatną, korzystać z obciążeń w chmurze prywatnej i uzyskiwać dostęp do usług platformy Azure za pośrednictwem tego połączenia ExpressRoute.

W przypadku wdrażania chmury prywatnej automatycznej synchronizacji wymagana jest jedna/22 przestrzeń adresów sieci prywatnej. Ta przestrzeń adresowa nie powinna nakładać się na przestrzenie adresowe używane w innych sieciach wirtualnych w ramach subskrypcji. W tej przestrzeni adresowej, zarządzanie, aprowizacji i sieci vMotion są obsługiwane automatycznie. Routing jest oparty na protokole BGP i jest automatycznie inicjowany i domyślnie włączony dla każdego wdrożenia chmury prywatnej.

Po wdrożeniu chmury prywatnej są udostępniane adresy IP dla Menedżera vCenter i NSX-T. Aby uzyskać dostęp do tych interfejsów zarządzania, należy utworzyć dodatkowe zasoby w sieci wirtualnej w ramach subskrypcji. Procedury tworzenia tych zasobów i ustanawiania prywatnej komunikacji równorzędnej ExpressRoute są dostępne w samouczkach.

Projektowanie sieci logicznej chmury prywatnej i implementowanie jej przy użyciu NSX-T. Chmura prywatna zawiera wstępnie zainicjowaną NSX-T. Brama warstwy 0 & Brama warstwy 1 została wstępnie zainicjowana. Można utworzyć segment i dołączyć go do istniejącej bramy warstwy 1 lub dołączyć do nowej bramy warstwy 1, którą można zdefiniować. Logiczne składniki sieci NSX zapewniają łączność południowo-zachodnią między obciążeniami, a także zapewniają łączność północ-południe z Internetem i usługami platformy Azure. 

## <a name="on-premises-interconnectivity"></a>Międzyfirmowa łączność lokalna

Możesz również połączyć środowiska lokalne z chmurami prywatnymi automatycznej synchronizacji. Ten typ międzyłączności jest rozszerzeniem podstawowej międzyłączności opisanej w poprzedniej sekcji.

![Sieć wirtualna i lokalna pełna łączność z chmurą prywatną](./media/concepts/adjacency-overview-drawing-double.png)

Aby zapewnić pełną łączność z chmurą prywatną, należy użyć Azure Portal, aby włączyć ExpressRoute Global Reach między obwodem prywatnej chmury ExpressRoute i lokalnym obwodem usługi ExpressRoute. Ta konfiguracja rozszerza podstawową łączność w taki sposób, aby obejmowała dostęp do chmur prywatnych w środowiskach lokalnych.

Obwód usługi Azure Virtual Network ExpressRoute jest wymagany do nawiązania połączenia ze środowisk lokalnych z chmurą prywatną na platformie Azure. Ten obwód usługi ExpressRoute znajduje się w Twojej subskrypcji i nie jest częścią wdrożenia chmury prywatnej. Lokalny obwód ExpressRoute wykracza poza zakres tego dokumentu. Jeśli potrzebujesz łączności lokalnej z chmurą prywatną, możesz użyć jednego z istniejących obwodów usługi ExpressRoute lub kupić jeden z nich w Azure Portal.

Po połączeniu z Global Reach dwa obwody usługi ExpressRoute będą kierować ruchem sieciowym między środowiskami lokalnymi i chmurą prywatną. W powyższym diagramie przedstawiono połączenie międzyfirmowe z chmurą prywatną. Wzajemne połączenia reprezentowane na diagramie umożliwiają następujące przypadki użycia:

- Gorąca/zimna vMotion — vCenter
- Lokalna do automatycznej synchronizacji dostępu do zarządzania chmurą prywatną

Aby zapewnić pełną łączność, w Azure Portal można żądać klucza autoryzacji i prywatnego identyfikatora komunikacji równorzędnej dla Global Reach. Za pomocą klucza i identyfikatora można ustalić Global Reach między obwodem usługi ExpressRoute w ramach subskrypcji i obwodem usługi ExpressRoute dla nowej chmury prywatnej. [Samouczek dotyczący tworzenia chmury prywatnej](tutorial-create-private-cloud.md) zapewnia procedury umożliwiające żądanie i użycie klucza i identyfikatora.

Wymagania dotyczące routingu w ramach rozwiązania wymagają zaplanowania przestrzeni adresów sieci chmury prywatnej, aby uniknąć nakładania się innych sieci wirtualnych i sieci lokalnych. Automatyczna synchronizacja chmur prywatnych wymaga co najmniej `/22` bloku adresów sieciowych CIDR dla podsieci, jak pokazano poniżej. Ta sieć uzupełnia sieci lokalne. Aby można było połączyć się ze środowiskami lokalnymi i sieciami wirtualnymi, musi to być nienakładający się blok adresów sieciowych.

Przykładowy `/22` blok adresów sieciowych CIDR:`10.10.0.0/22`

Podsieci:

| Użycie sieci             | Podsieć | Przykład        |
| ------------------------- | ------ | -------------- |
| Zarządzanie chmurą prywatną            | `/24`    | `10.10.0.0/24`   |
| Sieć vMotion       | `/24`    | `10.10.1.0/24`   |
| Obciążenia maszyn wirtualnych | `/24`   | `10.10.2.0/24`   |
| Komunikacja równorzędna ExpressRoute | `/24`    | `10.10.3.8/30`   |

## <a name="next-steps"></a>Następne kroki 

Następnym krokiem jest zapoznanie się z [pojęciami dotyczącymi magazynu w chmurze prywatnej](concepts-storage.md).

<!-- LINKS - external -->
[enable Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach

<!-- LINKS - internal -->

