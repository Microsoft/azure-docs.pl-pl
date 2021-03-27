---
title: Zagadnienia dotyczące sieci — dedykowany moduł HSM platformy Azure | Microsoft Docs
description: Omówienie zagadnień dotyczących sieci dotyczących dedykowanych wdrożeń modułu HSM platformy Azure
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 3370389027805cfb5a68b5b0551d14dc31154804
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611841"
---
# <a name="azure-dedicated-hsm-networking"></a>Dedykowana sieć HSM platformy Azure

Dedykowany moduł HSM platformy Azure wymaga wysoce bezpiecznego środowiska sieciowego. Jest to prawdziwe, czy pochodzi z chmury platformy Azure z powrotem do środowiska IT klienta (lokalnego), przy użyciu aplikacji rozproszonych lub scenariuszy wysokiej dostępności. Usługa Azure Networking zapewnia te informacje, a istnieją cztery różne obszary, które należy rozwiązać.

- Tworzenie urządzeń HSM w ramach Virtual Network (VNet) na platformie Azure
- Łączenie lokalnych z zasobami opartymi na chmurze w celu konfigurowania i zarządzania urządzeniami HSM
- Tworzenie i łączenie sieci wirtualnych w celu połączenia między zasobami aplikacji a urządzeniami modułu HSM
- Łączenie sieci wirtualnych między regionami w celu komunikacji wzajemnej oraz włączanie scenariuszy wysokiej dostępności

## <a name="virtual-network-for-your-dedicated-hsms"></a>Sieć wirtualna dla dedykowanego sprzętowych modułów zabezpieczeń

Dedykowane sprzętowych modułów zabezpieczeń są zintegrowane z Virtual Network i umieszczane w sieci prywatnej przez klientów na platformie Azure. Pozwala to na dostęp do urządzeń z maszyn wirtualnych lub zasobów obliczeniowych w sieci wirtualnej.  
Aby uzyskać więcej informacji na temat integrowania usług platformy Azure z siecią wirtualną i udostępnianych przez nią funkcji, zobacz temat Dokumentacja [usługi Virtual Network for Azure](../virtual-network/virtual-network-for-azure-services.md) .

### <a name="virtual-networks"></a>Sieci wirtualne

Przed zainicjowaniem obsługi dedykowanego urządzenia HSM klienci muszą najpierw utworzyć Virtual Network na platformie Azure lub użyć tego, który już istnieje w subskrypcji klientów. Sieć wirtualna definiuje obwód zabezpieczeń dla dedykowanego urządzenia HSM. Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnych, zobacz [dokumentację sieci wirtualnej](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Podsieci

Podsieci umożliwiają segmentację sieci wirtualnej na oddzielne przestrzenie adresowe, które są używane przez zasoby platformy Azure, które znajdują się w nich. Dedykowane sprzętowych modułów zabezpieczeń są wdrażane w podsieci w sieci wirtualnej. Każde urządzenie dedykowane HSM wdrożone w podsieci klienta otrzyma prywatny adres IP z tej podsieci. Podsieć, w której wdrożono urządzenie HSM, musi być jawnie delegowana do usługi: Microsoft. HardwareSecurityModules/modułów dedicatedhsms. Zapewnia to pewne uprawnienia do usługi HSM do wdrożenia w podsieci. Delegowanie do dedykowanej sprzętowych modułów zabezpieczeń nakłada pewne ograniczenia zasad w podsieci. Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) i trasy User-Defined (UDR) nie są obecnie obsługiwane w delegowanych podsieciach. W związku z tym, gdy podsieć zostanie delegowana do dedykowanej sprzętowych modułów zabezpieczeń, może być używana tylko do wdrażania zasobów modułu HSM. Wdrożenie innych zasobów klienta w podsieci zakończy się niepowodzeniem.


### <a name="expressroute-gateway"></a>Brama ExpressRoute

Wymagana bieżąca architektura jest konfiguracją bramy usługi ER w podsieci klientów, w której należy umieścić urządzenie HSM umożliwiające integrację urządzenia HSM z platformą Azure. Nie można użyć tej bramy usługi ER do łączenia lokalizacji lokalnych z urządzeniami modułu HSM klientów na platformie Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Łączenie lokalnego z platformą Azure

