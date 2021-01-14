---
title: Instalowanie SAP HANA na SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Jak zainstalować SAP HANA na SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81d44dae0fed45d4a4df76973c7e233fd71baff1
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98198972"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Jak zainstalować i skonfigurować SAP HANA (duże wystąpienia) na platformie Azure

Przed zapisaniem tego artykułu zapoznaj się z [typowymi wystąpieniami platformy Hana i jednostkami](hana-know-terms.md) [SKU dużych wystąpień platformy Hana](hana-available-skus.md).

Ponosisz odpowiedzialność za instalację SAP HANA. Po nawiązaniu połączenia między sieciami wirtualnymi platformy Azure i jednostkami dużej instancji HANA można rozpocząć instalowanie nowego SAP HANA na serwerze Azure (duże wystąpienia). 

> [!Note]
> W ramach zasad SAP instalacja SAP HANA musi być wykonywana przez osobę, która przekazała certyfikowany egzamin związany z technologią SAP, SAP HANA egzaminem certyfikacji instalacji lub który jest integratorem systemu z certyfikatem SAP (SI).

Planując instalację platformy HANA 2,0, zobacz [uwagi dotyczące pomocy technicznej SAP #2235581-SAP HANA: obsługiwane systemy operacyjne](https://launchpad.support.sap.com/#/notes/2235581/E) , aby upewnić się, że system operacyjny jest obsługiwany przez zainstalowaną wersję SAP HANA. Obsługiwane systemy operacyjne dla platformy HANA 2,0 są bardziej restrykcyjne niż obsługiwane systemy operacyjne dla platformy HANA 1,0. Musisz również sprawdzić, czy wersja systemu operacyjnego, której szukasz, jest wymieniona jako obsługiwana dla określonej jednostki. [](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Kliknij jednostkę, aby uzyskać wszystkie szczegóły z listą obsługiwanych systemów operacyjnych tej jednostki. 

Przed rozpoczęciem instalacji platformy HANA Sprawdź poprawność następujących danych:
- [Jednostki:/s](#validate-the-hana-large-instance-units)
- [Konfiguracja systemu operacyjnego](#operating-system)
- [Konfiguracja sieci](#networking)
- [Konfiguracja usługi Storage](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Sprawdź poprawność jednostek dużych wystąpień platformy HANA

Po otrzymaniu przez firmę Microsoft jednostki dużego wystąpienia usługi HANA Sprawdź poprawność następujących ustawień i dostosuj je w razie potrzeby.

**Pierwszy krok** po otrzymaniu dużego wystąpienia Hana i nawiązaniu dostępu i łączności z wystąpieniami polega na zaewidencjonowaniu Azure Portal niezależnie od tego, czy wystąpienia są wyświetlane z prawidłowymi jednostkami SKU i systemem operacyjnym. Przeczytaj [kontrolę nad dużymi wystąpieniami platformy Azure Hana za pomocą Azure Portal](./hana-li-portal.md) , aby uzyskać informacje niezbędne do przeprowadzenia kontroli.

**Drugi krok** po otrzymaniu dużego wystąpienia Hana i ustanowieniu dostępu i łączności z wystąpieniami polega na zarejestrowaniu systemu operacyjnego wystąpienia u dostawcy systemu operacyjnego. Ten krok obejmuje zarejestrowanie systemu operacyjnego SUSE Linux w wystąpieniu SUSE SMT, które zostało wdrożone na maszynie wirtualnej na platformie Azure. 

Jednostka dużego wystąpienia HANA może połączyć się z tym wystąpieniem SMT. (Aby uzyskać więcej informacji, zobacz [jak skonfigurować serwer SMT w systemie SUSE Linux](hana-setup-smt.md)). Alternatywnie system operacyjny Red Hat musi być zarejestrowany w programie Red Hat Subscription Manager, z którym należy się połączyć. Aby uzyskać więcej informacji, zobacz uwagi w artykule [co to jest SAP HANA na platformie Azure (duże wystąpienia)?](./hana-overview-architecture.md?toc=/azure/virtual-machines/linux/toc.json). 

Ten krok jest niezbędny do zastosowania poprawek do systemu operacyjnego, który jest odpowiedzialny za klienta. W przypadku oprogramowania SUSE Znajdź dokumentację dotyczącą instalowania i konfigurowania elementu SMT na tej stronie o [instalacji SMT](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**Trzeci krok** to sprawdzenie, czy są nowe poprawki i poprawki określonej wersji systemu operacyjnego. Sprawdź, czy poziom poprawek dużego wystąpienia HANA jest w najnowszym stanie. Mogą wystąpić sytuacje, w których nie dołączono najnowszych poprawek. Po przejściu przez jednostkę dużego wystąpienia HANA należy sprawdzić, czy należy zastosować poprawki.

**Czwarty krok** to zapoznaj się z odpowiednimi uwagami SAP na potrzeby instalowania i konfigurowania SAP HANA w określonej wersji systemu operacyjnego. Ze względu na zmieniające się zalecenia lub zmiany w informacjach lub konfiguracjach oprogramowania SAP, które są zależne od indywidualnych scenariuszy instalacji, firma Microsoft nie zawsze będzie w stanie dokładnie skonfigurować jednostkę dla dużych wystąpień platformy HANA. 

W związku z tym jest wymagane, aby klient mógł przeczytać uwagi SAP dotyczące SAP HANA dla dokładnej wersji systemu Linux. Sprawdź także konfiguracje wersji systemu operacyjnego/wersji i Zastosuj ustawienia konfiguracji, jeśli jeszcze tego nie zrobiono.

Sprawdź następujące parametry i ostatecznie Dostosuj do:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Począwszy od SLES12 SP1 i RHEL 7,2, te parametry muszą być ustawiane w pliku konfiguracji w katalogu/etc/sysctl.d. Na przykład musi zostać utworzony plik konfiguracji o nazwie 91-NetApp-HANA. conf. W przypadku starszych wersji SLES i RHEL te parametry muszą być ustawione w/etc/sysctl. conf.

W przypadku wszystkich wersji RHEL zaczynających się od RHEL 6,3 należy pamiętać o następujących kwestiach: 
- Parametr sunrpc.tcp_slot_table_entries = 128 musi być ustawiony w/etc/modprobe. d/sunrpc-Local. conf. Jeśli plik nie istnieje, należy go najpierw utworzyć, dodając wpis: 
    - Opcje sunrpc tcp_max_slot_table_entries = 128

**Piąty krok** polega na sprawdzeniu czasu systemowego jednostki dużego wystąpienia platformy Hana. Wystąpienia są wdrażane ze strefą czasową systemu. Ta strefa czasowa reprezentuje lokalizację regionu świadczenia usługi Azure, w którym znajduje się sygnatura dużego wystąpienia platformy HANA. Można zmienić czas systemowy lub strefę czasową wystąpień. 

Jeśli postanowisz więcej wystąpień w dzierżawie, musisz dostosować strefę czasową nowo dostarczonych wystąpień. Firma Microsoft nie ma wglądu w systemową strefę czasową skonfigurowaną z wystąpieniami po przekazują. W rezultacie nowo wdrożone wystąpienia mogą nie być ustawione w tej samej strefie czasowej, co wartość, która została zmieniona na. W razie potrzeby klient jest odpowiedzialny za dostosowanie strefy czasowej wystąpień, które zostały w nim przekazane. 

**Szósty krok** to sprawdzenie, czy są to etc/hosty. Gdy bloki są przekazywane, mają różne adresy IP, które są przypisane do różnych celów. Sprawdź plik etc/hosts. Gdy jednostki są dodawane do istniejącej dzierżawy, nie powinno być prawidłowo utrzymywane ani hosty nowo wdrożonych systemów przy użyciu adresów IP systemów, które zostały dostarczone wcześniej. Jest to Twoja odpowiedzialność jako klient, aby upewnić się, że nowo wdrożone wystąpienie może współdziałać i rozpoznać nazwy jednostek wdrożonych wcześniej w dzierżawie. 


## <a name="operating-system"></a>System operacyjny

Przestrzeń wymiany dla dostarczonego obrazu systemu operacyjnego jest ustawiana na 2 GB zgodnie z [uwagą do pomocy technicznej SAP #1999997 — często zadawane pytania: SAP HANA pamięci](https://launchpad.support.sap.com/#/notes/1999997/E). Jeśli chcesz użyć innego ustawienia, musisz ustawić je samodzielnie.

[SUSE Linux Enterprise Server 12 SP1 dla aplikacji SAP](https://www.suse.com/products/sles-for-sap/download/) to dystrybucja systemu Linux zainstalowanego dla SAP HANA na platformie Azure (duże wystąpienia). Ta określona dystrybucja udostępnia funkcje specyficzne dla SAP, czyli "poza Box" (włącznie z wstępnie ustawionymi parametrami na potrzeby uruchamiania oprogramowania SAP na SLES efektywnie).

Zapoznaj się z tematem [Biblioteka zasobów/oficjalne dokumenty](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) w witrynie sieci Web SUSE i [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) w sieci społeczności SAP (SCN) dla kilku użytecznych zasobów związanych z wdrażaniem SAP HANA w systemie SLES (w tym Konfiguracja wysokiej dostępności, zabezpieczenia, które są specyficzne dla operacji SAP itd.).

Poniżej znajduje się dodatkowy i przydatny system SAP dla linków związanych z SUSE:

- [SAP HANA w witrynie SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Najlepsze rozwiązania dotyczące oprogramowania SAP: replikacja z kolejki — SAP NetWeaver w systemie SUSE Linux Enterprise 12](https://www.suse.com/media/guide/SLES4SAP-NetWeaver-ha-guide-EnqRepl-12_color_en.pdf)
- [ClamSAP – SLES ochrona przed wirusami dla oprogramowania SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (w tym SLES 12 dla aplikacji SAP)

Poniżej przedstawiono uwagi dotyczące pomocy technicznej SAP, które mają zastosowanie w przypadku wdrażania SAP HANA w SLES 12:

- [Uwaga dotycząca pomocy technicznej SAP #1944799 — wytyczne SAP HANA dotyczące instalacji systemu operacyjnego SLES](http://service.sap.com/sap/support/notes/1944799)
- [Uwaga dotycząca pomocy technicznej SAP #2205917 — SAP HANA bazy danych zalecane ustawienia systemu operacyjnego dla SLES 12 dla aplikacji SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Uwaga dotycząca pomocy technicznej SAP #1984787 – SUSE Linux Enterprise Server 12: uwagi dotyczące instalacji](https://launchpad.support.sap.com/#/notes/1984787)
- [Uwaga dotycząca pomocy technicznej SAP #171356 — oprogramowanie SAP w systemie Linux: Informacje ogólne](https://launchpad.support.sap.com/#/notes/1984787)
- [Uwaga dotycząca pomocy technicznej SAP #1391070 – rozwiązania identyfikatora UUID systemu Linux](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux dla SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) to kolejna oferta do uruchamiania SAP HANA w dużych wystąpieniach platformy Hana. Wersje RHEL 7,2 i 7,3 są dostępne i obsługiwane. 

Poniżej znajdują się dodatkowe użyteczne rozwiązania SAP dotyczące linków z systemem Red Hat:
- [SAP HANA w witrynie Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Poniżej znajdują się uwagi dotyczące pomocy technicznej SAP, które mają zastosowanie do implementowania SAP HANA na Red Hat:

- [Uwaga dotycząca pomocy technicznej SAP #2009879-SAP HANA wytyczne dla systemu operacyjnego Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [Uwaga dotycząca pomocy technicznej SAP #2292690-SAP HANA DB: zalecane ustawienia systemu operacyjnego dla RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [Uwaga dotycząca pomocy technicznej SAP #1391070 – rozwiązania identyfikatora UUID systemu Linux](https://launchpad.support.sap.com/#/notes/1391070)
- [Uwaga dotycząca pomocy technicznej SAP #2228351-Linux: SAP HANA Database SPS 11 poprawka 110 (lub nowsza) w RHEL 6 lub SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [Uwaga dotycząca pomocy technicznej SAP #2397039 — często zadawane pytania: SAP w systemie RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [Uwaga dotycząca pomocy technicznej SAP #2002167-Red Hat Enterprise Linux 7. x: Instalacja i uaktualnienie](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Synchronizacja czasu

Aplikacje SAP, które są oparte na architekturze SAP NetWeaver, są wrażliwe na różnice czasu dla różnych składników wchodzących w skład systemu SAP. Krótkie zrzuty SAP ABAP z tytułem błędu ZDATE \_ duże różnice \_ czasu \_ są prawdopodobnie znane. Wynika to z faktu, że te krótkie zrzuty pojawiają się, gdy czas systemowy różnych serwerów lub maszyn wirtualnych jest zbyt daleko od siebie.

W przypadku SAP HANA na platformie Azure (duże wystąpienia) synchronizacja czasu wykonywana na platformie Azure nie ma zastosowania do jednostek obliczeniowych w sygnaturach dużych wystąpień. Ta synchronizacja nie ma zastosowania do uruchamiania aplikacji SAP na natywnych maszynach wirtualnych platformy Azure, ponieważ platforma Azure zapewnia poprawną synchronizację czasu systemu. 

W związku z tym należy skonfigurować oddzielny serwer czasu, który może być używany przez serwery aplikacji SAP działające na maszynach wirtualnych platformy Azure oraz wystąpienia bazy danych SAP HANA, które działają w dużych wystąpieniach HANA. Infrastruktura magazynu w sygnaturach dużych wystąpień jest synchronizowana z serwerami NTP.


## <a name="networking"></a>Sieć
Przyjęto założenie, że wykonano zalecenia dotyczące projektowania sieci wirtualnych platformy Azure oraz łączenia tych sieci wirtualnych z dużymi wystąpieniami HANA, zgodnie z opisem w następujących dokumentach:

- [Omówienie i architektura SAP HANA (duże wystąpienie) na platformie Azure](./hana-overview-architecture.md)
- [Infrastruktura i łączność SAP HANA (duże wystąpienia) na platformie Azure](hana-overview-infrastructure-connectivity.md)

Istnieją pewne szczegóły dotyczące sieci pojedynczych jednostek. Każda jednostka dużej instancji HANA ma dwa lub trzy adresy IP, które są przypisane do dwóch lub trzech portów kart sieciowych. Trzy adresy IP są używane w konfiguracjach skalowalnych w poziomie platformy HANA oraz w scenariuszu replikacji systemu HANA. Jeden z adresów IP przypisanych do karty sieciowej jednostki znajduje się poza pulą adresów IP serwera, która jest opisana w [SAP HANA (duże wystąpienia) omówienie i architektura na platformie Azure](./hana-overview-architecture.md).

Aby uzyskać więcej informacji na temat informacji o architekturze sieci Ethernet dla architektury, zobacz [obsługiwane scenariusze](hana-supported-scenario.md)dotyczące usługi HLI.

## <a name="storage"></a>Storage

Układ magazynu dla SAP HANA na platformie Azure (duże wystąpienia) jest konfigurowany przez SAP HANA na platformie Azure `service management` za pomocą zalecanych wytycznych dotyczących protokołu SAP. Te wytyczne zostały udokumentowane w dokumencie [SAP HANA wymagania dotyczące magazynu](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) . 

Ilościowe rozmiary różnych woluminów z różnymi jednostkami SKU dużych wystąpień usługi HANA są udokumentowane w [SAP HANA (duże wystąpienia) przegląd i architektura na platformie Azure](hana-overview-architecture.md).

Konwencje nazewnictwa woluminów magazynu są wymienione w poniższej tabeli:

| Użycie magazynu | Nazwa instalacji | Nazwa woluminu | 
| --- | --- | ---|
| Dane platformy HANA | /hana/data/SID/mnt0000\<m> | Hana_data_SID_mnt00001_tenant_vol IP:/magazynu |
| Dziennik platformy HANA | /hana/log/SID/mnt0000\<m> | Hana_log_SID_mnt00001_tenant_vol IP:/magazynu |
| Kopia zapasowa dziennika HANA | /hana/log/backups | Hana_log_backups_SID_mnt00001_tenant_vol IP:/magazynu |
| Platformy HANA — udostępnione | /hana/shared/SID | Magazyn IP:/hana_shared_SID_mnt00001_tenant_vol/Shared |
| usr/SAP | /usr/sap/SID | Magazyn IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* to identyfikator systemu wystąpienia Hana. 

*Dzierżawca* to wewnętrzne Wyliczenie operacji podczas wdrażania dzierżawy.

Platforma HANA usr/SAP współużytkuje ten sam wolumin. Nomenklatura mountpoints zawiera identyfikator systemu wystąpień HANA, a także numer instalacji. W przypadku wdrożeń skalowalnych istnieje tylko jedna instalacja, na przykład mnt00001. Z drugiej strony wdrożenia skalowalne w poziomie są widoczne jako wiele instalacji, ponieważ masz procesy robocze i główne. 

W przypadku środowisk skalowalnych w poziomie, danych, dzienników i woluminów kopii zapasowych dzienników są udostępniane i dołączane do każdego węzła w konfiguracji skalowania w poziomie. W przypadku konfiguracji, które są wieloma wystąpieniami SAP, tworzony jest inny zestaw woluminów i dołączony do jednostki dużego wystąpienia HANA. Aby uzyskać szczegółowe informacje o układzie magazynu dla danego scenariusza, zobacz [obsługiwane scenariusze](hana-supported-scenario.md)dotyczące usługi HLI.

Podczas przeglądania jednostki dużego wystąpienia HANA należy zastanowić się, że jednostki są dostarczane z Generous woluminem dla platformy HANA/danych i że istnieje wolumin HANA/log/kopia zapasowa. Przyczyną użycia platformy HANA/danych jest to, że migawki magazynu, z których oferujemy klient, korzystają z tego samego woluminu dysku. Im więcej migawek magazynu jest wykonywanych przez migawki na przypisanych woluminach magazynu. 

Wolumin HANA/log/backup nie powinien być woluminem dla kopii zapasowych bazy danych. Ma rozmiar, który ma być używany jako wolumin kopii zapasowej dziennika transakcji platformy HANA. Aby uzyskać więcej informacji, zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md). 

Oprócz dostarczonego magazynu można zakupić dodatkową pojemność magazynu w przyrostach 1 TB. Ten dodatkowy magazyn można dodać jako nowe woluminy do dużego wystąpienia platformy HANA.

Podczas dołączania do SAP HANA na platformie Azure `service management` klient określa identyfikator użytkownika (UID) i identyfikator grupy (GID) dla użytkownika sidadm i grupy sapsys (na przykład: 1 000 500). Podczas instalacji systemu SAP HANA należy użyć tych samych wartości. Ze względu na to, że chcesz wdrożyć wiele wystąpień HANA w jednostce, uzyskasz wiele zestawów woluminów (jeden zestaw dla każdego wystąpienia). W związku z tym w czasie wdrażania należy zdefiniować:

- Identyfikator SID różnych wystąpień HANA (sidadm pochodzi od niego).
- Rozmiary pamięci różnych wystąpień platformy HANA. Rozmiar pamięci na wystąpienie definiuje rozmiar woluminów w poszczególnych zestawach poszczególnych woluminów.

Na podstawie zaleceń dotyczących dostawcy magazynu są skonfigurowane następujące opcje instalacji dla wszystkich zainstalowanych woluminów (bez rozruchowej jednostki LUN):

- NFS RW, Verse = 4, twarde, Timeo = 600, elementu rsize = 1048576, wsize = 1048576, intr, noatime, blokada 0 0

Te punkty instalacji są konfigurowane w/etc/fstab, jak pokazano na poniższej grafice:

![fstab woluminów zainstalowanych w jednostce dużego wystąpienia platformy HANA](./media/hana-installation/image1_fstab.PNG)

Dane wyjściowe polecenia DF-h w jednostce dużej instancji S72m HANA wyglądają następująco:

![Zrzut ekranu przedstawia dane wyjściowe polecenia dla jednostki dużego wystąpienia HANA.](./media/hana-installation/image2_df_output.PNG)


Kontroler magazynu i węzły w sygnaturach dużych wystąpień są synchronizowane z serwerami NTP. W przypadku synchronizacji SAP HANA na platformie Azure (duże wystąpienia) i maszynach wirtualnych platformy Azure z serwerem NTP nie powinno być znaczącego odróżnienia czasu między infrastrukturą a jednostkami obliczeniowymi na platformie Azure lub w sygnaturach dużych wystąpień.

Aby zoptymalizować SAP HANA do użytego poniżej magazynu, ustaw następujące parametry konfiguracji SAP HANA:

- max_parallel_io_requests 128
- async_read_submit na
- async_write_submit_active na
- async_write_submit_blocks wszystkie
 
W przypadku wersji SAP HANA 1,0 do SPS12 te parametry można ustawić podczas instalacji bazy danych SAP HANA, zgodnie z opisem w temacie [SAP uwaga #2267798-Configuration SAP HANA Database](https://launchpad.support.sap.com/#/notes/2267798).

Możesz również skonfigurować parametry po instalacji bazy danych SAP HANA przy użyciu struktury hdbparam. 

Magazyn używany w dużych wystąpieniach platformy HANA ma ograniczenie rozmiaru pliku. [Ograniczenie rozmiaru wynosi 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) na plik. W przeciwieństwie do ograniczeń rozmiaru plików w systemach plików EXT3, HANA nie jest nieświadomy niejawnie ograniczenia magazynu wymuszonego przez magazyn dużych wystąpień platformy HANA. W efekcie program HANA nie utworzy automatycznie nowego pliku danych, gdy zostanie osiągnięty limit rozmiaru pliku 16TB. W miarę jak HANA próbuje zwiększyć plik poza 16 TB, program HANA zgłosi błędy i na końcu zakończy się awaria serwera indeksów.

> [!IMPORTANT]
> Aby zapobiec próbie zwiększenia rozmiaru plików danych poza 16 TB pamięci masowej magazynu platformy HANA, należy ustawić następujące parametry w pliku konfiguracji global.ini SAP HANA
> 
> - datavolume_striping = true
> - datavolume_striping_size_gb = 15000
> - Zobacz również artykuł SAP uwagi [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Należy pamiętać, że [#2631285](https://launchpad.support.sap.com/#/notes/2631285) uwagi SAP


W SAP HANA 2,0 platforma hdbparam jest przestarzała. W związku z tym parametry muszą być ustawiane za pomocą poleceń SQL. Aby uzyskać więcej informacji, zobacz temat [SAP uwagi #2399079: eliminacja hdbparam w Hana 2](https://launchpad.support.sap.com/#/notes/2399079).

Zapoznaj się z [scenariuszami obsługiwanymi](hana-supported-scenario.md) przez usługi HLI, aby dowiedzieć się więcej o układzie magazynu dla architektury.


**Następne kroki**

- Zapoznaj się z [instalacją platformy Hana na serwerze HLI](hana-example-installation.md)










































 







 
