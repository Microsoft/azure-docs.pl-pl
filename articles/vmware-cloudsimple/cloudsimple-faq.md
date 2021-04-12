---
title: Często zadawane pytania — rozwiązanie Azure VMware według CloudSimple
description: Często zadawane pytania dotyczące rozwiązań VMware platformy Azure według CloudSimple obejmują łączność, Sieć, zabezpieczenia, magazyn, integrację z platformą Azure i inne osoby.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3551327f9aa07fe218d3b05f73057e36f92abcad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895567"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Często zadawane pytania dotyczące rozwiązania VMware według CloudSimple

## <a name="cloudsimple-service"></a>Usługa CloudSimple

**Co to jest rozwiązanie Azure VMware przez CloudSimple?**

Rozwiązanie VMware firmy Azure według CloudSimple przekształca i rozszerza obciążenia VMware do prywatnych, dedykowanych chmur na platformie Azure w ciągu kilku minut. CloudSimple zajmuje się obsługą administracyjną, zarządzaniem infrastrukturą i organizowaniem obciążeń między środowiskiem lokalnym i platformą Azure. Ponieważ aplikacje działają dokładnie tak samo lokalnie i na platformie Azure, możesz skorzystać z elastyczności i usług w chmurze bez konieczności ponownego tworzenia architektury aplikacji. CloudSimple obniża łączny koszt posiadania przy użyciu modelu zużycia w chmurze, który zapewnia aprowizacji na żądanie, płatność zgodnie z oczekiwaniami i optymalizację pojemności.  Zobacz artykuł [co to jest rozwiązanie VMware na platformie Azure przez CloudSimple](cloudsimple-vmware-solutions-overview.md) , aby poznać funkcje, zalety i scenariusze.

**Co to jest chmura prywatna CloudSimple?**

Chmura prywatna CloudSimple to prywatna, dedykowana Chmura, która składa się ze środowiska obliczeniowego, magazynu i sieci o wysokiej wydajności wdrożonego w ramach infrastruktury Microsoft Azure (sprzęt i przestrzeń w centrum danych) w lokalizacjach platformy Azure.  Chmura prywatna zapewnia natywną platformę VMware jako usługę. W programie VMware, każda Chmura prywatna zawiera dokładnie jedno wystąpienie vCenter Server. VCenter Server zarządza wieloma węzłami ESXi zawartymi w jednym lub większej liczbie klastrów vSphere wraz z odpowiednim magazynem wirtualnej sieci SAN (sieci vSAN). Usługa CloudSimple może zawierać wiele chmur prywatnych w ramach subskrypcji platformy Azure.  Aby uzyskać więcej informacji, zobacz [Omówienie chmury prywatnej](cloudsimple-private-cloud.md).

**Gdzie jest dostępna usługa CloudSimple?**

CloudSimple jest dostępna w regionach Wschodnie stany USA, zachodnie stany USA i Europa Zachodnia z dodatkowymi regionami już wkrótce.

**Jak mogę włączyć moją subskrypcję usługi CloudSimple?**

[azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com)Aby włączyć subskrypcję usługi CloudSimple, możesz skontaktować się z przedstawicielem konto Microsoft. Podaj identyfikator subskrypcji w wiadomości e-mail, dla której usługa CloudSimple ma być włączona.  

**Jak mogę uzyskać dostęp do portalu CloudSimple?**

Dostęp do portalu CloudSimple można uzyskać z Azure Portal.  Aby uzyskać szczegółowe informacje, zobacz [dostęp do portalu VMware Solution by CloudSimple z Azure Portal](access-cloudsimple-portal.md).

**Jak mogę zwiększyć pojemność chmury prywatnej?**

Aby zwiększyć pojemność, Kup dodatkowe węzły z Azure Portal a następnie użyj węzłów, aby rozwinąć chmurę prywatną z portalu CloudSimple.  Można dodać dodatkowe węzły do istniejącego klastra vSphere lub dodać je do nowego klastra vSphere.  Aby uzyskać szczegółowe informacje, zobacz [Rozwiń chmurę prywatną CloudSimple](expand-private-cloud.md).

**Co się stanie z moją chmurą prywatną podczas konserwacji?**

CloudSimple zapewnia powiadomienie kilka dni przed upływem zaplanowanego interwału konserwacji.  Konserwacja odbywa się w niezakłóconym stopniu w celu zapewnienia dostępności chmury prywatnej.  Konserwacja może być następujących typów:

