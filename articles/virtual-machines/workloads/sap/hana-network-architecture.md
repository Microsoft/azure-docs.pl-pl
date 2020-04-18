---
title: Architektura sieci SAP HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Architektura sieci, jak wdrożyć SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b3bc87b183803c0854542d6925af7429b593d2af
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605170"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Architektura sieci SAP HANA (Duże wystąpienia)

Architektura usług sieciowych platformy Azure jest kluczowym składnikiem pomyślnego wdrożenia aplikacji SAP w dużym wystąpieniu HANA. Zazwyczaj sap HANA na platformie Azure (duże wystąpienia) wdrożenia mają większy krajobraz SAP z kilku różnych rozwiązań SAP o różnych rozmiarach baz danych, zużycie zasobów procesora CPU i wykorzystanie pamięci. Jest prawdopodobne, że nie wszystkie systemy IT znajdują się już na platformie Azure. Krajobraz SAP jest często hybrydowy, a także z punktu DBMS i sap punktu punktu widzenia przy użyciu mieszaniny NetWeaver i S/4HANA i SAP HANA i innych dbms. Platforma Azure oferuje różne usługi, które umożliwiają uruchamianie różnych systemów DBMS, NetWeaver i S/4HANA na platformie Azure. Platforma Azure oferuje również technologię sieciową, dzięki której platforma Azure będzie wyglądać jak wirtualne centrum danych we wdrożeniach oprogramowania lokalnego

O ile kompletne systemy INFORMATYCZNE nie są hostowane na platformie Azure. Funkcja sieci platformy Azure służy do łączenia świata lokalnego z zasobami platformy Azure, aby platforma Azure wyglądała jak wirtualne centrum danych. Używana funkcja sieci platformy Azure jest: 

