---
title: Konfiguracje i operacje infrastruktury SAP HANA na platformie Azure | Dokumenty firmy Microsoft
description: Przewodnik po operacjach dla systemów SAP HANA, które są wdrażane na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c4f3ec2727d06528eab788a2a24a6190fe26533
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606140"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure
Ten dokument zawiera wskazówki dotyczące konfigurowania infrastruktury platformy Azure i obsługi systemów SAP HANA, które są wdrażane na natywnych maszynach wirtualnych platformy Azure(VM). Dokument zawiera również informacje o konfiguracji dla sap HANA skalowaw w poziomie dla jednostki SKU maszyny Wirtualnej M128s. Ten dokument nie ma na celu zastąpienia standardowej dokumentacji SAP, która zawiera następującą zawartość:

- [Przewodnik administracyjny SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Przewodniki instalacji SAP](https://service.sap.com/instguides)
- [Uwagi SAP](https://service.sap.com/notes)

## <a name="prerequisites"></a>Wymagania wstępne
Aby skorzystać z tego przewodnika, potrzebujesz podstawowej wiedzy na temat następujących składników platformy Azure:

- [Maszyny wirtualne platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Sieci sieci platformy Azure i sieci wirtualne](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Aby dowiedzieć się więcej o SAP NetWeaver i innych składnikach SAP na platformie Azure, zobacz sekcję [SAP na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) w dokumentacji platformy [Azure.](https://docs.microsoft.com/azure/)

## <a name="basic-setup-considerations"></a>Podstawowe zagadnienia dotyczące konfiguracji
W poniższych sekcjach opisano podstawowe zagadnienia dotyczące instalacji wdrażania systemów SAP HANA na maszynach wirtualnych platformy Azure.

### <a name="connect-into-azure-virtual-machines"></a>Łączenie się z maszynami wirtualnymi platformy Azure
Jak udokumentowano w [przewodniku planowania maszyn wirtualnych platformy Azure,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)istnieją dwie podstawowe metody łączenia się z maszynami wirtualnymi platformy Azure:

- Połącz się za pośrednictwem Internetu i publicznych punktów końcowych na maszynie Wirtualnej skoku lub na maszynie Wirtualnej z systemem SAP HANA.
- Połącz się za pośrednictwem [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) lub usługi Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Łączność lokacja-lokacja za pośrednictwem sieci VPN lub usługi ExpressRoute jest niezbędna w scenariuszach produkcyjnych. Ten typ połączenia jest również potrzebny w przypadku scenariuszy nieprodukcyjnych, które są przesyłane do scenariuszy produkcyjnych, w których używane jest oprogramowanie SAP. Na poniższej ilustracji przedstawiono przykład łączności między lokacjami:

![Łączność między lokacjami](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Wybieranie typów maszyn wirtualnych platformy Azure
Typy maszyn wirtualnych platformy Azure, które mogą być używane w scenariuszach produkcyjnych, są wymienione w [dokumentacji SAP dla usługi IAAS.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html) W scenariuszach nieprodukcyjnych dostępna jest szersza różnorodność natywnych typów maszyn wirtualnych platformy Azure.

>[!NOTE]
> W przypadku scenariuszy nieprodukcyjnych należy użyć typów maszyn wirtualnych wymienionych w [#1928533 notatki SAP](https://launchpad.support.sap.com/#/notes/1928533). Aby uzyskać zastosowanie maszyn wirtualnych platformy Azure w scenariuszach produkcyjnych, sprawdź, czy na [liście Platform certyfikowanych przez](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)USŁUGĘ SAP HANA nie ma certyfikatów maszyn wirtualnych.

Wdrażanie maszyn wirtualnych na platformie Azure przy użyciu:

- Witryna Azure Portal.
- Polecenia cmdlet programu Azure PowerShell.
- Interfejsu wiersza polecenia platformy Azure.

Można również wdrożyć kompletną zainstalowaną platformę SAP HANA na usługach maszyny wirtualnej platformy Azure za pośrednictwem [platformy SAP Cloud.](https://cal.sap.com/) Proces instalacji jest opisany w [deploy SAP S/4HANA lub BW/4HANA na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) lub z automatyzacją wydaną w tym [miejscu.](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)

>[!IMPORTANT]
> Aby używać M208xx_v2 maszyn wirtualnych, należy zachować ostrożność wybierając obraz systemu Linux z galerii obrazów maszyny Wirtualnej platformy Azure. Aby przeczytać szczegóły, przeczytaj artykuł [Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci.](../../mv2-series.md)
> 


### <a name="storage-configuration-for-sap-hana"></a>Konfiguracja magazynu dla sap HANA
W przypadku konfiguracji magazynu i typów magazynów używanych z sap HANA na platformie Azure należy zapoznać się z [dokumentem Konfiguracje magazynu maszyn wirtualnych SAP HANA Azure](./hana-vm-operations-storage.md)


### <a name="set-up-azure-virtual-networks"></a>Konfigurowanie sieci wirtualnych platformy Azure
Jeśli masz łączność lokacja-lokacja z platformą Azure za pośrednictwem sieci VPN lub usługi ExpressRoute, musisz mieć co najmniej jedną sieć wirtualną platformy Azure, która jest połączona za pośrednictwem bramy wirtualnej z siecią VPN lub obwodem usługi ExpressRoute. W prostych wdrożeniach bramy wirtualnej można wdrożyć w podsieci sieci wirtualnej platformy Azure, która obsługuje również wystąpienia SAP HANA. Aby zainstalować sap HANA, należy utworzyć dwie dodatkowe podsieci w sieci wirtualnej platformy Azure. Jedna podsieć obsługuje maszyny wirtualne do uruchamiania wystąpień SAP HANA. W drugiej podsieci uruchomi się jumpbox lub zarządzania maszyn wirtualnych do obsługi SAP HANA Studio, inne oprogramowanie do zarządzania lub oprogramowania aplikacji.

> [!IMPORTANT]
> Ze względu na funkcjonalność, ale co ważniejsze ze względu na wydajność, nie jest obsługiwana konfiguracja [urządzeń wirtualnych sieci Azure](https://azure.microsoft.com/solutions/network-appliances/) w ścieżce komunikacji między aplikacją SAP a warstwą DBMS systemu SAP opartego na sap netweaver, Hybris lub S/4HANA. Komunikacja między warstwą aplikacji SAP a warstwą DBMS musi być bezpośrednia. Ograniczenie nie obejmuje [reguł ASG i sieciowej platformy Azure,](https://docs.microsoft.com/azure/virtual-network/security-overview) o ile te reguły ASG i sieciowej sieciowej umożliwiają bezpośrednią komunikację. Inne scenariusze, w których urządzenia NVA nie są obsługiwane, znajdują się w ścieżkach komunikacji między maszynami wirtualnymi platformy Azure reprezentującymi węzły klastra rozrusznika serca systemu Linux a urządzeniami SBD, zgodnie z opisem w [sekcji Wysoka dostępność dla maszyn wirtualnych SAP NetWeaver na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Lub w ścieżkach komunikacji między maszynami wirtualnymi platformy Azure a systemem SOFS systemu Windows Server skonfigurowanym zgodnie z opisem w [programie Cluster wystąpienie SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udziału plików na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). Urządzenia w ścieżkach komunikacyjnych mogą łatwo podwoić opóźnienie sieci między dwoma partnerami komunikacyjnymi, mogą ograniczać przepływność w ścieżkach krytycznych między warstwą aplikacji SAP a warstwą DBMS. W niektórych scenariuszach obserwowanych z klientami, urządzenia NVA może spowodować, że klastry Systemu Linux rozrusznika zakończy się niepowodzeniem w przypadkach, gdy komunikacja między węzłami klastra rozrusznika systemu Linux muszą komunikować się z ich urządzenia SBD za pośrednictwem urządzenia WUS.  
> 

> [!IMPORTANT]
> Inny projekt, który **nie** jest obsługiwany jest segregacja warstwy aplikacji SAP i warstwy DBMS do różnych sieci wirtualnych platformy Azure, które nie są [równorzędne](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ze sobą. Zaleca się segregować warstwę aplikacji SAP i warstwę DBMS przy użyciu podsieci w sieci wirtualnej platformy Azure zamiast korzystać z różnych sieci wirtualnych platformy Azure. Jeśli zdecydujesz się nie stosować się do zalecenia, a zamiast tego segregować dwie warstwy w różnych sieci wirtualnych, dwie sieci wirtualne muszą być [równorzędne](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Należy pamiętać, że ruch sieciowy między dwiema [równorzędnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) sieci wirtualnych platformy Azure podlegają kosztom transferu. Dzięki ogromnemu woluminowi danych w wielu terabajtach wymienianych między warstwą aplikacji SAP a warstwą DBMS można zgromadzić znaczne koszty, jeśli warstwa aplikacji SAP i warstwa DBMS są segregowane między dwiema równorzędnymi sieciami wirtualnymi platformy Azure. 

Po zainstalowaniu maszyn wirtualnych do uruchomienia sap HANA, maszyny wirtualne muszą:

- Zainstalowano dwie wirtualne karty sieciowe: jedną kartę sieciową do łączenia się z podsiecią zarządzania i jedną kartę sieciową do łączenia się z siecią lokalną lub innymi sieciami z wystąpieniem SAP HANA na maszynie Wirtualnej platformy Azure.
- Statyczne prywatne adresy IP, które są wdrażane dla obu wirtualnych kart sieciowych.

> [!NOTE]
> Należy przypisać statyczne adresy IP za pośrednictwem platformy Azure oznacza do poszczególnych vNICs. Nie należy przypisywać statycznych adresów IP w ramach systemu operacyjnego gościa do vNIC. Niektóre usługi platformy Azure, takie jak usługa Azure Backup Service, opierają się na tym, że co najmniej podstawowa usługa vNIC jest ustawiona na usługę DHCP, a nie na statyczne adresy IP. Zobacz też dokument [Rozwiązywanie problemów z tworzeniem kopii zapasowej maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Jeśli trzeba przypisać wiele statycznych adresów IP do maszyny Wirtualnej, należy przypisać wiele maszyn wirtualnych do maszyny Wirtualnej.
>
>

Jednak w przypadku wdrożeń, które są trwałe, należy utworzyć architekturę sieci wirtualnego centrum danych na platformie Azure. Ta architektura zaleca oddzielenie bramy sieci wirtualnej platformy Azure, która łączy się z lokalnymi w oddzielnej sieci wirtualnej platformy Azure. Ta oddzielna sieci wirtualnej powinna obsługiwać cały ruch, który pozostawia w środowisku lokalnym lub w Internecie. Takie podejście umożliwia wdrażanie oprogramowania do inspekcji i rejestrowania ruchu, który wchodzi do wirtualnego centrum danych na platformie Azure w tej oddzielnej sieci wirtualnej centrum. Więc masz jedną witrynę wirtualną, która obsługuje wszystkie oprogramowanie i konfiguracje, które odnosi się do ruchu w- i wychodzących do wdrożenia platformy Azure.

Artykuły [Wirtualne centrum danych platformy Azure: Perspektywa sieciowa](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) i [wirtualne centrum danych platformy Azure oraz płaszczyzna sterowania przedsiębiorstwa](https://docs.microsoft.com/azure/architecture/vdc/) zapewniają więcej informacji na temat podejścia wirtualnego centrum danych i powiązanego projektu sieci wirtualnej platformy Azure.


>[!NOTE]
>Ruch przepływający między hubową siecią wirtualną i szprychową siecią wirtualną przy użyciu [komunikacji równorzędnej sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) podlega dodatkowym [kosztom.](https://azure.microsoft.com/pricing/details/virtual-network/) Na podstawie tych kosztów może być konieczne rozważenie wprowadzenia kompromisów między uruchamianiem ścisłego projektowania sieci koncentratora i szprychy a uruchamianiem wielu [bram usługi Azure ExpressRoute,](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) które łączą się z "szprychami", aby ominąć komunikację równorzędną sieci wirtualnej. Jednak bramy usługi Azure ExpressRoute wprowadzają również dodatkowe [koszty.](https://azure.microsoft.com/pricing/details/vpn-gateway/) Mogą również wystąpić dodatkowe koszty oprogramowania innych firm używanego do rejestrowania ruchu sieciowego, inspekcji i monitorowania. W zależności od kosztów wymiany danych za pośrednictwem komunikacji równorzędnej sieci wirtualnej z jednej strony i kosztów utworzonych przez dodatkowe bramy usługi Azure ExpressRoute i dodatkowe licencje na oprogramowanie, można zdecydować o mikrosegmentacji w jednej sieci wirtualnej przy użyciu podsieci jako jednostki izolacji zamiast sieci wirtualnych.


Aby uzyskać omówienie różnych metod przypisywania adresów IP, zobacz [typy adresów IP i metody alokacji na platformie Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

W przypadku maszyn wirtualnych z systemem SAP HANA należy pracować z przypisanymi statycznymi adresami IP. Powodem jest to, że niektóre atrybuty konfiguracji dla adresów IP odniesienia HANA.

[Grupy zabezpieczeń sieciowej platformy Azure (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) są używane do kierowania ruchem kierowanym do wystąpienia SAP HANA lub jumpbox. Sieciowe grupy zabezpieczeń sieciowych i grupy [zabezpieczeń aplikacji](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) są skojarzone z podsiecią SAP HANA i podsiecią Zarządzanie.

Na poniższej ilustracji przedstawiono omówienie schematu wdrożenia szorstko dla systemu SAP HANA zgodnie z architekturą sieci wirtualnej koncentratora i szprychy:

![Schemat wdrożenia szorstko dla systemu SAP HANA](media/hana-vm-operations/hana-simple-networking-dmz.png)

Aby wdrożyć sap HANA na platformie Azure bez połączenia lokacja lokacja, nadal chcesz chronić wystąpienie SAP HANA przed publicznym Internetem i ukryć je za serwerem proxy do przodu. W tym podstawowym scenariuszu wdrożenie opiera się na wbudowanych usługach DNS platformy Azure, aby rozwiązać nazwy hostów. W bardziej złożonym wdrożeniu, w którym używane są publiczne adresy IP, usługi DNS wbudowane w platformę Azure są szczególnie ważne. Użyj usług Azure NSG i [usługi Azure NVAs](https://azure.microsoft.com/solutions/network-appliances/) do kontrolowania, monitorowania routingu z Internetu do architektury sieci wirtualnej platformy Azure na platformie Azure. Na poniższej ilustracji przedstawiono schemat szorstki do wdrażania sap HANA bez połączenia lokacja lokacja w architekturze sieci wirtualnej koncentratora i szprychy:
  
![Schemat wdrażania szorstko dla systemu SAP HANA bez połączenia lokacja lokacja](media/hana-vm-operations/hana-simple-networking-dmz.png)
 

Inny opis sposobu używania urządzeń ez/s azure do kontrolowania i monitorowania dostępu z Internetu bez architektury koncentratora i sieci wirtualnej szprychy można znaleźć w artykule [Wdrażanie wysoce dostępnych urządzeń wirtualnych sieciowych.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Konfigurowanie infrastruktury platformy Azure dla skalowania w poziomie SAP HANA
Aby dowiedzieć się, typy maszyn wirtualnych platformy Azure, które są certyfikowane dla skalowania W poziomie OLAP lub S/4HANA skalowawczo w poziomie, sprawdź [katalog sprzętu SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Znacznik wyboru w kolumnie "Klastrowanie" wskazuje obsługę skalowania w poziomie. Typ aplikacji wskazuje, czy jest obsługiwana skalowanie W poziomie OLAP czy skalowanie W poziomie S/4HANA. Aby uzyskać szczegółowe informacje na temat węzłów certyfikowanych w skali w poziomie dla każdej z maszyn wirtualnych, sprawdź szczegóły wpisów w określonej jednostce SKU maszyny Wirtualnej wymienionej w katalogu sprzętowym SAP HANA.

Minimalne wersje systemu operacyjnego do wdrażania konfiguracji skalowawcza w poziomie na maszynach wirtualnych platformy Azure, sprawdź szczegóły wpisów w określonej jednostce SKU maszyny Wirtualnej wymienione w katalogu sprzętowym SAP HANA. W konfiguracji skalowania W poziomie OLAP n węzła jeden węzeł działa jako węzeł główny. Inne węzły do limitu certyfikacji działają jako węzeł procesu roboczego. Dodatkowe węzły rezerwowe nie są wliczane do liczby certyfikowanych węzłów

>[!NOTE]
> Wdrożenia skalowania w poziomie maszyny Wirtualnej platformy Azure w systemie SAP HANA z węzłem wstrzymania są możliwe tylko przy użyciu magazynu [plików NetApp platformy Azure.](https://azure.microsoft.com/services/netapp/) Żaden inny magazyn platformy Azure z certyfikatem SAP HANA nie umożliwia konfiguracji węzłów rezerwowych SAP HANA
>

W przypadku usługi /hana/shared zalecamy również użycie [plików NetApp platformy Azure](https://azure.microsoft.com/services/netapp/). 

Typowy podstawowy projekt dla pojedynczego węzła w konfiguracji skalowawki w poziomie będzie wyglądać następująco:

![Podstawy skalowania w poziomie pojedynczego węzła](media/hana-vm-operations/scale-out-basics-anf-shared.PNG)

Podstawowa konfiguracja węzła maszyny Wirtualnej dla skalowania SAP HANA w poziomie wygląda następująco:

- W przypadku **usługi /hana/shared**należy użyć natywnej usługi NFS świadczonej za pośrednictwem usługi Azure NetApp Files. 
- Wszystkie inne woluminy dysków nie są współużytkowane przez różne węzły i nie są oparte na nfs. Konfiguracje instalacji i kroki dla skalowania w poziomie instalacji HANA z nieuwzdnionych **/hana/data** i **/hana/log** jest dalej w tym dokumencie. W przypadku magazynu z certyfikatem HANA, który może być używany, zapoznaj się z artykułem [Konfiguracje magazynu maszyn wirtualnych platformy SAP HANA Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)


Rozmiar woluminów lub dysków, należy sprawdzić dokument [SAP HANA TDI Wymagania dotyczące magazynu,](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)dla rozmiaru wymaganego w zależności od liczby węzłów procesu roboczego. Dokument publikuje formułę, którą należy zastosować, aby uzyskać wymaganą pojemność woluminu

Inne kryteria projektowe, które są wyświetlane w grafice konfiguracji pojedynczego węzła dla skalowania w poziomie SAP HANA VM jest sieci wirtualnej lub lepszą konfigurację podsieci. Sap zdecydowanie zaleca oddzielenie klienta/aplikacji skierowanego do ruchu z komunikacji między węzłami HANA. Jak pokazano w grafice, cel ten jest osiągnięty przez dwa różne vNICs dołączone do maszyny Wirtualnej. Oba vNICs są w różnych podsieci, mają dwa różne adresy IP. Następnie można kontrolować przepływ ruchu za pomocą reguł routingu przy użyciu sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika.

Szczególnie na platformie Azure nie istnieją żadne środki i metody wymuszania jakości usług i przydziałów na określonych vNIC. W rezultacie oddzielenie obsługi klienta/aplikacji i komunikacji wewnątrz węzła nie otwiera żadnych możliwości nadania priorytetu jednemu strumieniowi ruchu nad drugim. Zamiast tego separacja pozostaje miarą zabezpieczeń w ekranowaniu komunikacji wewnątrz węzła konfiguracji skalowanych w poziomie.  

>[!NOTE]
>Sap zaleca oddzielenie ruchu sieciowego do ruchu po stronie klienta/aplikacji i wewnątrz węzła, jak opisano w tym dokumencie. W związku z tym zaleca się umieszczenie architektury w miejscu, jak pokazano w ostatniej grafiki jest zalecane. Skonsultuj się również z zespołem ds. 
>

Z punktu widzenia sieci minimalna wymagana architektura sieci będzie wyglądać następująco:

![Podstawy skalowania w poziomie pojedynczego węzła](media/hana-vm-operations/overview-scale-out-networking.png)



### <a name="installing-sap-hana-scale-out-n-azure"></a>Instalowanie programu SAP HANA skalowaj w poziomie n Azure
Instalując skalowaną konfigurację SAP w poziomie, należy wykonać nierówne kroki:

- Wdrażanie nowej lub dostosowywanie istniejącej infrastruktury sieci wirtualnej platformy Azure
- Wdrażanie nowych maszyn wirtualnych przy użyciu zarządzanego magazynu w wersji Premium azure, woluminów dysków ultra i/lub nfs na podstawie pamięci ANF
- - Dostosuj routing sieciowy, aby upewnić się, że na przykład komunikacja wewnątrz węzła między maszynami wirtualnymi nie jest kierowana za pośrednictwem urządzenia [WUS.](https://azure.microsoft.com/solutions/network-appliances/) 
- Zainstaluj węzeł główny SAP HANA.
- Dostosowywanie parametrów konfiguracji węzła głównego SAP HANA
- Kontynuuj instalację węzłów procesu roboczego SAP HANA

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Instalacja sap HANA w konfiguracji skalowawki w poziomie
W miarę wdrażania infrastruktury maszyny Wirtualnej platformy Azure i wszystkich innych przygotowań należy zainstalować konfiguracje skalowania SAP HANA w poziomie w następujących krokach:

- Zainstaluj węzeł główny SAP HANA zgodnie z dokumentacją SAP
- W przypadku korzystania z usługi Azure Premium Storage lub Ultra dysków z dyskami nieudzielonymi /hana/data i /hana/log, należy zmienić plik global.ini i dodać parametr "basepath_shared = nie" do pliku global.ini. Ten parametr umożliwia sap HANA do uruchamiania w skali w poziomie bez "udostępnione" **/hana/data** i **/hana/log** woluminów między węzłami. Szczegóły są udokumentowane w [#2080991 SAP Note](https://launchpad.support.sap.com/#/notes/2080991). Jeśli używasz woluminów NFS opartych na anf dla /hana/data i /hana/log, nie musisz wprowadzać tej zmiany
- Po ewentualnej zmianie parametru global.ini uruchom ponownie wystąpienie SAP HANA
- Dodaj dodatkowe węzły procesu roboczego. Zobacz też <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Określ sieć wewnętrzną komunikacji między węzłami SAP HANA podczas instalacji lub później, używając na przykład lokalnego hdblcm. Aby uzyskać bardziej szczegółową dokumentację, zobacz także [#2183363 uwaga SAP](https://launchpad.support.sap.com/#/notes/2183363). 

Szczegóły dotyczące konfigurowania systemu skalowania w poziomie SAP HANA z węzłem rezerwowym w systemie SUSE Linux opisano szczegółowo w sekcji [Wdrażanie systemu skalowania SAP HANA w poziomie z węzłem gotowości na maszynach wirtualnych platformy Azure przy użyciu plików NetApp usługi Azure na serwerze SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse). Równoważną dokumentację red hat można znaleźć w artykule [Wdrażanie systemu skalowania w poziomie SAP HANA z węzłem gotowości na maszynach wirtualnych platformy Azure przy użyciu plików NetApp usługi Azure w systemie Red Hat Enterprise Linux.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) 


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>Sap HANA Dynamic Tiering 2.0 dla maszyn wirtualnych platformy Azure

Oprócz certyfikatów SAP HANA na maszynach wirtualnych z serii M platformy Azure usługa SAP HANA Dynamic Tiering 2.0 jest również obsługiwana na platformie Microsoft Azure (zobacz łącza dokumentacji dynamicznej warstwy SAP HANA w dalszej części). Chociaż nie ma różnicy w instalacji produktu lub jego obsługi, na przykład za pośrednictwem kokpitu SAP HANA wewnątrz maszyny wirtualnej platformy Azure, istnieje kilka ważnych elementów, które są obowiązkowe dla oficjalnej pomocy technicznej na platformie Azure. Te kluczowe punkty są opisane poniżej. W całym artykule skrót "DT 2.0" będzie używany zamiast pełnej nazwy Dynamic Tiering 2.0.

SAP HANA Dynamic Tiering 2.0 nie jest obsługiwany przez SAP BW lub S4HANA. Główne przypadki użycia w tej chwili są natywne aplikacje HANA.


### <a name="overview"></a>Omówienie

Poniższy rysunek przedstawia omówienie obsługi DT 2.0 na platformie Microsoft Azure. Istnieje zestaw obowiązkowych wymogów, które muszą być przestrzegane zgodnie z urzędową certyfikacją:

- DT 2.0 musi być zainstalowany na dedykowanej maszynie wirtualnej platformy Azure. Nie może działać na tej samej maszynie Wirtualnej, na której działa SAP HANA
- Maszyny wirtualne SAP HANA i DT 2.0 muszą być wdrażane w tej samej sieci wirtualnej platformy Azure
- Maszyny wirtualne SAP HANA i DT 2.0 muszą być wdrażane z włączoną obsługą sieci akcelerowanych platformy Azure
- Typ magazynu maszyn wirtualnych DT 2.0 musi być usługą Azure Premium Storage
- Wiele dysków platformy Azure musi być dołączonych do maszyny Wirtualnej DT 2.0
- Jest to wymagane do utworzenia raid oprogramowania / rozłożone woluminu (za pośrednictwem lvm lub mdadm) przy użyciu rozkładu na dyskach platformy Azure

Więcej szczegółów zostanie wyjaśnionych w poniższych sekcjach.

![Omówienie architektury SAP HANA DT 2.0](media/hana-vm-operations/hana-data-tiering.png)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Dedykowana maszyna wirtualna platformy Azure dla sap HANA DT 2.0

Na platformie Azure IaaS DT 2.0 jest obsługiwany tylko na dedykowanej maszynie Wirtualnej. Nie można uruchamiać DT 2.0 na tej samej maszynie Wirtualnej platformy Azure, na której jest uruchomione wystąpienie HANA. Początkowo dwa typy maszyn wirtualnych mogą być używane do uruchamiania SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Zobacz opis typu maszyny Wirtualnej [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Biorąc pod uwagę podstawową ideę DT 2.0, która polega na odciążaniu "ciepłych" danych w celu zaoszczędzenia kosztów, warto użyć odpowiednich rozmiarów maszyn wirtualnych. Nie ma jednak ścisłej zasady dotyczącej możliwych kombinacji. To zależy od konkretnego obciążenia klienta.

Zalecane konfiguracje będą:

| Typ maszyny wirtualnej SAP HANA | Typ maszyny wirtualnej DT 2.0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms ( M64ms ) | E32sv3 |
| M64s | E32sv3 |


Możliwe są wszystkie kombinacje maszyn wirtualnych z serii M z certyfikatem SAP HANA z obsługiwanymi maszynami wirtualnymi DT 2.0 (M64-32ms i E32sv3).


### <a name="azure-networking-and-sap-hana-dt-20"></a>Sieć platformy Azure i sap HANA DT 2.0

Instalowanie DT 2.0 na dedykowanej maszynie wirtualnej wymaga przepustowości sieci między maszyną wirtualną DT 2.0 a maszyną wirtualną SAP HANA o minimum 10 Gb. W związku z tym jest obowiązkowe, aby umieścić wszystkie maszyny wirtualne w tej samej sieci wirtualnej platformy Azure i włączyć platformę Azure przyspieszone sieci.

Zobacz dodatkowe informacje o przyspieszonej sieci platformy Azure [tutaj](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Magazyn maszyn wirtualnych dla SAP HANA DT 2.0

Zgodnie ze wskazówkami dotyczącymi najlepszych praktyk DT 2.0 przepustowość we/wy dysku powinna wynosić co najmniej 50 MB/s na rdzeń fizyczny. Patrząc na specyfikację dla dwóch typów maszyn wirtualnych platformy Azure, które są obsługiwane dla DT 2.0 maksymalny limit przepływności we/wy dysku dla maszyny Wirtualnej wyglądać:

- E32sv3 : 768 MB/s (bez zakaźnych), co oznacza stosunek 48 MB/s na rdzeń fizyczny
- M64-32ms: 1000 MB/s (bez zakaźnych), co oznacza stosunek 62,5 MB/s na rdzeń fizyczny

Jest wymagane do dołączenia wielu dysków platformy Azure do maszyny Wirtualnej DT 2.0 i utworzyć raid oprogramowania (striping) na poziomie systemu operacyjnego, aby osiągnąć maksymalny limit przepływności dysku na maszynę wirtualną. Jeden dysk platformy Azure nie może zapewnić przepływności, aby osiągnąć maksymalny limit maszyn wirtualnych w tym zakresie. Usługa Azure Premium storage jest obowiązkowa do uruchomienia DT 2.0. 

- Szczegółowe informacje na temat dostępnych typów dysków platformy Azure można znaleźć [tutaj](../../windows/disks-types.md)
- Szczegółowe informacje na temat tworzenia najazdu na oprogramowanie za pośrednictwem mdadm można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Szczegółowe informacje na temat konfigurowania lvm do tworzenia woluminu rozłożonego dla maksymalnej przepustowości można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

W zależności od wymagań dotyczących rozmiaru istnieją różne opcje, aby osiągnąć maksymalną przepustowość maszyny Wirtualnej. Poniżej przedstawiono możliwe konfiguracje dysków woluminów danych dla każdego typu maszyny Wirtualnej DT 2.0, aby osiągnąć górny limit przepływności maszyny Wirtualnej. Maszyna wirtualna E32sv3 powinna być traktowana jako poziom podstawowy dla mniejszych obciążeń. W przypadku, gdy okaże się, że nie jest wystarczająco szybki, może być konieczne zmienić rozmiar maszyny Wirtualnej do M64-32ms.
Ponieważ maszyna wirtualna M64-32ms ma dużo pamięci, obciążenie we/wy może nie osiągnąć limitu, szczególnie w przypadku obciążeń intensywnie korzystających z odczytu. W związku z tym mniej dysków w zestawie rozłożonym może być wystarczające w zależności od obciążenia specyficzne dla klienta. Ale aby być po bezpiecznej stronie konfiguracje dysku poniżej zostały wybrane w celu zagwarantowania maksymalnej przepustowości:


| Jednostka SKU maszyny wirtualnej | Konfigur dysków 1 | Konfigur dysk 2 | Konfigur dysk 3 | Konfigur dysk 4 | Konfigur dysków 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1,5 TB | 


Szczególnie w przypadku, gdy obciążenie jest intensywnie odczytu może zwiększyć wydajność we/wy, aby włączyć pamięć podręczną hosta platformy Azure "tylko do odczytu", zgodnie z zaleceniami dla ilości danych oprogramowania bazy danych. Natomiast dla dziennika transakcji pamięci podręcznej dysku hosta platformy Azure musi być "none". 

Jeśli chodzi o rozmiar woluminu dziennika zalecanym punktem wyjścia jest heurystyką 15% rozmiaru danych. Tworzenie woluminu dziennika można wykonać przy użyciu różnych typów dysków platformy Azure w zależności od wymagań dotyczących kosztów i przepływności. Dla woluminu dziennika wymagana jest wysoka przepustowość we/wy.  W przypadku korzystania z maszyny Wirtualnej typu M64-32ms jest obowiązkowe, aby włączyć [akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Akcelerator zapisu azure zapewnia optymalne opóźnienie zapisu dysku dla dziennika transakcji (dostępne tylko dla serii M). Istnieje kilka elementów do rozważenia, choć jak maksymalna liczba dysków na typ maszyny Wirtualnej. Szczegółowe informacje na temat akceleratora zapisu można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)


Oto kilka przykładów dotyczących rozmiaru woluminu dziennika:

| rozmiar woluminu danych i typ dysku | log woluminu i typu dysku config 1 | log woluminu i typu dysku config 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Podobnie jak w przypadku skalowania w poziomie SAP HANA, katalog /hana/shared musi być współużytkowany między maszyną wirtualną SAP HANA i maszyną wirtualną DT 2.0. Zalecana jest ta sama architektura, co w przypadku skalowania w poziomie SAP HANA przy użyciu dedykowanych maszyn wirtualnych, które działają jako serwer systemu plików NFS o wysokiej dostępności. Aby zapewnić udostępniony wolumin kopii zapasowej, można użyć identycznego projektu. Ale to do klienta, czy ha byłoby konieczne lub jeśli wystarczy po prostu użyć dedykowanej maszyny Wirtualnej z wystarczającą pojemnością do działania jako serwer kopii zapasowej.



### <a name="links-to-dt-20-documentation"></a>Łącza do dokumentacji DT 2.0 

- [Przewodnik po instalacji i aktualizacji dynamicznych warstw SAP HANA](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Samouczki i zasoby dotyczące dynamicznego warstwowania SAP HANA](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA Dynamiczne warstwowe poc](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [Sap HANA 2.0 SPS 02 dynamiczne ulepszenia warstwowe](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operacje wdrażania sap HANA na maszynach wirtualnych platformy Azure
W poniższych sekcjach opisano niektóre operacje związane z wdrażaniem systemów SAP HANA na maszynach wirtualnych platformy Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Czep operacji przywracania i przywracania na maszynach wirtualnych platformy Azure
W poniższych dokumentach opisano sposób utworzenia kopii zapasowej i przywrócenia wdrożenia SAP HANA:

- [Omówienie kopii zapasowych oprogramowania SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Kopia zapasowa sap hana na poziomie pliku](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Sap HANA migawka pamięci masowej benchmark](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Uruchamianie i ponowne uruchamianie maszyn wirtualnych zawierających sap HANA
Widoczna cecha chmury publicznej platformy Azure jest, że opłaty są pobierane tylko za minuty przetwarzania. Na przykład po zamknięciu maszyny Wirtualnej z systemem SAP HANA, są rozliczane tylko za koszty magazynu w tym czasie. Inna funkcja jest dostępna po określeniu statycznych adresów IP dla maszyn wirtualnych w początkowym wdrożeniu. Po ponownym uruchomieniu maszyny Wirtualnej z sap HANA, maszyna wirtualna jest ponownie uruchamiana z jej wcześniejszych adresów IP. 


### <a name="use-saprouter-for-sap-remote-support"></a>Użyj SAProuter dla zdalnej obsługi SAP
Jeśli masz połączenie lokacja-lokacja między lokalizacjami lokalnymi a platformą Azure i używasz składników SAP, prawdopodobnie używasz już narzędzia SAProuter. W takim przypadku wykonaj następujące elementy do zdalnej pomocy technicznej:

- Obsługa prywatnego i statycznego adresu IP maszyny Wirtualnej, która obsługuje sap HANA w konfiguracji SAProuter.
- Skonfiguruj sieć sieciowej podsieci, która obsługuje maszynę wirtualną HANA, aby zezwolić na ruch za pośrednictwem portu TCP/IP 3299.

Jeśli łączysz się z platformą Azure za pośrednictwem Internetu i nie masz routera SAP dla maszyny Wirtualnej z SAP HANA, musisz zainstalować składnik. Zainstaluj SAProuter w osobnej maszynie wirtualnej w podsieci Zarządzanie. Na poniższej ilustracji przedstawiono schemat szorstki do wdrażania sap HANA bez połączenia lokacja lokacja i saprouter:

![Schemat wdrażania szorstkego dla systemu SAP HANA bez połączenia lokacja lokacja i narzędzia SAProuter](media/hana-vm-operations/hana-simple-networking-saprouter.png)

Pamiętaj, aby zainstalować SAProuter w osobnej maszynie wirtualnej, a nie w jumpbox maszyny Wirtualnej. Oddzielna maszyna wirtualna musi mieć statyczny adres IP. Aby połączyć urządzenie SAProuter z urządzeniem SAProuter obsługiwanym przez sap, skontaktuj się z firmą SAP w celu uzyskania adresu IP. (SAProuter, który jest obsługiwany przez SAP jest odpowiednikiem SAProuter wystąpienia, które można zainstalować na maszynie wirtualnej.) Użyj adresu IP firmy SAP, aby skonfigurować wystąpienie SAProuter. W ustawieniach konfiguracji jedynym niezbędnym portem jest port TCP 3299.

Aby uzyskać więcej informacji na temat konfigurowania i obsługi połączeń zdalnej pomocy technicznej za pośrednictwem programu SAProuter, zobacz [dokumentację SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Wysoka dostępność dzięki rozwiązaniu SAP HANA na natywnych maszynach wirtualnych platformy Azure
Jeśli korzystasz z SUSE Linux Enterprise Server lub Red Hat, możesz utworzyć klaster rozrusznika serca za pomocą urządzeń STONITH. Za pomocą tych urządzeń można skonfigurować konfigurację SAP HANA, która używa replikacji synchroniczowej z replikacją systemu HANA i automatyczną przemówieniem awaryjnym. Aby uzyskać więcej informacji wymienionych w sekcji "następne kroki".

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z artykułami wymienionymi na liście
- [Konfiguracje magazynu maszyn wirtualnych platformy Azure SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- [Wdrażanie systemu skalowania w poziomie SAP HANA z węzłem wstrzymania na maszynach wirtualnych platformy Azure przy użyciu plików NetApp platformy Azure na serwerze SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Wdrażanie systemu skalowania w poziomie SAP HANA z węzłem wstrzymania na maszynach wirtualnych platformy Azure przy użyciu plików NetApp azure w systemie Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [Wysoka dostępność sap HANA na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Wysoka dostępność maszyn wirtualnych SAP HANA na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)

 

