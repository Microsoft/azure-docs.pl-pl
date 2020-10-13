---
title: 'Azure ExpressRoute: Azure dla inżynierów sieci'
description: Na tej stronie wyjaśniono tradycyjne inżynierów sieci, w jaki sposób działają sieci na platformie Azure.
documentationcenter: na
services: networking
author: osamaz
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: osamaz
ms.openlocfilehash: 4f513da4e7883cd273098039c9c4a4645d849f0f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85516264"
---
# <a name="azure-for-network-engineers"></a>Platforma Azure dla inżynierów sieci
Jako konwencjonalny inżynier sieci korzystasz z zasobów fizycznych, takich jak routery, przełączniki, kable i zapory, aby utworzyć infrastrukturę. W warstwie logicznej skonfigurowano wirtualną sieć LAN (VLAN), łączenie protokołu drzewa (STP), protokoły routingu (RIP, OSPF, BGP). Zarządzasz siecią przy użyciu narzędzi do zarządzania i interfejsu wiersza polecenia. Sieci w chmurze różnią się, gdzie punkty końcowe sieci są logiczne, a korzystanie z protokołów routingu jest minimalne. Do konfigurowania zasobów i zarządzania nimi na platformie Azure można używać interfejsów API Azure Resource Manager, interfejsu wiersza polecenia platformy Azure i programu PowerShell. W chmurze rozpocznie się podróż do sieci, opisując podstawowe dzierżawy sieci platformy Azure. 
## <a name="virtual-network"></a>Sieć wirtualna
Podczas projektowania sieci z dołu należy zebrać podstawowe informacje. Te informacje mogą być liczbami hostów, urządzeń sieciowych, liczby podsieci, routingu między podsieciami, domenami izolacji, takimi jak sieci VLAN. Te informacje ułatwiają zmianę rozmiarów urządzeń sieciowych i zabezpieczeń, a także tworzenie architektury w celu obsługi aplikacji i usług.

Planując wdrożenie aplikacji i usług na platformie Azure, rozpocznie się tworzenie granicy logicznej na platformie Azure, która jest nazywana siecią wirtualną. Ta sieć wirtualna jest zbliżone do granicy sieci fizycznej. Ponieważ jest to sieć wirtualna, nie potrzebujesz fizycznego sprzętu, ale nadal trzeba zaplanować jednostki logiczne, takie jak adresy IP, podsieci IP, Routing i zasady.

Podczas tworzenia sieci wirtualnej na platformie Azure jest ona wstępnie skonfigurowana z zakresem adresów IP (10.0.0.0/16). Ten zakres nie jest ustalony, można zdefiniować własny zakres adresów IP. Można zdefiniować zarówno zakres adresów IPv4, jak i IPv6. Zakresy adresów IP zdefiniowane dla sieci wirtualnej nie są anonsowane w Internecie. Można utworzyć wiele podsieci z zakresu adresów IP. Te podsieci będą używane do przypisywania adresów IP do interfejsów sieci wirtualnej (vNICs). Pierwsze cztery adresy IP z każdej podsieci są zarezerwowane i nie można ich używać do przydzielania adresów IP. Brak koncepcji sieci VLAN w chmurze publicznej. Można jednak utworzyć izolację w obrębie sieci wirtualnej na podstawie zdefiniowanych podsieci.

Można utworzyć jedną dużą podsieć obejmującą całą przestrzeń adresową sieci wirtualnej lub wybrać opcję utworzenia wielu podsieci. Jeśli jednak używasz bramy sieci wirtualnej, platforma Azure wymaga utworzenia podsieci o nazwie "podsieć bramy". Platforma Azure będzie używać tej podsieci do przypisywania adresów IP do bram sieci wirtualnej. 

## <a name="ip-allocation"></a>Alokacja adresów IP

Przypisanie adresu IP do hosta polega na tym, że adres IP jest przypisywany do karty interfejsu sieciowego (NIC). Do karty sieciowej na platformie Azure można przypisać dwa typy adresów IP:

