---
title: Najważniejsze wskazówki dotyczące tablicy wirtualnej StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano najlepsze rozwiązania dotyczące wdrażania i zarządzania StorSimple Virtual Array.
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
ms.openlocfilehash: bdf69a9ff7b3260b47042f296a47826e3c52387b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460651"
---
# <a name="storsimple-virtual-array-best-practices"></a>StorSimple Virtual Array best practices (Najlepsze rozwiązania dotyczące macierzy wirtualnej StorSimple)

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Microsoft Azure StorSimple Virtual Array to zintegrowane rozwiązanie magazynu, które zarządza zadaniami magazynu między lokalnym urządzeniem wirtualnym działającym w hipernadzorcy a magazynem w chmurze platformy Microsoft Azure. StorSimple Virtual Array jest wydajną i ekonomiczną alternatywą dla macierzy fizycznej serii 8000. Macierz wirtualna może działać w istniejącej infrastrukturze funkcji hypervisor, obsługuje zarówno protokoły iSCSI, jak i SMB i doskonale nadaje się do scenariuszy zdalnego biura/oddziału. Aby uzyskać więcej informacji na temat rozwiązań StorSimple, przejdź do [przeglądu usługi Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

W tym artykule opisano najlepsze rozwiązania zaimplementowane podczas początkowej instalacji, wdrażania i zarządzania tablicą wirtualną StorSimple. Te najlepsze rozwiązania zawierają sprawdzone wskazówki dotyczące konfigurowania i zarządzania macierzą wirtualną. Ten artykuł jest skierowany do administratorów IT, którzy wdrażają tablice wirtualne w swoich centrach danych i zarządzają nimi.

Firma Microsoft zaleca okresowy przegląd najlepszych rozwiązań, aby upewnić się, że urządzenie jest nadal zgodne, gdy zmiany są wprowadzane do konfiguracji lub przepływu operacji. Jeśli podczas implementowania tych najlepszych rozwiązań w macierzy [wirtualnej](storsimple-virtual-array-log-support-ticket.md) wystąpią jakiekolwiek problemy, skontaktuj się z pomocą techniczną firmy Microsoft w celu uzyskania pomocy.

## <a name="configuration-best-practices"></a>Najważniejsze wskazówki dotyczące konfiguracji
Te najlepsze rozwiązania obejmują wytyczne, które muszą być przestrzegane podczas początkowej instalacji i wdrażania macierzy wirtualnych. Te najlepsze rozwiązania obejmują te związane z inicjowania obsługi administracyjnej maszyny wirtualnej, ustawienia zasad grupy, zmiany rozmiaru, konfigurowanie sieci, konfigurowanie kont magazynu i tworzenie udziałów i woluminów dla tablicy wirtualnej. 

### <a name="provisioning"></a>Inicjowanie obsługi
StorSimple Virtual Array to maszyna wirtualna (VM) aprowizowana na hipernadzorcy (Hyper-V lub VMware) serwera hosta. Podczas inicjowania obsługi administracyjnej maszyny wirtualnej, upewnij się, że host jest w stanie poświęcić wystarczające zasoby. Aby uzyskać więcej informacji, przejdź do [minimalnych wymagań dotyczących zasobów,](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) aby aprowizować tablicę.

Implementowanie następujących najlepszych rozwiązań podczas inicjowania obsługi administracyjnej tablicy wirtualnej:

|  | Funkcja Hyper-V | VMware |
| --- | --- | --- |
| **Typ maszyny wirtualnej** |**Generacja 2** Maszyna wirtualna do użytku z systemem Windows Server 2012 lub nowszym oraz obrazem *vhdx.* <br></br> **Generacja 1** Maszyna wirtualna do użytku z systemem Windows Server 2008 lub nowszym i obrazem *vhd.* |Użyj maszyny wirtualnej w wersji 8 podczas korzystania z obrazu *vmdk.* |
| **Typ pamięci** |Skonfiguruj jako **pamięć statyczną**. <br></br> Nie należy używać opcji **pamięci dynamicznej.** | |
| **Typ dysku danych** |Udostępnianie jako **dynamicznie rozwijające się**.<br></br> **Stały rozmiar** zajmuje dużo czasu. <br></br> Nie należy używać opcji **różnicowania.** |Użyj opcji **thin provision.** |
| **Modyfikacja dysku danych** |Rozszerzanie lub zmniejszanie jest niedozwolone. Próba w tym celu powoduje utratę wszystkich danych lokalnych na urządzeniu. |Rozszerzanie lub zmniejszanie jest niedozwolone. Próba w tym celu powoduje utratę wszystkich danych lokalnych na urządzeniu. |

### <a name="sizing"></a>Ustalanie rozmiaru
Podczas zmiany rozmiaru tablicy wirtualnej StorSimple należy wziąć pod uwagę następujące czynniki:

* Lokalna rezerwacja wolumenów lub udziałów. Około 12% miejsca jest zarezerwowane w warstwie lokalnej dla każdego aprowizowanego woluminu warstwowego lub udziału. Około 10% miejsca jest również zarezerwowane dla woluminu przypiętego lokalnie dla systemu plików.
* Obciążenie migawki. Około 15% miejsca w warstwie lokalnej jest zarezerwowane dla migawek.
* Potrzeba przywracania. Jeśli wykonanie przywracania jako nowa operacja, zmiana rozmiaru powinna uwzględniać miejsce potrzebne do przywrócenia. Przywracanie odbywa się do udziału lub woluminu o tym samym rozmiarze.
* Niektóre bufory powinny być przydzielane dla nieoczekiwanego wzrostu.

Na podstawie powyższych czynników wymagania dotyczące rozmiaru mogą być reprezentowane przez następujące równanie:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Poniższe przykłady ilustrują, jak można rozmiar tablicy wirtualnej na podstawie wymagań.

#### <a name="example-1"></a>Przykład 1:
W macierzy wirtualnej chcesz mieć możliwość

* aprowizuj wolumin warstwowy o pojemności 2 TB lub udział.
* aprowizyj 1 TB warstwowego woluminu lub udziału.
* aprowizuj 300 GB lokalnie przypiętego woluminu lub udziału.

W przypadku poprzednich woluminów lub udziałów obliczmy wymagania dotyczące miejsca w warstwie lokalnej.

Po pierwsze, dla każdego warstwowego wolumenu/udziału rezerwacja lokalna będzie równa 12% wielkości wolumenu/udziału. W przypadku lokalnie przypiętego woluminu/udziału rezerwacja lokalna wynosi 10 % lokalnie przypiętego rozmiaru woluminu/udziału (oprócz aprowizowanego rozmiaru). W tym przykładzie

* Rezerwacja lokalna o pojemności 240 GB (dla woluminu/udziału warstwowego o pojemności 2 TB)
* Rezerwacja lokalna o pojemności 120 GB (dla woluminu/udziału warstwowego o pojemności 1 TB)
* 330 GB dla lokalnie przypiętego woluminu lub udziału (dodanie 10 % lokalnej rezerwacji do rozmiaru aprowizacji 300 GB)

Całkowita ilość miejsca wymaganego do tej pory w warstwie lokalnej wynosi: 240 GB + 120 GB + 330 GB = 690 GB.

Po drugie, potrzebujemy co najmniej tyle miejsca na poziomie lokalnym, co największa pojedyncza rezerwacja. Ta dodatkowa kwota jest używana w przypadku, gdy trzeba przywrócić z migawki w chmurze. W tym przykładzie największa rezerwacja lokalna wynosi 330 GB (w tym rezerwacja dla systemu plików), więc należy dodać, że do 690 GB: 690 GB + 330 GB = 1020 GB.
Jeśli wykonaliśmy kolejne dodatkowe przywracanie, zawsze możemy zwolnić miejsce z poprzedniej operacji przywracania.

Po trzecie, potrzebujemy 15% całkowitej przestrzeni lokalnej do przechowywania lokalnych migawek, tak aby tylko 85% z nich było dostępnych. W tym przykładzie byłoby to około 1020 GB&ast;= 0,85 aprowizowanego dysku danych TB. Tak więc aprowizowany dysk danych będzie&ast;(1020 (1/0.85))= 1200 GB = 1,20 TB ~ 1,25 TB (zaokrąglanie do najbliższego kwartyla)

Faktoring w nieoczekiwany wzrost i nowe przywraca, należy aprowizować dysk lokalny około 1,25 - 1,5 TB.

> [!NOTE]
> Zaleca się również, że dysk lokalny jest cienko aprowizacji. To zalecenie jest, ponieważ miejsce przywracania jest potrzebne tylko wtedy, gdy chcesz przywrócić dane, które są starsze niż pięć dni. Odzyskiwanie na poziomie elementu umożliwia przywracanie danych z ostatnich pięciu dni bez konieczności dodatkowego miejsca na przywracanie.


#### <a name="example-2"></a>Przykład 2:
W macierzy wirtualnej chcesz mieć możliwość

* aprowizacji woluminu warstwowego o pojemności 2 TB
* aprowizacji woluminu przypiętego lokalnie o pojemności 300 GB

Na podstawie 12 % lokalnej rezerwacji przestrzeni dla wolumenów/udziałów warstwowych i 10 % dla lokalnie przypiętych wolumenów/udziałów,

* Rezerwacja lokalna o pojemności 240 GB (dla woluminu/udziału warstwowego o pojemności 2 TB)
* 330 GB dla lokalnie przypiętego woluminu lub udziału (dodanie 10% lokalnej rezerwacji do aprowizowanego miejsca o pojemności 300 GB)

Całkowita ilość miejsca w warstwie lokalnej wynosi: 240 GB + 330 GB = 570 GB

Minimalna ilość miejsca lokalnego potrzebnego do przywracania wynosi 330 GB.

15% całkowitego dysku jest używane do przechowywania migawek, dzięki czemu jest dostępnych tylko 0,85. Tak więc rozmiar dysku wynosi (900&ast;(1/0,85)) = 1,06 TB ~ 1,25 TB (zaokrąglanie do najbliższego kwartyla)

Faktoring u wszelki nieoczekiwany wzrost, można aprowizować 1,25 - 1,5 TB dysku lokalnego.

### <a name="group-policy"></a>Zasady grupy
Zasady grupy to infrastruktura, która umożliwia implementowanie określonych konfiguracji dla użytkowników i komputerów. Ustawienia zasad grupy są zawarte w obiektach zasad grupy (GPO), które są połączone z następującymi kontenerami Usług domenowych Active Directory (AD DS): lokacje, domeny lub jednostki organizacyjne. 

Jeśli tablica wirtualna jest przyłączona do domeny, można zastosować do niej obiekty zasad grupy. Te obiekty zasad grupy mogą instalować aplikacje, takie jak oprogramowanie antywirusowe, które mogą niekorzystnie wpłynąć na działanie tablicy wirtualnej StorSimple.

W związku z tym zalecamy:

* Upewnij się, że tablica wirtualna znajduje się we własnej jednostce organizacyjnej (OU) dla usługi Active Directory.
* Upewnij się, że do tablicy wirtualnej nie są stosowane żadne obiekty zasad grupy. Można zablokować dziedziczenie, aby upewnić się, że tablica wirtualna (węzeł podrzędny) nie dziedziczy automatycznie żadnych zasad grupy od obiektu nadrzędnego. Aby uzyskać więcej informacji, przejdź do [blokowania dziedziczenia](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Networking
Konfiguracja sieci dla tablicy wirtualnej odbywa się za pośrednictwem lokalnego interfejsu użytkownika sieci Web. Interfejs sieci wirtualnej jest włączony za pośrednictwem funkcji hypervisor, w którym jest aprowizowana tablica wirtualna. Strona [Ustawienia sieciowe](storsimple-virtual-array-deploy3-fs-setup.md) służy do konfigurowania adresu IP interfejsu sieci wirtualnej, podsieci i bramy.  Można również skonfigurować podstawowy i pomocniczy serwer DNS, ustawienia czasu i opcjonalne ustawienia serwera proxy dla urządzenia. Większość konfiguracji sieci jest konfiguracją jednorazową. Przejrzyj [storsimple wymagania sieciowe](storsimple-ova-system-requirements.md#networking-requirements) przed wdrożeniem tablicy wirtualnej.

Podczas wdrażania tablicy wirtualnej zaleca się przestrzeganie następujących najlepszych rozwiązań:

* Upewnij się, że sieć, w której wdrożona jest macierz wirtualna, zawsze ma możliwość poświęcania przepustowości Internetu 5 Mb/s (lub większej).
  
  * Zapotrzebowanie na przepustowość Internetu zależy od charakterystyki obciążenia i szybkości zmiany szybkości transmisji danych.
  * Zmiana danych, która może być obsługiwana, jest wprost proporcjonalna do przepustowości Internetu. Na przykład podczas wykonywania kopii zapasowej przepustowość 5 Mb/s może pomieścić zmianę danych o około 18 GB w ciągu 8 godzin. Dzięki czterokrotnie większej przepustowości (20 Mb/s) możesz obsłużyć czterokrotnie większą zmianę danych (72 GB).
* Upewnij się, że łączność z Internetem jest zawsze dostępna. Sporadyczne lub zawodne połączenia internetowe z urządzeniami mogą spowodować utratę dostępu do danych w chmurze i mogą spowodować nieobsługiwać konfiguracji.
* Jeśli planujesz wdrożyć urządzenie jako serwer iSCSI:
  
  * Zaleca się wyłączenie opcji **Pobierz adres IP automatycznie** (DHCP).
  * Konfigurowanie statycznych adresów IP. Należy skonfigurować podstawowy i pomocniczy serwer DNS.
  * Jeśli definiowanie wielu interfejsów sieciowych w macierzy wirtualnej, tylko pierwszy interfejs sieciowy (domyślnie ten interfejs jest **Ethernet)** może dotrzeć do chmury. Aby kontrolować typ ruchu, można utworzyć wiele interfejsów sieci wirtualnej w macierzy wirtualnej (skonfigurowanej jako serwer iSCSI) i połączyć te interfejsy z różnymi podsieciami.
* Aby ograniczyć tylko przepustowość chmury (używanej przez macierz wirtualną), należy skonfigurować ograniczanie przepustowości routera lub zapory. Jeśli zdefiniujesz ograniczanie przepustowości w hipernadzorcy, będzie ona ograniczać wszystkie protokoły, w tym iSCSI i SMB, a nie tylko przepustowość chmury.
* Upewnij się, że synchronizacja czasu dla hipernadzorców jest włączona. Jeśli używasz funkcji Hyper-V, wybierz tablicę wirtualną w Menedżerze funkcji Hyper-V, przejdź do **usługi integracji ustawień &gt; **i upewnij się, że **synchronizacja czasu** jest zaznaczona.

### <a name="storage-accounts"></a>Konta magazynu
StorSimple Virtual Array może być skojarzony z jednym kontem magazynu. To konto magazynu może być automatycznie wygenerowanym kontem magazynu, kontem w tej samej subskrypcji co usługa lub kontem magazynu powiązanym z inną subskrypcją. Aby uzyskać więcej informacji, zobacz jak [zarządzać kontami magazynu dla tablicy wirtualnej](storsimple-virtual-array-manage-storage-accounts.md).

Użyj następujących zaleceń dla kont magazynu skojarzonych z macierzą wirtualną.

* Podczas łączenia wielu macierzy wirtualnych z jednym kontem magazynu należy uwzględnić maksymalną pojemność (64 TB) dla macierzy wirtualnej i maksymalny rozmiar (500 TB) dla konta magazynu. Ogranicza to liczbę pełnowymiarowych tablic wirtualnych, które mogą być skojarzone z tym kontem magazynu do około 7.
* Podczas tworzenia nowego konta magazynu
  
  * Zaleca się utworzenie go w regionie najbliższym zdalnemu biuru/oddziałowi, w którym jest wdrażana tablica wirtualna StorSimple w celu zminimalizowania opóźnień.
  * Pamiętaj, że nie można przenosić konta magazynu w różnych regionach. Również nie można przenieść usługi między subskrypcjami.
  * Użyj konta magazynu, który implementuje nadmiarowość między centrami danych. Magazyn geograficznie nadmiarowy (GRS), magazyn strefowy nadmiarowy (ZRS) i lokalnie nadmiarowa pamięć masowa (LRS) są obsługiwane do użytku z macierzą wirtualną. Aby uzyskać więcej informacji na temat różnych typów kont magazynu, przejdź do [replikacji magazynu platformy Azure](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Udziały i wolumeny
W przypadku tablicy wirtualnej StorSimple można aprowizować udziały, gdy jest on skonfigurowany jako serwer plików i woluminy skonfigurowane jako serwer iSCSI. Najlepsze rozwiązania dotyczące tworzenia udziałów i woluminów są związane z rozmiarem i typem skonfigurowanym.

#### <a name="volumeshare-size"></a>Rozmiar woluminu/udziału
W macierzy wirtualnej można aprowizować udziały, gdy jest on skonfigurowany jako serwer plików i woluminy skonfigurowane jako serwer iSCSI. Najlepsze rozwiązania dotyczące tworzenia udziałów i woluminów odnoszą się do rozmiaru i typu skonfigurowane. 

Należy pamiętać o następujących sprawdzonych praktyk podczas inicjowania obsługi administracyjnej udziałów lub woluminów na urządzeniu wirtualnym.

* Rozmiary plików względem aprowizowanego rozmiaru udziału warstwowego mogą mieć wpływ na wydajność warstwowego. Praca z dużymi plikami może spowodować spowolnienie warstwy. Podczas pracy z dużymi plikami zaleca się, aby największy plik był mniejszy niż 3% rozmiaru udziału.
* W macierzy wirtualnej można utworzyć maksymalnie 16 woluminów/udziałów. Limity rozmiaru woluminów/udziałów przypiętych lokalnie i warstwowych należy zawsze zapoznać się z [limitami tablic wirtualnych StorSimple](storsimple-ova-limits.md).
* Podczas tworzenia woluminu należy uwzględnić oczekiwane zużycie danych, a także przyszły wzrost. Woluminu lub udziału nie można później rozwinąć.
* Po utworzeniu woluminu/udziału nie można zmniejszyć rozmiaru woluminu/udziału w StorSimple.
* Podczas zapisywania do woluminu warstwowego na StorSimple, gdy dane woluminu osiągnie pewien próg (względem miejsca lokalnego zarezerwowanego dla woluminu), we/wy jest ograniczona. Dalsze zapisywanie do tego woluminu spowalnia we/wy znacznie. Chociaż można zapisywać do woluminu warstwowego poza jego pojemności aprowizacji (nie aktywnie zatrzymać użytkownika od zapisu poza pojemność aprowizowana), zobaczysz powiadomienie alertu, że masz oversubscribed. Po wyświetleniu alertu konieczne jest podjęcie środków zaradczych, takich jak usunięcie danych woluminu (rozszerzenie woluminu nie jest obecnie obsługiwane).
* W przypadku odzyskiwania po awarii, ponieważ liczba dopuszczalnych udziałów/wolumenów wynosi 16, a maksymalna liczba udziałów/wolumenów, które mogą być przetwarzane równolegle, wynosi również 16, liczba udziałów/wolumenów nie ma wpływu na RPO i RTO.

#### <a name="volumeshare-type"></a>Typ woluminu/udziału
StorSimple obsługuje dwa typy woluminów/udziałów na podstawie użycia: lokalnie przypięte i warstwowe. Lokalnie przypięte woluminy/udziały są aprowizacji grubo, podczas gdy woluminy warstwowe/udziały są cienko aprowizacji. Nie można przekonwertować lokalnie przypięty wolumin/udział do warstwowych lub *odwrotnie* po utworzeniu.

Podczas konfigurowania woluminów/udziałów StorSimple zaleca się zaimplementowanie następujących sprawdzonych rozwiązań:

* Zidentyfikuj typ woluminu na podstawie obciążeń, które zamierzasz wdrożyć przed utworzeniem woluminu. Użyj woluminów przypiętych lokalnie dla obciążeń, które wymagają lokalnych gwarancji danych (nawet podczas awarii chmury) i które wymagają niskich opóźnień w chmurze. Po utworzeniu woluminu w tablicy wirtualnej nie można zmienić typu woluminu z lokalnie przypiętego do warstwowego lub *odwrotnie*. Na przykład utwórz woluminy przypięte lokalnie podczas wdrażania obciążeń SQL lub obciążeń obsługujących maszyny wirtualne (maszyny wirtualne); użyj woluminów warstwowych dla obciążeń udziału plików.


#### <a name="volume-format"></a>Format woluminu
Po utworzeniu woluminów StorSimple na serwerze iSCSI należy zainicjować, zainstalować i sformatować woluminy. Ta operacja jest wykonywana na hoście podłączonym do urządzenia StorSimple. Podczas montażu i formatowania woluminów na hoście StorSimple zalecane są następujące najlepsze rozwiązania.

* Wykonaj szybki format na wszystkich woluminach StorSimple.
* Podczas formatowania woluminu StorSimple użyj rozmiaru jednostki alokacji (AUS) 64 KB (domyślnie jest to 4 KB). 64 KB AUS opiera się na testach przeprowadzanych we w domu dla typowych obciążeń StorSimple i innych obciążeń.
* W przypadku korzystania z tablicy wirtualnej StorSimple skonfigurowanej jako serwer iSCSI nie należy używać woluminów łączonych ani dysków dynamicznych, ponieważ te woluminy lub dyski nie są obsługiwane przez storsimple.

#### <a name="share-access"></a>Udostępnianie dostępu
Podczas tworzenia udziałów na serwerze plików tablicy wirtualnej należy postępować zgodnie z tymi wskazówkami:

* Podczas tworzenia udziału przypisz grupę użytkowników jako administratora udziału, a nie jednego użytkownika.
* Uprawnienia NTFS można zarządzać po utworzeniu udziału, edytując udziały za pośrednictwem Eksploratora Windows.

#### <a name="volume-access"></a>Dostęp do woluminu
Podczas konfigurowania woluminów iSCSI w tablicy wirtualnej StorSimple ważne jest kontrolowanie dostępu w razie potrzeby. Aby określić, które serwery hosta mogą uzyskiwać dostęp do woluminów, należy utworzyć i skojarzyć rekordy kontroli dostępu (ARS) z woluminami StorSimple.

Podczas konfigurowania analiz ARS dla woluminów StorSimple należy stosować następujące najlepsze rozwiązania:

* Zawsze skojarzyć co najmniej jeden ACR z woluminem.

* Podczas przypisywania więcej niż jednego acr do woluminu, upewnij się, że wolumin nie jest narażony w sposób, w którym może być jednocześnie dostępne przez więcej niż jednego hosta nieklastrowanego. Jeśli do woluminu przypisano wiele ARS, zostanie wyświetlony komunikat ostrzegawczy, aby przejrzeć konfigurację.

### <a name="data-security-and-encryption"></a>Bezpieczeństwo i szyfrowanie danych
Twoja tablica wirtualna StorSimple ma funkcje zabezpieczeń i szyfrowania danych, które zapewniają poufność i integralność danych. Podczas korzystania z tych funkcji zaleca się przestrzeganie następujących najlepszych rozwiązań: 

* Zdefiniuj klucz szyfrowania magazynu w chmurze, aby wygenerować szyfrowanie AES-256 przed wysłaniem danych z macierzy wirtualnej do chmury. Ten klucz nie jest wymagany, jeśli dane są szyfrowane na początek. Klucz może być generowany i przechowywany w bezpiecznym miejscu przy użyciu systemu zarządzania kluczami, takiego jak [magazyn kluczy platformy Azure.](../key-vault/general/overview.md)
* Podczas konfigurowania konta magazynu za pośrednictwem usługi StorSimple Manager, upewnij się, że tryb TLS, aby utworzyć bezpieczny kanał komunikacji sieciowej między urządzeniem StorSimple i chmury.
* Okresowo ponownie wygeneruj klucze dla kont magazynu (uzyskując dostęp do usługi Azure Storage), aby uwzględnić wszelkie zmiany dostępu na podstawie zmienionej listy administratorów.
* Dane w macierzy wirtualnej są kompresowane i deduplikowane przed wysłaniem ich na platformę Azure. Nie zaleca się korzystania z usługi roli Deduplikacja danych na hoście systemu Windows Server.

## <a name="operational-best-practices"></a>Najlepsze rozwiązania w zakresie operacji
Najlepsze rozwiązania operacyjne są wytyczne, które powinny być przestrzegane podczas codziennego zarządzania lub działania tablicy wirtualnej. Praktyki te obejmują określone zadania zarządzania, takie jak wykonywanie kopii zapasowych, przywracanie z zestawu kopii zapasowych, wykonywanie pracy awaryjnej, dezaktywacja i usuwanie tablicy, monitorowanie użycia systemu i kondycji oraz uruchamianie skanowania antywirusowego w macierzy wirtualnej.

### <a name="backups"></a>Tworzenie kopii zapasowych
Kopia zapasowa danych w macierzy wirtualnej jest tworzona w chmurze na dwa sposoby— domyślna automatyczna codzienna kopia zapasowa całego urządzenia rozpoczyna się o godzinie 22:30 lub za pośrednictwem ręcznej kopii zapasowej na żądanie. Domyślnie urządzenie automatycznie tworzy codzienne migawki w chmurze wszystkich danych na nim przebywających. Aby uzyskać więcej informacji, przejdź do [kopii zapasowej tablicy wirtualnej StorSimple](storsimple-virtual-array-backup.md).

Nie można zmienić częstotliwości i przechowywania skojarzonego z domyślnymi kopiami zapasowymi, ale można skonfigurować czas, w którym codziennie inicjowane są codzienne kopie zapasowe. Podczas konfigurowania godziny rozpoczęcia automatycznych kopii zapasowych zaleca się:

* Zaplanuj tworzenie kopii zapasowych poza godzinami szczytu. Czas rozpoczęcia kopii zapasowej nie powinien pokrywać się z licznymi we/wy hosta.
* Inicjuj ręczną kopię zapasową na żądanie podczas planowania pracy awaryjnej urządzenia lub przed oknem konserwacji, aby chronić dane w macierzy wirtualnej.

### <a name="restore"></a>Przywracanie
Można przywrócić z zestawu kopii zapasowych na dwa sposoby: przywrócić do innego woluminu lub udostępnić lub wykonać odzyskiwanie na poziomie elementu (dostępne tylko w macierzy wirtualnej skonfigurowanej jako serwer plików). Odzyskiwanie na poziomie elementu umożliwia szczegółowe odzyskiwanie plików i folderów z kopii zapasowej w chmurze wszystkich udziałów na urządzeniu StorSimple. Aby uzyskać więcej informacji, przejdź do [przywracania z kopii zapasowej](storsimple-virtual-array-clone.md).

Podczas przywracania należy pamiętać o następujących wskazówkach:

* Aplikacja StorSimple Virtual Array nie obsługuje przywracania w miejscu. Można to jednak łatwo osiągnąć za pomocą dwuetapowego procesu: zrobić miejsce na tablicy wirtualnej, a następnie przywrócić do innego woluminu/udziału.
* Podczas przywracania z woluminu lokalnego, należy pamiętać, że przywracanie będzie długotrwałą operacją. Chociaż wolumin może szybko przejść do trybu online, dane nadal są nawodnione w tle.
* Typ woluminu pozostaje taki sam podczas procesu przywracania. Wolumin warstwowy jest przywracany do innego woluminu warstwowego, a wolumin przypięty lokalnie do innego woluminu przypiętego lokalnie.
* Podczas próby przywrócenia woluminu lub udziału z zestawu kopii zapasowych, jeśli zadanie przywracania zakończy się niepowodzeniem, wolumin docelowy lub udział mogą być nadal tworzone w portalu. Ważne jest, aby usunąć ten nieużyny wolumin docelowy lub udział w portalu, aby zminimalizować wszelkie przyszłe problemy wynikające z tego elementu.

### <a name="failover-and-disaster-recovery"></a>Awaryjne i odzyskiwanie po awarii
Funkcja pracy awaryjnej urządzenia umożliwia migrację danych z urządzenia *źródłowego* w centrum danych do innego urządzenia *docelowego* znajdującego się w tej samej lub innej lokalizacji geograficznej. Przewija się w stan failover urządzenia jest dla całego urządzenia. Podczas pracy awaryjnej dane w chmurze dla urządzenia źródłowego zmienia własność na własność urządzenia docelowego.

W przypadku tablicy wirtualnej StorSimple można przejść w tryb fail over tylko do innej tablicy wirtualnej zarządzanej przez tę samą usługę Menedżera StorSimple. Niedozwolone jest przewijowanie awaryjne na urządzeniu z serii 8000 lub macierz zarządzane przez inną usługę StorSimple Manager (niż dla urządzenia źródłowego). Aby dowiedzieć się więcej na temat zagadnień dotyczących pracy awaryjnej, przejdź do [wymagań wstępnych dotyczących trybu failover urządzenia](storsimple-virtual-array-failover-dr.md).

Podczas wykonywania pracy awaryjnej dla tablicy wirtualnej należy pamiętać o następujących zachowaniach:

* W przypadku planowanej pracy awaryjnej zaleca się przetraktowanie wszystkich woluminów/udziałów w trybie offline przed rozpoczęciem pracy awaryjnej. Postępuj zgodnie z instrukcjami specyficznymi dla systemu operacyjnego, aby najpierw przetraktować woluminy/udziały na hoście, a następnie przetraktować je w trybie offline na urządzeniu wirtualnym.
* W przypadku odzyskiwania po awarii serwera plików zaleca się dołączenie urządzenia docelowego do tej samej domeny co źródło, aby uprawnienia udziału były automatycznie rozpoznawane. Tylko przewija się w ten sposób na urządzeniu docelowym w tej samej domenie.
* Po pomyślnym zakończeniu odzyskiwania po awarii urządzenie źródłowe jest automatycznie usuwane. Chociaż urządzenie nie jest już dostępne, maszyna wirtualna, która została zainicjowana w systemie hosta, nadal zużywa zasoby. Zaleca się usunięcie tej maszyny wirtualnej z systemu hosta, aby zapobiec naliczaniu opłat.
* Należy pamiętać, że nawet jeśli przewija się w trybie failover, **dane są zawsze bezpieczne w chmurze**. Należy wziąć pod uwagę następujące trzy scenariusze, w których praca awaryjna nie została pomyślnie ukończona:
  
  * Wystąpił błąd w początkowych etapach pracy awaryjnej, takich jak podczas przeprowadzania wstępnych kontroli odzyskiwania po awarii. W tej sytuacji urządzenie docelowe jest nadal użyteczne. Możesz ponowić próbę pracy awaryjnej na tym samym urządzeniu docelowym.
  * Wystąpił błąd podczas rzeczywistego procesu pracy awaryjnej. W takim przypadku urządzenie docelowe jest oznaczone jako bezużyteczne. Należy aprowizować i skonfigurować inną docelową tablicę wirtualną i używać jej do pracy awaryjnej.
  * Przewija się w pełni po tym, jak urządzenie źródłowe zostało usunięte, ale urządzenie docelowe ma problemy i nie można uzyskać dostępu do żadnych danych. Dane są nadal bezpieczne w chmurze i można je łatwo pobrać, tworząc inną tablicę wirtualną, a następnie używając jej jako urządzenia docelowego dla odzyskiwania po awarii.

### <a name="deactivate"></a>Dezaktywuj
Dezaktywacja tablicy wirtualnej StorSimple powoduje zerwanie połączenia między urządzeniem a odpowiednią usługą StorSimple Manager. Dezaktywacja jest **operacją trwałą** i nie można jej cofnąć. Dezaktywowanego urządzenia nie można ponownie zarejestrować w usłudze StorSimple Manager. Aby uzyskać więcej informacji, przejdź do [dezaktywacji i usunięcia tablicy wirtualnej StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Podczas dezaktywacji tablicy wirtualnej należy pamiętać o następujących sprawdzonych praktykach:

* Zrób migawkę wszystkich danych w chmurze przed dezaktywacją urządzenia wirtualnego. Po dezaktywacji tablicy wirtualnej wszystkie dane urządzenia lokalnego zostaną utracone. Zrobienie migawki w chmurze pozwoli na odzyskanie danych na późniejszym etapie.
* Przed dezaktywacją tablicy wirtualnej StorSimple należy zatrzymać lub usunąć klientów i hosty, które zależą od tego urządzenia.
* Usuń dezaktywowane urządzenie, jeśli nie jest już używane, aby nie naliczać opłat.

### <a name="monitoring"></a>Monitorowanie
Aby upewnić się, że storsimple virtual array jest w stanie ciągłej dobrej kondycji, należy monitorować tablicy i upewnij się, że otrzymujesz informacje z systemu, w tym alerty. Aby monitorować ogólną kondycję tablicy wirtualnej, należy zaimplementować następujące najlepsze rozwiązania:

* Skonfiguruj monitorowanie w celu śledzenia użycia dysku z dysku danych tablicy wirtualnej oraz dysku systemu operacyjnego. Jeśli jest uruchomiony program Hyper-V, można użyć kombinacji Menedżera maszyn wirtualnych programu System Center (SCVMM) i programu System Center Operations Manager do monitorowania hostów wirtualizacji.
* Skonfiguruj powiadomienia e-mail w tablicy wirtualnej, aby wysyłać alerty na określonych poziomach użycia.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Aplikacje do wyszukiwania indeksów i skanowania antywirusowego
A StorSimple Virtual Array można automatycznie warstwy danych z warstwy lokalnej do chmury Microsoft Azure. Gdy aplikacja, taka jak wyszukiwanie indeksu lub skanowanie antywirusowe jest używana do skanowania danych przechowywanych na StorSimple, należy zadbać, aby dane w chmurze nie były dostępne i pobierane z powrotem do warstwy lokalnej.

Podczas konfigurowania wyszukiwania indeksów lub skanowania antywirusowego w macierzy wirtualnej zaleca się zaimplementowanie następujących sprawdzonych rozwiązań:

* Wyłącz wszystkie automatycznie skonfigurowane operacje pełnego skanowania.
* W przypadku woluminów warstwowych skonfiguruj aplikację wyszukiwania indeksu lub skanowania antywirusowego w celu wykonania skanowania przyrostowego. Spowoduje to skanowanie tylko nowych danych prawdopodobnie przebywających w warstwie lokalnej. Dane, które są warstwowe do chmury nie jest dostępny podczas operacji przyrostowej.
* Upewnij się, że poprawne filtry wyszukiwania i ustawienia są skonfigurowane tak, aby skanowane były tylko zamierzone typy plików. Na przykład pliki obrazów (JPEG, GIF i TIFF) i rysunki inżynierskie nie powinny być skanowane podczas przebudowy indeksu przyrostowego lub pełnego.

Jeśli korzystasz z procesu indeksowania systemu Windows, postępuj zgodnie z tymi wskazówkami:

* Nie należy używać indeksatora systemu Windows dla woluminów warstwowych, ponieważ odwołuje duże ilości danych (TBs) z chmury, jeśli indeks musi być często przebudowywany. Przebudowa indeksu spowoduje pobranie wszystkich typów plików w celu indeksowania ich zawartości.
* Użyj procesu indeksowania systemu Windows dla woluminów przypiętych lokalnie, ponieważ będzie to uzyskiwać dostęp tylko do danych w warstwach lokalnych do tworzenia indeksu (dane w chmurze nie będą dostępne).

### <a name="byte-range-locking"></a>Blokowanie zakresu bajtów
Aplikacje mogą blokować określony zakres bajtów w plikach. Jeśli blokowanie zakresu bajtów jest włączone w aplikacjach, które zapisują do StorSimple, warstwa nie działa na tablicy wirtualnej. Aby warstwa działała, wszystkie obszary plików, do których dostęp uzyskał dostęp, powinny zostać odblokowane. Blokowanie zakresu bajtów nie jest obsługiwane w przypadku woluminów warstwowych w macierzy wirtualnej.

Zalecane środki w celu złagodzenia blokady zakresu bajtów obejmują:

* Wyłącz blokowanie zakresu bajtów w logice aplikacji.
* Użyj woluminów przypiętych lokalnie (zamiast warstwowych) dla danych skojarzonych z tą aplikacją. Woluminy przypięte lokalnie nie są warstwowe w chmurze.
* W przypadku korzystania z woluminów przypiętych lokalnie z włączoną blokadą zakresu bajtów wolumin może być w trybie online przed zakończeniem przywracania. W takich przypadkach należy poczekać na przywrócenie do ukończenia.

## <a name="multiple-arrays"></a>Wiele tablic
Wiele macierzy wirtualnych może być konieczne wdrożenie w celu uwzględnienia rosnącego zestawu roboczego danych, które mogą rozlać się na chmurę, wpływając w ten sposób na wydajność urządzenia. W takich przypadkach najlepiej jest skalować urządzenia w miarę wzrostu zestawu roboczego. Wymaga to dodania jednego lub więcej urządzeń w lokalnym centrum danych. Podczas dodawania urządzeń można:

* Podziel bieżący zestaw danych.
* Wdrażanie nowych obciążeń na nowych urządzeniach.
* W przypadku wdrażania wielu tablic wirtualnych zaleca się, aby z perspektywy równoważenia obciążenia rozdzielać tablicę między różne hosty funkcji hypervisor.
* Wiele macierzy wirtualnych (skonfigurowanych jako serwer plików lub serwer iSCSI) można wdrożyć w rozproszonym obszarze nazw systemu plików. Aby uzyskać szczegółowe kroki, przejdź do [rozwiązania Rozproszonego systemu nazw systemu plików z przewodnikiem wdrażania magazynu w chmurze hybrydowej](https://www.microsoft.com/download/details.aspx?id=45507). Replikacja rozproszonego systemu plików nie jest obecnie zalecana do użytku z tablicą wirtualną. 

## <a name="see-also"></a>Zobacz też
Dowiedz się, jak [administrować tablicą wirtualną StorSimple](storsimple-virtual-array-manager-service-administration.md) za pośrednictwem usługi StorSimple Manager.

