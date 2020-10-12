---
title: Tworzenie kopii zapasowych HANA i przywracanie na SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Jak wykonać kopię zapasową HANA i przywrócić ją na SAP HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79ef279423c524f0d409815e7ae163aa699f5428
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082209"
---
# <a name="backup-and-restore-on-sap-hana-on-azure"></a>Tworzenie kopii zapasowych i przywracanie na SAP HANA na platformie Azure

>[!IMPORTANT]
>Ten artykuł nie zastępuje dokumentacji dotyczącej administracji SAP HANA ani informacji o oprogramowaniu SAP. Oczekujemy, że masz pełne zrozumienie i doświadczenie w SAP HANA administrowania i działania, szczególnie w przypadku kopii zapasowych, przywracania, wysokiej dostępności i odzyskiwania po awarii. W tym artykule przedstawiono zrzuty ekranu z programu SAP HANA Studio. Zawartość, struktura i charakter ekranów narzędzi administracyjnych SAP i samych narzędzi mogą ulec zmianie z wersji SAP HANA na Release.

Ważne jest, aby wykonać kroki i procesy wykonywane w środowisku oraz z wersjami i wydaniami platformy HANA. Niektóre procesy opisane w tym artykule zostały uproszczone w celu lepszego zrozumienia. Nie są one przeznaczone do użycia jako szczegółowe kroki dla operacji ostatecznej handbooks. Jeśli chcesz utworzyć handbooks operacji dla konfiguracji, przetestuj i wykorzystaj procesy i udokumentowanie procesów związanych z określonymi konfiguracjami. 

Jednym z najważniejszych aspektów operacyjnych baz danych jest ochrona ich przed krytycznymi zdarzeniami. Przyczyną tych zdarzeń może być coś z naturalnych awarii do prostych błędów użytkowników.

Wykonanie kopii zapasowej bazy danych z możliwością przywrócenia jej do dowolnego punktu w czasie, na przykład przed usunięciem krytycznych danych przez kogoś, umożliwia przywrócenie stanu, który jest możliwie najbliżej tego, jak był on przed przerwą w działaniu.

Aby można było przywrócić możliwości przywracania, należy wykonać dwa typy kopii zapasowych:

- Kopie zapasowe bazy danych: pełne, przyrostowe lub różnicowe kopie zapasowe
- Kopie zapasowe dziennika transakcji

Oprócz kopii zapasowych pełnych baz danych wykonywanych na poziomie aplikacji można wykonywać kopie zapasowe z migawkami magazynu. Migawki magazynu nie zastępują kopii zapasowych dziennika transakcji. Kopie zapasowe dziennika transakcji są ważne, aby przywrócić bazę danych do określonego punktu w czasie lub opróżnić dzienniki z już zatwierdzonych transakcji. Migawki magazynu umożliwiają przyspieszenie odzyskiwania przez szybkie dostarczenie obrazu z przekazywaniem do przodu bazy danych. 

SAP HANA na platformie Azure (duże wystąpienia) oferuje dwie opcje tworzenia kopii zapasowej i przywracania:

- **Zrób to samodzielnie (możesz).** Po upewnieniu się, że jest wystarczająca ilość miejsca na dysku, wykonaj pełne kopie zapasowe bazy danych i dziennika przy użyciu jednej z poniższych metod tworzenia kopii zapasowej dysku. Można utworzyć kopię zapasową bezpośrednio w woluminach dołączonych do jednostek wystąpienia usługi HANA lub w udziałach NFS skonfigurowanych na maszynie wirtualnej platformy Azure. W tym drugim przypadku klienci skonfigurują maszynę wirtualną z systemem Linux na platformie Azure, Dołącz Magazyn Azure do maszyny wirtualnej i Udostępnij magazyn za pomocą skonfigurowanego serwera NFS w tej maszynie wirtualnej. W przypadku wykonywania kopii zapasowej na woluminach, które bezpośrednio dołączą do jednostek dużych wystąpień platformy HANA, skopiuj kopie zapasowe na konto usługi Azure Storage. Zrób to po skonfigurowaniu maszyny wirtualnej platformy Azure, która eksportuje udziały NFS oparte na usłudze Azure Storage. Możesz również użyć magazynu Azure Backup lub magazynu systemu Azure w chłodnym magazynie. 

   Innym rozwiązaniem jest użycie narzędzia ochrony danych innych firm do przechowywania kopii zapasowych po ich skopiowaniu na konto usługi Azure Storage. Opcja tworzenia kopii zapasowej możesz może być również niezbędna w przypadku danych, które są potrzebne do przechowywania przez dłuższy czas w celu zapewnienia zgodności i inspekcji. We wszystkich przypadkach kopie zapasowe są kopiowane do udziałów NFS reprezentowanych przez maszynę wirtualną i usługę Azure Storage.

- **Funkcja tworzenia kopii zapasowej i przywracania infrastruktury.** Można również użyć funkcji tworzenia kopii zapasowych i przywracania, która zapewnia podstawową infrastrukturę SAP HANA na platformie Azure (duże wystąpienia). Ta opcja spełnia potrzeby tworzenia kopii zapasowych i szybkiego przywracania. Pozostała część tej sekcji dotyczy funkcji tworzenia kopii zapasowych i przywracania, która jest oferowana z dużymi wystąpieniami platformy HANA. W tej sekcji omówiono także relacje tworzenia kopii zapasowych i przywracania danych w ramach funkcji odzyskiwania po awarii oferowanych przez duże wystąpienia platformy HANA.