* **Infrastruktura CloudSimple**.  Infrastruktura CloudSimple została zaprojektowana tak, aby była wysoce dostępna.  W trakcie tego typu interwału konserwacji nadmiarowe składniki są aktualizowane pojedynczo, aby uniknąć przerw w świadczeniu usług. Zapewniasz dostęp do prywatnej chmury programu vCenter, wszystkich maszyn wirtualnych, połączenia internetowego z chmury prywatnej oraz połączeń z lokalnym lub platformą Azure.
* **Portal CloudSimple**. W trakcie tego typu interwału konserwacji niektóre funkcje w portalu CloudSimple mogą być wyłączone lub niedostępne.  Powiadomienie przed interwałem konserwacji zawiera szczegółowe informacje o ograniczeniach funkcji, gdy odbywa się konserwacja.

## <a name="connectivity"></a>Łączność

**Jakie są moje opcje łączności z siecią CloudSimple region?**

Program CloudSimple udostępnia następujące opcje łączności, które łączą się z siecią regionu CloudSimple. W tym samym czasie można używać wielu opcji.

* **ExpressRoute połączenie z lokalnego centrum danych do sieci regionu CloudSimple**. Jest to bardzo szybkie, bezpieczne połączenie prywatne, które używa Global Reach do mostkowania lokalnego obwodu usługi ExpressRoute do obwodu usługi CloudSimple ExpressRoute. Aby uzyskać instrukcje dotyczące konfigurowania połączenia, zobacz [łączenie się z lokalnego do CloudSimple przy użyciu ExpressRoute](on-premises-connection.md).
* **ExpressRoute połączenie z sieci wirtualnej platformy Azure z siecią regionu CloudSimple**. Jest to bardzo szybkie i bezpieczne połączenie prywatne, które korzysta z bram sieci wirtualnej do mostkowania sieci wirtualnej na platformie Azure do obwodu usługi CloudSimple ExpressRoute. Aby uzyskać instrukcje dotyczące konfigurowania połączenia, zobacz [łączenie środowiska chmury prywatnej CloudSimple z siecią wirtualną platformy Azure za pomocą usługi ExpressRoute](azure-expressroute-connection.md).
* **Połączenie sieci VPN typu lokacja-lokacja z lokalnego centrum danych do sieci regionu CloudSimple**. Jest to bezpieczna wirtualna sieć prywatna z lokalnego urządzenia sieci VPN do regionu prywatnej chmury CloudSimple.  Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie bram sieci VPN w sieci CloudSimple](vpn-gateway.md).

**Jak mogę połączyć się z chmurą prywatną?**

Szczegóły chmury prywatnej można wyświetlić w portalu CloudSimple. Aby nawiązać połączenie z programem vCenter, który odpowiada chmurze prywatnej, należy najpierw sprawdzić, czy połączenie sieciowe zostało nawiązane przy użyciu sieci VPN typu lokacja-lokacja, sieci VPN typu punkt-lokacja lub ExpressRoute. Następnie uruchom Portal CloudSimple z Azure Portal a następnie kliknij pozycję **Uruchom klienta vSphere** na stronie głównej lub na stronie szczegółów chmury prywatnej.

**Jakie jest zalety obwody usługi ExpressRoute?**

Obwód usługi Azure ExpressRoute to szybkie i bezpieczne połączenie o małych opóźnieniach.  CloudSimple zapewnia dedykowany obwód ExpressRoute na region na klienta.  Za pomocą tego obwodu można nawiązać bezpieczne połączenie z poziomu lokalnej lub subskrypcji platformy Azure.

**Jakie koszty sieciowe mają być połączone z usługą CloudSimple?  Czy są naliczane opłaty za ruch wychodzący między CloudSimple i platformą Azure, czy między regionami?**

Nie ma opłat za CloudSimple dla ruchu wychodzącego w sieci.  Stawki standardowe platformy Azure dotyczą wszelkich ruchu wychodzącego z sieci wirtualnej lub lokalnego obwodu usługi ExpressRoute.

## <a name="networking"></a>Sieć

**Jakie funkcje sieciowe są dostępne dla mojej chmury prywatnej?**

Można udostępniać sieci VLAN (i ich podsieci) oraz tabele zapory i przypisywać publiczne adresy IP, które są mapowane na maszynę wirtualną działającą w chmurze prywatnej. Aby uzyskać szczegółowe informacje o funkcjach sieciowych, zobacz Omówienie [sieci VLAN i podsieci](cloudsimple-vlans-subnets.md), omówienie [tabel zapory](cloudsimple-firewall-tables.md)i [publiczny adres IP](cloudsimple-public-ip-address.md).

**Jak mogę skonfigurować różne podsieci dla aplikacji w mojej chmurze prywatnej?**