Podczas tworzenia zasobów opartych na chmurze jest typowe wymaganie połączenia prywatnego z lokalnymi zasobami IT. W przypadku dedykowanego modułu HSM, jest on głównie przeznaczony dla oprogramowania klienckiego modułu HSM, aby skonfigurować urządzenia HSM, a także dla działań takich jak kopie zapasowe i ściągania dzienników z sprzętowych modułów zabezpieczeń for Analysis. Kluczowym punktem decyzyjnym poniżej jest charakter połączenia, w którym są dostępne opcje.  Najbardziej elastyczną opcją jest sieć VPN typu lokacja-lokacja, ponieważ może istnieć wiele zasobów lokalnych, które wymagają bezpiecznej komunikacji z zasobami (w tym sprzętowych modułów zabezpieczeń) w chmurze platformy Azure. Będzie to wymagało, aby organizacja klienta mogła korzystać z urządzenia sieci VPN w celu ułatwienia połączenia. Połączenia sieci VPN typu punkt-lokacja można użyć, jeśli istnieje tylko jeden punkt końcowy w środowisku lokalnym, takim jak pojedyncza stacja robocza.
Aby uzyskać więcej informacji na temat opcji łączności, zobacz [VPN Gateway Opcje planowania](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> W tej chwili ExpressRoute nie jest opcją połączenia z zasobami lokalnymi. Należy również zauważyć, że brama ExpressRoute opisana powyżej nie dotyczy połączeń z infrastrukturą lokalną.

### <a name="point-to-site-vpn"></a>Sieć VPN typu punkt-lokacja

Wirtualna sieć prywatna typu punkt-lokacja to najprostsza forma bezpiecznego połączenia z lokalnym punktem końcowym. Może to być istotne, jeśli zamierzasz mieć tylko jedną stację roboczą administracji dla dedykowanych sprzętowych modułów zabezpieczeń opartych na platformie Azure.

### <a name="site-to-site-vpn"></a>Sieć VPN typu lokacja-lokacja

Wirtualna sieć prywatna typu lokacja-lokacja umożliwia bezpieczną komunikację między dedykowanymi sprzętowych modułów zabezpieczeńami opartymi na platformie Azure a lokalnymi. Przyczyną tego jest posiadanie funkcji tworzenia kopii zapasowych dla lokalnego modułu HSM i wymaganie połączenia między nimi w celu uruchomienia kopii zapasowej.

## <a name="connecting-virtual-networks"></a>Łączenie sieci wirtualnych

Typowa architektura wdrażania dedykowanego modułu HSM rozpocznie się z użyciem pojedynczej sieci wirtualnej i odpowiedniej podsieci, w której są tworzone i inicjowane urządzenia HSM. W tym samym regionie mogły istnieć dodatkowe sieci wirtualne i podsieci dla składników aplikacji, które mogłyby korzystać z dedykowanego modułu HSM. Aby umożliwić komunikację między tymi sieciami, używamy Virtual Network komunikacji równorzędnej.

### <a name="virtual-network-peering"></a>Komunikacja równorzędna sieci wirtualnej

Jeśli istnieje wiele sieci wirtualnych w regionie, który musi uzyskać dostęp do wszystkich zasobów, Virtual Network Komunikacja równorzędna może być używana do tworzenia bezpiecznych kanałów komunikacji między nimi.  Wirtualne sieci równorzędne nie tylko zapewniają bezpieczną komunikację, ale zapewniają również połączenia o małym opóźnieniu i dużej przepustowości między zasobami na platformie Azure.

![Komunikacja równorzędna sieci](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Łączenie w regionach platformy Azure

Urządzenia HSM umożliwiają przekierowanie ruchu do alternatywnego modułu HSM za pośrednictwem bibliotek oprogramowania. Przekierowywanie ruchu jest przydatne, jeśli urządzenia nie powiodą się lub dostęp do urządzenia zostanie utracony. Scenariusze awarii poziomu regionalnego można złagodzić przez wdrożenie sprzętowych modułów zabezpieczeń w innych regionach i umożliwienie komunikacji między sieciami wirtualnymi w różnych regionach.

### <a name="cross-region-ha-using-vpn-gateway"></a>Międzyregionowa HA przy użyciu bramy sieci VPN

W przypadku aplikacji rozproszonych globalnie lub dla regionalnych scenariuszy trybu failover o wysokiej dostępności wymagane jest połączenie sieci wirtualnych między regionami. Za pomocą dedykowanego modułu HSM platformy Azure można osiągnąć wysoką dostępność przy użyciu VPN Gateway, która zapewnia bezpieczny tunel między dwiema sieciami wirtualnymi. Aby uzyskać więcej informacji na temat połączeń między sieciami wirtualnymi przy użyciu VPN Gateway, zobacz artykuł [co to jest VPN Gateway?](../vpn-gateway/design.md#V2V)

> [!NOTE]
> Globalna komunikacja równorzędna sieci wirtualnych nie jest dostępna w scenariuszach łączności między regionami z dedykowanymi sprzętowych modułów zabezpieczeńami w tej chwili, a zamiast tego należy użyć bramy sieci VPN. 

![Diagram przedstawia dwa regiony połączone dwoma bramami V P N. Każdy region zawiera równorzędne sieci wirtualne.](media/networking/global-vnet.png)

## <a name="networking-restrictions"></a>Ograniczenia dotyczące sieci
> [!NOTE]
> Ograniczenie dedykowanej usługi HSM przy użyciu delegowania podsieci jest nakładane ograniczenia, które należy wziąć pod uwagę podczas projektowania architektury sieci docelowej dla wdrożenia modułu HSM. Korzystanie z delegowania podsieci oznacza, że sieciowych grup zabezpieczeń, UDR i globalna komunikacja równorzędna sieci wirtualnych nie są obsługiwane dla dedykowanego modułu HSM. Poniższe sekcje zawierają pomoc dotyczącą alternatywnych technik w celu osiągnięcia tego samego lub podobnego wyniku dla tych funkcji. 

Karta sieciowa HSM, która znajduje się w dedykowanej sieci wirtualnej modułu HSM, nie może korzystać z sieciowych grup zabezpieczeń ani tras zdefiniowanych przez użytkownika. Oznacza to, że nie można ustawić domyślnych zasad odmowy z punktu widzenia dedykowanej sieci wirtualnej HSM, a inne segmenty muszą być allowlisted, aby uzyskać dostęp do dedykowanej usługi HSM. 

Dodanie rozwiązania serwera proxy dla sieciowych urządzeń wirtualnych (urządzenie WUS) pozwala również na logiczne umieszczenie zapory urządzenie WUS w koncentratorze tranzytu/obwodów wirtualnych przed kartą sieciową HSM, co zapewnia niezbędną alternatywę dla sieciowych grup zabezpieczeń i UDR.

### <a name="solution-architecture"></a>Architektura rozwiązania
Ten projekt sieci wymaga następujących elementów:
1.  Sieć wirtualna do sieci tranzytowej lub obwodowej obwodowej z warstwą serwera proxy urządzenie WUS. Istnieją idealne co najmniej dwie urządzeń WUS. 
2.  Obwód usługi ExpressRoute z włączoną obsługą prywatnej komunikacji równorzędnej oraz połączenie z siecią wirtualną centrum tranzytowego.
3.  Komunikacja równorzędna sieci wirtualnej między siecią wirtualną centrum tranzytowego i dedykowaną siecią wirtualną HSM.
4.  Zaporę urządzenie WUS lub zaporę platformy Azure można wdrożyć w centrum jako opcję. 
5.  Dodatkowe sieci wirtualnych obciążenia mogą być połączone komunikacji równorzędnej z siecią wirtualną koncentratora. Klient firmy Gemalto może uzyskać dostęp do dedykowanej usługi HSM za pomocą sieci wirtualnej centrum.

![Diagram przedstawia sieć wirtualną z koncentratorem DMZ z warstwą urządzenie WUS proxy dla sieciowej grupy zabezpieczeń i UDR obejścia](media/networking/network-architecture.png)

Ponieważ dodanie rozwiązania proxy urządzenie WUS umożliwia także zaporę urządzenie WUS w koncentratorze tranzytu/DMZ, aby była logicznie umieszczana przed kartą sieciową HSM, zapewniając w ten sposób odpowiednie zasady domyślne odmowy. W naszym przykładzie będziemy używać w tym celu zapory platformy Azure i będą potrzebne następujące elementy:
1. Zapora platformy Azure wdrożona w podsieci "AzureFirewallSubnet" w sieci wirtualnej centrum DMZ
2. Tabela routingu z UDR, która kieruje ruch kierowany do prywatnego punktu końcowego usługi Azure ILB do zapory platformy Azure. Ta tabela routingu zostanie zastosowana do GatewaySubnet, gdzie znajduje się Brama wirtualna ExpressRoute klienta
3. Reguły zabezpieczeń sieci w AzureFirewall umożliwiają przekazywanie danych między zaufanym zakresem źródłowym i prywatnym punktem końcowym usługi Azure IBL na porcie TCP 1792. Ta logika zabezpieczeń spowoduje dodanie niezbędnych zasad "domyślne odmowa" do dedykowanej usługi HSM. W przypadku dedykowanej usługi HSM mogą być dozwolone tylko zakresy zaufanych źródłowych adresów IP. Wszystkie pozostałe zakresy zostaną usunięte.  
4. Tabela routingu z UDR, która kieruje ruch kierowany do Premium w zaporze platformy Azure. Ta tabela routingu zostanie zastosowana do podsieci serwera proxy urządzenie WUS. 
5. SIECIOWEJ grupy zabezpieczeń zastosowana do podsieci proxy urządzenie WUS, aby ufać tylko zakresowi podsieci zapory platformy Azure jako źródła i zezwalać na przekazywanie dalej do adresu IP karty sieciowej HSM za pośrednictwem portu TCP 1792. 

> [!NOTE]
> Ponieważ warstwa serwera proxy urządzenie WUS będzie podłączać adres IP klienta w trakcie przesyłania dalej do karty sieciowej HSM, nie są wymagane żadne UDR między siecią wirtualną HSM i siecią wirtualną koncentratora DMZ.  

### <a name="alternative-to-udrs"></a>Alternatywa dla UDR
Rozwiązanie warstwy urządzenie WUS wymienione powyżej działa jako alternatywa dla UDR. Istnieją pewne ważne punkty do uwagi.
1.  Translacja adresów sieciowych powinna być skonfigurowana w systemie urządzenie WUS, aby umożliwić poprawne kierowanie ruchu zwrotnego.
2. Klienci powinni wyłączyć kontrolę IP klienta w konfiguracji modułu HSM Luna, aby używać VNA do translatora adresów sieciowych. Następujące polecenia servce jako przykład.
```
Disable:
[hsm01] lunash:>ntls ipcheck disable
NTLS client source IP validation disabled
Command Result : 0 (Success)

Show:
[hsm01] lunash:>ntls ipcheck show
NTLS client source IP validation : Disable
Command Result : 0 (Success)
```
3.  Wdróż UDR dla ruchu przychodzącego w warstwie urządzenie WUS. 
4. Zgodnie z projektem, podsieci modułu HSM nie będą inicjować żądania połączenia wychodzącego do warstwy platformy.

### <a name="alternative-to-using-global-vnet-peering"></a>Alternatywa dla korzystania z Wirtualne sieci równorzędne globalnego
Istnieje kilka architektur, których można użyć jako alternatywy dla globalnej komunikacji równorzędnej sieci wirtualnej.
1.  Użyj połączenia między sieciami [wirtualnymi VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) 
2.  Połącz sieć wirtualną modułu HSM z inną siecią wirtualną za pomocą obwodu ER. Działa to najlepiej, gdy wymagana jest bezpośrednia ścieżka lokalna lub Sieć wirtualna sieci VPN. 

#### <a name="hsm-with-direct-express-route-connectivity"></a>Moduł HSM z łącznością Direct Express Route
![Diagram przedstawia moduł HSM z łącznością Direct Express Route](media/networking/expressroute-connectivity.png)

## <a name="next-steps"></a>Następne kroki

- [Często zadawane pytania](faq.md)
- [Możliwości obsługi](supportability.md)
- [Wysoka dostępność](high-availability.md)
- [Zabezpieczenia fizyczne](physical-security.md)
- [Monitorowanie](monitoring.md)
- [Architektura wdrożenia](deployment-architecture.md)