> [!NOTE]
>   Technologia tworzenia migawek używana przez podstawową infrastrukturę dużych wystąpień HANA ma zależność od migawek SAP HANA. W tym momencie migawki SAP HANA nie działają w połączeniu z wieloma dzierżawcami SAP HANA kontenerów wielodostępnych baz danych. W przypadku wdrożenia tylko jednej dzierżawy SAP HANA migawki działają i można użyć tej metody.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Używanie migawek magazynu SAP HANA na platformie Azure (duże wystąpienia)

Podstawowa SAP HANA infrastruktury magazynu na platformie Azure (duże wystąpienia) obsługuje migawki magazynu woluminów. Obsługiwane jest tworzenie kopii zapasowych i przywracanie woluminów z następującymi kwestiami:

- Zamiast pełnych kopii zapasowych bazy danych migawki woluminów magazynu są podejmowane często.
- Gdy migawka jest wyzwalana przez/Hana/Data i/Hana/Shared, która obejmuje/usr/SAP, Volumes, technologia migawek inicjuje migawkę SAP HANA przed uruchomieniem migawki magazynu. Ta SAP HANA migawka jest punktem instalacji na potrzeby przywracania dziennika po odzyskaniu migawki magazynu. Aby migawka platformy HANA zakończyła się pomyślnie, wymagane jest aktywne wystąpienie platformy HANA. W scenariuszu HSR migawka magazynu nie jest obsługiwana w bieżącym węźle pomocniczym, w którym nie można wykonać migawki platformy HANA.
- Po pomyślnym uruchomieniu migawki magazynu zostanie usunięta migawka SAP HANA.
- Kopie zapasowe dziennika transakcji są często wykonywane i przechowywane w woluminie/Hana/logbackups lub na platformie Azure. Możesz wyzwolić wolumin/Hana/logbackups zawierający kopie zapasowe dziennika transakcji, aby wykonać migawkę osobno. W takim przypadku nie trzeba uruchamiać migawki platformy HANA.
- W przypadku konieczności przywrócenia bazy danych do określonego punktu w czasie w przypadku awarii produkcyjnej należy zażądać, aby Microsoft Azure obsługiwać lub SAP HANA na platformie Azure Restore do określonej migawki magazynu. Przykładem jest planowane przywrócenie systemu piaskownicy do jego oryginalnego stanu.
- Migawka SAP HANA, która jest uwzględniona w migawce magazynu, to punkt przesunięcia dotyczący stosowania kopii zapasowych dziennika transakcji, które zostały uruchomione i zostały zapisane po wykonaniu migawki magazynu.
- Te kopie zapasowe dziennika transakcji są wykonywane w celu przywrócenia bazy danych z powrotem do określonego punktu w czasie.

Możliwe jest wykonanie migawek magazynu przeznaczonych dla trzech klas woluminów:

- Połączona migawka w porównaniu z/Hana/Data i/Hana/Shared, która obejmuje/usr/SAP. Ta migawka wymaga utworzenia migawki SAP HANA jako przygotowania dla migawki magazynu. Migawka SAP HANA gwarantuje, że baza danych jest w stanie spójnym z punktu widzenia magazynu. Dla procesu przywracania jest to punkt, który ma zostać skonfigurowany.
- Oddzielna migawka nad/Hana/logbackups.
- Partycja systemu operacyjnego.

