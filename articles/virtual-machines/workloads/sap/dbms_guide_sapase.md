---
title: Wdrażanie oprogramowania SAP ASE Azure Virtual Machines DBMS dla obciążeń SAP | Microsoft Docs
description: Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e90c78e8e7cb474756c1a5ea03fd90c33e14300
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963588"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP

W tym dokumencie omówiono kilka różnych obszarów, które należy wziąć pod uwagę podczas wdrażania oprogramowania SAP ASE na platformie Azure IaaS. Jako warunek wstępny do tego dokumentu należy przeczytać [zagadnienia dotyczące dokumentu dotyczące wdrożenia systemu azure Virtual Machines DBMS dotyczące obciążeń SAP](dbms_guide_general.md) i innych przewodników w [obciążeniu SAP w dokumentacji platformy Azure](./get-started.md). Ten dokument obejmuje oprogramowanie SAP ASE działające w systemach operacyjnych Linux i Windows. Minimalna obsługiwana wersja na platformie Azure to SAP ASE 16.0.02 (wersja 16 Support Pack 2). Zaleca się wdrożenie najnowszej wersji oprogramowania SAP i najnowszego poziomu poprawek.  Zalecany jest minimalny poziom oprogramowania SAP ASE 16.0.03.07 (wersja 16 z pakietem Service Pack 3).  Najnowszą wersję oprogramowania SAP można znaleźć w temacie systemowy [harmonogram wersji ASE 16,0 i informacje o liście CR](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information).

Dodatkowe informacje o obsłudze wersji z aplikacjami SAP lub lokalizacją nośnika instalacyjnego znajdują się poza macierzą dostępności produktu SAP w następujących lokalizacjach:

