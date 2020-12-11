---
title: Architektura sieci SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Architektura sieci wdrażania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b18e1cd20a4b0a886258fd56003cd273d92381fa
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093982"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Architektura sieci SAP HANA (duże wystąpienia)

Architektura usług sieciowych platformy Azure to kluczowy składnik pomyślnego wdrożenia aplikacji SAP na dużym wystąpieniu platformy HANA. Na ogół wdrożenia programu SAP HANA na platformie Azure (duże wystąpienia) mają większy poziom oprogramowania SAP z kilkoma różnymi rozwiązaniami SAP, które mają różne rozmiary baz danych, użycie zasobów procesora CPU i użycie pamięci. Prawdopodobnie nie wszystkie systemy IT znajdują się już na platformie Azure. Oprogramowanie SAP krajobrazu jest często hybrydowe, a także z punktu SAP HANA widzenia aplikacji punktu w systemie DBMS i programu SAP. Platforma Azure oferuje różne usługi, które umożliwiają uruchamianie różnych systemów DBMS, NetWeaver i S/4HANA na platformie Azure. Platforma Azure oferuje także technologię sieciową, dzięki której platforma Azure będzie wyglądać jak wirtualne centrum danych do lokalnych wdrożeń oprogramowania

Chyba że kompletne systemy IT są hostowane na platformie Azure. Funkcja sieci platformy Azure służy do łączenia lokalnego świata z zasobami platformy Azure, aby platforma Azure wyglądała jak wirtualne centrum danych. Używane funkcje sieci platformy Azure to: 