- Sieci wirtualne platformy Azure są połączone z obwodem [usługi ExpressRoute,](https://azure.microsoft.com/services/expressroute/) który łączy się z lokalnymi zasobami sieciowymi.
- Obwód usługi ExpressRoute łączący się lokalnie z platformą Azure powinien mieć minimalną przepustowość [1 Gb/s lub wyższą.](https://azure.microsoft.com/pricing/details/expressroute/) Ta minimalna przepustowość umożliwia odpowiednią przepustowość do przesyłania danych między systemami lokalnymi i systemami uruchamiane na maszynach wirtualnych. Umożliwia również odpowiednią przepustowość dla połączenia z systemami platformy Azure od użytkowników lokalnych.
- Wszystkie systemy SAP na platformie Azure są skonfigurowane w sieciach wirtualnych do komunikowania się ze sobą.
- Usługa Active Directory i usługa DNS hostowane lokalnie są rozszerzane na platformę Azure za pośrednictwem usługi ExpressRoute z lokalnego lub są kompletne na platformie Azure.

W przypadku integracji dużych wystąpień HANA z siecią szkieletową centrum danych platformy Azure używana jest również technologia Azure ExpressRoute


> [!NOTE] 
> Tylko jedna subskrypcja platformy Azure może być połączona tylko z jedną dzierżawą w sygnatury wystąpienia dużego HANA w określonym regionie platformy Azure. Z drugiej strony jeden dzierżawy siekania dużych wystąpień HANA może być połączony tylko z jedną subskrypcją platformy Azure. To wymaganie jest zgodne z innymi obiektami podlegania rozliczania na platformie Azure.

Jeśli sap HANA na platformie Azure (duże wystąpienia) jest wdrażany w wielu różnych regionach platformy Azure, oddzielna dzierżawa jest wdrażana w sygnatury wystąpienia dużych HANA. Można uruchomić zarówno w ramach tej samej subskrypcji platformy Azure, tak długo, jak te wystąpienia są częścią tego samego środowiska SAP. 

> [!IMPORTANT] 
> Tylko metoda wdrażania usługi Azure Resource Manager jest obsługiwana przez sap HANA na platformie Azure (duże wystąpienia).

 

## <a name="additional-virtual-network-information"></a>Dodatkowe informacje o sieci wirtualnej

Aby połączyć sieć wirtualną z usługą ExpressRoute, należy utworzyć bramę usługi Azure ExpressRoute. Aby uzyskać więcej informacji, zobacz [Temat Bramy usługi Expressroute dla usługi ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Brama usługi Azure ExpressRoute jest używana z usługą ExpressRoute do infrastruktury poza platformą Azure lub do stempla dużego wystąpienia platformy Azure. Bramę usługi Azure ExpressRoute można połączyć z maksymalnie czterema różnymi obwodami usługi ExpressRoute, o ile połączenia te pochodzą z różnych routerów brzegowych firmy Microsoft. Aby uzyskać więcej informacji, zobacz [SAP HANA (Duże wystąpienia) infrastruktury i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Maksymalna przepustowość, którą można osiągnąć za pomocą bramy usługi ExpressRoute, wynosi 10 Gb/s przy użyciu połączenia usługi ExpressRoute. Kopiowanie plików między maszyną wirtualną, która znajduje się w sieci wirtualnej i systemu lokalnego (jako strumień pojedynczej kopii) nie osiąga pełnej przepływności różnych jednostek SKU bramy. Aby wykorzystać pełną przepustowość bramy usługi ExpressRoute, użyj wielu strumieni. Lub należy skopiować różne pliki w równoległych strumieniach pojedynczego pliku.


## <a name="networking-architecture-for-hana-large-instance"></a>Architektura sieci dla wystąpienia dużych hana
Architekturę sieci dla wystąpienia dużych HANA można podzielić na cztery różne części:

- Lokalne połączenie sieciowe i usługi ExpressRoute z platformą Azure. Ta część jest domeną klienta i jest połączona z platformą Azure za pośrednictwem usługi ExpressRoute. Ten obwód usługi Expressroute jest w pełni opłacany przez Ciebie jako klienta. Przepustowość powinna być wystarczająco duża, aby obsłużyć ruch sieciowy między zasobami lokalnymi a regionem platformy Azure, z którego nawiązujesz połączenie. Zobacz prawy dolny na poniższym rysunku.
- Usługi sieciowe platformy Azure, jak wcześniej wspomniano, z sieciami wirtualnymi, które ponownie wymagają dodanych bram usługi ExpressRoute. Ta część jest obszarem, w którym należy znaleźć odpowiednie projekty dla wymagań aplikacji, zabezpieczeń i zgodności. Czy używasz HANA Duże wystąpienie jest inny punkt do rozważenia pod względem liczby sieci wirtualnych i jednostek SKU bramy platformy Azure do wyboru. Zobacz prawy górny rączek na rysunku.
- Łączność wystąpienia dużych HANA za pośrednictwem technologii Usługi ExpressRoute na platformie Azure. Ta część jest wdrażana i obsługiwana przez firmę Microsoft. Wszystko, co musisz zrobić, to podać niektóre zakresy adresów IP po wdrożeniu zasobów w dużym wystąpieniu HANA połączyć obwód Usługi ExpressRoute do sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [SAP HANA (Duże wystąpienia) infrastruktury i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Nie ma żadnych dodatkowych opłat dla Ciebie jako klienta za łączność między siecią szkieletową centrum danych platformy Azure a jednostkami dużych wystąpień HANA.
- Sieci w obrębie sygnatury hana dużych wystąpień, który jest w większości przezroczyste dla Ciebie.

![Sieć wirtualna połączona z sap HANA na platformie Azure (duże wystąpienia) i lokalnie](./media/hana-overview-architecture/image1-architecture.png)

Wymóg, że zasoby lokalne muszą łączyć się za pośrednictwem usługi ExpressRoute z platformą Azure, nie zmienia się, ponieważ używasz wystąpienia dużego HANA. Wymaganie, aby mieć jedną lub wiele sieci wirtualnych, które uruchamiają maszyny wirtualne, które hostują warstwę aplikacji, która łączy się z wystąpieniami HANA hostowanych w jednostkach dużych wystąpień HANA, również nie zmienia się. 

Różnice między wdrożeniami SAP na platformie Azure są następujące:

- Jednostki hana dużych wystąpień dzierżawy klienta są połączone za pośrednictwem innego obwodu usługi ExpressRoute do sieci wirtualnych. Aby oddzielić warunki ładowania, lokalne obwody usługi ExpressRoute sieci lokalnej do platformy Azure i obwody między sieciami wirtualnymi platformy Azure a wystąpieniami dużymi HANA nie współużytkują te same routery.
- Profil obciążenia między warstwą aplikacji SAP a wystąpieniem dużym HANA ma inny charakter, z wieloma małymi żądaniami i seriami, takimi jak transfery danych (zestawy wyników) z sap HANA do warstwy aplikacji.
- Architektura aplikacji SAP jest bardziej wrażliwa na opóźnienia sieci niż typowe scenariusze, w których dane są wymieniane między lokalnymi i platformy Azure.
- Brama usługi Azure ExpressRoute ma co najmniej dwa połączenia usługi ExpressRoute. Jeden obwód, który jest podłączony z lokalnego i jeden, który jest połączony z hana dużych wystąpień. Pozostawia to tylko miejsce na kolejne dwa dodatkowe obwody z różnych MSEE do łączenia się z bramą usługi ExpressRoute. To ograniczenie jest niezależne od użycia szybkiej ścieżki usługi ExpressRoute. Wszystkie podłączone obwody mają maksymalną przepustowość dla przychodzących danych bramy usługi ExpressRoute.

W przypadku wersji 3 sygnatur dużych wystąpień HANA opóźnienie sieci między maszynami wirtualnymi a jednostkami dużych wystąpień HANA może być wyższe niż typowe opóźnienie w obie strony sieci VM-to-VM. W zależności od regionu platformy Azure zmierzone wartości mogą przekraczać opóźnienie 0,7 ms w obie strony sklasyfikowane jako poniżej średniej w [sap Note #1100926 — często zadawane pytania: Wydajność sieci](https://launchpad.support.sap.com/#/notes/1100926/E). W zależności od regionu platformy Azure i narzędzia do pomiaru opóźnienia sieci w obie strony między jednostką Azure VM i HANA Large Instance jednostki, zmierzone opóźnienie może wynosić do i około 2 milisekund. Niemniej jednak klienci pomyślnie wdrażają aplikacje SAP oparte na sap HANA w dużym wystąpieniu SAP HANA. Upewnij się, że dokładnie przetestować procesy biznesowe w azure HANA wystąpienie duże. Nowa funkcja o nazwie Szybka ścieżka usługi ExpressRoute jest w stanie znacznie zmniejszyć opóźnienie sieci między dużymi wystąpieniami HANA a maszynami wirtualnymi warstwy aplikacji na platformie Azure (zobacz poniżej). 

W przypadku wersji 4 sygnatur dużych wystąpień HANA opóźnienie sieci między maszynami wirtualnymi platformy Azure, które są wdrażane w pobliżu sygnatury dużych wystąpień HANA, jest doświadczane w celu spełnienia średniej lub lepszej niż średnia klasyfikacji, zgodnie z dokumentami w [sap Note #1100926 — CZĘSTO zadawane pytania: Wydajność sieci,](https://launchpad.support.sap.com/#/notes/1100926/E) jeśli skonfigurowano szybką ścieżkę usługi Azure ExpressRoute (zobacz poniżej). Aby wdrożyć maszyny wirtualne platformy Azure w pobliżu jednostek dużych wystąpień HANA w wersji 4, musisz korzystać z [grup umieszczania zbliżeniowego platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) Sposób, w jaki grupy miejsc docelowych zbliżeniowych mogą być używane do lokalizowania warstwy aplikacji SAP w tym samym centrum danych platformy Azure, co hostowane jednostki dużych wystąpień HANA w wersji 4, jest opisany w [grupach umieszczania zbliżeniowego platformy Azure w celu uzyskania optymalnego opóźnienia sieci w aplikacjach SAP.](sap-proximity-placement-scenarios.md)

Aby zapewnić deterministyczne opóźnienie sieci między maszynami wirtualnymi a wystąpieniem dużym HANA, wybór jednostki SKU bramy usługi ExpressRoute jest niezbędny. W przeciwieństwie do wzorców ruchu między lokalnymi i maszyn wirtualnych wzorzec ruchu między maszynami wirtualnymi i HANA Duże wystąpienie może rozwijać małe, ale wysokie serie żądań i woluminów danych do przesłania. Aby dobrze obsługiwać takie wybuchy, zdecydowanie zalecamy użycie jednostki SKU bramy UltraPerformance. Dla klasy typu II jednostek SKU dużych wystąpień HANA użycie jednostki SKU bramy UltraPerformance jako bramy usługi ExpressRoute jest obowiązkowe.

> [!IMPORTANT] 
> Biorąc pod uwagę ogólny ruch sieciowy między warstwami aplikacji SAP i bazy danych, tylko jednostki SKU bramy HighPerformance lub UltraPerformance dla sieci wirtualnych są obsługiwane do łączenia się z sap HANA na platformie Azure (duże wystąpienia). W przypadku jednostek SKU typu II dużych wystąpień HANA tylko jednostka SKU bramy UltraPerformance jest obsługiwana jako brama usługi ExpressRoute. Wyjątki mają zastosowanie podczas korzystania z szybkiej ścieżki usługi ExpressRoute (patrz poniżej)

### <a name="expressroute-fast-path"></a>Szybka ścieżka usługi ExpressRoute
Aby zmniejszyć opóźnienie, usługa ExpressRoute Fast Path została wprowadzona i wydana w maju 2019 r. dla określonej łączności dużych wystąpień HANA z sieciami wirtualnymi platformy Azure, które hostują maszyny wirtualne aplikacji SAP. Główną różnicą w rozwiązaniu wdrożonym do tej pory jest, że przepływy danych między maszynami wirtualnymi i wystąpieniami dużymi HANA nie są już kierowane za pośrednictwem bramy usługi ExpressRoute. Zamiast tego maszyny wirtualne przypisane w podsieci sieci wirtualnej platformy Azure komunikują się bezpośrednio z dedykowanym routerem brzegowym przedsiębiorstwa. 

> [!IMPORTANT] 
> Funkcja szybkiej ścieżki usługi ExpressRoute wymaga, aby podsieci z uruchomionymi maszynami wirtualnymi aplikacji SAP znajdują się w tej samej sieci wirtualnej platformy Azure, która została połączona z dużymi wystąpieniami HANA. Maszyny wirtualne znajdujące się w sieciach wirtualnych platformy Azure, które są równorzędne z siecią wirtualną platformy Azure podłączoną bezpośrednio do jednostek dużych wystąpień HANA, nie korzystają z szybkiej ścieżki usługi ExpressRoute. W rezultacie typowe projekty sieci wirtualnych koncentratora i szprychy, w których obwody usługi ExpressRoute łączą się z siecią wirtualną koncentratora, a sieci wirtualne zawierające warstwę aplikacji SAP (szprychy) są równorzędne, optymalizacja przez usługę ExpressRoute Fast Path nie będzie działać. W dodatku szybka ścieżka usługi ExpressRoute nie obsługuje obecnie reguł routingu zdefiniowanych przez użytkownika (UDR). Aby uzyskać więcej informacji, zobacz [Brama sieci wirtualnej usługi ExpressRoute i FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


Aby uzyskać więcej informacji na temat konfigurowania szybkiej ścieżki usługi ExpressRoute, przeczytaj dokument [Podłącz sieć wirtualną do wystąpień dużych HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> Brama usługi UltraPerformance ExpressRoute jest wymagana do pracy z szybką ścieżką usługi ExpressRoute


## <a name="single-sap-system"></a>Pojedynczy system SAP

Infrastruktura lokalna wcześniej pokazana jest połączona za pośrednictwem usługi ExpressRoute na platformie Azure. Obwód usługi ExpressRoute łączy się z routerem microsoft enterprise edge (MSEE). Aby uzyskać więcej informacji, zobacz [Omówienie techniczne usługi ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Po ustanowieniu trasy łączy się z szkieletem platformy Azure.

> [!NOTE] 
> Aby uruchomić krajobrazy SAP na platformie Azure, połącz się z routerem brzegowym przedsiębiorstwa najbliżej regionu platformy Azure w krajobrazie SAP. Sygnatury dużych wystąpień HANA są połączone za pośrednictwem dedykowanych urządzeń routera brzegowego przedsiębiorstwa w celu zminimalizowania opóźnień sieci między maszynami wirtualnymi w znakach azure IaaS i HANA Large Instance.

Brama usługi ExpressRoute dla maszyn wirtualnych, które hostują wystąpienia aplikacji SAP, jest połączona z jednym obwodem usługi ExpressRoute, który łączy się z lokalnymi urządzeniami. Ta sama sieć wirtualna jest podłączona do oddzielnego routera brzegowego przedsiębiorstwa przeznaczonego do łączenia się ze znaczkami dużych wystąpień. Korzystając z szybkiej ścieżki usługi ExpressRoute, przepływ danych z dużych wystąpień HANA do maszyn wirtualnych warstwy aplikacji SAP nie są już kierowane za pośrednictwem bramy usługi ExpressRoute, co zmniejsza opóźnienia w obie strony sieci.

Ten system jest prostym przykładem jednego systemu SAP. Warstwa aplikacji SAP jest hostowana na platformie Azure. Baza danych SAP HANA jest uruchamiana na platformie SAP HANA na platformie Azure (duże wystąpienia). Założenie jest takie, że przepustowość bramy usługi ExpressRoute przepływności 2 Gb/s lub 10 Gb/s nie stanowi wąskiego gardła.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Wiele systemów SAP lub dużych systemów SAP

Jeśli wiele systemów SAP lub dużych systemów SAP są wdrażane w celu łączenia się z SAP HANA na platformie Azure (duże wystąpienia), przepływność bramy usługi ExpressRoute może stać się wąskim gardłem. Lub chcesz izolować systemy produkcyjne i nieprodukcyjne w różnych sieciach wirtualnych platformy Azure. W takim przypadku podziel warstwy aplikacji na wiele sieci wirtualnych. Można również utworzyć specjalną sieć wirtualną, która łączy się z HANA Duże wystąpienie w przypadkach takich jak:

- Wykonywanie kopii zapasowych bezpośrednio z wystąpień HANA w wystąpieniu dużym HANA do maszyny Wirtualnej na platformie Azure, która obsługuje udziały NFS.
- Kopiowanie dużych kopii zapasowych lub innych plików z jednostek dużych wystąpień HANA na miejsce na dysku zarządzanym na platformie Azure.

Użyj oddzielnej sieci wirtualnej do hostowania maszyn wirtualnych, które zarządzają magazynem masowego transferu danych między dużymi wystąpieniami HANA a platformą Azure. To rozwiązanie pozwala uniknąć skutków transferu dużych plików lub danych z wystąpienia dużych HANA na platformę Azure na bramie usługi ExpressRoute, która obsługuje maszyny wirtualne, które uruchamiają warstwę aplikacji SAP. 

Aby uzyskać bardziej skalowalną architekturę sieci:

- Wykorzystaj wiele sieci wirtualnych dla jednej, większej warstwy aplikacji SAP.
- Wdrożenie jednej oddzielnej sieci wirtualnej dla każdego wdrożonego systemu SAP w porównaniu z połączeniem tych systemów SAP w oddzielnych podsieciach w tej samej sieci wirtualnej.

  Bardziej skalowalna architektura sieci dla sap HANA na platformie Azure (duże wystąpienia):

![Wdrażanie warstwy aplikacji SAP w wielu sieciach wirtualnych](./media/hana-overview-architecture/image4-networking-architecture.png)

W zależności od reguł i ograniczeń, należy zastosować między różnymi sieciami wirtualnymi obsługującymi maszyny wirtualne różnych systemów SAP, należy równorzędnie tych sieci wirtualnych. Aby uzyskać więcej informacji na temat komunikacji równorzędnej w sieci wirtualnej, zobacz [Komunikacja równorzędna sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Routing na platformie Azure

Domyślnie wdrożenie, trzy zagadnienia routingu sieciowego są ważne dla sap HANA na platformie Azure (duże wystąpienia):

* Sap HANA na platformie Azure (duże wystąpienia) można uzyskać tylko za pośrednictwem maszyn wirtualnych platformy Azure i dedykowanego połączenia Usługi ExpressRoute, a nie bezpośrednio z lokalnego. Bezpośredni dostęp z lokalnego do jednostek dużych wystąpień HANA, dostarczonych przez firmę Microsoft do Ciebie, nie jest możliwy natychmiast. Ograniczenia routingu przechodniego są spowodowane bieżącą architekturą sieci platformy Azure używaną dla dużego wystąpienia SAP HANA. Niektórzy klienci administracyjni i wszystkie aplikacje, które potrzebują bezpośredniego dostępu, takie jak SAP Solution Manager działający lokalnie, nie mogą łączyć się z bazą danych SAP HANA. Dla wyjątków sprawdź sekcję "Routing bezpośredni do dużych wystąpień HANA".

* Jeśli masz hana jednostek wystąpienia wdrożone w dwóch różnych regionach platformy Azure do odzyskiwania po awarii, te same przejściowe ograniczenia routingu stosowane w przeszłości. Innymi słowy adresy IP jednostki dużych wystąpień HANA w jednym regionie (na przykład zachodnie stany USA) nie były kierowane do jednostki dużego wystąpienia HANA rozmieszczonej w innym regionie (na przykład na wschód stanów USA). To ograniczenie było niezależne od używania komunikacji równorzędnej sieci platformy Azure w różnych regionach lub wzajemnego łączenia obwodów usługi ExpressRoute łączących jednostki dużych wystąpień HANA z sieciami wirtualnymi. Aby uzyskać reprezentację graficzną, zobacz rysunek w sekcji "Użyj jednostek dużych wystąpień HANA w wielu regionach". To ograniczenie, które zostało wprowadzone wraz z wdrożoną architekturą, zabroniło natychmiastowego użycia replikacji systemu HANA jako funkcji odzyskiwania po awarii. W przypadku ostatnich zmian wyszukuj sekcję "Użyj jednostek dużych wystąpień HANA w wielu regionach". 

* Urządzenia SAP HANA na platformie Azure (duże wystąpienia) mają przypisany adres IP z zakresu adresów puli adresów IP serwera, który został przesłany podczas żądania wdrożenia wystąpienia dużych hana. Aby uzyskać więcej informacji, zobacz [SAP HANA (Duże wystąpienia) infrastruktury i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ten adres IP jest dostępny za pośrednictwem subskrypcji platformy Azure i obwodu, który łączy sieci wirtualne platformy Azure z wystąpieniami dużymi hana. Adres IP przypisany poza zakresem adresów puli adresów IP serwera jest bezpośrednio przypisany do jednostki sprzętowej. Nie *jest* już przypisany za pośrednictwem translatora kluczy, jak miało to miejsce w przypadku pierwszych wdrożeń tego rozwiązania. 

### <a name="direct-routing-to-hana-large-instances"></a>Routing bezpośredni do dużych wystąpień HANA

Domyślnie routing przechodni nie działa w następujących scenariuszach:

* Między jednostkami dużych wystąpień HANA a wdrożeniem lokalnym.

* Między hana dużych wystąpień routingu, które są wdrażane w dwóch różnych regionach.

Istnieją trzy sposoby włączania routingu przechodniego w tych scenariuszach:

- Odwrotny serwer proxy do kierowania danych, do i z. Na przykład F5 BIG-IP, NGINX z usługą Traffic Manager wdrożony w sieci wirtualnej platformy Azure, która łączy się z dużych wystąpień HANA i lokalnie jako rozwiązanie wirtualnej zapory/routingu ruchu.
- Używanie [reguł IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) na maszynie wirtualnej systemu Linux w celu umożliwienia routingu między lokalizacjami lokalnymi i jednostkami dużych wystąpień HANA lub między jednostkami dużych wystąpień HANA w różnych regionach. Maszyny Wirtualnej z uruchomionymi tabelami IPTables należy wdrożyć w sieci wirtualnej platformy Azure, która łączy się z dużymi wystąpieniami HANA i lokalnymi. Maszyna wirtualna musi być odpowiednio dobrany, tak, że przepustowość sieci maszyny Wirtualnej jest wystarczająca dla oczekiwanego ruchu sieciowego. Aby uzyskać szczegółowe informacje na temat przepustowości sieci maszyn wirtualnych, zapoznaj się z artykułem [Rozmiary maszyn wirtualnych systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)na platformie Azure .
- [Zapora azure](https://azure.microsoft.com/services/azure-firewall/) będzie innym rozwiązaniem, aby włączyć ruch bezpośredni między jednostkami lokalnymi i HANA dużych wystąpień. 

Cały ruch tych rozwiązań będzie kierowany za pośrednictwem sieci wirtualnej platformy Azure i jako taki ruch może być dodatkowo ograniczony przez urządzenia miękkie używane lub przez grupy zabezpieczeń sieciowej platformy Azure, dzięki czemu niektóre adresy IP lub adresy IP mogą być blokowane lub jawnie dozwolone dostęp do dużych wystąpień HANA. 

> [!NOTE]  
> Należy pamiętać, że implementacja i obsługa rozwiązań niestandardowych obejmujących urządzenia sieciowe innych firm lub tabele IPTables nie jest dostarczana przez firmę Microsoft. Wsparcie musi być zapewnione przez dostawcę używanego składnika lub integratora. 

#### <a name="express-route-global-reach"></a>Globalny zasięg trasy ekspresowej
Firma Microsoft wprowadziła nową funkcję o nazwie [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Globalny zasięg może służyć do dużych wystąpień HANA w dwóch scenariuszach:

- Włącz bezpośredni dostęp z lokalnego do jednostek dużych wystąpień HANA wdrożonych w różnych regionach
- Włącz bezpośrednią komunikację między jednostkami dużych wystąpień HANA wdrożonymi w różnych regionach


##### <a name="direct-access-from-on-premises"></a>Bezpośredni dostęp z lokalnego
W regionach platformy Azure, w których oferowany jest globalny zasięg, można zażądać włączenia funkcji globalnego zasięgu dla obwodu usługi ExpressRoute, który łączy sieć lokalną z siecią wirtualną platformy Azure, która łączy się również z jednostkami dużych wystąpień HANA. Istnieją pewne konsekwencje kosztowe dla lokalnej strony obwodu usługi ExpressRoute. W przypadku cen sprawdź ceny dodatku [Global Reach](https://azure.microsoft.com/pricing/details/expressroute/). Nie ma żadnych dodatkowych kosztów związanych z obwodem, który łączy jednostki dużych wystąpień HANA z platformą Azure. 

> [!IMPORTANT]  
> W przypadku korzystania z globalnego zasięgu w celu umożliwienia bezpośredniego dostępu między jednostkami dużych wystąpień HANA a zasobami lokalnymi, przepływ danych i kontroli sieciowej nie jest **kierowany za pośrednictwem sieci wirtualnych platformy Azure,** ale bezpośrednio między routerami wymiany przedsiębiorstwa firmy Microsoft. W rezultacie wszelkie reguły sieciowej grupy sieciowej lub ASG lub dowolnego typu zapory, urządzenia WUS lub serwera proxy wdrożonego w sieci wirtualnej platformy Azure nie są dotykane. **Jeśli korzystasz z usługi ExpressRoute Global Reach, aby włączyć bezpośredni dostęp z lokalnego do hana Duże jednostki wystąpienia ograniczenia i uprawnienia dostępu hana dużych jednostek wystąpienia muszą być zdefiniowane w zapory lokalnej po stronie lokalnej** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Łączenie dużych wystąpień HANA w różnych regionach platformy Azure
W ten sam sposób, ponieważ usługa ExpressRoute Global Reach może służyć do łączenia lokalnego z jednostkami dużych wystąpień HANA, może służyć do łączenia dwóch dzierżaw dużych wystąpień HANA, które są wdrażane dla Ciebie w dwóch różnych regionach. Izolacja jest obwodów usługi ExpressRoute, które dzierżawy dużych wystąpień HANA są używane do łączenia się z platformą Azure w obu regionach. Nie ma żadnych dodatkowych opłat za łączenie dwóch dzierżaw dużych wystąpień HANA, które są wdrażane w dwóch różnych regionach. 

> [!IMPORTANT]  
> Przepływ danych i przepływ sterowania ruchu sieciowego między różnymi dzierżawami hana dużych wystąpień nie będą kierowane za pośrednictwem sieci platformy Azure. W rezultacie nie można użyć funkcji platformy Azure lub urządzeń WDO do wymuszania ograniczeń komunikacji między dwoma dzierżawami dużych wystąpień HANA. 

Aby uzyskać więcej informacji na temat włączania globalnego zasięgu usługi ExpressRoute, przeczytaj dokument [Podłączanie sieci wirtualnej do wystąpień dużych HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Łączność z Internetem hana duże wystąpienie
Hana Duże wystąpienie *nie* ma bezpośredniej łączności z Internetem. Na przykład to ograniczenie może ograniczyć możliwość rejestrowania obrazu systemu operacyjnego bezpośrednio u dostawcy systemu operacyjnego. Może być konieczne wykonanie pracy z lokalnym serwerem narzędzi do zarządzania subskrypcją systemu Linux Linux Enterprise Server lub menedżerem subskrypcji Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Szyfrowanie danych między maszynami wirtualnymi a dużym wystąpieniem HANA
Dane przesyłane między wystąpieniem dużym HANA a maszynami wirtualnymi nie są szyfrowane. Jednak wyłącznie w przypadku wymiany między hana dbms stronie i JDBC /ODBC aplikacji opartych na, można włączyć szyfrowanie ruchu. Aby uzyskać więcej informacji, zobacz [tę dokumentację firmy SAP](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Używanie jednostek dużych wystąpień HANA w wielu regionach

Aby zrealizować konfigurację odzyskiwania po awarii, musisz mieć jednostki dużych wystąpień SHANA w wielu regionach platformy Azure. Nawet przy użyciu platformy Azure [Global Vnet Peering], przechodnie routingu domyślnie nie działa między dzierżawami dużych wystąpień HANA w dwóch różnych regionach. Jednak globalny zasięg otwiera ścieżkę komunikacji między jednostkami dużych wystąpień HANA, które zostały zainicjowane w dwóch różnych regionach. Ten scenariusz użycia usługi ExpressRoute Global Reach umożliwia:

 - Replikacja systemu HANA bez żadnych dodatkowych serwerów proxy lub zapór
 - Kopiowanie kopii zapasowych między jednostkami dużych wystąpień HANA w dwóch różnych regionach w celu wykonywania kopii systemowych lub odświeżania systemu


![Sieć wirtualna połączona ze znacznikami dużych wystąpień platformy Azure w różnych regionach platformy Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

Rysunek pokazuje, jak różne sieci wirtualne w obu regionach są połączone z dwoma różnymi obwodami usługi ExpressRoute, które są używane do łączenia się z sap HANA na platformie Azure (duże wystąpienia) w obu regionach platformy Azure (szare linie). Powodem tych dwóch połączeń krzyżowych jest ochrona przed awarią MSEE po obu stronach. Przepływ komunikacji między dwiema sieciami wirtualnymi w dwóch regionach platformy Azure ma być obsługiwany przez [globalną komunikację równorzędną](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) dwóch sieci wirtualnych w dwóch różnych regionach (niebieska linia kropkowana). Gruba czerwona linia opisuje połączenie Globalny zasięg usługi ExpressRoute, które umożliwia jednostkom dużych wystąpień HANA dzierżawców w dwóch różnych regionach komunikowanie się ze sobą. 

> [!IMPORTANT] 
> Jeśli używano wielu obwodów usługi ExpressRoute, ustawienia as path prepending i Local Preference BGP powinny być używane w celu zapewnienia prawidłowego routingu ruchu.

**Następne kroki**
- Odwołuje się do [architektury magazynu SAP HANA (duże wystąpienia)](hana-storage-architecture.md)