- Publiczne adresy IP — używane do komunikacji przychodzącej i wychodzącej (bez translatora adresów sieciowych (NAT)) z Internetem i innymi zasobami platformy Azure, które nie są połączone z siecią wirtualną. Przypisanie publicznego adresu IP do karty sieciowej jest opcjonalne. Publiczne adresy IP należą do przestrzeni adresów IP firmy Microsoft.
- Prywatne adresy IP — używane do komunikacji w ramach sieci wirtualnej, sieci lokalnej i Internetu (przy użyciu translatora adresów sieciowych). Przestrzeń adresów IP, która została zdefiniowana w sieci wirtualnej, jest uznawana za prywatną nawet w przypadku skonfigurowania przestrzeni publicznych adresów IP. Firma Microsoft nie anonsuje tego miejsca w Internecie. Do maszyny wirtualnej należy przypisać co najmniej jeden prywatny adres IP.

Podobnie jak w przypadku hostów fizycznych lub urządzeń, istnieją dwa sposoby przydzielenia adresu IP do zasobu — dynamiczny lub statyczny. Na platformie Azure domyślna metoda alokacji jest dynamiczna, gdzie adres IP jest przydzielany podczas tworzenia maszyny wirtualnej lub uruchamiania zatrzymanej maszyny wirtualnej. Adres IP jest zwalniany, gdy zatrzymujesz lub usuwasz maszynę wirtualną. Aby mieć pewność, że adres IP maszyny wirtualnej pozostaje taki sam, ustaw metodę alokacji jawnie jako statyczną. W takim przypadku adres IP jest przypisywany natychmiast. Jest on zwalniany tylko wtedy, gdy usuniesz maszynę wirtualną lub zmienisz jej metodę alokacji na dynamiczną. 

Prywatne adresy IP są przydzieleni z podsieci zdefiniowanych w ramach sieci wirtualnej. Dla maszyny wirtualnej należy wybrać podsieć do alokacji adresów IP. Jeśli maszyna wirtualna zawiera wiele kart sieciowych, można wybrać inną podsieć dla każdej karty sieciowej.

## <a name="routing"></a>Routing
Podczas tworzenia sieci wirtualnej platforma Azure tworzy tabelę routingu dla sieci. Ta tabela routingu zawiera następujące typy tras.
- Trasy systemowe
- Trasy domyślne podsieci
- Trasy z innych sieci wirtualnych
- Trasy protokołu BGP
- Trasy punktu końcowego usługi
- Trasy zdefiniowane przez użytkownika (UDR)

Po utworzeniu sieci wirtualnej po raz pierwszy bez definiowania podsieci, platforma Azure tworzy wpisy routingu w tabeli routingu. Trasy te są nazywane trasami systemowymi. Trasy systemowe są zdefiniowane w tej lokalizacji. Nie można modyfikować tych tras. Można jednak przesłonić trasy systemów przez skonfigurowanie UDR.

Podczas tworzenia jednej lub wielu podsieci w sieci wirtualnej, platforma Azure tworzy domyślne wpisy w tabeli routingu, aby umożliwić komunikację między tymi podsieciami w ramach sieci wirtualnej. Trasy te można modyfikować za pomocą tras statycznych, które są trasami zdefiniowanymi przez użytkownika (UDR) na platformie Azure.

Podczas tworzenia komunikacji równorzędnej sieci wirtualnej między dwiema sieciami wirtualnymi dodawana jest trasa dla każdego zakresu adresów w obrębie przestrzeni adresowej w każdej sieci wirtualnej, dla której jest tworzona komunikacja równorzędna.

Jeśli Brama sieci lokalnej wymienia trasy protokołu BGP (Border Gateway Protocol) z bramą sieci wirtualnej platformy Azure, trasa jest dodawana do każdej trasy propagowanej z bramy sieci lokalnej. Trasy te są wyświetlane w tabeli routingu jako trasy protokołu BGP.

Publiczne adresy IP dla niektórych usług są dodawane do tabeli tras przez platformę Azure po włączeniu punktu końcowego usługi do usługi. Punkty końcowe usługi są włączone dla poszczególnych podsieci w sieci wirtualnej. Po włączeniu punktu końcowego usługi trasa jest dodawana tylko do tabeli tras dla podsieci, która należy do tej usługi. Platforma Azure zarządza adresami w tabeli tras automatycznie po zmianie adresów.

