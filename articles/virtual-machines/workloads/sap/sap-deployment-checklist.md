---
title: Planowanie obciążeń SAP i Lista kontrolna wdrożenia | Microsoft Docs
description: Lista kontrolna planowania wdrożeń obciążeń SAP na platformie Azure i wdrażania obciążeń
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ac75ab31f8c9cdd9405115db4f5c35d28707e29f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950367"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Obciążenia SAP na platformie Azure: Lista kontrolna planowania i wdrażania

Ta lista kontrolna jest przeznaczona dla klientów, którzy przenoszą aplikacje SAP NetWeaver, S/4HANA i Hybris do infrastruktury platformy Azure jako usługi. Przez cały czas trwania projektu, partner klienta i/lub SAP powinien przejrzeć listę kontrolną. Należy pamiętać, że wiele testów jest zakończonych na początku projektu i w fazie planowania. Po zakończeniu wdrażania niebezpośrednie zmiany wdrożonej infrastruktury platformy Azure lub wydań oprogramowania SAP mogą stać się złożone.

Przejrzyj listę kontrolną w najważniejszych punktach kontrolnych w projekcie. Takie działanie umożliwi wykrywanie małych problemów przed ich rozbudowanymi problemami. Będziesz również mieć wystarczająco dużo czasu, aby ponownie opracowywać i testować wszelkie niezbędne zmiany. Nie należy uważać, aby ta lista kontrolna została ukończona. W zależności od sytuacji może być konieczne wykonanie wielu dalszych kontroli.

Lista kontrolna nie obejmuje zadań, które są niezależne od platformy Azure. Na przykład interfejsy aplikacji SAP zmieniają się podczas przejścia na platformę Azure lub do dostawcy hostingu.

Ta lista kontrolna może być również używana dla systemów, które są już wdrożone. Od czasu wdrożenia mogły zostać dodane nowe funkcje, takie jak akcelerator zapisu i Strefy dostępności, oraz nowe typy maszyn wirtualnych. Dlatego warto przejrzeć listę kontrolną okresowo, aby upewnić się, że znasz nowe funkcje platformy Azure.

## <a name="project-preparation-and-planning-phase"></a>Faza przygotowywania i planowania projektu
W tej fazie planujesz migrację obciążenia SAP na platformę Azure. Co najmniej w tej fazie należy utworzyć następujące dokumenty oraz zdefiniować i omówić następujące elementy migracji:

1. Dokument projektu wysokiego poziomu. Ten dokument powinien zawierać:
    - Bieżący spis składników i aplikacji SAP oraz docelowy spis aplikacji dla platformy Azure.
    - Macierz przypisania odpowiedzialności (RACI) definiująca obowiązki i przydziały osób, których dotyczy. Zacznij od wysokiego poziomu i pracuj z bardziej szczegółowymi poziomami w trakcie planowania i pierwszego wdrożenia.
    - Architektura rozwiązania wysokiego poziomu.
    - Decyzja o tym, do których regionów świadczenia usług Azure ma zostać wdrożony. Zobacz [listę regionów świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/). Aby dowiedzieć się, które usługi są dostępne w poszczególnych regionach, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/).
    - Architektura sieci do łączenia się z lokalnego do platformy Azure. Zacznij od zapoznania się z [planem wirtualnego centrum danych platformy Azure](/azure/architecture/vdc/).
    - Zasady zabezpieczeń dotyczące uruchamiania wysokiego wpływu na platformę Azure. Aby dowiedzieć się więcej o zabezpieczeniach danych, Zacznij od [dokumentacji zabezpieczeń platformy Azure](../../../security/index.yml).