Sieci VLAN można tworzyć w chmurze prywatnej z poziomu portalu CloudSimple.  Po utworzeniu sieci VLAN można utworzyć rozproszoną grupę portów na serwerze vCenter chmury prywatnej przy użyciu sieci VLAN i utworzyć maszyny wirtualne połączone z grupą portów rozproszonych.  Można włączyć tabele zapory dla sieci VLAN/podsieci i zdefiniować reguły zapory w celu zabezpieczenia ruchu sieciowego.

**Jakie ustawienia zapory są dostępne dla moich chmur prywatnych?**

Można skonfigurować reguły dla ruchu północ-południe i wschód-zachód.  Reguły są zdefiniowane w tabeli zapory.  Tabelę zapory można dołączyć do sieci VLAN w chmurze prywatnej.  Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie tabel zapory i reguł dla chmur prywatnych](firewall.md).

**Czy można przypisywać publiczne adresy IP do maszyn wirtualnych w środowisku chmury prywatnej?**

W portalu CloudSimple można przydzielić nowy publiczny adres IP i skojarzyć go z prywatnym adresem IP maszyny wirtualnej lub urządzenia.  Możesz również utworzyć nowe reguły zapory lub zastosować istniejące reguły zapory, aby zezwolić na ruch z określonych portów i adresów IP w portalu. Aby uzyskać szczegółowe informacje, zobacz [przydzielanie publicznych adresów IP dla środowiska chmury prywatnej](public-ips.md).

## <a name="security"></a>Zabezpieczenia

**Jakie są moje opcje zabezpieczeń w witrynie CloudSimple?**

Usługa CloudSimple zapewnia następujące funkcje zabezpieczeń w celu zabezpieczenia środowiska chmury prywatnej:

* **Szyfrowanie danych w czasie spoczynku**. Możesz zaszyfrować dane przechowywane w magazynie sieci vSAN w chmurze prywatnej. program sieci vSAN obsługuje zewnętrzne serwery zarządzania kluczami, które można wdrożyć w sieci wirtualnej platformy Azure lub w środowisku lokalnym.  Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie szyfrowania sieci vSAN dla prywatnej chmury CloudSimple](vsan-encryption.md).
* **Zabezpieczenia sieciowe**. Sterowanie przepływem ruchu sieciowego za pomocą reguł zapory, które są stosowane między chmurą prywatną i Internetem, chmurą prywatną i środowiskiem lokalnym lub w podsieciach chmury prywatnej.
* **Bezpieczne połączenie prywatne**. Bezpieczne połączenie prywatne jest nawiązywane między siecią lokalną i subskrypcją platformy Azure.

## <a name="compute"></a>Compute

**Jakiego rodzaju hosty są dostępne?**

CloudSimple oferuje następujące typy hostów:

* **Węzeł CS28:** Procesor: 2,2 2 GHz, łącznie 28 rdzeni, 48 HT.  PAMIĘĆ RAM: 256 GB.  Magazyn: pamięć podręczna interfejsu NVMe 1600 GB, dane 5760 GB (wszystkie-Flash). Sieć: karta sieciowa 4x25Gbe
* **Węzeł CS36:** Procesor 2,3 2 GHz, łączne rdzenie 36, 72 HT.  PAMIĘĆ RAM: 512 GB.  Magazyn: 3200 GB pamięci podręcznej interfejsu NVMe 11520 GB danych (wszystkie-Flash).  Sieć: karta sieciowa 4x25Gbe
* **Węzeł CS36m:** Procesor 2,3 2 GHz, łączne rdzenie 36, 72 HT.  PAMIĘĆ RAM: 576 GB.  Magazyn: 3200 GB pamięci podręcznej interfejsu NVMe 13360 GB danych (wszystkie-Flash).  Sieć: karta sieciowa 4x25Gbe

**Jak są obsługiwane błędy sprzętu?**

Cała infrastruktura CloudSimple jest stale monitorowana przez platformę CloudSimple i naszych zespołów operacyjnych usług.  Jeśli awaria sprzętowa zostanie wykryta, do chmury prywatnej zostanie dodany nowy węzeł i zostanie usunięty węzeł zakończony niepowodzeniem.

## <a name="storage"></a>Storage

**Jakiego typu magazyn jest obsługiwany w chmurze prywatnej?**

