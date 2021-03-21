---
title: Najlepsze rozwiązania dotyczące macierzy wirtualnej StorSimple | Microsoft Docs
description: W tym artykule opisano najlepsze rozwiązania dotyczące wdrażania macierzy wirtualnej StorSimple i zarządzania nią.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 71b018da6b54ebf2b45a261378ea521a397159e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94964987"
---
# <a name="storsimple-virtual-array-best-practices"></a>StorSimple Virtual Array best practices (Najlepsze rozwiązania dotyczące macierzy wirtualnej StorSimple)

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Microsoft Azure StorSimple Virtual Array to zintegrowane rozwiązanie magazynu, które zarządza zadaniami magazynu między lokalnym urządzeniem wirtualnym działającym w ramach funkcji hypervisor i Microsoft Azure magazynu w chmurze. StorSimple Virtual Array to wydajna i ekonomiczna alternatywa dla macierzy fizycznej serii 8000. Macierz wirtualna może działać w istniejącej infrastrukturze funkcji hypervisor, obsługuje zarówno Protokoły iSCSI, jak i SMB, a także jest odpowiednia dla scenariuszy zdalnego biura/biura oddziału. Aby uzyskać więcej informacji na temat rozwiązań StorSimple, przejdź do [omówienia Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

W tym artykule opisano najlepsze rozwiązania zaimplementowane podczas wstępnej instalacji, wdrożenia i zarządzania macierzą wirtualną StorSimple. Te najlepsze rozwiązania zawierają sprawdzone wskazówki dotyczące instalacji i zarządzania macierzą wirtualną. Ten artykuł jest przeznaczony dla administratorów IT, którzy wdrażają tablice wirtualne i zarządzają nimi w swoich centrach danych.

Zalecamy okresowe przeglądy najlepszych rozwiązań w celu zapewnienia, że urządzenie jest nadal zgodne, gdy zmiany zostaną wprowadzone w ramach instalacji lub przepływu operacji. Jeśli podczas wdrażania tych najlepszych rozwiązań w macierzy wirtualnej wystąpią jakiekolwiek problemy, [skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-virtual-array-log-support-ticket.md) w celu uzyskania pomocy.

## <a name="configuration-best-practices"></a>Najlepsze rozwiązania dotyczące konfiguracji
Te najlepsze rozwiązania obejmują wytyczne, które należy wykonać podczas wstępnej instalacji i wdrożenia macierzy wirtualnych. Poniżej przedstawiono najlepsze rozwiązania związane z obsługą administracyjną maszyny wirtualnej, ustawieniami zasad grupy, rozmiarami, konfigurowaniem sieci, konfigurowaniem kont magazynu oraz tworzeniem udziałów i woluminów dla macierzy wirtualnej. 

### <a name="provisioning"></a>Inicjowanie obsługi
StorSimple Virtual Array to maszyna wirtualna (VM) obsługiwana w funkcji hypervisor (Hyper-V lub VMware) serwera hosta. Podczas aprowizacji maszyny wirtualnej upewnij się, że host może przeznaczyć wystarczającą ilość zasobów. Aby uzyskać więcej informacji, przejdź do [minimalnych wymagań dotyczących zasobów](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) , aby udostępnić tablicę.

Podczas aprowizacji macierzy wirtualnej należy zaimplementować następujące najlepsze rozwiązania:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Typ maszyny wirtualnej** |**Generacja 2** Maszyna wirtualna do użycia z systemem Windows Server 2012 lub nowszym oraz obrazem *VHDX* . <br></br> **Generacja 1** Maszyna wirtualna do użycia z systemem Windows Server 2008 lub nowszym oraz obrazem *VHD* . |Użyj maszyny wirtualnej w wersji 8 przy użyciu obrazu *. vmdk* . |
| **Typ pamięci** |Skonfiguruj jako **pamięć statyczną**. <br></br> Nie używaj opcji **pamięci dynamicznej** . | |
| **Typ dysku danych** |Inicjowanie obsługi **administracyjnej.**<br></br> **Stały rozmiar** zajmuje dużo czasu. <br></br> Nie należy używać opcji **różnicowych** . |Użyj opcji **alokowania elastycznego** . |
| **Modyfikowanie dysku danych** |Rozszerzanie lub zmniejszanie jest niedozwolone. Próba wykonania tej czynności spowoduje utratę wszystkich danych lokalnych na urządzeniu. |Rozszerzanie lub zmniejszanie jest niedozwolone. Próba wykonania tej czynności spowoduje utratę wszystkich danych lokalnych na urządzeniu. |

### <a name="sizing"></a>Ustalanie rozmiaru
Podczas określania wielkości macierzy wirtualnej StorSimple należy wziąć pod uwagę następujące czynniki:

* Rezerwacja lokalna dla woluminów lub udziałów. Około 12% miejsca jest zarezerwowane w warstwie lokalnej dla każdego udostępnionego woluminu warstwowego lub udziału. Około 10% miejsca jest również zarezerwowane dla woluminu przypiętego lokalnie dla systemu plików.
* Obciążenie migawki. Około 15% miejsca w warstwie lokalnej jest zarezerwowane dla migawek.
* Potrzeba do przywracania. W przypadku przywracania jako nowej operacji, ustalanie wielkości powinno uwzględniać miejsce na potrzeby przywracania. Przywracanie jest wykonywane w udziale lub woluminie o takim samym rozmiarze.
* Pewien bufor powinien zostać przydzielony do dowolnego nieoczekiwanego wzrostu.

W oparciu o powyższe czynniki wymagania dotyczące ustalania wielkości mogą być reprezentowane przez następujące równanie:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

W poniższych przykładach pokazano, jak można zmienić rozmiar tablicy wirtualnej zgodnie z wymaganiami.

#### <a name="example-1"></a>Przykład 1:
W macierzy wirtualnej chcesz mieć możliwość

* Udostępnij wolumin o pojemności 2 TB lub udział w poziomie.
* Inicjowanie obsługi administracyjnej woluminu lub udziału warstwowego o pojemności 1 TB.
* Zapewnij 300 GB woluminu przypiętego lokalnie lub udziału.

W przypadku poprzednich woluminów lub udziałów pozwól nam obliczyć wymagania dotyczące miejsca w warstwie lokalnej.

Po pierwsze dla każdego woluminu/udziału warstwowego rezerwacja lokalna będzie równa 12% rozmiaru woluminu/udziału. W przypadku przypiętego lokalnie woluminu/udziału lokalna rezerwacja wynosi 10% rozmiaru woluminu przypiętego lokalnie lub udziału (poza rozmiarem zainicjowanym). W tym przykładzie potrzebujesz

* Rezerwacja lokalna 240 GB (dla woluminu/udziału warstwowego 2 TB)
* Rezerwacja lokalna 120 GB (dla woluminu/udziału warstwowego 1 TB)
* 330 GB dla woluminów przypiętych lokalnie lub udziału (dodanie 10% rezerwacji lokalnej do rozmiaru o rozmiarze 300 GB)

Całkowite wymagane miejsce w warstwie lokalnej to: 240 GB + 120 GB + 330 GB = 690 GB.

Po drugie potrzebujemy co najmniej tak dużej ilości miejsca w warstwie lokalnej jako największej rezerwacji pojedynczej. Ta dodatkowa kwota jest używana w przypadku konieczności przywrócenia z migawki w chmurze. W tym przykładzie największe rezerwacja lokalna to 330 GB (w tym rezerwacja dla systemu plików), dlatego należy dodać ją do 690 GB: 690 GB + 330 GB = 1020 GB.
Jeśli wykonasz kolejne dodatkowe operacje przywracania, zawsze można zwolnić miejsce z poprzedniej operacji przywrócenia.

Trzecia potrzeba 15% całkowitego miejsca lokalnego do przechowywania lokalnych migawek, dzięki czemu dostępna będzie tylko 85%. W tym przykładzie będzie to około 1020 GB = 0,85 miejsca na &ast; dysku z danymi, które zainicjowano. W związku z tym dysk danych z zainicjowaną obsługą będzie (1020 &ast; (1/0.85)) = 1200 GB = 1,20 TB ~ 1,25 TB (zaokrąglanie do najbliższego kwartyl)

W nieoczekiwanym wzroście i nowym przywracaniu należy udostępnić dysk lokalny o pojemności około 1,25 – 1,5 TB.

> [!NOTE]
> Zalecamy również, aby dysk lokalny został alokowany elastycznie. To zalecenie wynika z faktu, że miejsce przywracania jest wymagane tylko wtedy, gdy chcesz przywrócić dane starsze niż pięć dni. Odzyskiwanie na poziomie elementu umożliwia przywracanie danych z ostatnich pięciu dni bez konieczności dodatkowego miejsca na przywracanie.


#### <a name="example-2"></a>Przykład 2:
W macierzy wirtualnej chcesz mieć możliwość

* Udostępnianie woluminu warstwowego o pojemności 2 TB
* Udostępnianie woluminu przypiętego lokalnie 300 GB

W oparciu o 12% zarezerwowane miejsce lokalne dla woluminów warstwowych/udziałów i 10% dla lokalnie przypiętych woluminów/udziałów, potrzebujemy

* 240 GB rezerwacji lokalnej (dla 2 TB woluminu warstwowego/udziału)
* 330 GB dla woluminów przypiętych lokalnie lub udziału (do 10% rezerwacji lokalnej na miejsce zainicjowane 300 GB)

Łączna ilość miejsca wymaganego w warstwie lokalnej to: 240 GB + 330 GB = 570 GB

Minimalna przestrzeń lokalna wymagana do przywracania to 330 GB.

15% całkowitego dysku służy do przechowywania migawek, aby była dostępna tylko 0,85. Oznacza to, że rozmiar dysku to (900 &ast; (1/0.85)) = 1,06 TB ~ 1,25 TB (zaokrąglanie do najbliższego kwartyl)

W przypadku nieoczekiwanego wzrostu można zainicjować obsługę dysku lokalnego 1,25-1,5 TB.

### <a name="group-policy"></a>Zasady grupy
Zasady grupy to infrastruktura, która umożliwia implementowanie określonych konfiguracji dla użytkowników i komputerów. Ustawienia zasady grupy są zawarte w obiektach zasady grupy (GPO), które są połączone z następującymi kontenerami Active Directory Domain Services (AD DS): lokacje, domeny lub jednostki organizacyjne (OU). 

Jeśli Macierz wirtualna jest przyłączona do domeny, można do niej zastosować obiekty zasad grupy. Te obiekty zasad grupy mogą instalować aplikacje, takie jak oprogramowanie antywirusowe, które może niekorzystnie wpłynąć na działanie macierzy wirtualnej StorSimple.

W związku z tym zalecamy:

* Upewnij się, że tablica wirtualna ma własną jednostkę organizacyjną (OU) dla Active Directory.
* Upewnij się, że żadne obiekty zasad grupy (GPO) nie są stosowane do macierzy wirtualnej. Można zablokować dziedziczenie, aby upewnić się, że Macierz wirtualna (węzeł podrzędny) nie dziedziczy automatycznie żadnych obiektów zasad grupy z elementu nadrzędnego. Aby uzyskać więcej informacji, przejdź do [bloku Blokowanie dziedziczenia](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731076(v=ws.11)).

### <a name="networking"></a>Sieć
Konfiguracja sieci dla macierzy wirtualnej odbywa się za pomocą lokalnego interfejsu użytkownika sieci Web. Interfejs sieci wirtualnej jest włączany za pomocą funkcji hypervisor, w której zainicjowano obsługę macierzy wirtualnej. Na stronie [Ustawienia sieci](storsimple-virtual-array-deploy3-fs-setup.md) można skonfigurować adres IP, podsieć i bramę interfejsu sieci wirtualnej.  Można również skonfigurować podstawowy i pomocniczy serwer DNS, ustawienia czasu oraz opcjonalne ustawienia serwera proxy dla urządzenia. Większość konfiguracji sieci to jednorazowa konfiguracja. Przed wdrożeniem macierzy wirtualnej zapoznaj się z [wymaganiami dotyczącymi sieci StorSimple](storsimple-ova-system-requirements.md#networking-requirements) .

Podczas wdrażania macierzy wirtualnej zalecamy przestrzeganie następujących najlepszych rozwiązań:

* Upewnij się, że sieć, w której wdrożono macierz wirtualną, ma zawsze pojemność dedykowanej przepustowości internetowej 5 MB/s (lub więcej).
  
  * Przepustowość internetowa jest różna w zależności od charakterystyki obciążenia i współczynnika zmian danych.
  * Zmiana danych, którą można obsłużyć, jest bezpośrednio proporcjonalna do przepustowości Internetu. Przykładowo podczas tworzenia kopii zapasowej przepustowość 5 MB/s może obsłużyć zmianę danych wynoszącą około 18 GB w ciągu 8 godzin. W przypadku czterech razy większej przepustowości (20 MB/s) można obsłużyć cztery razy więcej zmian danych (72 GB).
* Upewnij się, że połączenie z Internetem jest zawsze dostępne. Sporadyczne lub zawodne połączenia internetowe z urządzeniami mogą skutkować utratą dostępu do danych w chmurze i może to spowodować nieobsługiwaną konfigurację.
* Jeśli planujesz wdrożenie urządzenia jako serwera iSCSI:
  
  * Zalecamy wyłączenie opcji **Pobierz adres IP automatycznie** (DHCP).
  * Skonfiguruj statyczne adresy IP. Należy skonfigurować podstawowy i pomocniczy serwer DNS.
  * Jeśli zdefiniowano wiele interfejsów sieciowych w macierzy wirtualnej, tylko pierwszy interfejs sieciowy (Domyślnie ten interfejs to **Ethernet**) może dotrzeć do chmury. Aby kontrolować typ ruchu, można utworzyć wiele interfejsów sieci wirtualnych w macierzy wirtualnej (skonfigurowany jako serwer iSCSI) i połączyć te interfejsy z różnymi podsieciami.
* Aby ograniczyć przepustowość chmury (używaną przez macierz wirtualną), skonfiguruj ograniczenie przepustowości routera lub zapory. Jeśli zdefiniujesz ograniczenie w funkcji hypervisor, zostaną one ograniczone do wszystkich protokołów, w tym iSCSI i SMB, a nie tylko z przepustowością chmury.
* Upewnij się, że synchronizacja czasu dla funkcji hypervisor jest włączona. W przypadku korzystania z funkcji Hyper-V wybierz macierz wirtualną w Menedżerze funkcji Hyper-V, przejdź do pozycji **ustawienia &gt; usługi integracji** i upewnij się, że jest zaznaczona opcja **synchronizacja czasu** .

### <a name="storage-accounts"></a>Konta magazynu
Macierz wirtualna StorSimple może być skojarzona z jednym kontem magazynu. To konto magazynu może być automatycznie wygenerowanym kontem magazynu, kontem w tej samej subskrypcji co usługa lub kontem magazynu związanym z inną subskrypcją. Aby uzyskać więcej informacji, zobacz jak [zarządzać kontami magazynu dla macierzy wirtualnej](storsimple-virtual-array-manage-storage-accounts.md).

Poniższe zalecenia dotyczą kont magazynu skojarzonych z wirtualną tablicą.

* W przypadku łączenia wielu tablic wirtualnych z jednym kontem magazynu współczynnik w maksymalnej pojemności (64 TB) dla macierzy wirtualnej oraz maksymalny rozmiar (500 TB) dla konta magazynu. Ogranicza to liczbę tablic wirtualnych o pełnym rozmiarze, które mogą być skojarzone z tym kontem magazynu, do około 7.
* Podczas tworzenia nowego konta magazynu
  
  * Zalecamy utworzenie go w regionie najbliższym zdalnej biurze/oddziału, w którym wdrożono macierz wirtualną StorSimple, aby zminimalizować opóźnienia.
  * Należy pamiętać, że nie można przenieść konta magazynu w różnych regionach. Nie można również przenieść usługi między subskrypcjami.
  * Użyj konta magazynu, które implementuje nadmiarowość między centrami danych. Geo-Redundant Storage (GRS), magazyn strefowo nadmiarowy (ZRS) i Magazyn lokalnie nadmiarowy (LRS) są obsługiwane na potrzeby używania z wirtualną tablicą. Aby uzyskać więcej informacji na temat różnych typów kont magazynu, przejdź do [replikacji usługi Azure Storage](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Udziały i woluminy
W przypadku macierzy wirtualnej StorSimple można udostępnić udziały, gdy jest ona skonfigurowana jako serwer plików i woluminy, gdy są skonfigurowane jako serwer iSCSI. Najlepsze rozwiązania dotyczące tworzenia udziałów i woluminów są związane z rozmiarem i skonfigurowanym typem.

#### <a name="volumeshare-size"></a>Rozmiar woluminu/udziału
W macierzy wirtualnej można udostępnić udziały, gdy jest ona skonfigurowana jako serwer plików i woluminy, gdy są skonfigurowane jako serwer iSCSI. Najlepsze rozwiązania związane z tworzeniem udziałów i woluminów odnoszą się do rozmiaru i skonfigurowanego typu. 

Należy pamiętać o następujących najlepszych rozwiązaniach dotyczących aprowizacji udziałów lub woluminów na urządzeniu wirtualnym.

* Rozmiary plików względem alokowanego rozmiaru udziału warstwowego mogą mieć wpływ na wydajność warstw. Praca z dużymi plikami może spowodować spowolnienie warstwy. Podczas pracy z dużymi plikami zalecamy, aby największy plik był mniejszy niż 3% rozmiaru udziału.
* W macierzy wirtualnej można utworzyć maksymalnie 16 woluminów/udziałów. W przypadku limitów rozmiaru przypiętych lokalnie i woluminów warstwowych/udziałów zawsze odwołują się do [limitów macierzy wirtualnych StorSimple](storsimple-ova-limits.md).
* Podczas tworzenia woluminu należy wziąć pod uwagę zużycie danych, jak również w przyszłości. Nie można później rozszerzyć woluminu lub udziału.
* Po utworzeniu woluminu/udziału nie można zmniejszyć rozmiaru woluminu/udziału w StorSimple.
* Podczas zapisywania do woluminu warstwowego w StorSimple, gdy dane woluminu osiągnie określony próg (względem lokalnego miejsca zarezerwowanego dla woluminu), operacja we/wy jest ograniczana. Kontynuowanie zapisu w tym woluminie powoduje znaczne spowolnienie operacji we/wy. Chociaż można zapisywać na woluminie warstwowym poza swoją zainicjowaną pojemnością (nie zatrzymasz aktywnie możliwości zapisu poza zainicjowaną pojemnością), zobaczysz powiadomienie o alercie, które spowoduje zasubskrybowanie subskrypcji. Po wyświetleniu alertu konieczne jest wykonanie środków zaradczych, takich jak usuwanie danych woluminu (rozwinięcie woluminu nie jest obecnie obsługiwane).
* W przypadku przypadków użycia odzyskiwania po awarii, ponieważ liczba dozwolonych udziałów/woluminów wynosi 16, a maksymalna liczba udziałów/woluminów, które mogą być przetwarzane równolegle, również wynosi 16, liczba udziałów/woluminów nie ma wpływu na cel i RTO.

#### <a name="volumeshare-type"></a>Typ woluminu/udziału
StorSimple obsługuje dwa typy woluminów/udziałów na podstawie użycia: lokalnie przypięte i warstwowe. Woluminy przypięte lokalnie/udziały są elastycznie udostępniane, a woluminy/udziały warstwowe są alokowane elastycznie. Po utworzeniu woluminu przypiętego lokalnie lub udziału nie można skonwertować na warstwowy lub *odwrotnie* .

Zalecamy wdrożenie następujących najlepszych rozwiązań podczas konfigurowania woluminów StorSimple/udziałów:

* Określ typ woluminu na podstawie obciążeń, które zamierzasz wdrożyć przed utworzeniem woluminu. Używaj woluminów przypiętych lokalnie dla obciążeń wymagających lokalnych gwarancji danych (nawet w przypadku przestoju w chmurze) i wymagających niskich opóźnień w chmurze. Po utworzeniu woluminu w macierzy wirtualnej nie można zmienić typu woluminu z lokalnego przypiętego na warstwowy lub *odwrotnie*. Na przykład utwórz woluminy przypięte lokalnie podczas wdrażania obciążeń SQL lub obciążeń obsługujących maszyny wirtualne (VM); Użyj woluminów warstwowych dla obciążeń udziałów plików.


#### <a name="volume-format"></a>Format woluminu
Po utworzeniu woluminów StorSimple na serwerze iSCSI należy zainicjować, zainstalować i sformatować woluminy. Ta operacja jest wykonywana na hoście połączonym z urządzeniem StorSimple. W przypadku instalowania i formatowania woluminów na hoście StorSimple zalecane są następujące najlepsze rozwiązania.

* Wykonaj szybkie formatowanie na wszystkich woluminach StorSimple.
* Podczas formatowania woluminu StorSimple Użyj rozmiaru jednostki alokacji (Australia) z 64 KB (wartość domyślna to 4 KB). Badanie na 64 KB opiera się na testowaniu wykonanym we własnym zakresie dla wspólnych obciążeń StorSimple i innych obciążeń.
* W przypadku używania macierzy wirtualnej StorSimple skonfigurowanej jako serwer iSCSI nie należy używać woluminów łączonych ani dysków dynamicznych, ponieważ te woluminy lub dyski nie są obsługiwane przez program StorSimple.

#### <a name="share-access"></a>Dostęp do udziału
Podczas tworzenia udziałów na serwerze plików macierzy wirtualnej postępuj zgodnie z następującymi wskazówkami:

* Podczas tworzenia udziału należy przypisać grupę użytkowników jako administratora udziału zamiast pojedynczego użytkownika.
* Możesz zarządzać uprawnieniami NTFS po utworzeniu udziału, edytując udziały za pomocą Eksploratora Windows.

#### <a name="volume-access"></a>Dostęp do woluminu
Podczas konfigurowania woluminów iSCSI w macierzy wirtualnej StorSimple należy kontrolować dostęp wszędzie tam, gdzie jest to konieczne. Aby określić, które serwery hosta mogą uzyskać dostęp do woluminów, tworzyć i kojarzyć rekordy kontroli dostępu (rekordami ACR) z woluminami StorSimple.

Podczas konfigurowania rekordami ACR dla woluminów StorSimple należy stosować następujące najlepsze rozwiązania:

* Zawsze należy skojarzyć co najmniej jeden ACR z woluminem.

* Podczas przypisywania więcej niż jednego ACR do woluminu upewnij się, że wolumin nie jest ujawniony w sposób, w którym może być jednocześnie dostępny przez więcej niż jeden nieklastrowany host. Jeśli do woluminu przypisano wiele rekordami ACR, zostanie wyświetlony komunikat ostrzegawczy umożliwiający przejrzenie konfiguracji.

### <a name="data-security-and-encryption"></a>Bezpieczeństwo i szyfrowanie danych
Wirtualna macierz StorSimple ma funkcje zabezpieczeń i szyfrowania danych, które zapewniają poufność i integralność danych. W przypadku korzystania z tych funkcji zaleca się wykonanie następujących najlepszych rozwiązań: 

* Zdefiniuj klucz szyfrowania magazynu w chmurze, aby generować szyfrowanie AES-256 przed wysłaniem danych z macierzy wirtualnej do chmury. Ten klucz nie jest wymagany, jeśli dane są szyfrowane i zaczynają się od. Klucz może być wygenerowany i bezpieczny przy użyciu systemu zarządzania kluczami, takiego jak [Magazyn kluczy Azure](../key-vault/general/overview.md).
* Podczas konfigurowania konta magazynu za pośrednictwem usługi StorSimple Manager upewnij się, że włączono tryb TLS, aby utworzyć bezpieczny kanał na potrzeby komunikacji sieciowej między urządzeniem StorSimple a chmurą.
* Wygeneruj ponownie klucze dla kont magazynu (przez uzyskanie dostępu do usługi Azure Storage) okresowo, aby uwzględnić wszelkie zmiany dostępu na podstawie zmienionej listy administratorów.
* Dane w macierzy wirtualnej są kompresowane i deduplikowane przed wysłaniem ich do platformy Azure. Nie zalecamy korzystania z usługi roli deduplikacji danych na hoście z systemem Windows Server.

## <a name="operational-best-practices"></a>Najlepsze rozwiązania w zakresie operacji
Najlepsze rozwiązania praktyczne to wskazówki, które należy stosować w trakcie codziennego zarządzania lub operacji macierzy wirtualnej. Te praktyki obejmują określone zadania zarządzania, takie jak wykonywanie kopii zapasowych, przywracanie z zestawu kopii zapasowych, wykonywanie trybu failover, dezaktywowanie i usuwanie macierzy, monitorowanie użycia systemu i kondycji oraz uruchamianie skanów wirusów w macierzy wirtualnej.

### <a name="backups"></a>Tworzenie kopii zapasowych
Kopie zapasowe danych w macierzy wirtualnej są tworzone w chmurze na dwa sposoby — domyślnie zautomatyzowana, automatyczna kopia zapasowa całego urządzenia, rozpoczynając od 22:30 lub przez ręczną kopię zapasową na żądanie. Domyślnie urządzenie automatycznie tworzy codzienne migawki w chmurze wszystkich znajdujących się na niej danych. Aby uzyskać więcej informacji, przejdź do [kopii zapasowej macierzy wirtualnej StorSimple](storsimple-virtual-array-backup.md).

Nie można zmienić częstotliwości i przechowywania skojarzonych z domyślnymi kopiami zapasowymi, ale można skonfigurować czas, w którym codzienne kopie zapasowe są inicjowane codziennie. Podczas konfigurowania czasu rozpoczęcia dla zautomatyzowanych kopii zapasowych zalecamy:

* Zaplanuj kopie zapasowe w godzinach poza godzinami szczytu. Godzina rozpoczęcia tworzenia kopii zapasowej nie powinna być zbieżna z licznymi operacjami we/wy hosta.
* Zainicjuj ręczne tworzenie kopii zapasowej na żądanie podczas planowania przełączenia urządzenia w tryb failover lub przed oknem obsługi, aby chronić dane w macierzy wirtualnej.

### <a name="restore"></a>Przywracanie
Można przywrócić z zestawu kopii zapasowych na dwa sposoby: przywrócić do innego woluminu lub udziału lub wykonać odzyskiwanie na poziomie elementu (dostępne tylko w przypadku macierzy wirtualnej skonfigurowanej jako serwer plików). Odzyskiwanie na poziomie elementu umożliwia przeprowadzenie szczegółowego odzyskiwania plików i folderów z kopii zapasowej w chmurze wszystkich udziałów na urządzeniu StorSimple. Aby uzyskać więcej informacji, przejdź do [przywracania z kopii zapasowej](storsimple-virtual-array-clone.md).

Podczas wykonywania przywracania należy pamiętać o następujących kwestiach:

* Wirtualna macierz StorSimple nie obsługuje przywracania w miejscu. Można to jednak łatwo osiągnąć przez dwuetapowy proces: Zwolnij miejsce na macierzy wirtualnej, a następnie Przywróć inny wolumin/udział.
* W przypadku przywracania z woluminu lokalnego należy pamiętać, że przywracanie będzie długotrwałą operacją. Mimo że wolumin może szybko przechodzić w tryb online, dane są nadal odwodnione w tle.
* Typ woluminu pozostaje taki sam podczas procesu przywracania. Wolumin warstwowy jest przywracany do innego woluminu warstwowego i wolumin przypięty lokalnie do innego woluminu przypiętego lokalnie.
* W przypadku próby przywrócenia woluminu lub udziału z zestawu kopii zapasowych w przypadku niepowodzenia zadania przywracania w portalu nadal można utworzyć wolumin docelowy lub udział. Ważne jest, aby usunąć ten nieużywany wolumin docelowy lub udział w portalu w celu zminimalizowania wszelkich przyszłych problemów wynikających z tego elementu.

### <a name="failover-and-disaster-recovery"></a>Tryb failover i odzyskiwanie po awarii
Tryb failover urządzenia umożliwia Migrowanie danych z urządzenia *źródłowego* w centrum danych do innego urządzenia *docelowego* znajdującego się w tej samej lub innej lokalizacji geograficznej. Tryb failover urządzenia jest przeznaczony dla całego urządzenia. Podczas pracy w trybie failover dane w chmurze dla urządzenia źródłowego zmieniają własność na urządzenie docelowe.

W przypadku macierzy wirtualnej StorSimple można przełączyć się w tryb failover tylko do innej macierzy wirtualnej zarządzanej przez tę samą usługę StorSimple Manager. Przełączenie w tryb failover do urządzenia z serii 8000 lub macierzy zarządzanej przez inną usługę StorSimple Manager (niż w przypadku urządzenia źródłowego) jest niedozwolone. Aby dowiedzieć się więcej na temat zagadnień dotyczących trybu failover, przejdź do [sekcji wymagania wstępne dotyczące](storsimple-virtual-array-failover-dr.md)przełączenia urządzenia w tryb failover.

W przypadku przełączenia w tryb failover dla macierzy wirtualnej należy pamiętać o następujących kwestiach:

* W przypadku planowanego przejścia w tryb failover zaleca się, aby przed zainicjowaniem trybu failover wszystkie woluminy/udziały zostały przełączony do trybu offline. Postępuj zgodnie z instrukcjami specyficznymi dla systemu operacyjnego, aby najpierw przełączyć woluminy/udziały w tryb offline na hoście, a następnie przełączyć je do trybu offline na urządzeniu wirtualnym.
* W przypadku odzyskiwania po awarii serwera plików (DR) zaleca się dołączenie urządzenia docelowego do tej samej domeny, co źródło, aby uprawnienia do udziału były automatycznie rozwiązywane. W tej wersji jest obsługiwane tylko przejście w tryb failover do urządzenia docelowego w tej samej domenie.
* Po pomyślnym zakończeniu odzyskiwania po awarii urządzenie źródłowe zostanie automatycznie usunięte. Chociaż urządzenie nie jest już dostępne, maszyna wirtualna, która została zainicjowana w systemie hosta, nadal zużywa zasoby. Zalecamy usunięcie tej maszyny wirtualnej z systemu hosta, aby uniknąć naliczania opłat.
* Należy pamiętać, że nawet jeśli przejście w tryb failover nie powiedzie się, **dane są zawsze bezpieczne w chmurze**. Rozważ następujące trzy scenariusze, w których praca w trybie failover nie została pomyślnie ukończona:
  
  * Wystąpił błąd podczas początkowych etapów trybu failover, takich jak podczas przeprowadzania wstępnego sprawdzania odzyskiwania po awarii. W takiej sytuacji urządzenie docelowe nadal będzie można użyć. Możesz ponowić próbę przełączenia w tryb failover na tym samym urządzeniu docelowym.
  * Wystąpił błąd podczas rzeczywistego procesu przełączania do trybu failover. W takim przypadku urządzenie docelowe jest oznaczone jako niezdatne do użytku. Musisz zainicjować obsługę administracyjną i skonfigurować inną docelową macierz wirtualną i użyć jej do przejścia w tryb failover.
  * Przełączenie w tryb failover zostało ukończone, gdy urządzenie źródłowe zostało usunięte, ale urządzenie docelowe ma problemy i nie można uzyskać dostępu do żadnych danych. Dane są nadal bezpieczne w chmurze i można je łatwo pobrać, tworząc kolejną tablicę wirtualną, a następnie używając jej jako urządzenia docelowego na potrzeby odzyskiwania po awarii.

### <a name="deactivate"></a>Dezaktywuj
W przypadku dezaktywowania macierzy wirtualnej StorSimple można nawiązać połączenie między urządzeniem i odpowiadającą mu usługą StorSimple Manager. Dezaktywacja jest operacją **trwałą** i nie można jej cofnąć. Nie można ponownie zarejestrować dezaktywowanego urządzenia w usłudze StorSimple Manager. Aby uzyskać więcej informacji, przejdź do obszaru [dezaktywowanie i usuwanie macierzy wirtualnej StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Podczas dezaktywowania macierzy wirtualnej należy pamiętać o następujących najlepszych rozwiązaniach:

* Przed zdezaktywowaniem urządzenia wirtualnego Wykonaj migawkę w chmurze dla wszystkich danych. W przypadku dezaktywowania macierzy wirtualnej wszystkie dane na urządzeniu lokalnym zostaną utracone. Wykonanie migawki w chmurze umożliwi odzyskanie danych na późniejszym etapie.
* Przed dezaktywacją macierzy wirtualnej StorSimple należy zatrzymać lub usunąć klientów i hosty, które są zależne od tego urządzenia.
* Usuń zdezaktywowane urządzenie, jeśli nie jest już używane, aby nie naliczać opłat.

### <a name="monitoring"></a>Monitorowanie
Aby upewnić się, że Macierz wirtualna StorSimple jest w stanie ciągłej kondycji, musisz monitorować tablicę i upewnić się, że otrzymujesz informacje z systemu, w tym alerty. Aby monitorować ogólną kondycję macierzy wirtualnej, należy zaimplementować następujące najlepsze rozwiązania:

* Skonfiguruj monitorowanie, aby śledzić użycie dysku z dysku danych macierzy wirtualnych oraz dysk systemu operacyjnego. W przypadku korzystania z funkcji Hyper-V można użyć kombinacji System Center Virtual Machine Manager (SCVMM) i System Center Operations Manager do monitorowania hostów wirtualizacji.
* Skonfiguruj powiadomienia e-mail w macierzy wirtualnej w celu wysyłania alertów na określonych poziomach użycia.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Wyszukiwanie indeksu i aplikacje antywirusowe
Macierz wirtualna StorSimple może automatycznie warstwować dane z warstwy lokalnej do chmury Microsoft Azure. Gdy aplikacja, taka jak wyszukiwanie indeksu lub skanowanie w poszukiwaniu wirusów, jest używana do skanowania danych przechowywanych w usłudze StorSimple, należy zadbać o to, aby dane w chmurze nie były dostępne i ściągane z powrotem do warstwy lokalnej.

Zalecamy zaimplementowanie następujących najlepszych rozwiązań podczas konfigurowania wyszukiwania indeksu lub skanowania wirusów w macierzy wirtualnej:

* Wyłącz wszystkie automatycznie skonfigurowane operacje pełnego skanowania.
* W przypadku woluminów warstwowych Skonfiguruj przeszukiwanie indeksu lub aplikację skanowania antywirusowego w celu przeprowadzenia skanowania przyrostowego. Spowoduje to przeskanowanie tylko nowych danych znajdujących się w warstwie lokalnej. Podczas operacji przyrostowej nie można uzyskać dostępu do danych, które są warstwowe w chmurze.
* Upewnij się, że skonfigurowano poprawne filtry i ustawienia wyszukiwania, aby były skanowane tylko odpowiednie typy plików. Na przykład pliki obrazów (JPEG, GIF i TIFF) i rysunki inżynieryjne nie powinny być skanowane podczas ponownego kompilowania indeksu przyrostowego lub pełnego.

W przypadku korzystania z procesu indeksowania systemu Windows postępuj zgodnie z następującymi wskazówkami:

* Nie należy używać indeksatora systemu Windows dla woluminów warstwowych, ponieważ odwołują duże ilości danych (TBs) z chmury, jeśli indeks wymaga ponownego skompilowania. Ponowne skompilowanie indeksu spowoduje pobranie wszystkich typów plików w celu indeksowania ich zawartości.
* Użyj procesu indeksowania systemu Windows dla woluminów przypiętych lokalnie, ponieważ spowoduje to uzyskanie dostępu tylko do danych w warstwach lokalnych w celu skompilowania indeksu (dane w chmurze nie będą dostępne).

### <a name="byte-range-locking"></a>Blokowanie zakresu bajtów
Aplikacje mogą blokować określony zakres bajtów w plikach. Jeśli blokowanie zakresu bajtów jest włączone dla aplikacji, które są zapisywane w StorSimple, warstwowe nie działa w macierzy wirtualnej. Aby przedziały działały, wszystkie obszary plików, do których uzyskuje się dostęp, powinny być odblokowane. Blokowanie zakresu bajtów nie jest obsługiwane w przypadku woluminów warstwowych w macierzy wirtualnej.

Zalecane miary umożliwiające złagodzenie blokowania zakresu bajtów obejmują:

* Wyłącz blokowanie zakresu bajtów w logice aplikacji.
* Użyj lokalnie przypiętych woluminów (zamiast warstwowych) dla danych skojarzonych z tą aplikacją. Woluminy przypięte lokalnie nie są warstwami w chmurze.
* W przypadku używania woluminów przypiętych lokalnie z włączonym blokowaniem zakresu bajtów wolumin może przejść do trybu online przed ukończeniem przywracania. W tych przypadkach należy poczekać na zakończenie przywracania.

## <a name="multiple-arrays"></a>Wiele tablic
Może być konieczne wdrożenie wielu tablic wirtualnych na potrzeby rosnącego zestawu roboczego danych, które mogą zostać rozlane w chmurze w taki sposób, aby miało to wpływ na wydajność urządzenia. W tych przypadkach najlepszym rozwiązaniem jest skalowanie urządzeń w miarę wzrostu zestawu roboczego. Wymaga to dodania co najmniej jednego urządzenia do lokalnego centrum danych. Podczas dodawania urządzeń można:

* Podziel bieżący zestaw danych.
* Wdróż nowe obciążenia na nowych urządzeniach.
* W przypadku wdrażania wielu tablic wirtualnych zaleca się, aby z perspektywy równoważenia obciążenia rozesłać tablicę między różnymi hostami funkcji hypervisor.
* Wiele macierzy wirtualnych (w przypadku skonfigurowania serwera plików lub serwera iSCSI) można wdrożyć w przestrzeni nazw rozproszony system plików. Aby uzyskać szczegółowe instrukcje, przejdź do [rozwiązania rozproszony system plików przestrzeń nazw z przewodnikiem wdrażania hybrydowego magazynu w chmurze](https://www.microsoft.com/download/details.aspx?id=45507). Replikacja rozproszony system plików nie jest obecnie zalecana do użycia z wirtualną macierzą. 

## <a name="see-also"></a>Zobacz też
Dowiedz się, jak [administrować macierzą wirtualną StorSimple](storsimple-virtual-array-manager-service-administration.md) za pośrednictwem usługi StorSimple Manager.