2.  Dokument techniczny projektowania. Ten dokument powinien zawierać:
    - Diagram blokowy rozwiązania.
    - Ustalanie rozmiarów składników obliczeniowych, magazynu i sieci na platformie Azure. Aby zmienić rozmiar SAP maszyn wirtualnych platformy Azure, zobacz [SAP 
    -  Uwaga #1928533] ( https://launchpad.support.sap.com/#/notes/1928533) .
    - Ciągłość działania i architektura odzyskiwania po awarii.
    - Szczegółowe informacje na temat wersji systemów operacyjnych, baz danych, jądra i pakietu SAP support. Nie musi to być prawdą, że każda wersja systemu operacyjnego obsługiwana przez oprogramowanie SAP NetWeaver lub S/4HANA jest obsługiwana na maszynach wirtualnych platformy Azure. Ta sama wartość dotyczy wersji systemu DBMS. Zapoznaj się z następującymi źródłami, aby wyrównać i w razie potrzeby uaktualnić wydania SAP, wersje DBMS i wersje systemu operacyjnego w celu zapewnienia wsparcia SAP i platformy Azure. Aby uzyskać pełną pomoc techniczną od oprogramowania SAP i Microsoft, musisz mieć kombinacje wydań obsługiwane przez oprogramowanie SAP i platformę Azure. W razie potrzeby należy zaplanować uaktualnienie niektórych składników oprogramowania. Więcej informacji o obsługiwanych oprogramowaniu SAP, OS i DBMS opisano tutaj:
        - [Uwaga dotycząca pomocy technicznej SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Ta Uwaga definiuje minimalne wersje systemu operacyjnego obsługiwane na maszynach wirtualnych platformy Azure. Definiuje również minimalne wersje bazy danych wymagane dla większości baz danych innych niż HANA. Na koniec oferuje Określanie rozmiarów SAP dla typów maszyn wirtualnych platformy Azure obsługiwanych przez oprogramowanie SAP.
        - [Uwaga dotycząca pomocy technicznej SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553). Ta Uwaga definiuje zasady pomocy technicznej dotyczące usługi Azure Storage i obsługi relacji wymaganych z firmą Microsoft.
        - [Uwaga dotycząca pomocy technicznej SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). Ta Uwaga definiuje Macierz obsługi Oracle dla platformy Azure. Platforma Oracle obsługuje tylko system Windows i Oracle Linux jako systemy operacyjne gościa na platformie Azure dla obciążeń SAP. Ta instrukcja obsługi dotyczy również warstwy aplikacji SAP, w której działają wystąpienia SAP. Jednak platforma Oracle nie obsługuje wysokiej dostępności usług SAP Central w Oracle Linux za pomocą Pacemaker. Jeśli potrzebujesz wysokiej dostępności dla ASCS na Oracle Linux, musisz użyć pakietu ochrony oprogramowanie SIOS dla systemu Linux. Aby uzyskać szczegółowe dane dotyczące certyfikacji oprogramowania SAP, zobacz uwagi dotyczące pomocy technicznej SAP [#1662610 — szczegóły pomocy technicznej dla programu oprogramowanie SIOS Protection Suite dla systemu Linux](https://launchpad.support.sap.com/#/notes/1662610). W przypadku systemu Windows rozwiązanie Windows Server Failover Clustering dla systemu SAP dla usług SAP Central jest obsługiwane w połączeniu z bazą danych Oracle jako warstwą DBMS.
        - [Uwaga dotycząca pomocy technicznej SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581). Ta Uwaga zawiera macierz pomocy technicznej dla SAP HANA w różnych wersjach systemu operacyjnego.
        - Obsługiwane przez SAP HANA maszyny wirtualne platformy Azure i [duże wystąpienia Hana](./hana-overview-architecture.md) są wymienione w [witrynie sieci Web SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
        - [Macierz dostępności produktu SAP](https://support.sap.com/en/).
        - [Uwaga dotycząca pomocy technicznej SAP #2555629-SAP HANA 2,0 dynamiczne warstwy — Obsługa funkcji hypervisor i chmury](https://launchpad.support.sap.com/#/notes/2555629)
        - [Uwaga dotycząca pomocy technicznej SAP #1662610 — szczegóły pomocy technicznej dla programu oprogramowanie SIOS Protection Suite dla systemu Linux](https://launchpad.support.sap.com/#/notes/1662610)
        - Uwagi dotyczące oprogramowania SAP dla innych produktów specyficznych dla oprogramowania SAP.     
    - Korzystanie z konfiguracji klastra z obsługą wiele identyfikatorów SID dla usług SAP Central jest obsługiwane w systemach operacyjnych Windows, SLES i RHEL na platformie Azure. Należy pamiętać, że wzmocnienie usługi RADIUS może zwiększyć ASCS/SCS miejsce na tym klastrze z wieloma identyfikatorami SID. Dokumentację dotyczącą odpowiedniego scenariusza systemu operacyjnego gościa można znaleźć w następujących artykułach:
        - [Rozwiązanie SAP ASCS/SCS o wysokiej dostępności z użyciem usługi Windows Server Failover Clustering i dysku udostępnionego na platformie Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [Rozwiązanie SAP ASCS/SCS o wysokiej dostępności z użyciem klastra trybu failover systemu Windows Server i udziału plików na platformie Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
        - [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server for SAP — Przewodnik dotyczący wiele identyfikatorów SID](./high-availability-guide-suse-multi-sid.md)
        - [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux for SAP — Przewodnik dotyczący wiele identyfikatorów SID](./high-availability-guide-rhel-multi-sid.md)
    - Architektura wysokiej dostępności i odzyskiwania po awarii.
        - W oparciu o RTO i cel punktu odzyskiwania należy określić, co ma być podobne do potrzeb.
        - Aby zapewnić wysoką dostępność w ramach strefy, sprawdź, co wymaga system DBMS do zaoferowania na platformie Azure. Większość pakietów systemu DBMS oferuje synchroniczną metodę synchronicznej rezerwy gorącą, którą zalecamy dla systemów produkcyjnych. Zapoznaj się również z dokumentacją dotyczącą oprogramowania SAP dla różnych baz danych, rozpoczynając od [uwagi dotyczącej wdrożenia systemu Azure Virtual Machines DBMS dla obciążeń SAP](./dbms_guide_general.md) i powiązanych dokumentów.
           Używanie klastra trybu failover systemu Windows Server z konfiguracją dysku udostępnionego dla warstwy DBMS, na przykład [opisanej dla SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017), nie jest obsługiwane. Zamiast tego należy używać rozwiązań, takich jak:
           - [Opcja Zawsze włączone programu SQL Server](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](../oracle/configure-oracle-dataguard.md)
           - [Replikacja systemu HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - W przypadku odzyskiwania po awarii w regionach platformy Azure Zapoznaj się z rozwiązaniami oferowanymi przez różnych dostawców systemów DBMS. Większość z nich obsługuje replikację asynchroniczną lub wysyłanie dzienników.
        - W przypadku warstwy aplikacji SAP należy określić, czy będą uruchamiane systemy testowe regresji biznesowej, które najlepiej sprawdzają się w przypadku replik wdrożeń produkcyjnych w tym samym regionie świadczenia usługi Azure lub w regionie odzyskiwania po awarii. W drugim przypadku można wskazać ten system regresji biznesowej jako element docelowy odzyskiwania dla wdrożeń produkcyjnych.
        - Jeśli nie chcesz umieszczać systemów nieprodukcyjnych w witrynie odzyskiwania po awarii, poszukaj w Azure Site Recovery jako metody replikowania warstwy aplikacji SAP do regionu Azure DR. Aby uzyskać więcej informacji, zobacz [Konfigurowanie odzyskiwania po awarii dla wielowarstwowego wdrażania aplikacji SAP NetWeaver](../../../site-recovery/site-recovery-sap.md).
        - Jeśli zdecydujesz się użyć połączonej konfiguracji HADR Cluster za pomocą [strefy dostępności platformy Azure](../../../availability-zones/az-overview.md), zapoznaj się z regionami świadczenia usługi Azure, w których strefy dostępności są dostępne. Należy również uwzględnić ograniczenia kont, które mogą być wprowadzane przez zwiększenie opóźnień sieci między dwoma Strefy dostępności.  
3.  Spis wszystkich interfejsów SAP (SAP i non-SAP).
4.  Projektowanie usług Foundation Services. Ten projekt powinien zawierać następujące elementy:
    - Projektowanie Active Directory i DNS.
    - Topologia sieci na platformie Azure i przypisywanie różnych systemów SAP.
    - Struktura [kontroli dostępu opartej na rolach (Azure RBAC)](../../../role-based-access-control/overview.md) dla zespołów zarządzających infrastrukturą i aplikacjami SAP na platformie Azure.
    - Topologia grupy zasobów.
    - [Strategia tagowania](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing).
    - Konwencje nazewnictwa dla maszyn wirtualnych i innych składników infrastruktury i/lub nazw logicznych.
5.  Kontrakt Microsoft Professional lub pomoc techniczna Premier. Skontaktuj się z pomocą techniczną firmy Microsoft, jeśli masz umowę pomocy technicznej Premier z firmą Microsoft. Aby uzyskać wymagania dotyczące pomocy technicznej SAP, zobacz [uwagi dotyczące pomocy technicznej sap #2015553](https://launchpad.support.sap.com/#/notes/2015553).
6.  Liczba subskrypcji platformy Azure i limit przydziału rdzeni dla subskrypcji. [Otwórz żądania obsługi, aby zwiększyć przydziały subskrypcji platformy Azure](../../../azure-portal/supportability/resource-manager-core-quotas-request.md) zgodnie z wymaganiami.
7.  Planowanie zmniejszenia i migracji danych na potrzeby migrowania danych SAP na platformę Azure. W przypadku systemów SAP NetWeaver w systemie SAP zawarto wskazówki dotyczące ograniczania ilości dużych ilości danych. Zobacz [ten przewodnik SAP](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) dotyczący zarządzania danymi w systemach SAP ERP. Część zawartości dotyczy również systemów NetWeaver i S/4HANA.
8.  Automatyczne podejście do wdrożenia. Celem automatyzacji wdrożeń infrastruktury na platformie Azure jest wdrożenie w sposób deterministyczny i uzyskanie deterministycznych wyników. Wielu klientów używa programu PowerShell lub skryptów opartych na interfejsie wiersza polecenia. Istnieją jednak różne technologie "open source", których można użyć do wdrożenia infrastruktury platformy Azure dla oprogramowania SAP, a nawet w przypadku instalowania programu SAP. Przykłady można znaleźć w witrynie GitHub:
    - [Automatyczne wdrożenia SAP w chmurze platformy Azure](https://github.com/Azure/sap-hana)
    - [Instalacja SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Zdefiniuj regularne przeglądy projektowe i wdrożeniowe erze od użytkownika jako klienta, integratora systemów, firmy Microsoft i innych podmiotów.


## <a name="pilot-phase-strongly-recommended"></a>Faza pilotażowa (zdecydowanie zalecane)
 
Możesz uruchomić pilotaż przed lub podczas planowania i przygotowywania projektu. Możesz również użyć fazy pilotażowej do testowania podejścia i projektów wykonanych w fazie planowania i przygotowywania. Możesz też rozwinąć fazę pilotażową, aby uczynić ją prawdziwej weryfikacji koncepcji.

Zalecamy skonfigurowanie i zweryfikowanie pełnego projektu rozwiązania HADR Cluster i bezpieczeństwa podczas wdrażania pilotażowego. Niektórzy klienci wykonują testy skalowalne w tej fazie. Inni klienci używają wdrożeń systemów piaskownicy SAP jako fazy pilotażowej. Załóżmy, że został już zidentyfikowany system do migracji na platformę Azure dla pilotażu.

1. Optymalizuj transfer danych do platformy Azure. Optymalny wybór jest wysoce zależny od konkretnego scenariusza. Transfer z lokalizacji lokalnej za pośrednictwem [usługi Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) jest najszybszy, jeśli obwód ExpressRoute ma wystarczającą przepustowość. W innych scenariuszach transfer przez Internet jest szybszy.
2. W przypadku niejednorodnej migracji platformy SAP, która obejmuje eksport i import danych, przetestuj i zoptymalizuj fazy eksportu i importu. W przypadku dużych migracji, w których SQL Server jest platformą docelową, można znaleźć [zalecenia](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Jeśli nie jest wymagane uaktualnienie połączonego wydania, można użyć monitora migracji/SWPM. Procesu [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) można użyć podczas łączenia migracji z uaktualnieniem wydania SAP. W tym celu należy spełnić pewne wymagania dotyczące kombinacji platformy DBMS źródłowej i docelowej. Ten proces jest udokumentowany w [opcji Migrowanie bazy danych (DMO) z sumy 2,0 SP03](https://launchpad.support.sap.com/#/notes/2631152).
   1.  Eksportuj do źródła, Eksportuj plik na platformę Azure i zaimportuj wydajność. Maksymalizuj nakładanie się między eksportem a importem.
   2.  Oceń ilość bazy danych na platformie docelowej i docelowej na potrzeby określania rozmiarów infrastruktury.
   3.  Sprawdzanie poprawności i optymalizowanie chronometrażu.
1. Weryfikacja techniczna.
   1. Typy maszyn wirtualnych.
        - Zapoznaj się z informacjami w temacie Informacje o pomocy technicznej SAP, w katalogu sprzętu SAP HANA i w usłudze PAM SAP ponownie. Upewnij się, że nie ma żadnych zmian na obsługiwanych maszynach wirtualnych platformy Azure, obsługiwanych wersjach systemu operacyjnego dla tych typów maszyn wirtualnych oraz obsługiwanych wersjach oprogramowania SAP i DBMS.
        - Sprawdź ponownie rozmiar aplikacji oraz infrastrukturę wdrażaną na platformie Azure. Jeśli przenosisz istniejące aplikacje, możesz często korzystać z niezbędnych punktów SAP z infrastruktury, z której korzystasz, oraz na [stronie internetowej test porównawczy SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) i porównać ją z numerami punktów SAP wymienionymi w temacie [pomoc techniczna SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Zadbaj również [o klasyfikację punktów SAP w tym artykule](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) .
        - Oceń i przetestuj rozmiary maszyn wirtualnych platformy Azure w odniesieniu do maksymalnej przepustowości magazynu i przepływności sieci dla typów maszyn wirtualnych wybranych podczas fazy planowania. Dane można znaleźć tutaj:
           -  [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ważne jest, aby uwzględnić *maksymalną przepływność dysku niebuforowanego* dla rozmiaru.
           -  [Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ważne jest, aby uwzględnić *maksymalną przepływność dysku niebuforowanego* dla rozmiaru.
   2. Pamięć.
        - Sprawdź dokument [typy magazynów Azure dla obciążeń SAP](./planning-guide-storage.md)
        - Za pomocą [usługi Azure SSD w warstwie Standardowa Storage](../../disks-types.md#standard-ssd) można korzystać w przypadku maszyn wirtualnych, które reprezentują warstwy aplikacji SAP, oraz wdrażania systemów DBMS, które nie są wrażliwe na wydajność.
        - Ogólnie rzecz biorąc nie zalecamy używania [dysków HDD w warstwie Standardowa platformy Azure](../../disks-types.md#standard-hdd).
        - Użyj [Premium Storage platformy Azure](../../disks-types.md#premium-ssd) dla wszystkich maszyn wirtualnych z systemem DBMS, które są zdalnie wrażliwe na wydajność.
        - Użyj [usługi Azure Managed disks](https://azure.microsoft.com/services/managed-disks/).
        - Użyj usługi Azure akcelerator zapisu dla dysków dziennika DBMS z serii M. Należy pamiętać o limitach i użyciu akcelerator zapisu, jak opisano w [Akcelerator zapisu](../../how-to-enable-write-accelerator.md).
        - W przypadku różnych typów systemów DBMS należy zapoznać się z [ogólną dokumentacją systemu DBMS powiązaną z programem SAP](./dbms_guide_general.md) i dokumentacją specyficzną dla systemu DBMS, na którą wskazuje dokument ogólny.
        - Aby uzyskać więcej informacji na temat SAP HANA, zobacz [SAP HANA konfiguracje i operacje infrastruktury na platformie Azure](./hana-vm-operations.md).
        - Nie należy instalować dysków danych platformy Azure na maszynę wirtualną z systemem Linux platformy Azure przy użyciu identyfikatora urządzenia. Zamiast tego należy użyć uniwersalnego identyfikatora unikatowego (UUID). Należy zachować ostrożność podczas instalowania dysków danych platformy Azure przy użyciu narzędzi graficznych. Sprawdź podwójne wpisy w/etc/fstab, aby upewnić się, że identyfikator UUID jest używany do instalowania dysków. Więcej szczegółów można znaleźć w [tym artykule](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk).
   3. Sieci.
        - Przetestuj i Oceń infrastrukturę sieci wirtualnej oraz dystrybucję aplikacji SAP w różnych sieciach wirtualnych platformy Azure lub w ich obrębie.
        -  Oceń podejście do architektury sieci wirtualnej typu Hub i szprych lub podejście mikrosegmentacji w ramach jednej sieci wirtualnej platformy Azure. Podstawa tej oceny:
               1. Koszty wymiany danych między [równorzędnymi sieciami wirtualnymi platformy Azure](../../../virtual-network/virtual-network-peering-overview.md). Aby uzyskać informacje o kosztach, zobacz [Cennik usługi Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network/).
               2. Zalety szybkiego rozłączenia komunikacji równorzędnej między sieciami wirtualnymi platformy Azure w przeciwieństwie do zmiany sieciowej grupy zabezpieczeń w celu odizolowania podsieci w sieci wirtualnej. Ta ocena jest wykonywana w przypadku, gdy aplikacje lub maszyny wirtualne hostowane w podsieci sieci wirtualnej stałą się zagrożeniem bezpieczeństwa.
                3. Centralne rejestrowanie i inspekcja ruchu sieciowego między środowiskiem lokalnym, światem zewnętrznym i wirtualnym centrum danych, które zostało utworzone na platformie Azure.
        - Oceń i przetestuj ścieżkę danych między warstwą aplikacji SAP i warstwą SAP DBMS.
            -  Umieszczenie [urządzeń wirtualnych sieci platformy Azure](https://azure.microsoft.com/solutions/network-appliances/) w ścieżce komunikacji między aplikacją SAP a warstwą DBMS systemów SAP opartych na oprogramowaniu SAP NetWeaver, Hybris lub S/4HANA nie jest obsługiwane.
            -  Umieszczenie warstwy aplikacji SAP i SAP DBMS w różnych sieciach wirtualnych platformy Azure, które nie są połączone za pomocą komunikacji równorzędnej, nie jest obsługiwane.
            -  Aby zdefiniować trasy między warstwą aplikacji SAP i warstwą SAP DBMS, można użyć [grup zabezpieczeń aplikacji i zasad grupy zabezpieczeń sieci](../../../virtual-network/network-security-groups-overview.md) .
        - Upewnij się, że [usługa Azure przyspieszone sieci](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jest włączona na maszynach wirtualnych używanych w warstwie aplikacji SAP i w warstwie systemu SAP DBMS. Należy pamiętać, że różne poziomy systemu operacyjnego są konieczne do obsługi przyspieszonej sieci na platformie Azure:
            - Windows Server 2012 R2 lub nowszy.
            - SUSE Linux 12 z dodatkiem SP3 lub nowszym.
            - RHEL 7,4 lub nowszy.
            - Oracle Linux 7,5. Jeśli używasz jądra RHCKL, wymagana jest wersja 3.10.0-862.13.1. el7. Jeśli używasz jądra Oracle UEK, wymagana jest wersja 5.
        - Przetestuj i Oceń opóźnienia sieci między maszynami wirtualnymi i maszynami wirtualnymi w warstwie aplikacji SAP zgodnie z uwagami dotyczącymi obsługi SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) i [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Oceń wyniki na wskazówki dotyczące opóźnień sieci w programie [SAP support uwagi #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Opóźnienie sieci powinno być w zakresie umiarkowanym lub dobrym. Wyjątki dotyczą ruchu między maszynami wirtualnymi i jednostkami dużych wystąpień platformy HANA, zgodnie z opisem w [tym artykule](./hana-network-architecture.md#networking-architecture-for-hana-large-instance).
        - Upewnij się, że wdrożenia ILB są skonfigurowane do używania bezpośredniego powrotu do serwera. To ustawienie spowoduje skrócenie opóźnienia, gdy usługa Azure ILB będzie używana do konfiguracji wysokiej dostępności na warstwie DBMS.
        - Jeśli używasz Azure Load Balancer razem z systemami operacyjnymi gościa z systemem Linux, sprawdź, czy parametr sieci systemu Linux **net.IPv4.tcp_timestamps** ma wartość **0**. To zalecenie powoduje konflikt z zaleceniami we wcześniejszych wersjach programu [SAP uwagi #2382421](https://launchpad.support.sap.com/#/notes/2382421). Uwaga dotycząca oprogramowania SAP jest teraz aktualizowana do stanu, w którym ten parametr musi być ustawiony na **wartość 0** , aby pracować z modułami równoważenia obciążenia platformy Azure.
        - W celu uzyskania optymalnego opóźnienia sieci należy rozważyć użycie [grup umieszczania usługi Azure zbliżeniowe](../../linux/co-location.md) . Aby uzyskać więcej informacji, zobacz [grupy umieszczania zbliżeniowe platformy Azure w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP](sap-proximity-placement-scenarios.md).
   4. Wdrożenia wysokiej dostępności i odzyskiwania po awarii.
        - W przypadku wdrażania warstwy aplikacji SAP bez definiowania określonej strefy dostępności platformy Azure upewnij się, że wszystkie maszyny wirtualne, które uruchamiają wystąpienia okna dialogowego SAP lub wystąpienia oprogramowania pośredniczącego pojedynczego systemu SAP są wdrożone w [zestawie dostępności](../../manage-availability.md).
        - Jeśli nie potrzebujesz wysokiej dostępności dla usług SAP Central i systemu DBMS, możesz wdrożyć te maszyny wirtualne w tym samym zestawie dostępności co warstwa aplikacji SAP.
        - W przypadku ochrony usług SAP Central i warstwy DBMS w celu zapewnienia wysokiej dostępności przy użyciu replikacji pasywnej należy umieścić dwa węzły dla usług SAP Central w jednym osobnym zestawie dostępności i dwóch węzłach systemu DBMS w innym zestawie dostępności.
        - W przypadku wdrażania w Strefy dostępności platformy Azure nie można używać zestawów dostępności. Należy jednak upewnić się, że węzły Active i pasywny Central Services są wdrażane w dwóch różnych Strefy dostępnościach. Użyj Strefy dostępności, które mają najmniejsze opóźnienia między nimi.
          Należy pamiętać, że należy użyć [usługi Azure usługa Load Balancer w warstwie Standardowa](../../../load-balancer/load-balancer-standard-availability-zones.md) do użycia w przypadku tworzenia klastrów trybu failover systemu Windows lub Pacemaker dla warstwy DBMS i SAP Central Services w strefy dostępności. Nie można użyć [podstawowego Load Balancer](../../../load-balancer/load-balancer-overview.md) dla wdrożeń strefowych.
   5. Ustawienia limitu czasu.
        - Sprawdź ślady programu SAP NetWeaver dla deweloperów wystąpień SAP, aby upewnić się, że nie ma przerw między serwerem z kolejki i procesami roboczymi SAP. Te przerwy połączeń można uniknąć, ustawiając te dwa parametry rejestru:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. Aby uzyskać więcej informacji, zobacz [KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. Aby uzyskać więcej informacji, zobacz [KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - Aby uniknąć przekroczenia limitu czasu GRAFICZNEgo interfejsu użytkownika między lokalnymi interfejsami i warstwami aplikacji SAP wdrożonymi na platformie Azure, sprawdź, czy te parametry są ustawione w domyślnym. pfl lub profilu wystąpienia:
            - rdisp/keepalive_timeout = 3600
            - rdisp/utrzymywanie aktywności = 20
        - Aby zapobiec zakłóceniom ustanowionych połączeń między procesem kolejkowania SAP a procesami roboczymi SAP, należy ustawić **wartość true** dla parametru **umieścić/encni/set_so_keepalive** . Zobacz również artykuł [SAP uwagi #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Jeśli używasz konfiguracji klastra trybu failover systemu Windows, upewnij się, że czas reakcji na węzły, które nie odpowiada, jest ustawiony poprawnie dla platformy Azure. [Próg sieci klastra trybu failover dostrajania](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) zawiera listę parametrów i ich wpływ na sensitivities pracy awaryjnej. Przy założeniu, że węzły klastra znajdują się w tej samej podsieci, należy zmienić następujące parametry:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. Ustawienia lub poprawki systemu operacyjnego
        - W przypadku uruchamiania platformy HANA w oprogramowaniu SAP Przeczytaj następujące informacje i dokumenty:
            -   [Uwaga dotycząca pomocy technicznej SAP #2814271 — SAP HANA kopia zapasowa na platformie Azure kończy się niepowodzeniem](https://launchpad.support.sap.com/#/notes/2814271)
            -   [Uwaga dotycząca pomocy technicznej SAP #2753418 potencjalne obniżenie wydajności z powodu powrotu czasomierza](https://launchpad.support.sap.com/#/notes/2753418)
            -   [Uwaga dotycząca pomocy technicznej SAP #2791572-spadek wydajności z powodu braku obsługi VDSO dla funkcji Hyper-V na platformie Azure](https://launchpad.support.sap.com/#/notes/2791572)
            -   [Uwaga dotycząca pomocy technicznej SAP #2382421 — Optymalizacja konfiguracji sieci na poziomie platformy HANA i systemu operacyjnego](https://launchpad.support.sap.com/#/notes/2382421)
            -   [Uwaga dotycząca pomocy technicznej SAP #2694118-Red Hat Enterprise Linux HA Add-On na platformie Azure](https://launchpad.support.sap.com/#/notes/2694118)
            -   [Uwaga dotycząca pomocy technicznej SAP #1984787 — SUSE LINUX Enterprise Server 12: uwagi dotyczące instalacji](https://launchpad.support.sap.com/#/notes/1984787)
            -   [Uwaga dotycząca pomocy technicznej SAP #2002167-Red Hat Enterprise Linux 7. x: Instalacja i uaktualnienie](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [Uwaga dotycząca pomocy technicznej SAP #2292690-SAP HANA DB: zalecane ustawienia systemu operacyjnego dla RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [Uwaga dotycząca pomocy technicznej SAP #2772999-Red Hat Enterprise Linux 8. x: Instalacja i konfiguracja](https://launchpad.support.sap.com/#/notes/2772999)
            -   [Uwaga dotycząca pomocy technicznej SAP #2777782-SAP HANA DB: zalecane ustawienia systemu operacyjnego dla RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [Uwaga dotycząca pomocy technicznej SAP #2578899-SUSE Linux Enterprise Server 15: Uwaga dotycząca instalacji](https://launchpad.support.sap.com/#/notes/2578899)
            -   [Uwaga dotycząca pomocy technicznej SAP #2455582-Linux: uruchamianie aplikacji SAP skompilowanych w serwisie 4. x](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [Uwaga dotycząca pomocy technicznej SAP #2729475-HWCCT nie powiodła się z powodu błędu "funkcja hypervisor nie jest obsługiwana" na maszynach wirtualnych platformy Azure certyfikowanych dla SAP HANA](https://launchpad.support.sap.com/#/notes/2729475)
1. Przetestuj procedury wysokiej dostępności i odzyskiwania po awarii.
   1. Symuluj sytuacje trybu failover, zamykając maszyny wirtualne (systemy operacyjne Windows gościa) lub umieszczając systemy operacyjne w trybie awaryjnego (systemy operacyjne gościa Linux). Ten krok pomoże Ci ustalić, czy konfiguracje trybu failover działają zgodnie z założeniami.
   1. Zmierz, jak długo trwa wykonywanie trybu failover. Jeśli czasy są zbyt długie, Rozważ następujące kwestie:
        - W przypadku systemu SUSE Linux Użyj urządzeń SBD zamiast agenta usługi Azure ogrodzenia, aby przyspieszyć pracę w trybie failover.
        - W przypadku SAP HANA, jeśli ponowne załadowanie danych trwa zbyt długo, należy rozważyć zapewnienie większej przepustowości magazynu.
   3. Przetestuj sekwencję wykonywania kopii zapasowej/przywracania i chronometrażu i wprowadź poprawki, jeśli zachodzi taka potrzeba. Upewnij się, że czasy wykonywania kopii zapasowej są wystarczające. Należy również przetestować działania przywracania i przywracania. Upewnij się, że czasy przywracania znajdują się w RTO umowy SLA wszędzie tam, gdzie RTO korzysta z bazy danych lub procesu przywracania maszyny wirtualnej.
   4. Testowanie funkcjonalności i architektury odzyskiwania między regionami.
1. Sprawdzanie zabezpieczeń.
   1. Przetestuj ważność architektury kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure. Celem jest oddzielenie i ograniczenie dostępu i uprawnień różnych zespołów. Na przykład członkowie zespołu podstawy SAP powinni mieć możliwość wdrażania maszyn wirtualnych i przypisywania dysków z usługi Azure Storage do danej sieci wirtualnej platformy Azure. Jednak zespół podstawy SAP nie powinien mieć możliwości tworzenia własnych sieci wirtualnych ani zmieniać ustawień istniejących sieci wirtualnych. Członkowie zespołu sieci nie powinni mieć możliwości wdrażania maszyn wirtualnych w sieciach wirtualnych, w których są uruchomione maszyny wirtualne aplikacji SAP i DBMS. Ani członkowie tego zespołu mogą zmieniać atrybuty maszyn wirtualnych, a nawet usuwać maszyny wirtualne lub dyski.  
   1.  Sprawdź, czy [sieciowe grupy zabezpieczeń i zasady ASC](../../../virtual-network/network-security-groups-overview.md) działają zgodnie z oczekiwaniami, i Włącz ochronę chronionych zasobów.
   1.  Upewnij się, że wszystkie zasoby, które muszą być szyfrowane, są szyfrowane. Zdefiniuj i Implementuj procesy w celu tworzenia kopii zapasowych certyfikatów, przechowywania i uzyskiwania dostępu do tych certyfikatów oraz przywracania zaszyfrowanych jednostek.
   1.  Użyj [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) dla dysków systemu operacyjnego, jeśli to możliwe, z punktu widzenia obsługi systemu operacyjnego.
   1.  Upewnij się, że nie używasz zbyt wielu warstw szyfrowania. W niektórych przypadkach warto używać Azure Disk Encryption wraz z jedną z metod Transparent Data Encryption systemu DBMS do ochrony różnych dysków lub składników na tym samym serwerze.  Na przykład na serwerze SAP DBMS Azure Disk Encryption (ADE) można włączyć na dysku rozruchowym systemu operacyjnego (Jeśli system operacyjny obsługuje ADE) i te dyski danych nie są używane przez pliki trwałości danych w systemie DBMS.  Przykładem jest użycie ADE na dysku przechowującym klucze szyfrowania TDE systemu DBMS.
1. Testowanie wydajnościowe. W oprogramowaniu SAP w oparciu o śledzenie i pomiary SAP należy wykonać następujące porównania:
   - W razie potrzeby Porównaj 10 najważniejszych raportów online z bieżącą implementacją.
   - W razie potrzeby Porównaj 10 najważniejszych zadań wsadowych z bieżącą implementacją.
   - Porównywanie transferów danych za pomocą interfejsów z systemem SAP. Zapoznaj się z interfejsami, w których wiesz, że przekazanie odbywa się między różnymi lokalizacjami, takimi jak lokalne, na platformę Azure.


## <a name="non-production-phase"></a>Faza nieprodukcyjna 
W tej fazie przyjmujemy, że po pomyślnym pilotażowym lub sprawdzaniu koncepcji (ZK) zaczynasz wdrożyć nieprodukcyjne systemy SAP na platformie Azure. Zapoznaj się ze wszystkimi poznaniami i doświadczeniem w ramach koncepcji ZK dla tego wdrożenia. Wszystkie kryteria i kroki wymienione dla POCs mają również zastosowanie do tego wdrożenia.

W tej fazie zwykle wdrażane są systemy deweloperskie, systemy testowania jednostek oraz systemy testowania regresji biznesowej na platformie Azure. Zalecamy, aby co najmniej jeden system nieprodukcyjny w jednym wierszu aplikacji SAP miał pełną konfigurację wysokiej dostępności, która będzie miała przyszły system produkcyjny. Poniżej przedstawiono kilka dodatkowych kroków, które należy wykonać w tej fazie:  

1.  Przed przeniesieniem systemów ze starej platformy na platformę Azure Zbierz dane dotyczące zużycia zasobów, takie jak użycie procesora CPU, przepustowość magazynu i dane IOPS. Szczególnie Zbieraj te dane z jednostek warstwy DBMS, ale również Zbieraj je z jednostek warstwy aplikacji. Należy również mierzyć opóźnienia sieci i magazynu.
2.  Zapisz wzorce czasu korzystania z dostępności dla systemów. Celem jest określenie, czy systemy nieprodukcyjne muszą być dostępne codziennie, codziennie czy też w przypadku niektórych faz tygodnia lub miesiąca można zamknąć systemy nieprodukcyjne.
3.  Przetestuj i ustal, czy chcesz utworzyć własne obrazy systemu operacyjnego dla maszyn wirtualnych na platformie Azure, czy chcesz użyć obrazu z galerii udostępnionych obrazów systemu Azure. Jeśli używasz obrazu z galerii obrazów udostępnionych, upewnij się, że używasz obrazu, który odzwierciedla umowę pomocy technicznej z dostawcą systemu operacyjnego. W przypadku niektórych dostawców systemów operacyjnych Galeria obrazów udostępnionych umożliwia korzystanie z własnych obrazów licencji. W przypadku innych obrazów systemu operacyjnego pomoc techniczna jest uwzględniona w cenie oferowanej przez platformę Azure. Jeśli zdecydujesz się utworzyć własne obrazy systemu operacyjnego, możesz znaleźć dokumentację w następujących artykułach:
    -   [Tworzenie uogólnionego obrazu maszyny wirtualnej z systemem Windows wdrożonej na platformie Azure](../../windows/capture-image-resource.md)
    -   [Tworzenie uogólnionego obrazu maszyny wirtualnej z systemem Linux wdrożonej na platformie Azure](../../linux/capture-image.md)
3.  Jeśli używasz obrazów SUSE i Red Hat Linux z galerii obrazów udostępnionych, musisz użyć obrazów dla SAP dostarczonych przez dostawców systemu Linux w galerii obrazów udostępnionych.
4.  Upewnij się, że spełniasz wymagania dotyczące pomocy technicznej SAP dla umów pomocy technicznej firmy Microsoft. Zobacz [uwagi dotyczące pomocy technicznej SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553). W przypadku dużych wystąpień HANA zapoznaj się z [wymaganiami dotyczącymi](./hana-onboarding-requirements.md)dołączania.
4.  Upewnij się, że odpowiednie osoby otrzymują [powiadomienia o planowanej konserwacji](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) , aby można było wybrać najlepsze przestoje.
5.  Często sprawdzaj prezentacje platformy Azure na kanałach takich jak [Channel 9](https://channel9.msdn.com/) , aby uzyskać nowe funkcje, które mogą być stosowane do wdrożeń.
6.  Sprawdź uwagi SAP związane z platformą Azure, takie jak [Pomoc techniczna #1928533](https://launchpad.support.sap.com/#/notes/1928533), dla nowych jednostek SKU maszyn wirtualnych i nowo obsługiwanych wersji systemów operacyjnych i DBMS. Porównaj ceny nowych typów maszyn wirtualnych z poprzednimi typami maszyn wirtualnych, aby można było wdrożyć maszyny wirtualne z najlepszym wskaźnikiem cen/wydajności.
7.  Sprawdź ponownie uwagi dotyczące pomocy technicznej SAP, katalog SAP HANA sprzętu i moduł PAM SAP. Upewnij się, że nie wprowadzono żadnych zmian w obsługiwanych maszynach wirtualnych dla platformy Azure, obsługiwanych wersjach systemu operacyjnego na tych maszynach wirtualnych oraz obsługiwanych wersjach oprogramowania SAP i DBMS.
8.  Sprawdź [witrynę sieci Web SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) pod kątem nowych jednostek SKU certyfikowanych przez platformę Hana na platformie Azure. Porównaj ceny nowych jednostek SKU z zaplanowanymi do użycia. Ostatecznie wprowadź niezbędne zmiany, aby użyć tych, które mają najlepszy współczynnik cen/wydajności.
9.  Dostosuj skrypty wdrażania do używania nowych typów maszyn wirtualnych i Uwzględnij nowe funkcje platformy Azure, których chcesz użyć.
10. Po wdrożeniu infrastruktury Przetestuj i Oceń opóźnienia sieci między maszynami wirtualnymi z warstwy aplikacji SAP i maszynami wirtualnymi DBMS, zgodnie z uwagami dotyczącymi pomocy technicznej SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) i [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Oceń wyniki na wskazówki dotyczące opóźnień sieci w programie [SAP support uwagi #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Opóźnienie sieci powinno być w zakresie umiarkowanym lub dobrym. Wyjątki dotyczą ruchu między maszynami wirtualnymi i jednostkami dużych wystąpień platformy HANA, zgodnie z opisem w [tym artykule](./hana-network-architecture.md#networking-architecture-for-hana-large-instance). Upewnij się, że żadne ograniczenia wymienione w temacie [uwagi dotyczące wdrażania systemu azure Virtual Machines DBMS dla obciążeń SAP](./dbms_guide_general.md#azure-network-considerations) i [SAP HANA konfiguracje infrastruktury i operacje na platformie Azure](./hana-vm-operations.md) mają zastosowanie do wdrożenia.
11. Upewnij się, że maszyny wirtualne są wdrożone w poprawnej [grupie umieszczania usługi Azure zbliżeniowe](../../linux/co-location.md), zgodnie z opisem w [grupach umieszczania usługi Azure zbliżeniowe w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP](sap-proximity-placement-scenarios.md)
11. Przed zastosowaniem obciążenia należy wykonać wszystkie pozostałe kontrole wymienione na etapie fazy koncepcji.
12. W miarę jak obowiązuje obciążenie, należy zarejestrować użycie zasobów przez systemy na platformie Azure. Porównaj to użycie z rekordami ze starej platformy. Dostosuj rozmiary maszyn wirtualnych w przyszłych wdrożeniach, Jeśli zobaczysz, że masz duże różnice. Należy pamiętać, że w przypadku, gdy Downsize, magazyn i przepustowość sieci maszyn wirtualnych zostaną również zredukowane.
    - [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Sizes for Linux virtual machines in Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure) 
13. Eksperymentowanie z funkcją i procesami kopiowania systemu. Celem jest ułatwienie kopiowania systemu deweloperskiego lub systemu testowego, dzięki czemu zespoły projektu mogą szybko uzyskiwać nowe systemy. 
14. Zoptymalizuj i stworzyć właściwy zespół oparty na rolach, uprawnienia i procesy platformy Azure, aby upewnić się, że masz Rozdzielenie obowiązków. W tym samym czasie upewnij się, że wszystkie zespoły mogą wykonywać swoje zadania w infrastrukturze platformy Azure.
15. Ćwiczenia, testowanie i dokumentowanie procedur wysokiej dostępności i odzyskiwania po awarii, aby umożliwić pracownikom wykonywanie tych zadań. Zidentyfikuj niedoskonałości i Dostosuj nowe funkcje platformy Azure, które są integrowane ze wdrożeniami.


## <a name="production-preparation-phase"></a>Faza przygotowania produkcyjnego 
W tej fazie należy zebrać informacje o doświadczeniu i Poznaniu w ramach wdrożeń nieprodukcyjnych i zastosować je do przyszłych wdrożeń produkcyjnych. Konieczne jest również przygotowanie pracy transferu danych między bieżącą lokalizacją hostingu i platformą Azure.

1.  Przed przejściem do platformy Azure należy wykonać uaktualnienia wydania SAP w systemach produkcyjnych.
1.  Zgadzają się z właścicielami biznesowymi w zakresie testów funkcjonalnych i gospodarczych, które należy wykonać po migracji systemu produkcyjnego.
1.  Upewnij się, że te testy zostały zakończone z systemami źródłowymi w bieżącej lokalizacji hostingu. Unikaj przeprowadzania testów po raz pierwszy po przeniesieniu systemu na platformę Azure.
1.  Przetestuj proces migrowania systemów produkcyjnych na platformę Azure. Jeśli nie przenosisz wszystkich systemów produkcyjnych na platformę Azure w tym samym przedziale czasowym, grupy kompilacji systemów produkcyjnych, które muszą znajdować się w tej samej lokalizacji hostingu. Migracja danych testowych. Oto kilka typowych metod:
    - Użyj metod systemu DBMS, takich jak Backup/Restore, w połączeniu z usługą SQL Server Always On, replikacja systemu HANA lub wysyłanie dziennika w celu wypróbowania i zsynchronizowania zawartości bazy danych na platformie Azure.
    - Użyj funkcji Backup/Restore dla mniejszych baz danych.
    - Aby przeprowadzić migracje heterogeniczne, należy użyć monitora migracji SAP zintegrowanego z usługą SAP SWPM.
    - Użyj procesu [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) , jeśli chcesz połączyć migrację z uaktualnieniem SAP Release. Należy pamiętać, że nie wszystkie kombinacje źródłowego systemu DBMS i docelowego systemu DBMS są obsługiwane. Więcej informacji można znaleźć w temacie uwagi dotyczące pomocy technicznej dla oprogramowania SAP dla różnych wersji programu DMO. Na przykład [opcja migracji bazy danych (DMO) z sumy 2,0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Należy sprawdzić, czy przepływność transferu danych jest lepsza przez Internet czy za pomocą ExpressRoute, na wypadek konieczności przeniesienia kopii zapasowych lub plików eksportu SAP. Jeśli przenosisz dane za pomocą Internetu, może być konieczna zmiana niektórych reguł grupy zabezpieczeń sieci/aplikacji, które należy wprowadzić w przyszłych systemach produkcyjnych.
1.  Przed przeniesieniem systemów ze starej platformy na platformę Azure Zbierz dane dotyczące użycia zasobów. Przydatne dane obejmują użycie procesora CPU, przepływność magazynu i dane IOPS. Szczególnie Zbieraj te dane z jednostek warstwy DBMS, ale również Zbieraj je z jednostek warstwy aplikacji. Należy również mierzyć opóźnienia sieci i magazynu.
1.  Sprawdź ponownie informacje o pomocy technicznej SAP i wymagane ustawienia systemu operacyjnego, katalog SAP HANA sprzętu i moduł PAM SAP. Upewnij się, że nie wprowadzono żadnych zmian w obsługiwanych maszynach wirtualnych dla platformy Azure, obsługiwanych wersjach systemu operacyjnego w tych maszynach wirtualnych oraz obsługiwanych wersjach oprogramowania SAP i DBMS.
1.  Zaktualizuj skrypty wdrażania, aby wziąć pod uwagę najnowsze decyzje dotyczące typów maszyn wirtualnych i funkcji platformy Azure.
1.  Po wdrożeniu infrastruktury i aplikacji Sprawdź, czy:
    - Wdrożono poprawne typy maszyn wirtualnych z prawidłowymi atrybutami i rozmiarami magazynu.
    - Maszyny wirtualne znajdują się w poprawnych i odpowiednich wersjach systemu operacyjnego oraz poprawek i są jednorodne.
    - Maszyny wirtualne są zaostrzone w razie potrzeby i w jednolity sposób.
    - Poprawna wersja i poprawki aplikacji zostały zainstalowane i wdrożone.
    - Maszyny wirtualne zostały wdrożone w zestawach dostępności platformy Azure zgodnie z harmonogramem.
    - Usługa Azure Premium Storage jest używana w przypadku dysków z uwzględnieniem opóźnień lub gdy wymagana jest [Umowa SLA dla jednej maszyny wirtualnej o 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) .
    - Usługa Azure akcelerator zapisu została wdrożona prawidłowo.
        - Upewnij się, że w ramach maszyn wirtualnych, miejsc do magazynowania lub zestawów rozłożonych zostały prawidłowo skompilowane na dyskach, które wymagają akcelerator zapisu.
        - Sprawdź [konfigurację RAID oprogramowania w systemie Linux](../../linux/configure-raid.md).
        - Sprawdź [konfigurację LVM na maszynach wirtualnych z systemem Linux na platformie Azure](../../linux/configure-lvm.md).
    - [Usługi Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) są używane wyłącznie.
    - Maszyny wirtualne zostały wdrożone w prawidłowych zestawach dostępności i Strefy dostępności.
    - [Przyspieszona sieć platformy Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jest włączona na maszynach wirtualnych używanych w warstwie aplikacji SAP i w warstwie systemu SAP DBMS.
    - Żadne [urządzenia wirtualne sieci platformy Azure](https://azure.microsoft.com/solutions/network-appliances/) nie znajdują się w ścieżce komunikacji między aplikacją SAP a warstwą DBMS systemów SAP w oparciu o SAP NetWeaver, Hybris lub S/4HANA.
    - Reguły grupy zabezpieczeń aplikacji i sieciowej grupy zabezpieczeń umożliwiają komunikację zgodnie z potrzebami i planowanych i zablokowanych komunikacji, gdy jest to wymagane.
    - Ustawienia limitu czasu są ustawiane poprawnie, zgodnie z wcześniejszym opisem.
    - Maszyny wirtualne są wdrażane w poprawnej [grupie umieszczania usługi Azure zbliżeniowe](../../linux/co-location.md), zgodnie z opisem w [grupach umieszczania usługi Azure zbliżeniowe w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP](sap-proximity-placement-scenarios.md)
    - Opóźnienie sieci między maszynami wirtualnymi warstwy aplikacji SAP i maszynami wirtualnymi DBMS jest testowane i sprawdzane zgodnie z opisem w temacie Informacje o pomocy technicznej SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) i [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Oceń wyniki na wskazówki dotyczące opóźnień sieci w programie [SAP support uwagi #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Opóźnienie sieci powinno być w zakresie umiarkowanym lub dobrym. Wyjątki dotyczą ruchu między maszynami wirtualnymi i jednostkami dużych wystąpień platformy HANA, zgodnie z opisem w [tym artykule](./hana-network-architecture.md#networking-architecture-for-hana-large-instance).
    - Szyfrowanie zostało zaimplementowane w razie potrzeby i z odpowiednią metodą szyfrowania.
    - Interfejsy i inne aplikacje mogą łączyć nowo wdrożoną infrastrukturę.
1.  Utwórz element PlayBook do zaplanowania zaplanowanej konserwacji platformy Azure. Określ kolejność, w jakiej systemy i maszyny wirtualne powinny być ponownie uruchamiane w celu przeprowadzenia zaplanowanej konserwacji.
    

## <a name="go-live-phase"></a>Faza go-Live
W fazie go-Live Pamiętaj, aby postępować zgodnie z elementy PlayBook opracowaną w trakcie wcześniejszych faz. Wykonaj kroki, które zostały przetestowane i praktyczne. Nie Akceptuję ostatnich zmian w konfiguracjach i procesach. Wykonaj również następujące czynności:

1. Sprawdź, czy Azure Portal monitorowanie i inne narzędzia do monitorowania działają. Zalecamy użycie monitora wydajności systemu Windows (PerfMon) dla systemów Windows i SAR dla systemu Linux.
    - Liczniki procesora CPU.
        - Średni czas procesora CPU, łącznie (wszystkie procesory CPU)
        - Średni czas procesora CPU, każdy procesor (128 procesorów na maszynach wirtualnych M128)
        - Czas jądra procesora CPU, każdy pojedynczy procesor
        - Czas użytkownika procesora CPU, każdy pojedynczy procesor
    - Memory (pamięć).
        - Wolna pamięć
        - Strona pamięci na sekundę
        - Stronicowana pamięć/sekundę
    - 3,5.
        - Odczyt dysku w KB/s, na poszczególnych dyskach
        - Odczyty dysku/s na poszczególnych dyskach
        - Odczyt dysku w mikrosekundach/odczyt, na pojedynczy dysk
        - Zapis na dysku w KB/s, na poszczególnych dyskach
        - Zapis na dysku/sekundę, na poszczególnych dyskach
        - Zapis na dysku w mikrosekundach/odczyt na poszczególnych dyskach
    - NFS.
        - Pakiety sieciowe w/s
        - Wychodzące pakiety sieciowe/s
        - Sieć KB na sekundę
        - Sieć w KB/s
1.  Po migracji danych wykonaj wszystkie testy weryfikacyjne, które zostały uzgodnione z właścicielami firmy. Akceptuj wyniki testów weryfikacyjnych tylko wtedy, gdy masz wyniki dla oryginalnych systemów źródłowych.
1.  Sprawdź, czy interfejsy działają i czy inne aplikacje mogą komunikować się z nowo wdrożonymi systemami produkcyjnymi.
1.  Sprawdź system transportu i korekty za pomocą STMS transakcji SAP.
1.  Wykonaj kopie zapasowe bazy danych po udostępnieniu systemu dla środowiska produkcyjnego.
1.  Wykonaj kopie zapasowe maszyn wirtualnych dla warstwy aplikacji SAP po wydaniu systemu dla środowiska produkcyjnego.
1.  W przypadku systemów SAP, które nie były częścią bieżącej fazy go-Live, ale które komunikują się z systemami SAP przenoszonymi na platformę Azure w ramach tej fazy go-Live, należy zresetować bufor nazw hostów w SM51. Spowoduje to usunięcie starych buforowanych adresów IP skojarzonych z nazwami wystąpień aplikacji przenoszonych na platformę Azure.  


## <a name="post-production"></a>Publikuj produkcję
Ta faza dotyczy monitorowania, działania i administrowania systemem. Z punktu widzenia oprogramowania SAP typowe zadania, które były wymagane do ukończenia w starej lokalizacji hostingu, mają zastosowanie. Wykonaj również te zadania specyficzne dla platformy Azure:

1. Przejrzyj faktury platformy Azure w przypadku systemów o wysokim obciążeniu.
2. Zoptymalizuj wydajność cen/wydajności po stronie maszyny wirtualnej i po stronie magazynu.
3. Zoptymalizuj czas, w którym można zamknąć system.  


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver](./planning-guide.md)
- [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver](./deployment-guide.md)
- [Zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](./dbms_guide_general.md)