Usługa CloudSimple oferuje wszystkie-Flash usługi VMware sieci vSAN Storage z każdą chmurą prywatną.  Każdy vSphere jest tworzony z własnym magazynem danych sieci vSAN.  Aby uzyskać szczegółowe informacje, zobacz [prywatne składniki VMware Components-sieci vSAN Storage](vmware-components.md#vsan-storage).

**Czy jest obsługiwane szyfrowanie danych?**
Tak.  Można skonfigurować magazyn sieci vSAN w chmurze prywatnej w taki sposób, aby korzystał z serwera zarządzania kluczami (KMS) wdrożonego lokalnie lub na platformie Azure w celu szyfrowania danych przechowywanych w usłudze sieci vSAN.

**Jak są obsługiwane dyski z błędami?**

CloudSimple stale monitoruje wszystkie składniki sprzętowe chmury prywatnej.  Jeśli awaria dysku zostanie wykryta lub dysk zostanie zidentyfikowany jako niepowodzenie (na podstawie heurystycznych), nowy węzeł zostanie automatycznie dodany do chmury prywatnej.  Węzeł z dyskiem zakończonym niepowodzeniem lub niepowodzeniem zostanie usunięty z chmury prywatnej.

## <a name="vmware"></a>VMware

**Jak mogę wykonywać na dużą skalę przekazywanie lub migrację aplikacji i danych z lokalnego systemu?**

CloudSimple zapewnia natywne rozwiązanie VMware vSphere.  Wszystkie narzędzia VMware do migracji danych zbiorczych mogą być używane z chmurą prywatną.  Dostępne są następujące opcje:

* Oprogramowanie VMware HCX na potrzeby migracji zbiorczej danych.
* Zimna migracja danych przy użyciu usługi Storage vMotion z lokalnego do CloudSimple.

**Czy mogę zainstalować dowolne narzędzia VMware?**

CloudSimple zapewnia natywne rozwiązanie VMware vSphere.  Wszystkie narzędzia VMware używane do zarządzania lokalnym środowiskiem vSphere mogą być używane w systemie CloudSimple.  CloudSimple obsługuje model do przynoszenia własnych licencji (BYOL) służący do instalowania narzędzi VMware.

**Jak są zarządzane aktualizacje i uaktualnienia?**

CloudSimple zarządza i aktualizuje wszystkie składniki infrastruktury chmury prywatnej w niezakłócony sposób.  Wszystkie aktualizacje i poprawki zabezpieczeń wydane przez dostawców oprogramowania VMware lub infrastruktury są planowane do aktualizacji, gdy tylko zakwalifikują się do CloudSimple.

Program CloudSimple nie wykonuje uaktualnień ani aktualizacji aplikacji zainstalowanych w chmurze prywatnej.

## <a name="azure-integration"></a>Integracja z platformą Azure

**Jakie usługi platformy Azure są obsługiwane?**

Usługa CloudSimple udostępnia połączenie usługi Azure ExpressRoute z subskrypcją na platformie Azure.  Wszystkie usługi działające w ramach subskrypcji mogą łączyć się z chmurą prywatną.  Przykłady obejmują:

* **Azure Active Directory** jako źródło tożsamości dla programu CloudSimple vCenter.
* **Usługa Azure Storage** do przechowywania kopii zapasowych, obrazów i innych danych z chmury prywatnej.
* **Aplikacje hybrydowe** z architekturą aplikacji, która obejmuje chmury publiczne i prywatne.  Na przykład można utworzyć serwery WebServer na platformie Azure, które uzyskują dostęp do serwerów aplikacji i baz danych w chmurze prywatnej.
* **Usługa Azure monitor** i **usługa Azure Security Center** dla obciążeń działających na potrzeby rejestrowania, metryk wydajności i zarządzania zabezpieczeniami w oprogramowaniu VMware.

**Jak mogę mapowanie moich dzierżawców VMware na platformę Azure?**

Usługa CloudSimple zapewnia unikatową zdolność do zarządzania maszynami wirtualnymi VMware w chmurze prywatnej z poziomu Azure Portal.  Pulę zasobów programu vCenter skonfigurowaną z żądanymi ograniczeniami zasobów można zamapować na subskrypcję przez administratora globalnego.  

**Jakie korzyści z licencjonowania mogę uzyskać na platformie Azure?**

Dzięki usłudze CloudSimple możesz skorzystać z korzyści użycia hybrydowego platformy Azure i zaoszczędzić do 90% na licencji. Ta korzyść zachowuje inwestycję w licencje firmy Microsoft i obniża całkowity koszt posiadania względem innych rozwiązań w chmurze. Można również uzyskać rozszerzone aktualizacje zabezpieczeń dla systemów Windows Server 2008 i Microsoft SQL Server 2008.  Model "BYOL" umożliwia zachowanie niskich kosztów dla typowych aplikacji, takich jak Veeam i Zerto.  