Najnowsze skrypty i dokumentacja migawek można znaleźć w witrynie [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Po pobraniu pakietu skryptu migawek z usługi [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)otrzymujesz trzy pliki. Jeden z plików jest udokumentowany w pliku PDF dla podanej funkcji. Po pobraniu zestawu narzędzi postępuj zgodnie z instrukcjami w temacie "Pobierz narzędzia migawek".

## <a name="storage-snapshot-considerations"></a>Zagadnienia dotyczące migawek magazynu

>[!NOTE]
>Migawki magazynu zużywają miejsce do magazynowania przydzieloną do jednostek dużego wystąpienia platformy HANA. Należy wziąć pod uwagę następujące aspekty planowania migawek magazynu oraz liczbę migawek magazynu, które mają być przechowywane. 

Określone Mechanics migawek magazynu dla SAP HANA na platformie Azure (duże wystąpienia) obejmują:

- Określona migawka magazynu w punkcie w czasie, gdy zostanie zajęta, zużywa niewiele miejsca do magazynowania.
- Ponieważ zmiany zawartości danych i zawartość w SAP HANA pliki danych zmieniają się w woluminie magazynu, migawka musi przechowywać oryginalną zawartość bloku i zmiany danych.
- W efekcie migawka magazynu zwiększa rozmiar. Im dłużej migawka istnieje, tym większa jest migawka magazynu.
- Im więcej zmian wprowadzonych w woluminie bazy danych SAP HANA w okresie istnienia migawki magazynu, tym większa ilość miejsca w migawce magazynu.

SAP HANA na platformie Azure (duże wystąpienia) zawiera stałe rozmiary woluminów dla SAP HANA danych i dzienników. Wykonywanie migawek tych woluminów Eats w przestrzeni woluminów. Należy:

- Określ, kiedy mają być planowane planowanie migawek magazynu.
- Monitoruj użycie miejsca dla woluminów magazynu. 
- Zarządzanie liczbą przechowywanych migawek. 

Migawki magazynu można wyłączyć podczas importowania mas danych lub wykonywania innych znaczących zmian w bazie danych HANA. 


Poniższe sekcje zawierają informacje dotyczące wykonywania tych migawek i zawiera ogólne zalecenia:

- Mimo że sprzęt może utrzymywać 255 migawek na woluminie, chcesz zachować wartość poniżej tej liczby. Zalecenie ma wartość 250 lub mniejszą.
- Przed wykonaniem migawek magazynu należy monitorować i śledzić wolne miejsce.
- Zmniejsz liczbę migawek magazynu na podstawie ilości wolnego miejsca. Można obniżyć liczbę zachowywanych migawek lub można ją rozłożyć na woluminy. Dodatkowy magazyn można zamówić w jednostkach 1 – terabajt.
- W trakcie działań takich jak przenoszenie danych do SAP HANA przy użyciu narzędzi migracji platformy SAP (R3load) lub przywracanie SAP HANA baz danych z kopii zapasowych należy wyłączyć migawki magazynu na woluminie/Hana/Data. 
- W przypadku większych reorganizacji tabel SAP HANA należy unikać migawek magazynu, jeśli jest to możliwe.
- Migawki magazynu są wymagane do skorzystania z możliwości odzyskiwania po awarii SAP HANA na platformie Azure (duże wystąpienia).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Wymagania wstępne dotyczące korzystania z samoobsługowych migawek magazynu

Aby upewnić się, że skrypt migawek działa prawidłowo, upewnij się, że język Perl jest zainstalowany w systemie operacyjnym Linux na serwerze z dużymi wystąpieniami HANA. Język Perl jest wstępnie zainstalowany w jednostce dużego wystąpienia platformy HANA. Aby sprawdzić wersję języka Perl, użyj następującego polecenia:

`perl -v`

![Klucz publiczny jest kopiowany przez uruchomienie tego polecenia](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Konfigurowanie migawek magazynu

Aby skonfigurować migawki magazynu z dużymi wystąpieniami platformy HANA, wykonaj następujące kroki.
1. Upewnij się, że język Perl jest zainstalowany w systemie operacyjnym Linux na serwerze z dużymi wystąpieniami HANA.
1. Zmodyfikuj konfigurację/etc/ssh/SSH, \_ Aby dodać wiersz _Mac HMAC-SHA1_.
1. Utwórz konto użytkownika SAP HANA kopii zapasowej w węźle głównym dla każdego uruchomionego wystąpienia SAP HANA, jeśli ma to zastosowanie.
1. Zainstaluj klienta programu SAP HANA HDB na wszystkich serwerach SAP HANA — duże wystąpienia.
1. Na pierwszym serwerze SAP HANA — duże wystąpienia każdego regionu Utwórz klucz publiczny, aby uzyskać dostęp do podstawowej infrastruktury magazynu, która kontroluje tworzenie migawek.
1. Skopiuj skrypty i plik konfiguracji z usługi [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) do lokalizacji **hdbsql** w instalacji SAP HANA.
1. Zmodyfikuj plik *HANABackupDetails.txt* stosownie do potrzeb, aby uzyskać odpowiednie specyfikacje klienta.

Pobierz najnowsze skrypty i dokumentację migawek z witryny [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). Aby zapoznać się z wymienionymi wcześniej krokami, zobacz [Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>Zagadnienia dotyczące scenariuszy MCOD
W przypadku uruchamiania [scenariusza MCOD](https://launchpad.support.sap.com/#/notes/1681092) z wieloma wystąpieniami SAP HANA w jednej jednostce usługi Hana o dużej instancji, istnieją oddzielne woluminy magazynu obsługiwane dla każdego wystąpienia SAP HANA. Aby uzyskać więcej informacji na temat MDC i innych zagadnień, zobacz "ważne rzeczy do zapamiętania" w [narzędziu Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Krok 1. Instalowanie klienta programu SAP HANA HDB

System operacyjny Linux zainstalowany w SAP HANA na platformie Azure (duże wystąpienia) zawiera foldery i skrypty niezbędne do uruchamiania migawek magazynu SAP HANA na potrzeby tworzenia kopii zapasowych i odzyskiwania po awarii. Zapoznaj się z najnowszymi wersjami w serwisie [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

Użytkownik jest odpowiedzialny za Instalowanie SAP HANA klienta HDB w jednostkach dużego wystąpienia HANA podczas instalowania SAP HANA.

### <a name="step-2-change-the-etcsshssh_config"></a>Krok 2. zmiana konfiguracji/etc/ssh/SSH \_

Ten krok jest opisany w artykule "Włączanie komunikacji z magazynem" w [narzędziu Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-3-create-a-public-key"></a>Krok 3. Tworzenie klucza publicznego

Aby umożliwić dostęp do interfejsów migawek magazynu dla dzierżawy dużego wystąpienia platformy HANA, Ustanów procedurę logowania za pomocą klucza publicznego. 

Na pierwszej SAP HANA na serwerze Azure (duże wystąpienia) w dzierżawie Utwórz klucz publiczny, aby uzyskać dostęp do infrastruktury magazynu. Przy użyciu klucza publicznego hasło nie jest wymagane do logowania się do interfejsów migawek magazynu. Nie trzeba również obsługiwać poświadczeń hasła przy użyciu klucza publicznego. 

Aby wygenerować klucz publiczny, zobacz "Włączanie komunikacji z magazynem" w [narzędziu Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-4-create-an-sap-hana-user-account"></a>Krok 4. Tworzenie konta użytkownika SAP HANA

Aby rozpocząć tworzenie migawek SAP HANA, należy utworzyć konto użytkownika w SAP HANA, które mogą być używane w skryptach migawek magazynu. Utwórz w tym celu konto użytkownika SAP HANA w programie SAP HANA Studio. Użytkownik musi zostać utworzony w ramach SYSTEMDB, a *nie* w bazie danych identyfikatorów SID dla MDC. W środowisku jednego kontenera użytkownik jest tworzony w bazie danych dzierżawcy. To konto musi mieć uprawnienia administratora i **odczytu katalogu** **kopii zapasowej** . 

Aby skonfigurować konto użytkownika i korzystać z niego, zobacz "Włączanie komunikacji z usługą SAP HANA" w witrynie [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Krok 5. Autoryzuj konto użytkownika SAP HANA

W tym kroku zostanie autoryzowane konto użytkownika SAP HANA utworzone w taki sposób, aby skrypty nie wymagały przesyłania haseł w czasie wykonywania. SAP HANA polecenie `hdbuserstore` umożliwia tworzenie SAP HANA klucza użytkownika. Klucz jest przechowywany w co najmniej jednym węźle SAP HANA. Klucz użytkownika umożliwia użytkownikowi dostęp SAP HANA bez konieczności zarządzania hasłami w ramach procesu tworzenia skryptów. Proces tworzenia skryptów został omówiony w dalszej części tego artykułu.

>[!IMPORTANT]
>Uruchom te polecenia konfiguracji z tym samym kontekstem użytkownika, w którym są uruchamiane polecenia migawek. W przeciwnym razie polecenia migawek nie będą działały prawidłowo.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Krok 6. Uzyskiwanie skryptów migawek, Konfigurowanie migawek i testowanie konfiguracji i łączności

Pobierz najnowszą wersję skryptów z usługi [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). Sposób instalacji skryptów został zmieniony z wydaniem 4,1 skryptów. Aby uzyskać więcej informacji, zobacz "Włączanie komunikacji z SAP HANA" w [narzędziach Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Dokładną sekwencję poleceń można znaleźć w sekcji "Łatwa instalacja narzędzi migawek (domyślnie)" w [narzędziu Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Zalecamy zastosowanie instalacji domyślnej. 

Aby przeprowadzić uaktualnienie z wersji 3. x do 4,1, zobacz sekcję "Uaktualnianie istniejącej instalacji" w [narzędziu Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Aby odinstalować zestaw narzędzi 4,1, zobacz "Dezinstalacja narzędzi migawek" w [narzędziach Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Nie zapomnij uruchomić kroków opisanych w sekcji "Kończenie instalacji narzędzi migawek" w [narzędziach Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Informacje o różnych skryptach i plikach w miarę ich instalacji zostały opisane w artykule "co to są narzędzia migawek?". w [narzędziach Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Przed skonfigurowaniem narzędzi migawek upewnij się, że skonfigurowano również lokalizacje i ustawienia kopii zapasowej HANA. Aby uzyskać więcej informacji, zobacz "SAP HANA Configuration" w [narzędziu Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Konfiguracja zestawu narzędzi migawek została opisana w artykule "plik konfiguracji-HANABackupCustomerDetails.txt" w [narzędziu Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-connectivity-with-sap-hana"></a>Testowanie łączności z SAP HANA

Po umieszczeniu wszystkich danych konfiguracyjnych w pliku *HANABackupCustomerDetails.txt* Sprawdź, czy konfiguracje są poprawne dla danych wystąpienia platformy Hana. Użyj skryptu `testHANAConnection` , który jest niezależny od konfiguracji skalowania w górę lub w poziomie SAP HANA.

Aby uzyskać więcej informacji, zobacz "Sprawdzanie łączności za pomocą SAP HANA-testHANAConnection" w [narzędziach Microsoft Snapshot Tools dla SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-storage-connectivity"></a>Testowanie połączenia z magazynem

Następny krok testu polega na sprawdzeniu łączności z magazynem na podstawie danych umieszczonych w pliku konfiguracji *HANABackupCustomerDetails.txt* . Następnie uruchom migawkę testową. Przed uruchomieniem `azure_hana_backup` polecenia należy uruchomić ten test. Aby uzyskać sekwencję poleceń dla tego testu, zobacz "Sprawdzanie łączności z usługą Storage-testStorageSnapshotConnection" "w [narzędziach Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Po pomyślnym zalogowaniu się do interfejsów maszyny wirtualnej magazynu skrypt kontynuuje działanie fazy 2 i utworzy migawkę testową. Dane wyjściowe są wyświetlane w tym miejscu dla konfiguracji SAP HANA skalowanie w poziomie z trzema węzłami.

Jeśli migawka testowa zostanie pomyślnie uruchomiona z skryptem, można zaplanować rzeczywiste migawki magazynu. Jeśli nie powiedzie się, zbadaj problemy przed przechodzeniem do przodu. Migawka testowa powinna pozostawać w pobliżu do momentu wykonania pierwszych rzeczywistych migawek.


### <a name="step-7-perform-snapshots"></a>Krok 7. wykonywanie migawek

Po zakończeniu kroków przygotowywania można rozpocząć konfigurowanie i planowanie rzeczywistych migawek magazynu. Skrypt do zaplanowania działa z SAP HANA skalowalności i skalowania w poziomie. W celu okresowego i regularnego wykonywania skryptu kopii zapasowej należy zaplanować skrypt za pomocą narzędzia firmy cronus. 

Aby uzyskać dokładną składnię polecenia i funkcje, zobacz "wykonywanie kopii zapasowej migawek-azure_hana_backup" w [narzędziach Microsoft Snapshot Tools dla SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

Po uruchomieniu skryptu `azure_hana_backup` tworzy migawkę magazynu w następujących trzech etapach:

1. Wykonuje migawkę SAP HANA.
1. Wykonuje migawkę magazynu.
1. Usuwa migawkę SAP HANA utworzoną przed uruchomieniem migawki magazynu.

Aby uruchomić skrypt, należy wywołać go z folderu wykonywalnego HDB, do którego został skopiowany. 

Okres przechowywania jest administrowany liczbą migawek, które są przesyłane jako parametr podczas uruchamiania skryptu. Czas, który jest objęty migawek magazynu, jest funkcją okresu wykonywania oraz liczbą migawek przesłanych jako parametr podczas uruchamiania skryptu. 

Jeśli liczba przechowywanych migawek przekracza liczbę, która jest określana jako parametr w wywołaniu skryptu, najstarsza migawka magazynu tej samej etykiety jest usuwana przed uruchomieniem nowej migawki. Liczba nadana jako ostatni parametr wywołania jest liczbą, której można użyć do kontrolowania liczby przechowywanych migawek. Za pomocą tej liczby można również kontrolować, pośrednio, miejsce na dysku używane na potrzeby migawek. 


## <a name="snapshot-strategies"></a>Strategie migawek
Częstotliwość migawek dla różnych typów zależy od tego, czy jest używana funkcja odzyskiwania po awarii dla dużych wystąpień platformy HANA. Ta funkcja opiera się na migawkach magazynu, co może wymagać specjalnych zaleceń dotyczących częstotliwości i okresów wykonywania migawek magazynu. 

W następujących kwestiach i zaleceniach przyjęto, że *nie* używasz funkcji odzyskiwania po awarii, która oferuje duże wystąpienia Hana. Zamiast tego należy użyć migawek magazynu, aby utworzyć kopie zapasowe i zapewnić odzyskiwanie do punktu w czasie w ciągu ostatnich 30 dni. Uwzględniając ograniczenia liczby migawek i miejsca, należy wziąć pod uwagę następujące wymagania:

- Czas odzyskiwania dla odzyskiwania do punktu w czasie.
- Używane miejsce.
- Cele punktu odzyskiwania i czasu odzyskiwania dotyczące potencjalnego odzyskiwania po awarii.
- Ostateczne wykonywanie kopii zapasowych w pełnej bazie danych platformy HANA na dyskach. Za każdym razem, gdy jest wykonywana pełna kopia zapasowa z dysku lub interfejs **BACKINT** , wykonywanie migawek magazynu kończy się niepowodzeniem. Jeśli planujesz uruchamiać kopie zapasowe pełnych baz danych na podstawie migawek magazynu, upewnij się, że wykonywanie migawek magazynu jest wyłączone w tym czasie.
- Liczba migawek na wolumin, które są ograniczone do 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Jeśli nie używasz funkcji odzyskiwania po awarii w dużych wystąpieniach platformy HANA, okres migawek jest krótszy. W takich przypadkach należy wykonać połączone migawki w/Hana/Data i/Hana/Shared, które obejmują/usr/SAP w 12-godzinnym lub 24-godzinnym okresie. Przechowuj migawki miesięcznie. Ta sama wartość dotyczy migawek woluminu kopii zapasowej dziennika. Wykonywanie kopii zapasowych dziennika transakcji SAP HANA względem woluminu kopii zapasowej dziennika występuje w ciągu 5 minut do 15-minutowych okresów.

Zaplanowane migawki magazynu są najlepiej wykonywane przy użyciu firmy cronus. Użyj tego samego skryptu dla wszystkich kopii zapasowych i potrzeb związanych z odzyskiwaniem po awarii. Zmodyfikuj dane wejściowe skryptu w celu dopasowania do różnych czasów wykonywania kopii zapasowych. Wszystkie te migawki są zaplanowanych inaczej w witrynie CRONUS w zależności od ich czasu wykonywania. Może to być co godzinę, co 12 godzin, codziennie lub co tydzień. 

Poniższy przykład przedstawia harmonogram CRONUS w/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
W poprzednim przykładzie migawka łączona co godzinę obejmuje woluminy, które zawierają/Hana/Data i/hana/shared/SID, w tym/usr/SAP, lokalizacje. Ten typ migawki umożliwia szybsze odzyskiwanie do punktu w czasie w ciągu ostatnich dwóch dni. Na tych woluminach jest również codziennych migawek. W związku z tym masz dwa dni na pokrycie co godzinę migawek i cztery tygodnie pokrycia przez codzienne migawki. Kopie zapasowe dziennika transakcji są również codzienne. Te kopie zapasowe są przechowywane przez cztery tygodnie. 

Jak widać w trzecim wierszu crontab, kopia zapasowa dziennika transakcji HANA jest zaplanowana do uruchomienia co 5 minut. Czasy rozpoczęcia różnych zadań firmy CRONUS, które uruchamiają migawki magazynu, są rozkładane. W ten sposób migawki nie są uruchamiane w tym samym czasie w określonym momencie. 

W poniższym przykładzie wykonywana jest połączona migawka obejmująca woluminy, które zawierają/Hana/Data i/hana/shared/SID, w tym/usr/SAP, lokalizacje w godzinie. Te migawki są przechowywane przez dwa dni. Migawki woluminów kopii zapasowej dziennika transakcji są uruchamiane w oparciu o 5 minut i są przechowywane przez cztery godziny. Tak jak wcześniej, zadanie tworzenia kopii zapasowej pliku dziennika transakcji HANA zostanie uruchomione co 5 minut. 

Migawka woluminu kopii zapasowej dziennika transakcji jest wykonywana z opóźnieniem 2-minutowym po rozpoczęciu tworzenia kopii zapasowej dziennika transakcji. W normalnych warunkach kopia zapasowa dziennika transakcji SAP HANA kończy się w ciągu dwóch minut. Tak jak wcześniej wolumin zawierający rozruchowy numer LUN jest archiwizowany raz dziennie przez migawkę magazynu i jest przechowywany przez cztery tygodnie.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Poniższa ilustracja ilustruje sekwencje poprzedniego przykładu. Rozruchowa jednostka LUN jest wykluczona.

![Relacja między kopiami zapasowymi i migawkami](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA wykonuje regularne zapisy względem woluminu/Hana/log, aby udokumentować zatwierdzone zmiany w bazie danych. Regularnie SAP HANA zapisuje punkt zapisu do woluminu/Hana/Data. Jak określono w crontab, kopia zapasowa dziennika transakcji SAP HANA jest uruchamiana co 5 minut. 

Zobaczysz również, że migawka SAP HANA jest uruchamiana co godzinę w wyniku wyzwolenia migawki połączonego magazynu za pośrednictwem woluminów/Hana/Data i/hana/shared/SID. Po pomyślnym zakończeniu migawki platformy HANA zostanie uruchomiona połączona migawka magazynu. Zgodnie z instrukcją crontab, migawka magazynu na woluminie/Hana/logbackup jest uruchamiana co 5 minut, około 2 minut po wykonaniu kopii zapasowej dziennika transakcji platformy HANA.

> 

>[!IMPORTANT]
> Użycie migawek magazynu dla SAP HANA kopii zapasowych jest przydatne tylko wtedy, gdy migawki są wykonywane w połączeniu z kopiami zapasowymi SAP HANA dziennika transakcji. Te kopie zapasowe dziennika transakcji muszą obejmować okresy między migawkami magazynu. 

Jeśli ustawisz zobowiązanie dla użytkowników odzyskania do określonego momentu przez 30 dni, musisz:

- Uzyskuj dostęp do złożonej migawki magazynu za pośrednictwem/Hana/Data i/hana/shared/SID, który jest 30-dniowy, w skrajnych przypadkach. 
- Twórz ciągłe kopie zapasowe dziennika transakcji, które obejmują czas między wszystkimi połączonymi migawkami magazynu. Dlatego najstarsza migawka woluminu kopii zapasowej dziennika transakcji musi być wcześniejsza niż 30 dni. W takim przypadku kopie zapasowe dziennika transakcji są kopiowane do innego udziału NFS znajdującego się w usłudze Azure Storage. W takim przypadku można ściągnąć stare kopie zapasowe dziennika transakcji z tego udziału NFS.

Aby skorzystać z migawek magazynu i ostatecznej replikacji kopii zapasowych dziennika transakcji, należy zmienić lokalizację, w której SAP HANA są zapisywane kopie zapasowe dziennika transakcji. Tę zmianę można wprowadzić w programie HANA Studio. 

Mimo że SAP HANA tworzy kopie zapasowe pełnych segmentów dziennika automatycznie, określ interwał tworzenia kopii zapasowej dziennika, który ma być deterministyczny. Jest to szczególnie ważne w przypadku korzystania z opcji odzyskiwania po awarii, ponieważ zazwyczaj chcesz uruchamiać kopie zapasowe dzienników z okresem deterministycznym. W następnym przypadku 15 minut jest ustawiony jako interwał kopii zapasowej dziennika.

![Planowanie SAP HANA dzienników kopii zapasowych w programie SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Można również wybrać kopie zapasowe, które są częściej niż co 15 minut. Ustawienie częstsze jest często używane w połączeniu z funkcjami odzyskiwania po awarii w dużych wystąpieniach platformy HANA. Niektórzy klienci wykonują kopie zapasowe dziennika transakcji co 5 minut.

Jeśli kopia zapasowa bazy danych nigdy nie została utworzona, ostatnim krokiem jest wykonanie kopii zapasowej bazy danych opartej na plikach w celu utworzenia pojedynczego wpisu kopii zapasowej, który musi istnieć w katalogu kopii zapasowych. W przeciwnym razie SAP HANA nie może zainicjować określonych kopii zapasowych dziennika.

![Tworzenie kopii zapasowej opartej na plikach w celu utworzenia pojedynczego wpisu kopii zapasowej](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Po uruchomieniu pierwszej pomyślnej migawki magazynu Usuń migawkę testową uruchomioną w kroku 6. Aby uzyskać więcej informacji, zobacz "usuwanie migawek testowych-removeTestStorageSnapshot" w [narzędziach Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitoruj liczbę i rozmiar migawek na woluminie dysku

Na określonym woluminie magazynu można monitorować liczbę migawek i użycie magazynu dla tych migawek. `ls`Polecenie nie pokazuje katalogu lub plików migawek. Polecenie systemu operacyjnego Linux `du` wyświetla szczegóły dotyczące tych migawek magazynu, ponieważ są one przechowywane na tych samych woluminach. Użyj polecenia z następującymi opcjami:

- `du –sh .snapshot`: Ta opcja zapewnia łączną liczbę wszystkich migawek w katalogu migawek.
- `du –sh --max-depth=1`: Ta opcja zawiera listę wszystkich migawek zapisywanych w folderze **. snapshot** oraz rozmiar każdej migawki.
- `du –hc`: Ta opcja zapewnia łączny rozmiar używany przez wszystkie migawki.

Użyj tych poleceń, aby upewnić się, że migawki, które są pobierane i przechowywane, nie korzystają ze wszystkich magazynów woluminów.

>[!NOTE]
>Migawki rozruchowej jednostki LUN nie są widoczne dla poprzednich poleceń.

### <a name="get-details-of-snapshots"></a>Pobierz szczegóły migawek
Aby uzyskać więcej informacji na temat migawek, Użyj skryptu `azure_hana_snapshot_details` . Ten skrypt można uruchomić w dowolnej lokalizacji, jeśli istnieje aktywny serwer w lokalizacji odzyskiwania po awarii. Skrypt zawiera następujące dane wyjściowe, podzielone na poszczególne woluminy, które zawierają migawki: 
   * Rozmiar łącznej liczby migawek w woluminie
   * Następujące szczegóły dotyczące każdej migawki w tym woluminie: 
      - Nazwa migawki 
      - Czas utworzenia 
      - Rozmiar migawki
      - Częstotliwość tworzenia migawek
      - Identyfikator kopii zapasowej HANA skojarzony z tą migawką (jeśli dotyczy)

Składnia polecenia i danych wyjściowych znajduje się w temacie "list snapshots-azure_hana_snapshot_details" w [narzędziach Microsoft migawek Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Zmniejsz liczbę migawek na serwerze

Jak wyjaśniono wcześniej, można zmniejszyć liczbę niektórych etykiet przechowywanych migawek. Ostatnie dwa parametry polecenia umożliwiającego zainicjowanie migawki to etykieta i liczba migawek, które mają zostać zachowane.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

W poprzednim przykładzie etykieta migawki to **dailyhana**. Liczba migawek z tą etykietą, które mają być przechowywane, wynosi **28**. W odpowiedzi na użycie miejsca na dysku można zmniejszyć liczbę przechowywanych migawek. Prostą metodą zredukowania liczby migawek do 15, na przykład jest uruchomienie skryptu z ostatnim parametrem ustawionym na **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

W przypadku uruchomienia skryptu z tym ustawieniem liczba migawek obejmujących nową migawkę magazynu wynosi 15. Zostaną zachowane 15 najnowszych migawek i zostaną usunięte 15 starszych migawek.

 >[!NOTE]
 > Ten skrypt zmniejsza liczbę migawek tylko wtedy, gdy migawki są starsze niż jedna godzina. Skrypt nie usuwa migawek, które są starsze niż jedna godzina. Ograniczenia te są związane z opcjonalną oferowaną funkcją odzyskiwania po awarii.

Jeśli nie chcesz już przechowywać zestawu migawek z prefiksem kopii zapasowej **dailyhana** w przykładach składni, uruchom skrypt z **wartością 0** jako numer przechowywania. Wszystkie migawki, które pasują do tej etykiety, zostaną usunięte. Usunięcie wszystkich migawek może mieć wpływ na możliwości odzyskiwania po awarii w dużych wystąpieniach platformy HANA.

Drugą opcją usunięcia określonych migawek jest użycie skryptu `azure_hana_snapshot_delete` . Ten skrypt służy do usuwania migawki lub zestawu migawek przy użyciu identyfikatora kopii zapasowej HANA, który znajduje się w programie HANA Studio, lub za pośrednictwem samej nazwy migawki. Obecnie identyfikator kopii zapasowej jest powiązany tylko z migawkami utworzonymi dla typu migawki platformy **Hana** . Kopie zapasowe migawek typu **dzienników** i **rozruchu** nie wykonują SAP HANA migawki, dlatego nie istnieje identyfikator kopii zapasowej do znalezienia dla tych migawek. Jeśli nazwa migawki zostanie wprowadzona, szuka wszystkich migawek na różnych woluminach, które pasują do podanej nazwy migawki. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Aby uzyskać więcej informacji na temat skryptu, zobacz "Usuwanie migawki — azure_hana_snapshot_delete" w [narzędziach Microsoft Snapshot Tools dla SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Uruchom skrypt jako **katalog główny**użytkownika.

>[!IMPORTANT]
>Jeśli istnieją dane, które istnieją tylko w migawce, którą planujesz usunąć, po usunięciu migawki te dane zostaną utracone w nieskończoność.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Przywracanie na poziomie pliku z migawki magazynu

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
W przypadku typów migawek **Hana** i **Logs**można uzyskać dostęp do migawek bezpośrednio na woluminach w katalogu **. snapshot** . Dla każdej z migawek istnieje podkatalog. Skopiuj każdy plik w stanie, w którym znajdował się w punkcie migawki z tego podkatalogu, do rzeczywistej struktury katalogów. 

W bieżącej wersji skryptu *nie* ma skryptu przywracania podanego dla przywracania migawek jako samoobsługi. Przywracanie migawek można wykonać w ramach samoobsługowego skryptu odzyskiwania po awarii w lokacji odzyskiwania po awarii podczas pracy w trybie failover. Aby przywrócić żądaną migawkę z istniejących dostępnych migawek, należy skontaktować się z zespołem ds. operacyjnych firmy Microsoft, otwierając żądanie obsługi.

>[!NOTE]
>Przywracanie pojedynczego pliku nie działa w przypadku migawek jednostki LUN rozruchowej niezależnej od typu jednostek dużych wystąpień usługi HANA. Katalog **. snapshot** nie jest ujawniany w rozruchowym numerze LUN. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Odzyskaj do najnowszej migawki platformy HANA

W scenariuszu produkcyjnym proces odzyskiwania z migawki magazynu może być uruchamiany jako zdarzenie klienta z obsługą Microsoft Azure. Jest to wysoce pilność, jeśli dane zostały usunięte w systemie produkcyjnym i jedynym sposobem na jego pobranie jest przywrócenie produkcyjnej bazy danych.

W innej sytuacji odzyskiwanie do punktu w czasie może być małe i planowane z wyprzedzeniem. Można zaplanować to odzyskiwanie przy użyciu SAP HANA na platformie Azure zamiast podnoszenia flag o wysokim priorytecie. Na przykład można zaplanować uaktualnienie oprogramowania SAP przez zastosowanie nowego pakietu rozszerzenia. Następnie należy przywrócić migawkę reprezentującą stan przed uaktualnieniem pakietu udoskonalania.

Przed wysłaniem żądania należy przygotować. SAP HANA w usłudze Azure Team może następnie obsłużyć żądanie i udostępnić przywrócone woluminy. Następnie należy przywrócić bazę danych HANA na podstawie migawek.

Aby uzyskać informacje na temat możliwości przywrócenia migawki przy użyciu nowego zestawu narzędzi, zobacz sekcję "jak przywrócić migawkę" w [podręczniku odzyskiwania ręcznego dla SAP HANA na platformie Azure z migawki magazynu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Aby przygotować się do żądania, wykonaj następujące kroki.

1. Zdecyduj, która migawka ma zostać przywrócona. Tylko wolumin Hana/dane jest przywracany, chyba że postanowisz inaczej. 

1. Zamknij wystąpienie platformy HANA.

   ![Zamykanie wystąpienia platformy HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Odinstaluj woluminy danych w każdym węźle bazy danych HANA. Jeśli woluminy danych są nadal zainstalowane w systemie operacyjnym, przywracanie migawek kończy się niepowodzeniem.

   ![Odinstaluj woluminy danych w każdym węźle bazy danych HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Otwórz żądanie pomocy technicznej platformy Azure i Dołącz instrukcje dotyczące przywracania określonej migawki:

   - Podczas przywracania: SAP HANA w usłudze platformy Azure może polecić wzięcie udziału w konferencji konferencyjnej, zweryfikować i upewnienie się, że przywrócono poprawną migawkę magazynu. 

   - Po przywróceniu: SAP HANA w usłudze Azure powiadamia użytkownika o przywróceniu migawki magazynu.

1. Po zakończeniu procesu przywracania Zainstaluj ponownie wszystkie woluminy danych.

   ![Zainstaluj ponownie wszystkie woluminy danych](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Kolejną możliwością, na przykład SAP HANA plików danych odzyskanych z migawki magazynu, opisano w kroku 7 w [podręczniku odzyskiwania ręcznego dla SAP HANA na platformie Azure z migawki magazynu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Aby przywrócić kopię zapasową migawki, zobacz Podręcznik [odzyskiwania ręcznego dla SAP HANA na platformie Azure z migawki magazynu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Jeśli migawka została przywrócona przez operacje firmy Microsoft, nie musisz wykonywać kroku 7.


### <a name="recover-to-another-point-in-time"></a>Odzyskaj do innego punktu w czasie
Aby przywrócić do określonego punktu w czasie, zobacz "odzyskiwanie bazy danych do następującego punktu w czasie" w [podręczniku odzyskiwania ręcznego dla SAP HANA na platformie Azure z migawki magazynu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


## <a name="next-steps"></a>Następne kroki
- Zobacz [zasady odzyskiwania po awarii i przygotowania](hana-concept-preparation.md).
