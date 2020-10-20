---
title: Łączenie platformy Azure z chmurami publicznymi | Microsoft Docs
description: Opisz różne sposoby łączenia z platformą Azure z innymi chmurami publicznymi
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: duau
ms.openlocfilehash: fb4c34345ab902e243f22eae06fd5ea174fd2992
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202195"
---
# <a name="connecting-azure-with-public-clouds"></a>Łączenie platformy Azure z chmurami publicznymi

Wiele przedsiębiorstw realizuje strategię wielu chmur ze względu na cele biznesowe i techniczne. Dotyczy to kosztów, elastyczności, dostępności funkcji, zapewnienia nadmiarowości, suwerenności danych itp. Ta strategia pomaga im wykorzystać najlepsze z obu chmur. 

Takie podejście stanowi również wyzwania dla przedsiębiorstwa w zakresie architektury sieci i aplikacji. Niektóre z tych wyzwań to opóźnienia i przepływność danych. Aby rozwiązać te problemy, klienci mogą łączyć się z wieloma chmurami bezpośrednio. Niektórzy dostawcy usług oferują rozwiązanie umożliwiające połączenie wielu dostawców chmury dla klientów. W innych przypadkach klient może wdrożyć własny router, aby połączyć się z wieloma chmurami publicznymi.
## <a name="connectivity-via-expressroute"></a>Łączność za pośrednictwem ExpressRoute
ExpressRoute umożliwia klientom rozszeranie sieci lokalnych do chmury firmy Microsoft przez połączenie prywatne przez dostawcę połączenia. Dzięki ExpressRoute klienci mogą nawiązywać połączenia z usługami w chmurze firmy Microsoft.

Istnieją trzy sposoby nawiązywania połączenia za pośrednictwem ExpressRoute.

1. Dostawca Layer3
2. Dostawca layer2
3. Połączenie bezpośrednie

### <a name="layer3-provider"></a>Dostawca Layer3

Dostawcy Layer3 są często nazywani dostawcami protokołu IP sieci VPN lub MPLS. Klienci wykorzystują tych dostawców do łączności MultiPoint między centrami danych, gałęziami i chmurą. Klienci łączą się z dostawcą L3 za pośrednictwem protokołu BGP lub za pośrednictwem statycznej trasy domyślnej. Dostawca usług anonsuje trasy między lokacjami klientów, centrami danych i chmurą publiczną. 
 
Podczas nawiązywania połączenia za pośrednictwem dostawcy usługi Layer3 firma Microsoft będzie ogłaszać trasy sieci wirtualnej klienta dla dostawcy usług za pośrednictwem protokołu BGP. Dostawca może mieć dwie różne implementacje.

![Diagram przedstawiający dostawcę Layer3.](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

Dostawca może obsłużyć każdego dostawcę chmury w osobnym VRF, jeśli ruch ze wszystkich dostawców chmury zostanie osiągnięty na routerze klienta. Jeśli klient korzysta z protokołu BGP z dostawcą usług, trasy te zostaną domyślnie ponownie ogłoszone dla innych dostawców chmury. 

Jeśli dostawca usług jest wyładunkowy dla wszystkich dostawców chmury w tym samym VRF, trasy będą anonsowane innym dostawcom chmury bezpośrednio od dostawcy usług. Jest to zakładane przez standardowe operacje BGP, w przypadku których trasy eBGP są domyślnie anonsowane do innych sąsiadów eBGP.

Każda chmura publiczna ma inny limit prefiksów, dlatego podczas dystrybucji dostawcy usług trasy należy zachować ostrożność w dystrybucji tras.

### <a name="layer2-provider-and-direct-connection"></a>Dostawca layer2 i połączenie bezpośrednie

Chociaż łączność fizyczna w obu modelach jest różna, ale w Layer3 BGP jest ustanawiana bezpośrednio między MSEE i routerem klienta. Klient ExpressRoute Direct łączy się bezpośrednio z MSEE. W przypadku layer2 Usługa Service Provider rozszerza sieć VLAN z lokalizacji klienta do chmury. Klienci uruchamiają protokół BGP w oparciu o sieć layer2, aby połączyć ich kontrolery domen z chmurą.
![Diagram przedstawiający dostawcę layer2 i bezpośrednie połączenie.](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
W obu przypadkach klient będzie miał połączenia typu punkt-punkt z każdą chmurą publiczną. Klient nawiąże połączenie BGP z każdą chmurą publiczną. Trasy odebrane przez jednego dostawcę chmury będą domyślnie anonsowane innemu dostawcy chmury. Każdy dostawca chmury ma inny limit prefiksów, dlatego w przypadku anonsowania trasy klient powinien wziąć pod uwagę te limity. Klient może używać zwykłych pokręteł BGP z firmą Microsoft podczas anonsowania tras z innych chmur publicznych.

## <a name="direct-connection-with-expressroute"></a>Bezpośrednie połączenie z ExpressRoute

Klienci mogą wybrać opcję połączenia usługi ExpressRoute bezpośrednio z ofertą bezpośredniej łączności dostawcy chmury. Dwaj dostawcy chmury będą z powrotem połączeni z powrotem, a protokół BGP zostanie nawiązany bezpośrednio między routerami. Ten typ połączenia jest już dostępny w oprogramowaniu Oracle.

## <a name="site-to-site-vpn"></a>Sieć VPN typu lokacja-lokacja

Klienci mogą korzystać z Internetu, aby połączyć swoje wystąpienia na platformie Azure z innymi chmurami publicznymi. Prawie wszyscy dostawcy chmury oferują możliwości sieci VPN typu lokacja-lokacja. Mogą jednak wystąpić niezgodności z powodu braku niektórych wariantów. Na przykład niektórzy dostawcy chmury obsługują tylko protokołu IKEv1, więc w chmurze jest wymagany punkt końcowy zakończenia sieci VPN. Dla tych dostawców chmury obsługujących protokół IKEv2 można nawiązać bezpośredni tunel między bramami sieci VPN w obu dostawców chmury.

Połączenia sieci VPN typu lokacja-lokacja nie są uznawane za rozwiązanie o wysokiej przepływności i małych opóźnieniach. Może jednak służyć jako kopia zapasowa do łączności fizycznej.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [ExpressRoute często zadawanych][ER-FAQ] pytań, aby uzyskać odpowiedzi na pytania dotyczące ExpressRoute i sieci wirtualnej.

Zobacz [Konfigurowanie bezpośredniego połączenia między platformą Azure i Oracle Cloud][ER-OCI] na potrzeby łączności między platformą Azure i firmą Oracle

<!--Link References-->
[ER-FAQ]: ./expressroute-faqs.md
[ER-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md