- [Uwaga dotycząca pomocy technicznej SAP #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [Uwaga dotycząca pomocy technicznej SAP #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [Uwaga dotycząca pomocy technicznej SAP #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [Uwaga dotycząca pomocy technicznej SAP #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Uwaga: w całej dokumentacji w ramach i poza świecie SAP, nazwa produktu jest przywoływana jako Sybase ASE lub SAP ASE lub w niektórych przypadkach. Aby zachować spójność, użyjemy nazwy **SAP ASE** w tej dokumentacji.

## <a name="operating-system-support"></a>Obsługa systemów operacyjnych
Macierz dostępności produktu SAP zawiera obsługiwane kombinacje systemów operacyjnych i jądra SAP dla każdej aplikacji SAP.  Dystrybucje systemu Linux 12. x, SUSE 15. x, Red Hat 7. x są w pełni obsługiwane.  Oracle Linux jako system operacyjny dla oprogramowania SAP ASE nie jest obsługiwany.  Zalecane jest korzystanie z najnowszych dostępnych wydań systemu Linux. Klienci systemu Windows powinni używać wersji systemu Windows Server 2016 lub Windows Server 2019.  Starsze wersje systemu Windows, takie jak Windows 2012, są obsługiwane technicznie, ale Najnowsza wersja systemu Windows jest zawsze zalecana.


## <a name="specifics-to-sap-ase-on-windows"></a>Specyficzne dla oprogramowania SAP ASE w systemie Windows
Począwszy od Microsoft Azure można migrować istniejące aplikacje SAP ASE do usługi Azure Virtual Machines. System SAP ASE na maszynie wirtualnej platformy Azure umożliwia zredukowanie całkowitego kosztu posiadania wdrożenia, zarządzania i konserwacji aplikacji korporacyjnych przez łatwe Migrowanie tych aplikacji do Microsoft Azure. W przypadku oprogramowania SAP ASE na maszynie wirtualnej platformy Azure Administratorzy i deweloperzy mogą nadal korzystać z tych samych narzędzi programistycznych i administracyjnych, które są dostępne lokalnie.

Microsoft Azure oferuje wiele różnych typów maszyn wirtualnych, które pozwalają uruchamiać najmniejsze systemy SAP i Landscapes do dużych systemów SAP i Landscapes z tysiącami użytkowników. Numery punktów SAP ustalania rozmiarów dla różnych magazynów maszyn wirtualnych z certyfikatem SAP są udostępniane w [uwagi technicznej sap #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Dokumentację dotyczącą instalacji oprogramowania SAP ASE w systemie Windows można znaleźć w [podręczniku instalacji oprogramowania SAP ASE dla systemu Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

Blokowanie stron w pamięci jest ustawieniem uniemożliwiającym stronicowanie buforu bazy danych SAP ASE.  To ustawienie jest przydatne w przypadku dużych zajętych systemów z dużą ilością pamięci. Aby uzyskać więcej informacji, skontaktuj się z firmą BC-DB-SYB. 


## <a name="linux-operating-system-specific-settings"></a>Ustawienia specyficzne dla systemu operacyjnego Linux
Na maszynach wirtualnych z systemem Linux Uruchom polecenie `saptune` with profile SAP-ASE w systemie Linux, które powinny być domyślnie włączone i można je zweryfikować za pomocą polecenia  

`cat /proc/meminfo` 

Rozmiar strony jest zwykle 2048 KB. Aby uzyskać szczegółowe informacje, zobacz artykuł [ogromne strony w systemie Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Zalecenia dotyczące maszyn wirtualnych i struktur dysków dla wdrożeń oprogramowania SAP ASE

Aplikacje SAP ASE for SAP NetWeaver są obsługiwane na dowolnym typie maszyny wirtualnej wymienionym w temacie [Pomoc techniczna sap #1928533](https://launchpad.support.sap.com/#/notes/1928533) typowe typy maszyn wirtualnych używane do średniego rozmiaru serwerów baz danych SAP ASE to Esv3.  Duże bazy danych z obsługą wielu terabajtów mogą korzystać z typów maszyn wirtualnych serii M. Wydajność zapisu na dysku dziennika transakcji SAP ASE można ulepszyć, włączając akcelerator zapisu serii M. Akcelerator zapisu należy uważnie przetestować przy użyciu oprogramowania SAP ASE ze względu na sposób, w jaki środowisko SAP ASE wykonuje operacje zapisu dziennika.  Przejrzyj [uwagi dotyczące pomocy technicznej SAP #2816580](../../how-to-enable-write-accelerator.md) i Rozważ uruchomienie testu wydajnościowego.  
Akcelerator zapisu jest zaprojektowana tylko dla dysku dziennika transakcji. W pamięci podręcznej na poziomie dysku powinna być ustawiona wartość NONE. Nie należy zaistnieć, jeśli usługa Azure akcelerator zapisu nie będzie zawierać podobnych ulepszeń, tak jak w przypadku innych systemów DBMS. W zależności od sposobu, w jaki środowisko SAP ASE zapisuje w dzienniku transakcji, może to być spowodowane brakiem przyspieszenia przez akcelerator zapisu platformy Azure.
Dla urządzeń z danymi i dzienników zaleca się używanie oddzielnych dysków.  Systemowe bazy danych sybsecurity i `saptools` nie wymagają dysków dedykowanych i mogą być umieszczane na dyskach zawierających dane i dzienniki bazy danych SAP 

![Konfiguracja magazynu dla oprogramowania SAP ASE](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Systemy plików, rozmiar rozłożony & zrównoważenie we/wy 
Oprogramowanie SAP ASE zapisuje dane sekwencyjnie na urządzeniach magazynu dyskowego, chyba że zostały skonfigurowane inaczej. Oznacza to, że pusta baza danych SAP ASE z czterema urządzeniami będzie zapisywać dane tylko na pierwszym urządzeniu.  Inne urządzenia dyskowe będą zapisywane tylko wtedy, gdy pierwsze urządzenie zostanie zapełnione.  Liczba operacji we/wy odczytu i zapisu dla każdego urządzenia z oprogramowaniem SAP ASE może być inna. Aby zrównoważyć dysk we wszystkich dostępnych dyskach platformy Azure, należy użyć funkcji miejsca do magazynowania systemu Windows lub LVM2 Linux. W systemie Linux zaleca się używanie systemu plików XFS do formatowania dysków. Rozmiar rozłożonego LVM powinien być testowany z testem wydajności. rozmiar rozłożenia 128 KB jest dobrym punktem początkowym. W systemie Windows należy przetestować rozmiar jednostki alokacji systemu plików NTFS (Australia). 64 KB może służyć jako wartość początkowa. 

Zaleca się skonfigurowanie automatycznego rozszerzania bazy danych zgodnie z opisem w artykule [Konfigurowanie automatycznego rozszerzania przestrzeni bazy danych w oprogramowaniu SAP adaptacyjne Server Enterprise](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/)  i [sap support uwagi #1815695](https://launchpad.support.sap.com/#/notes/1815695). 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Przykładowe oprogramowanie SAP ASE na maszynie wirtualnej platformy Azure, konfiguracje systemu plików i dysku 
Poniższe szablony pokazują przykładowe konfiguracje dla systemów Linux i Windows. Przed potwierdzeniem konfiguracji maszyny wirtualnej i dysku upewnij się, że przydziały przepustowości sieci i magazynu poszczególnych maszyn wirtualnych są wystarczające do spełnienia wymagań firmy. Należy również pamiętać, że różne typy maszyn wirtualnych platformy Azure mają różną maksymalną liczbę dysków, które można dołączyć do maszyny wirtualnej. Na przykład maszyna wirtualna E4s_v3 ma limit przepływności we/wy magazynu 48 MB/s. Jeśli przepływność magazynu wymaganego przez działanie tworzenia kopii zapasowej bazy danych przekracza 48 MB/s, nie będzie możliwe uniknięcie większego typu maszyn wirtualnych o większej przepływności przepustowości magazynu. Podczas konfigurowania usługi Azure Storage należy również pamiętać, że szczególnie w przypadku [usługi Azure Premium Storage](../../premium-storage-performance.md) przepustowość i liczby operacji wejścia/wyjścia na sekundę na GB pojemności są zmieniane. Więcej informacji na ten temat zawiera artykuł [jakie typy dysków są dostępne na platformie Azure?](../../disks-types.md). Przydziały dla określonych typów maszyn wirtualnych platformy Azure są udokumentowane w artykule [zoptymalizowane rozmiary maszyn wirtualnych](../../sizes-memory.md) oraz połączone z nim artykuły. 

> [!NOTE]
>  Jeśli system DBMS jest przenoszony z zasobów lokalnych na platformę Azure, zaleca się przeprowadzenie monitorowania na maszynie wirtualnej i ocenę przepustowości procesora, pamięci, operacji we/wy i magazynu. Porównaj wartości szczytowe zaobserwowane z limitami przydziału maszyn wirtualnych udokumentowanymi w powyższych artykułach

Przykłady podane poniżej służą do celów ilustracyjnych i mogą być modyfikowane na podstawie indywidualnych potrzeb. Ze względu na projekt oprogramowania SAP ASE liczba urządzeń z danymi nie jest tak ważna jak w przypadku innych baz danych. Liczba urządzeń z danymi szczegółowo opisanych w tym dokumencie jest tylko przewodnikiem. 

Przykład konfiguracji małego serwera z programem SAP ASE DB o rozmiarze bazy danych wynoszącym od 50 GB do 250 GB, na przykład Menedżera rozwiązań SAP, może wyglądać następująco:

| Konfiguracja | Windows | Linux | Komentarze |
| --- | --- | --- | --- |
| Typ maszyny wirtualnej | E4s_v3 (4 vCPU/32 GB pamięci RAM) | E4s_v3 (4 vCPU/32 GB pamięci RAM) | --- |
| Accelerated Networking | Włącz | Włącz | ---|
| Wersja oprogramowania SAP ASE | 16.0.03.07 lub wyższy | 16.0.03.07 lub wyższy | --- |
| Liczba urządzeń z danymi | 4 | 4 | ---|
| Liczba urządzeń dziennika | 1 | 1 | --- |
| Liczba urządzeń tymczasowych | 1 | 1 | Więcej SAP BW obciążenia |
| System operacyjny | Windows Server 2019 | SUSE 12 SP4/15 SP1 lub RHEL 7,6 | --- |
| Agregacja dysku | Miejsca do magazynowania | LVM2 | --- |
| System plików | NTFS | XFS |
| Rozmiar bloku formatu | wymaga testowania obciążenia | wymaga testowania obciążenia | --- |
| Liczba dysków z danymi | Premium Storage: 2 x P10 (RAID0) | Premium Storage: 2 x P10 (RAID0)| Cache = tylko do odczytu |
| # i typ dysków dziennika | Premium Storage: 1 x P20  | Premium Storage: 1 x P20 | Pamięć podręczna = brak |
| Parametr MaxMemory środowiska ASE | 90% fizycznej pamięci RAM | 90% fizycznej pamięci RAM | założenie pojedynczego wystąpienia |
| Liczba urządzeń kopii zapasowej | 4 | 4| --- |
| # i typ dysków kopii zapasowej | 1 | 1 | --- |


Przykład konfiguracji dla średniego serwera z systemem operacyjnym SAP ASE z rozmiarem bazy danych wynoszącym 250 GB – 750 GB, na przykład w przypadku mniejszych systemów SAP Business Suite, może wyglądać następująco:

| Konfiguracja | Windows | Linux | Komentarze |
| --- | --- | --- | --- |
| Typ maszyny wirtualnej | E16s_v3 (16 vCPU/128 GB pamięci RAM) | E16s_v3 (16 vCPU/128 GB pamięci RAM) | --- |
| Accelerated Networking | Włącz | Włącz | ---|
| Wersja oprogramowania SAP ASE | 16.0.03.07 lub wyższy | 16.0.03.07 lub wyższy | --- |
| Liczba urządzeń z danymi | 8 | 8 | ---|
| Liczba urządzeń dziennika | 1 | 1 | --- |
| Liczba urządzeń tymczasowych | 1 | 1 | Więcej SAP BW obciążenia |
| System operacyjny | Windows Server 2019 | SUSE 12 SP4/15 SP1 lub RHEL 7,6 | --- |
| Agregacja dysku | Miejsca do magazynowania | LVM2 | --- |
| System plików | NTFS | XFS |
| Rozmiar bloku formatu | wymaga testowania obciążenia | wymaga testowania obciążenia | --- |
| Liczba dysków z danymi | Premium Storage: 4 x P20 (RAID0) | Premium Storage: 4 x P20 (RAID0)| Cache = tylko do odczytu |
| # i typ dysków dziennika | Premium Storage: 1 x P20  | Premium Storage: 1 x P20 | Pamięć podręczna = brak |
| Parametr MaxMemory środowiska ASE | 90% fizycznej pamięci RAM | 90% fizycznej pamięci RAM | założenie pojedynczego wystąpienia |
| Liczba urządzeń kopii zapasowej | 4 | 4| --- |
| # i typ dysków kopii zapasowej | 1 | 1 | --- |

Przykład konfiguracji małego serwera z systemem operacyjnym SAP ASE o rozmiarze bazy danych wynoszącym od 750 GB do 2000 GB, na przykład w większym systemie SAP Business Suite, może wyglądać następująco:

| Konfiguracja | Windows | Linux | Komentarze |
| --- | --- | --- | --- |
| Typ maszyny wirtualnej | E64s_v3 (64 vCPU/432 GB pamięci RAM) | E64s_v3 (64 vCPU/432 GB pamięci RAM) | --- |
| Accelerated Networking | Włącz | Włącz | ---|
| Wersja oprogramowania SAP ASE | 16.0.03.07 lub wyższy | 16.0.03.07 lub wyższy | --- |
| Liczba urządzeń z danymi | 16 | 16 | ---|
| Liczba urządzeń dziennika | 1 | 1 | --- |
| Liczba urządzeń tymczasowych | 1 | 1 | Więcej SAP BW obciążenia |
| System operacyjny | Windows Server 2019 | SUSE 12 SP4/15 SP1 lub RHEL 7,6 | --- |
| Agregacja dysku | Miejsca do magazynowania | LVM2 | --- |
| System plików | NTFS | XFS |
| Rozmiar bloku formatu | wymaga testowania obciążenia | wymaga testowania obciążenia | --- |
| Liczba dysków z danymi | Premium Storage: 4 x P30 (RAID0) | Premium Storage: 4 x P30 (RAID0)| Cache = tylko do odczytu |
| # i typ dysków dziennika | Premium Storage: 1 x P20  | Premium Storage: 1 x P20 | Pamięć podręczna = brak |
| Parametr MaxMemory środowiska ASE | 90% fizycznej pamięci RAM | 90% fizycznej pamięci RAM | założenie pojedynczego wystąpienia |
| Liczba urządzeń kopii zapasowej | 4 | 4| --- |
| # i typ dysków kopii zapasowej | 1 | 1 | --- |


Przykład konfiguracji małego serwera z systemem operacyjnym SAP ASE z rozmiarem bazy danych wynoszącym 2 TB +, na przykład w większym globalnie używanym systemie SAP Business Suite, może wyglądać jak

| Konfiguracja | Windows | Linux | Komentarze |
| --- | --- | --- | --- |
| Typ maszyny wirtualnej | Seria M (1,0 do 4,0 TB pamięci RAM)  | Seria M (1,0 do 4,0 TB pamięci RAM) | --- |
| Accelerated Networking | Włącz | Włącz | ---|
| Wersja oprogramowania SAP ASE | 16.0.03.07 lub wyższy | 16.0.03.07 lub wyższy | --- |
| Liczba urządzeń z danymi | 32 | 32 | ---|
| Liczba urządzeń dziennika | 1 | 1 | --- |
| Liczba urządzeń tymczasowych | 1 | 1 | Więcej SAP BW obciążenia |
| System operacyjny | Windows Server 2019 | SUSE 12 SP4/15 SP1 lub RHEL 7,6 | --- |
| Agregacja dysku | Miejsca do magazynowania | LVM2 | --- |
| System plików | NTFS | XFS |
| Rozmiar bloku formatu | wymaga testowania obciążenia | wymaga testowania obciążenia | --- |
| Liczba dysków z danymi | Premium Storage: 4 + x P30 (RAID0) | Premium Storage: 4 + x P30 (RAID0)| Cache = tylko do odczytu, rozważ użycie usługi Azure Ultra Disk |
| # i typ dysków dziennika | Premium Storage: 1 x P20  | Premium Storage: 1 x P20 | Pamięć podręczna = brak, rozważ użycie usługi Azure Ultra Disk |
| Parametr MaxMemory środowiska ASE | 90% fizycznej pamięci RAM | 90% fizycznej pamięci RAM | założenie pojedynczego wystąpienia |
| Liczba urządzeń kopii zapasowej | 16 | 16 | --- |
| # i typ dysków kopii zapasowej | 4 | 4 | Użyj LVM2/miejsca do magazynowania |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Zagadnienia dotyczące przywracania & kopii zapasowych dla oprogramowania SAP ASE na platformie Azure
Zwiększenie liczby urządzeń danych i kopii zapasowych zwiększa wydajność tworzenia kopii zapasowych i przywracania. Zalecane jest rozłożenie dysków platformy Azure, które obsługują urządzenie kopii zapasowej SAP ASE, jak pokazano w tabelach przedstawionych wcześniej. Należy zachować ostrożność, aby zrównoważyć liczbę urządzeń i dysków kopii zapasowej oraz zapewnić, że przepływność kopii zapasowych nie powinna przekraczać 40%-50% całkowitego limitu przydziału przepływności maszyny wirtualnej. Zalecane jest używanie kompresji kopii zapasowej SAP jako domyślnej. Więcej szczegółów można znaleźć w artykułach:

- [Uwaga dotycząca pomocy technicznej SAP #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [Uwaga dotycząca pomocy technicznej SAP #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [Uwaga dotycząca pomocy technicznej SAP #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Nie używaj D:\ dysków lub/temp miejsce jako miejsce docelowe bazy danych lub dziennika.

### <a name="impact-of-database-compression"></a>Wpływ kompresji bazy danych
W konfiguracjach, w których przepustowość we/wy może stać się czynnikem ograniczającym, miary, które zmniejszają liczbę IOPS może pomóc w rozciągnięciu obciążenia, można uruchomić w scenariuszu IaaS, takim jak platforma Azure. Dlatego zaleca się, aby przed przekazaniem istniejącej bazy danych SAP na platformę Azure była używana kompresja SAP ASE.

Zalecenie dotyczące zastosowania kompresji przed przekazaniem na platformę Azure wynika z kilku powodów:

* Ilość danych do przekazania na platformę Azure jest niższa
* Czas trwania wykonywania kompresji jest krótszy, przy założeniu, że jeden może korzystać z silniejszego sprzętu z większą liczbą procesorów CPU lub wyższą przepustowością operacji we/wy lub mniejszą zwłoką we/wy w środowisku lokalnym
* Mniejsze rozmiary baz danych mogą prowadzić do mniejszej ilości kosztów alokacji dysku

Dane i LOB-Compression pracy na maszynie wirtualnej hostowanej na platformie Azure Virtual Machines, ponieważ działa ona lokalnie. Aby uzyskać więcej informacji o tym, jak sprawdzić, czy kompresja jest już używana w istniejącej bazie danych SAP ASE, [należy sprawdzić uwagi dotyczące pomocy technicznej sap 1750510](https://launchpad.support.sap.com/#/notes/1750510). Aby uzyskać więcej szczegółowych informacji o sprawdzaniu kompresji bazy danych SAP ASE, zobacz [uwagi dotyczące pomocy technicznej sap #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Wysoka dostępność oprogramowania SAP ASE na platformie Azure 
W podręczniku użytkowników HADR Cluster szczegółowo zawarto konfigurację i konfigurację rozwiązania z 2 węzłami SAP ASE "Always-On".  Ponadto obsługiwany jest również trzeci węzeł odzyskiwania po awarii. Oprogramowanie SAP ASE obsługuje wiele konfiguracji o wysokiej dostępności, w tym dysk udostępniony i natywne klastrowanie systemu operacyjnego (zmiennoprzecinkowy adres IP). Jedyną obsługiwaną konfiguracją na platformie Azure jest korzystanie z Menedżera błędów bez zmiennoprzecinkowych adresów IP.  Metoda zmiennoprzecinkowego adresu IP nie będzie działała na platformie Azure.  Jądro SAP jest aplikacją "HA aware" i wie o podstawowym i pomocniczym serwerze SAP ASE. Nie ma żadnych bliskich integracji między programem SAP ASE i platformą Azure, wewnętrzny moduł równoważenia obciążenia platformy Azure nie jest używany. W związku z tym, standardowa dokumentacja SAP ASE powinna być stosowana od [podręcznika użytkowników programu SAP ASE HADR Cluster](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 

> [!NOTE]
> Jedyną obsługiwaną konfiguracją na platformie Azure jest korzystanie z Menedżera błędów bez zmiennoprzecinkowych adresów IP.  Metoda zmiennoprzecinkowego adresu IP nie będzie działała na platformie Azure. 

### <a name="third-node-for-disaster-recovery"></a>Trzeci węzeł odzyskiwania po awarii
Oprócz korzystania z programu SAP ASE Always-On w przypadku lokalnej wysokiej dostępności, można chcieć rozszerzenie konfiguracji na węzeł asynchronicznie replikowany w innym regionie świadczenia usługi Azure. Dokumentację tego scenariusza można znaleźć [tutaj](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199).

## <a name="sap-ase-database-encryption--ssl"></a>Szyfrowanie bazy danych SAP ASE & SSL 
Menedżer aprowizacji oprogramowania SAP (SWPM) zapewnia możliwość szyfrowania bazy danych podczas instalacji.  Jeśli chcesz użyć szyfrowania, zaleca się korzystanie z pełnego szyfrowania bazy danych SAP.  Zobacz szczegóły udokumentowane w:

- [Uwaga dotycząca pomocy technicznej SAP #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [Uwaga dotycząca pomocy technicznej SAP #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [Uwaga dotycząca pomocy technicznej SAP #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [Uwaga dotycząca pomocy technicznej SAP #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Jeśli baza danych SAP ASE jest zaszyfrowana, kompresja zrzutu kopii zapasowych nie będzie działała. Zobacz również temat [Pomoc techniczna SAP uwagi #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Lista kontrolna wdrażania oprogramowania SAP ASE na platformie Azure
 
- Wdrażanie oprogramowania SAP ASE 16.0.03.07 lub nowszego
- Aktualizacja do najnowszej wersji i poprawek elementów Faultmanager i SAPHostAgent
- Wdróż w najnowszym certyfikowanym systemie operacyjnym, takim jak Windows 2019, SUSE 15,1 lub RedHat 7,6 lub nowszy
- Używanie maszyn wirtualnych z certyfikatem SAP — dużej ilości jednostek SKU maszyn wirtualnych platformy Azure, takich jak Es_v3 lub dla dużych systemów x serii M
- Dopasuj liczbę operacji we/wy dysku i łączny zagregowany limit przepływności maszyny wirtualnej z projektem dysku.  Wdróż wystarczającą liczbę dysków
- Agregowanie dysków przy użyciu funkcji miejsca do magazynowania systemu Windows lub systemu Linux LVM2 z prawidłowym rozmiarem rozłożenia i systemem plików
- Tworzenie wystarczającej liczby urządzeń na potrzeby danych, dzienników, tymczasowych i kopii zapasowych
- Rozważ użycie UltraDisk dla dużych systemów x 
- Uruchamianie `saptune` oprogramowania SAP-ASE w systemie operacyjnym Linux 
- Zabezpieczanie bazy danych za pomocą szyfrowania bazy danych — ręcznie przechowuj klucze w Azure Key Vault 
- Ukończ [listę kontrolną SAP na platformie Azure](./sap-deployment-checklist.md) 
- Konfiguruj kopię zapasową dziennika i pełną kopię zapasową 
- Testowanie HA/DR, tworzenie kopii zapasowych i przywracanie oraz wykonywanie testów obciążeniowych & 
- Potwierdź, że automatyczne rozszerzenie bazy danych działa 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Monitorowanie wystąpień bazy danych za pomocą DBACockpit
W przypadku systemów SAP, które korzystają z oprogramowania SAP ASE jako platformy bazy danych, DBACockpit jest dostępny jako osadzone okna przeglądarki w transakcjach DBACockpit lub jako WebDynpro. Jednak Pełna funkcjonalność monitorowania i administrowania bazą danych jest dostępna tylko w implementacji WebDynpro tylko DBACockpit.

Zgodnie z systemami lokalnymi należy wykonać kilka kroków, aby włączyć wszystkie funkcje SAP NetWeaver używane przez implementację WebDynpro DBACockpit. Postępuj zgodnie z [uwagą pomocy technicznej SAP #1245200](https://launchpad.support.sap.com/#/notes/1245200) , aby włączyć użycie webdynpros i wygenerować wymagane. Wykonując instrukcje podane w powyższych informacjach, należy również skonfigurować program Internet Communications Manager ( `ICM` ) wraz z portami używanymi na potrzeby połączeń HTTP i https. Domyślne ustawienie dla protokołu HTTP wygląda następująco:

> ICM/server_port_0 = PROT = HTTP, PORT = 8000, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> ICM/server_port_1 = PROT = HTTPS, PORT = 443 $ $, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> 

a linki wygenerowane w ramach transakcji DBACockpit wyglądają podobnie do:

> https: \/ / \<fullyqualifiedhostname> : 44300/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> http: \/ / \<fullyqualifiedhostname> : 8000/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> 

W zależności od tego, jak maszyna wirtualna platformy Azure hostującym system SAP jest połączona z usługami AD i DNS, należy się upewnić, że ICM korzysta z w pełni kwalifikowanej nazwy hosta, którą można rozwiązać na komputerze, na którym jest otwierany DBACockpit. Zobacz [uwagi dotyczące pomocy technicznej SAP #773830](https://launchpad.support.sap.com/#/notes/773830) , aby zrozumieć, jak ICM określa w pełni kwalifikowaną nazwę hosta na podstawie parametrów profilu i w razie potrzeby jawnie ustaw parametr icm/host_name_full.

Jeśli maszyna wirtualna została wdrożona w scenariuszu Cloud-Only bez połączenia między środowiskiem lokalnym i platformą Azure, należy zdefiniować publiczny adres IP i `domainlabel` . Format publicznej nazwy DNS maszyny wirtualnej wygląda następująco:

> `<custom domainlabel`>. `<azure region`>. cloudapp.azure.com
> 
> 

Więcej szczegółów dotyczących nazwy DNS można znaleźć [tutaj] [Virtual-Machines-azurerm-a-azuresm].

Ustawienie opcji ICM/host_name_full parametru profilu SAP na nazwę DNS maszyny wirtualnej platformy Azure link może wyglądać podobnie do:

> https: \/ /mydomainlabel.westeurope.cloudapp.NET:44300/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> http: \/ /mydomainlabel.westeurope.cloudapp.NET:8000/SAP/BC/WebDynpro/SAP/dba_cockpit

W takim przypadku należy upewnić się, że:

* Dodaj reguły ruchu przychodzącego do sieciowej grupy zabezpieczeń w Azure Portal dla portów TCP/IP używanych do komunikacji z funkcją ICM
* Dodawanie reguł ruchu przychodzącego do konfiguracji zapory systemu Windows dla portów TCP/IP używanych do komunikowania się z funkcją ICM

W przypadku zautomatyzowanego importowania wszystkich dostępnych poprawek zaleca się okresowe stosowanie do nich uwagi SAP dotyczącej kolekcji poprawek:

* [Uwaga dotycząca pomocy technicznej SAP #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [Uwaga dotycząca pomocy technicznej SAP #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [Uwaga dotycząca pomocy technicznej SAP #1882376](https://launchpad.support.sap.com/#/notes/1882376)

Więcej informacji na temat panelu sterowania usługi DBA for SAP ASE można znaleźć w następujących informacjach o oprogramowaniu SAP:

* [Uwaga dotycząca pomocy technicznej SAP #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [Uwaga dotycząca pomocy technicznej SAP #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [Uwaga dotycząca pomocy technicznej SAP #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [Uwaga dotycząca pomocy technicznej SAP #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [Uwaga dotycząca pomocy technicznej SAP #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [Uwaga dotycząca pomocy technicznej SAP #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [Uwaga dotycząca pomocy technicznej SAP #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [Uwaga dotycząca pomocy technicznej SAP #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Przydatne linki, uwagi & oficjalne dokumenty dotyczące oprogramowania SAP ASE
Strona początkowa [dokumentacji oprogramowania SAP ASE 16.0.03.07](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) zawiera linki do różnych dokumentów, których dokumenty dotyczą:

- Kursy szkoleniowe dotyczące oprogramowania SAP ASE — monitorowanie & administracyjnych
- Kurs szkoleniowy dla oprogramowania SAP ASE — uaktualnienie & instalacji

są przydatne. Innym przydatnym dokumentem są [Aplikacje SAP w systemie SAP adaptacyjne Server Best Practices dla migracji i środowiska uruchomieniowego](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf).

Inne przydatne uwagi dotyczące pomocy technicznej SAP:

- [Uwaga dotycząca pomocy technicznej SAP #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [Uwaga dotycząca pomocy technicznej SAP #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [Uwaga dotycząca pomocy technicznej SAP #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [Uwaga dotycząca pomocy technicznej SAP #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [Uwaga dotycząca pomocy technicznej SAP #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [Uwaga dotycząca pomocy technicznej SAP #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [Uwaga dotycząca pomocy technicznej SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Uwaga dotycząca pomocy technicznej SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [Uwaga dotycząca pomocy technicznej SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [Uwaga dotycząca pomocy technicznej SAP #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [Uwaga dotycząca pomocy technicznej SAP #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [Uwaga dotycząca pomocy technicznej SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Uwaga dotycząca pomocy technicznej SAP #1588316](https://launchpad.support.sap.com/#/notes/158831) 

Inne informacje są publikowane na 

- [Aplikacje SAP w systemie SAP adaptacja serwera Enterprise](https://community.sap.com/topics/applications-on-ase)
- [InfoCenter środowiska SAP ASE](http://infocenter.sybase.com/help/index.jsp) 
- [Środowisko SAP ASE zawsze włączone z trzecią instalacją węzła DR](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

Biuletyn miesięczny jest publikowany za pomocą [uwagi technicznej SAP #2381575](https://launchpad.support.sap.com/#/notes/2381575) 


## <a name="next-steps"></a>Następne kroki
Sprawdź obciążenie artykułu [SAP na platformie Azure: Lista kontrolna planowania i wdrażania](./sap-deployment-checklist.md)