Trasy zdefiniowane przez użytkownika są również nazywane trasami niestandardowymi. Tworzysz UDR na platformie Azure, aby zastąpić domyślne trasy systemowe platformy Azure lub dodać dodatkowe trasy do tabeli tras podsieci. Na platformie Azure utworzysz tabelę tras, a następnie skojarz tabelę tras z podsieciami sieci wirtualnych.

Gdy w tabeli routingu istnieją konkurencyjne wpisy, platforma Azure wybiera następny przeskok na podstawie najdłuższych pasujących prefiksów, podobnie jak w przypadku tradycyjnych routerów. Jeśli jednak istnieje wiele wpisów następnego przeskoku z tym samym prefiksem adresu, platforma Azure wybiera trasy w następującej kolejności.
1. Trasy zdefiniowane przez użytkownika (UDR)
1. Trasy protokołu BGP
1. Trasy systemowe

## <a name="security"></a>Zabezpieczenia

Można filtrować ruch sieciowy do i z zasobów w sieci wirtualnej przy użyciu sieciowych grup zabezpieczeń. W przypadku trzciny stosowane są również wirtualne urządzenia sieciowe (urządzenie WUS), takie jak Zapora platformy Azure lub zapory od innych dostawców. Można kontrolować sposób, w jaki platforma Azure kieruje ruchem z podsieci. Można także ograniczyć, kto w organizacji może współpracować z zasobami w sieciach wirtualnych.

Sieciowa grupa zabezpieczeń zawiera listę reguł listy kontroli dostępu (ACL), które blokują lub zezwalają na ruch sieciowy do podsieci i/lub kart sieciowych. Sieciowe grupy zabezpieczeń można kojarzyć z podsieciami lub poszczególnymi kartami sieciowymi połączonymi z podsiecią. Gdy sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, reguły listy ACL dotyczą wszystkich maszyn wirtualnych w tej podsieci. Ponadto ruch do poszczególnych kart sieciowych można ograniczyć przez skojarzenie sieciowej grupy zabezpieczeń bezpośrednio z kartą sieciową.

Sieciowe grupy zabezpieczeń zawierają dwa zestawy reguł: zestaw reguł przychodzących i wychodzących. Priorytety reguł muszą być unikatowe w poszczególnych zestawach. Każda reguła ma właściwości protokołu, zakresów portów źródłowych i docelowych, prefiksów adresów, kierunku ruchu, priorytetu i typu dostępu. Wszystkie sieciowe grupy zabezpieczeń zawierają zestaw reguł domyślnych. Reguł domyślnych nie można usunąć, ale ponieważ mają przypisany najniższy priorytet, mogą być zastąpione przez tworzone zasady.

## <a name="verification"></a>Weryfikacja
### <a name="routes-in-virtual-network"></a>Trasy w sieci wirtualnej
Połączenie tworzonych tras, domyślnych tras platformy Azure oraz wszelkich tras rozmnożonych przez sieć lokalną za pośrednictwem bramy sieci VPN platformy Azure (Jeśli sieć wirtualna jest połączona z siecią lokalną) za pośrednictwem protokołu BGP (Border Gateway Protocol), to obowiązujące trasy dla wszystkich interfejsów sieciowych w podsieci. Te efektywne trasy można wyświetlić, przechodząc do karty sieciowej za pomocą portalu, programu PowerShell lub interfejsu wiersza polecenia.
### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
Obowiązujące reguły zabezpieczeń stosowane do interfejsu sieciowego są agregacją reguł istniejących w sieciowej grupy zabezpieczeń skojarzonym z interfejsem sieciowym i podsiecią, w której znajduje się interfejs sieciowy. Możesz wyświetlić wszystkie obowiązujące reguły zabezpieczeń z sieciowych grup zabezpieczeń, które są stosowane do interfejsów sieciowych maszyny wirtualnej, przechodząc do karty sieciowej za pośrednictwem portalu, programu PowerShell lub interfejsu wiersza polecenia.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usłudze [Virtual Network][VNet].

Dowiedz się więcej na temat [routingu sieci wirtualnej][vnet-routing].

Dowiedz się więcej o [sieciowych grupach zabezpieczeń][network-security].

<!--Link References-->
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[vnet-routing]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview
[network-security]: https://docs.microsoft.com/azure/virtual-network/security-overview