- Sieci wirtualne platformy Azure są połączone z obwodem usługi [ExpressRoute](https://azure.microsoft.com/services/expressroute/) , który łączy się z zasobami sieci lokalnej.
- Obwód usługi ExpressRoute, który łączy się z siecią lokalną z platformą Azure, powinien mieć przepustowość minimalną wynoszącą [1 GB/s lub wyższą](https://azure.microsoft.com/pricing/details/expressroute/). Ta minimalna przepustowość zapewnia odpowiednią przepustowość przesyłania danych między systemami lokalnymi i systemami uruchomionymi na maszynach wirtualnych. Zapewnia również odpowiednią przepustowość połączenia z systemami platformy Azure od użytkowników lokalnych.
- Wszystkie systemy SAP na platformie Azure są konfigurowane w sieciach wirtualnych, aby komunikować się ze sobą.
- Active Directory i usługa DNS hostowane lokalnie są rozszerzane na platformę Azure za pośrednictwem usługi ExpressRoute z poziomu lokalnego lub działają na platformie Azure.

W przypadku konkretnego przypadku integrowania dużych wystąpień usługi HANA z siecią szkieletową centrum danych platformy Azure jest używana technologia ExpressRoute systemu Azure.


> [!NOTE] 
> Tylko jedna subskrypcja platformy Azure może być połączona tylko z jedną dzierżawą w sygnaturze dużego wystąpienia HANA w określonym regionie platformy Azure. Z kolei pojedyncza dzierżawa sygnatury dużego wystąpienia HANA może być połączona tylko z jedną subskrypcją platformy Azure. Ten wymóg jest zgodny z innymi obiektami rozliczanymi na platformie Azure.

Jeśli SAP HANA na platformie Azure (duże wystąpienia) zostaną wdrożone w wielu różnych regionach świadczenia usługi Azure, w sygnaturze dużego wystąpienia HANA zostanie wdrożona oddzielna dzierżawa. Można uruchamiać oba te same subskrypcje platformy Azure, o ile te wystąpienia są częścią tego samego oprogramowania SAP krajobrazu. 

> [!IMPORTANT] 
> Tylko Azure Resource Manager Metoda wdrażania jest obsługiwana w przypadku SAP HANA na platformie Azure (duże wystąpienia).

 

## <a name="additional-virtual-network-information"></a>Dodatkowe informacje o sieci wirtualnej

Aby połączyć sieć wirtualną z usługą ExpressRoute, należy utworzyć bramę usługi Azure ExpressRoute. Aby uzyskać więcej informacji, zobacz [Informacje o bramach ExpressRoute dla ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Brama Azure ExpressRoute jest używana z usługą ExpressRoute do infrastruktury poza platformą Azure lub z sygnaturą dużego wystąpienia platformy Azure. Bramę Azure ExpressRoute można podłączyć do maksymalnie czterech obwodów usługi ExpressRoute, o ile te połączenia pochodzą z różnych routerów granicznych firmy Microsoft. Aby uzyskać więcej informacji, zobacz [SAP HANA (duże wystąpienia) infrastruktura i łączność na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Maksymalna przepływność, którą można osiągnąć za pomocą bramy ExpressRoute, to 10 GB/s przy użyciu połączenia ExpressRoute. Kopiowanie plików między MASZYNą wirtualną znajdującą się w sieci wirtualnej a systemem lokalnym (jako pojedynczy strumień kopii) nie osiąga pełnej przepływności różnych jednostek SKU bramy. Aby wykorzystać pełną przepustowość bramy ExpressRoute, Użyj wielu strumieni. Lub należy skopiować różne pliki do strumieni równoległych pojedynczego pliku.


## <a name="networking-architecture-for-hana-large-instance"></a>Architektura sieci dla dużego wystąpienia HANA
W przypadku dużego wystąpienia usługi HANA architektura sieci można podzielić na cztery różne części:

- Sieć lokalna i ExpressRoute połączenie z platformą Azure. Ta część jest domeną klienta i jest połączona z platformą Azure za pomocą usługi ExpressRoute. Ten obwód ExpressRoute jest w pełni płacony przez Ciebie jako klient. Przepustowość powinna być wystarczająco duża, aby obsługiwać ruch sieciowy między zasobami lokalnymi i regionem świadczenia usługi Azure, z którym nawiązujesz połączenie. Zobacz dolny prawy na poniższej ilustracji.
- Usługi sieciowe platformy Azure, jak wspomniano wcześniej, z sieciami wirtualnymi, które wymagają dodania bram ExpressRoute. Ta część to obszar, w którym należy znaleźć odpowiednie projekty dotyczące wymagań aplikacji, zabezpieczeń i wymagań dotyczących zgodności. Bez względu na to, czy jest używane duże wystąpienie platformy HANA, należy wziąć pod uwagę liczbę sieci wirtualnych i jednostek SKU bramy platformy Azure do wyboru. Zobacz górne prawo na rysunku.
- Łączność z dużym wystąpieniem HANA za pośrednictwem technologii ExpressRoute na platformie Azure. Ta część jest wdrażana i obsługiwana przez firmę Microsoft. Wszystko, co musisz zrobić, udostępnia niektóre zakresy adresów IP po wdrożeniu zasobów w dużym wystąpieniu HANA Połącz obwód ExpressRoute z sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [SAP HANA (duże wystąpienia) infrastruktura i łączność na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Klient nie ma dodatkowej opłaty za połączenie między jednostkami sieci szkieletowej a usługą Azure Data Center oraz wystąpieniem dużej instancji HANA.
- Obsługa sieci w sygnaturze dużej instancji HANA, która jest w większości przejrzysta.

![Sieć wirtualna połączona z SAP HANA na platformie Azure (duże wystąpienia) i lokalna](./media/hana-overview-architecture/image1-architecture.png)

Wymaganie, aby Twoje zasoby lokalne musiały łączyć się za pomocą usługi ExpressRoute z platformą Azure, nie ulegają zmianie, ponieważ używasz dużego wystąpienia HANA. Wymagana jest jedna lub wiele sieci wirtualnych, na których są uruchomione maszyny wirtualne, które obsługują warstwę aplikacji, która nawiązuje połączenie z wystąpieniami HANA hostowanymi w jednostkach dużej instancji HANA, również nie ulegają zmianie. 

Różnice dotyczące wdrożeń SAP na platformie Azure:

- Jednostki dużego wystąpienia HANA dzierżawy klienta są połączone za pomocą innego obwodu ExpressRoute w sieci wirtualne. Aby oddzielić warunki ładowania, lokalne sieci wirtualne usługi Azure Virtual Network ExpressRoute i obwody między sieciami wirtualnymi platformy Azure a dużymi wystąpieniami HANA nie współdzielą tych samych routerów.
- Profil obciążenia między warstwą aplikacji SAP a dużym wystąpieniem HANA ma inny charakter, a wiele małych żądań i serii, takich jak transfery danych (zestawy wyników), z SAP HANA do warstwy aplikacji.
- Architektura aplikacji SAP jest bardziej wrażliwa na opóźnienia sieci niż typowe scenariusze, w których dane są wymieniane między środowiskiem lokalnym i platformą Azure.
- Brama Azure ExpressRoute ma co najmniej dwa połączenia ExpressRoute. Jeden obwód połączony ze współdziałaniem lokalnym i taki, który jest połączony z dużymi wystąpieniami platformy HANA. To pozostawia tylko miejsce na inne dwa dodatkowe obwody z różnych MSEE do nawiązania połączenia z bramą ExpressRoute. To ograniczenie jest niezależne od użycia funkcji ExpressRoute Fast Path. Wszystkie połączone obwody współużytkują maksymalną przepustowość dla danych przychodzących bramy ExpressRoute.

W przypadku poprawki 3 sygnatury dużych wystąpień platformy HANA opóźnienie sieci między maszynami wirtualnymi i jednostkami dużych wystąpień platformy HANA może być większe niż typowe opóźnienie rundy sieci maszyny wirtualnej na maszynę wirtualną. W zależności od regionu świadczenia usługi Azure mierzone wartości mogą przekroczyć czas oczekiwania na 0,7 MS, sklasyfikowany poniżej poniżej średniej w programie [SAP uwaga #1100926 — często zadawane pytania: wydajność sieci](https://launchpad.support.sap.com/#/notes/1100926/E). Zależnie od regionu i narzędzia platformy Azure do mierzenia opóźnienia sieci między MASZYNami wirtualnymi platformy Azure i jednostką dużego wystąpienia HANA mierzone opóźnienie może wynosić maksymalnie 2 milisekund. Niemniej jednak klienci wdrażają aplikacje SAP oparte na SAP HANA na SAP HANA dużym wystąpieniu. Pamiętaj o gruntownym przetestowaniu procesów firmy w dużym wystąpieniu platformy Azure HANA. Nowe funkcje, o nazwie ExpressRoute Fast Path, umożliwiają zredukowanie opóźnienia sieci między dużymi wystąpieniami i maszynami wirtualnymi aplikacji platformy Azure w znacznym stopniu (patrz poniżej). 

W przypadku poprawki 4 sygnatur dużego wystąpienia usługi HANA opóźnienie sieci między maszynami wirtualnymi platformy Azure wdrożonymi w pobliżu sygnatury dużego wystąpienia HANA jest zgodne ze średnią lub lepszą niż średnia klasyfikacja, zgodnie z opisem w temacie [SAP uwagi #1100926 — często zadawane pytania: wydajność sieci](https://launchpad.support.sap.com/#/notes/1100926/E) , jeśli usługa Azure ExpressRoute Fast Path jest skonfigurowana (patrz poniżej). Aby można było wdrażać maszyny wirtualne platformy Azure w pobliżu jednostek z dużą ilością wystąpień w wersji HANA 4, należy skorzystać z [grup umieszczania usługi Azure zbliżeniowe](../../linux/co-location.md). Sposób, w jaki można używać grup umieszczania zbliżeniowe do lokalizowania warstwy aplikacji SAP w tym samym centrum danych platformy Azure, ponieważ wersja 4 hostowanych jednostek dużego wystąpienia HANA jest opisana w [grupach umieszczania bliskości platformy Azure w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP](sap-proximity-placement-scenarios.md).

Aby zapewnić niejednoznaczne opóźnienie sieci między maszynami wirtualnymi i usługą HANA, wybór jednostki SKU bramy ExpressRoute jest istotny. W przeciwieństwie do wzorców ruchu między środowiskiem lokalnym i maszynami wirtualnymi, wzorzec ruchu między maszynami wirtualnymi i dużym wystąpieniem HANA może tworzyć małe, ale wysokie obciążenia żądań i woluminów danych do przesłania. W celu obsługi takich serii zdecydowanie zalecamy użycie jednostki SKU bramy UltraPerformance. W przypadku klasy typu II jednostek SKU o dużej instancji HANA użycie jednostki SKU bramy UltraPerformance jako bramy ExpressRoute jest obowiązkowe.

> [!IMPORTANT] 
> Uwzględniając ogólny ruch sieciowy między warstwami aplikacji SAP i bazy danych, obsługiwane są tylko jednostki SKU bramy HighPerformance lub UltraPerformance dla sieci wirtualnych w celu łączenia się z SAP HANA na platformie Azure (duże wystąpienia). W przypadku dużych wystąpień usługi HANA typu II jednostki SKU jako bramy ExpressRoute jest obsługiwana tylko jednostka SKU bramy UltraPerformance. Wyjątki mają zastosowanie w przypadku używania szybkiej ścieżki ExpressRoute (patrz poniżej)

### <a name="expressroute-fast-path"></a>Szybka ścieżka ExpressRoute
Aby obniżyć czas oczekiwania, ExpressRoute szybka ścieżka została wprowadzona i wydana w 2019 maja dla konkretnej łączności z dużymi wystąpieniami platformy HANA do sieci wirtualnych Azure, które obsługują maszyny wirtualne aplikacji SAP. Poprzednia różnica w rozwiązaniu została wdrożona na tyle, że dane przepływów między maszynami wirtualnymi i dużymi wystąpieniami platformy HANA nie są już kierowane przez bramę ExpressRoute. Zamiast nich maszyny wirtualne przypisane w podsieciach sieci wirtualnej platformy Azure komunikują się bezpośrednio z dedykowanym routerem brzegowym przedsiębiorstwa. 

> [!IMPORTANT] 
> Funkcja ExpressRoute Fast Path wymaga, aby podsieci z uruchomionymi maszynami wirtualnymi aplikacji SAP znajdują się w tej samej sieci wirtualnej platformy Azure, która została połączona z dużymi wystąpieniami HANA. Maszyny wirtualne znajdujące się w sieciach wirtualnych platformy Azure, które są połączone bezpośrednio z usługą Azure Virtual Network podłączonymi do jednostek dużego wystąpienia HANA, nie są korzystne od ExpressRoute Fast Path. W wyniku typowych projektów sieci wirtualnych typu Hub i szprych, w których obwody usługi ExpressRoute są nawiązywane połączenie z siecią wirtualną koncentratora i sieciami wirtualnymi zawierającymi warstwę aplikacji SAP (szprychy), Komunikacja równorzędna nie będzie zadziałała. W obszarze funkcja ExpressRoute Fast Path nie obsługuje już dzisiaj reguł routingu zdefiniowanych przez użytkownika (UDR). Aby uzyskać więcej informacji, zobacz [ExpressRoute Virtual Network Gateway and FastPath](../../../expressroute/expressroute-about-virtual-network-gateways.md). 


Aby uzyskać więcej informacji na temat konfigurowania szybkiej ścieżki usługi ExpressRoute, Przeczytaj dokument [łączenie sieci wirtualnej z dużymi wystąpieniami platformy Hana](./hana-connect-vnet-express-route.md).    

> [!NOTE]
> Brama UltraPerformance ExpressRoute jest wymagana do działania funkcji ExpressRoute Fast Path


## <a name="single-sap-system"></a>Pojedynczy system SAP

Wcześniej pokazana infrastruktura lokalna jest połączona przez ExpressRoute na platformę Azure. Obwód ExpressRoute nawiązuje połączenie z routerem brzegowym Microsoft Enterprise (MSEE). Aby uzyskać więcej informacji, zobacz [Omówienie techniczne ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Po ustanowieniu trasy nawiązuje połączenie ze szkieletem platformy Azure.

> [!NOTE] 
> Aby uruchomić oprogramowanie SAP Landscapes na platformie Azure, Połącz się z routerem brzegowym przedsiębiorstwa znajdującym się najbliżej regionu świadczenia usługi Azure w poziomie. Sygnatury dużych wystąpień HANA są połączone za pośrednictwem dedykowanych urządzeń routera brzegowego przedsiębiorstwa, aby zminimalizować opóźnienie sieci między maszynami wirtualnymi w sygnaturach dużych wystąpień usługi Azure IaaS i HANA.

Brama ExpressRoute dla maszyn wirtualnych, które hostuje wystąpienia aplikacji SAP, jest połączona z jednym obwodem usługi ExpressRoute, który łączy się z lokalną usługą. Ta sama sieć wirtualna jest połączona z osobnym routerem brzegowym przedsiębiorstwa dedykowanym do łączenia z dużymi sygnaturami wystąpień. Dzięki szybkiej ścieżce ExpressRoute przepływ danych z dużych wystąpień platformy HANA do maszyn wirtualnych warstwy aplikacji SAP nie jest już kierowany przez bramę ExpressRoute i z tym, że zmniejsza to opóźnienie sieci.

Ten system jest prostym przykładem pojedynczego systemu SAP. Warstwa aplikacji SAP jest hostowana na platformie Azure. Baza danych SAP HANA jest uruchamiana na SAP HANA na platformie Azure (duże wystąpienia). Przyjęto założenie, że przepustowość bramy ExpressRoute 2 GB/s lub 10 GB/s nie reprezentuje wąskiego gardła.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Wiele systemów SAP lub dużych systemów SAP

Jeśli wdrożono wiele systemów SAP lub dużych systemów SAP do łączenia się z SAP HANA na platformie Azure (duże wystąpienia), przepływność bramy ExpressRoute może stać się wąskim gardłem. Lub chcesz odizolować systemy produkcyjne i nieprodukcyjne w różnych sieciach wirtualnych platformy Azure. W takim przypadku należy podzielić warstwy aplikacji na wiele sieci wirtualnych. Można również utworzyć specjalną sieć wirtualną, która łączy się z dużym wystąpieniem platformy HANA w przypadku takich przypadków, jak:

- Tworzenie kopii zapasowych bezpośrednio z wystąpień HANA w usłudze HANA w dużym wystąpieniu do maszyny wirtualnej na platformie Azure, która hostuje udziały NFS.
- Kopiowanie dużych kopii zapasowych lub innych plików z jednostek wystąpienia usługi HANA do przestrzeni dyskowej zarządzanej na platformie Azure.

Użyj oddzielnej sieci wirtualnej, aby hostować maszyny wirtualne, które zarządzają magazynem do masowego transferu danych między dużymi wystąpieniami platformy HANA i platformą Azure. To rozwiązanie pozwala uniknąć wpływu dużego pliku lub transferu danych z dużego wystąpienia HANA na platformę Azure w ramach bramy ExpressRoute, która obsługuje maszyny wirtualne, na których jest uruchomiona warstwa aplikacji SAP. 

Aby uzyskać bardziej skalowalną architekturę sieci:

- Wykorzystanie wielu sieci wirtualnych dla jednej, większej warstwy aplikacji SAP.
- Wdróż jedną oddzielną sieć wirtualną dla każdego wdrożonego systemu SAP, porównując te systemy SAP w oddzielne podsieci w tej samej sieci wirtualnej.

  Bardziej skalowalna architektura sieci dla SAP HANA na platformie Azure (duże wystąpienia):

![Wdrażanie warstwy aplikacji SAP przez wiele sieci wirtualnych](./media/hana-overview-architecture/image4-networking-architecture.png)

Zależnie od zasad i ograniczeń, które mają być stosowane między różnymi sieciami wirtualnymi obsługującymi maszyny wirtualne z różnymi systemami SAP, należy nawiązać komunikację równorzędną z tymi sieciami wirtualnymi. Aby uzyskać więcej informacji na temat wirtualnych sieci równorzędnych, zobacz [wirtualne sieci równorzędne](../../../virtual-network/virtual-network-peering-overview.md).


## <a name="routing-in-azure"></a>Routing na platformie Azure

Wdrożenie domyślne, trzy zagadnienia dotyczące routingu sieciowego są ważne dla SAP HANA na platformie Azure (duże wystąpienia):

* Dostęp do SAP HANA na platformie Azure (duże wystąpienia) można uzyskać tylko za pośrednictwem maszyn wirtualnych platformy Azure i dedykowanego połączenia ExpressRoute, które nie są bezpośrednio dostępne w środowisku lokalnym. Bezpośredni dostęp z zasobów lokalnych do jednostek dużego wystąpienia platformy HANA, jak to zostało dostarczone przez firmę Microsoft, nie jest możliwe od razu. Ograniczenia routingu przechodniego są spowodowane bieżącą architekturą sieci platformy Azure używaną do SAP HANA dużych wystąpień. Niektórzy klienci administracyjni i wszystkie aplikacje, które potrzebują bezpośredniego dostępu, takie jak Menedżer rozwiązań SAP działające lokalnie, nie mogą połączyć się z bazą danych SAP HANA. W przypadku wyjątków sprawdź sekcję "Kierowanie bezpośrednie routingu do usługi HANA duże wystąpienia".

* Jeśli masz duże jednostki wystąpienia HANA wdrożone w dwóch różnych regionach świadczenia usługi Azure na potrzeby odzyskiwania po awarii, te same przejściowe ograniczenia routingu są stosowane w przeszłości. Innymi słowy adresy IP jednostki dużego wystąpienia HANA w jednym regionie (na przykład zachodnie stany USA) nie były kierowane do jednostki dużego wystąpienia HANA wdrożonej w innym regionie (na przykład Wschodnie stany USA). To ograniczenie było niezależne od używania komunikacji równorzędnej sieci platformy Azure między regionami lub łączących się między sobą obwodów ExpressRoute, które łączą jednostki usługi HANA z sieciami wirtualnymi. Aby uzyskać reprezentację graficzną, zobacz rysunek w sekcji "Korzystanie z jednostek usługi HANA Large Instances" w wielu regionach. To ograniczenie, które zostało dołączone do wdrożonej architektury, nie zabroniona do natychmiastowego korzystania z replikacji systemu HANA jako funkcji odzyskiwania po awarii. Najnowsze zmiany można znaleźć w sekcji "Korzystanie z jednostek usługi HANA Large Instances" w wielu regionach. 

* Jednostki SAP HANA na platformie Azure (duże wystąpienia) mają przypisany adres IP z zakresu adresów puli IP serwera, który został przesłany podczas żądania wdrożenia dużego wystąpienia platformy HANA. Aby uzyskać więcej informacji, zobacz [SAP HANA (duże wystąpienia) infrastruktura i łączność na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ten adres IP jest dostępny za pomocą subskrypcji i obwodu platformy Azure, który łączy sieci wirtualne platformy Azure z dużymi wystąpieniami HANA. Adres IP przypisany z tego zakresu adresów puli adresów IP jest bezpośrednio przypisany do jednostki sprzętowej. *Nie* jest już przypisane za pośrednictwem translatora adresów sieciowych, tak jak w przypadku pierwszego wdrożenia tego rozwiązania. 

### <a name="direct-routing-to-hana-large-instances"></a>Bezpośrednie kierowanie do dużych wystąpień platformy HANA

Domyślnie Routing przechodni nie działa w następujących scenariuszach:

* Między jednostkami dużych wystąpień HANA i wdrożeniem lokalnym.

* Między routingiem dużego wystąpienia HANA, które są wdrożone w dwóch różnych regionach.

Istnieją trzy sposoby włączania routingu przechodniego w tych scenariuszach:

- Zwrotny serwer proxy do przesyłania danych do i z. Na przykład F5 BIG-IP, NGINX z Traffic Manager wdrożoną w sieci wirtualnej platformy Azure, która łączy się z dużymi wystąpieniami HANA i lokalnymi jako wirtualną zaporą/rozwiązaniem routingu ruchu.
- Używanie reguł dołączenie iptables na maszynie wirtualnej z systemem Linux w celu włączenia routingu między lokalizacjami lokalnymi i jednostkami dużej instancji HANA lub między jednostkami dużych wystąpień usługi HANA w różnych regionach. Maszyna wirtualna z systemem dołączenie iptables musi zostać wdrożona w sieci wirtualnej platformy Azure, która łączy się z dużymi wystąpieniami HANA i lokalnymi. Należy odpowiednio zmienić rozmiar maszyny wirtualnej, dzięki czemu przepustowość sieci maszyny wirtualnej jest wystarczająca dla oczekiwanego ruchu sieciowego. Aby uzyskać szczegółowe informacje dotyczące przepustowości sieci maszyn wirtualnych, sprawdź [rozmiary artykułów maszyn wirtualnych z systemem Linux na platformie Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Zapora platformy Azure](https://azure.microsoft.com/services/azure-firewall/) to inne rozwiązanie umożliwiające bezpośrednie przesyłanie ruchu między jednostkami dużych wystąpień lokalnych i Hana. 

Cały ruch tych rozwiązań jest kierowany za pośrednictwem sieci wirtualnej platformy Azure. w związku z tym ten ruch może być dodatkowo ograniczony przez urządzenia miękkie używane lub przez grupy zabezpieczeń sieci platformy Azure, dzięki czemu niektóre adresy IP lub zakresy adresów IP z lokalnego programu mogą być blokowane lub jawnie dozwolone dostęp do dużych wystąpień usługi HANA. 

> [!NOTE]  
> Należy pamiętać, że implementacja i obsługa niestandardowych rozwiązań obejmujących urządzenia sieciowe innych firm lub dołączenie iptables nie są dostarczane przez firmę Microsoft. Pomoc techniczna musi być świadczona przez dostawcę używanego składnika lub integratora. 

#### <a name="express-route-global-reach"></a>Global Reach trasy ekspresowej
Firma Microsoft wprowadziła nową funkcję o nazwie [ExpressRoute Global REACH](../../../expressroute/expressroute-global-reach.md). Global Reach można używać dla dużych wystąpień platformy HANA w dwóch scenariuszach:

- Włącz bezpośredni dostęp z zasobów lokalnych do jednostek dużego wystąpienia platformy HANA wdrożonych w różnych regionach
- Włącz bezpośrednią komunikację między jednostkami dużej instancji HANA wdrożonymi w różnych regionach


##### <a name="direct-access-from-on-premises"></a>Bezpośredni dostęp z zasobów lokalnych
W regionach świadczenia usługi Azure, w których jest oferowana Global Reach, możesz zażądać włączenia Global Reach funkcji dla obwodu usługi ExpressRoute, który łączy sieć lokalną z siecią wirtualną platformy Azure, która łączy się również z jednostkami dużych wystąpień programu HANA. Istnieją pewne koszty związane z lokalną stroną obwodu usługi ExpressRoute. W przypadku cen Sprawdź ceny [dodatku Global REACH](https://azure.microsoft.com/pricing/details/expressroute/). Nie ma dodatkowych kosztów związanych z obwodem łączącym jednostki dużych wystąpień platformy HANA z platformą Azure. 

> [!IMPORTANT]  
> W przypadku używania Global Reach do włączania bezpośredniego dostępu między jednostkami dużych wystąpień usługi HANA i zasobami lokalnymi przepływ danych i kontroli sieci **nie jest kierowany za pośrednictwem sieci wirtualnych platformy Azure**, ale bezpośrednio między routerami programu Microsoft Enterprise Exchange. W wyniku dowolnych reguł sieciowej grupy zabezpieczeń lub ASG lub dowolnego typu zapory, urządzenie WUS lub serwera proxy wdrożonego w sieci wirtualnej platformy Azure nie jest to możliwe. **W przypadku używania ExpressRoute Global Reach w celu włączenia bezpośredniego dostępu z zasobów lokalnych do platformy HANA ograniczenia i uprawnienia dostępu do dużych wystąpień usługi HANA muszą być zdefiniowane w zaporach po stronie lokalnej** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Łączenie dużych wystąpień HANA w różnych regionach świadczenia usługi Azure
W taki sam sposób, jak ExpressRoute Global Reach może służyć do łączenia się z jednostkami dużej instancji platformy Hana, może służyć do łączenia dwóch dzierżawców dużych wystąpień usługi HANA wdrożonych dla Ciebie w dwóch różnych regionach. Izolacja to obwody usługi ExpressRoute, które są używane przez dzierżawców z dużymi wystąpieniami platformy HANA do łączenia się z platformą Azure w obu regionach. Nie ma dodatkowych opłat za połączenie dwóch dzierżawców dużych wystąpień usługi HANA wdrożonych w dwóch różnych regionach. 

> [!IMPORTANT]  
> Przepływ danych i przepływ sterowania ruchu sieciowego między różnymi dzierżawcami dużych wystąpień usługi HANA nie będą kierowane za pomocą sieci platformy Azure. W związku z tym nie można użyć funkcji platformy Azure ani urządzeń WUS, aby wymusić ograniczenia komunikacji między dwoma dzierżawcami dużych wystąpień HANA. 

Aby uzyskać więcej informacji na temat pobierania ExpressRoute Global Reach, Przeczytaj dokument [łączenie sieci wirtualnej z dużymi wystąpieniami platformy Hana](./hana-connect-vnet-express-route.md).


## <a name="internet-connectivity-of-hana-large-instance"></a>Łączność internetowa z dużym wystąpieniem HANA
Duże wystąpienie HANA nie *ma* bezpośredniej łączności z Internetem. To ograniczenie może na przykład ograniczyć możliwość rejestrowania obrazu systemu operacyjnego bezpośrednio z dostawcą systemu operacyjnego. Może być konieczne skontaktowanie się z lokalnym serwerem narzędzi do zarządzania subskrypcjami SUSE Linux Enterprise Server lub Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Szyfrowanie danych między maszynami wirtualnymi i dużym wystąpieniem HANA
Dane przesyłane między dużymi wystąpieniami HANA i maszynami wirtualnymi nie są szyfrowane. Jednak w przypadku wymiany między aplikacjami platformy HANA DBMS i JDBC/ODBC można włączyć szyfrowanie ruchu. Aby uzyskać więcej informacji, zobacz [tę dokumentację w systemie SAP](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Korzystanie z jednostek dużych wystąpień platformy HANA w wielu regionach

Aby zrealizować zestaw odzyskiwania po awarii, musisz mieć SHANA duże wystąpienia w wielu regionach świadczenia usługi Azure. Nawet w przypadku korzystania z platformy Azure [globalna komunikacja równorzędna sieci wirtualnych] Routing przechodni domyślnie nie działa między dzierżawcami dużych wystąpień usługi HANA w dwóch różnych regionach. Jednak Global Reach otwiera ścieżkę komunikacji między jednostkami dużej instancji HANA, które zostały zainicjowane w dwóch różnych regionach. Ten scenariusz użycia ExpressRoute Global Reach umożliwia:

 - Replikacja systemu HANA bez dodatkowych serwerów proxy lub zapór
 - Kopiowanie kopii zapasowych między jednostkami dużych wystąpień programu HANA w dwóch różnych regionach w celu wykonywania kopii systemu lub odświeżeń systemu


![Sieć wirtualna połączona z sygnaturami dużych wystąpień platformy Azure w różnych regionach platformy Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

Na rysunku przedstawiono sposób, w jaki różne sieci wirtualne w obu regionach są podłączone do dwóch różnych obwodów usługi ExpressRoute, które są używane do łączenia się z SAP HANA na platformie Azure (duże wystąpienia) w obu regionach platformy Azure (szare linie). Przyczyną tych dwóch połączeń krzyżowych jest ochrona przed awarią MSEE po obu stronach. Przepływ komunikacji między dwiema sieciami wirtualnymi w dwóch regionach platformy Azure jest traktowany jak w przypadku [globalnej komunikacji równorzędnej](/archive/blogs/azureedu/how-to-setup-global-vnet-peering-in-azure) dwóch sieci wirtualnych w dwóch różnych regionach (niebieska linia kropkowana). Gruba Czerwona linia zawiera opis połączenia z usługą ExpressRoute Global Reach, które umożliwia jednostkom dużych wystąpień usługi HANA dzierżawy w dwóch różnych regionach, aby komunikować się ze sobą. 

> [!IMPORTANT] 
> Jeśli użyto wielu obwodów usługi ExpressRoute, w celu zapewnienia prawidłowego routingu ruchu należy użyć ustawień protokołu BGP w zależności od ścieżki i preferencji lokalnych.

**Następne kroki**
- Odwołuje się [SAP HANA (duże wystąpienia) architektura magazynu](hana-storage-architecture.md)