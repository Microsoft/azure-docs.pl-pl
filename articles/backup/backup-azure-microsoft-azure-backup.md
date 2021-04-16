---
title: Używanie Azure Backup Server kopii zapasowej obciążeń
description: W tym artykule dowiesz się, jak przygotować środowisko do ochrony i kopii zapasowej obciążeń przy użyciu Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: b13eb22ad11535114b1cb82630bc1b490a03173f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517576"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Instalowanie i uaktualnianie Azure Backup Server

> [!div class="op_single_selector"]
>
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [ScDPM](backup-azure-dpm-introduction.md)
>
>

> Dotyczy: MABS v3. (Program MABS w wersji 2 nie jest już obsługiwany. Jeśli używasz wersji wcześniejszej niż WERSJA 3 usługi MABS, uaktualnij do najnowszej wersji).

W tym artykule wyjaśniono, jak przygotować środowisko do obsługi kopii zapasowej obciążeń przy użyciu Microsoft Azure Backup Server (MABS). Za Azure Backup Server można chronić obciążenia aplikacji, takie jak maszyny wirtualne funkcji Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange i klienci systemu Windows z jednej konsoli.

> [!NOTE]
> Azure Backup Server teraz chronić maszyny wirtualne VMware i zapewnia większe możliwości zabezpieczeń. Zainstaluj produkt zgodnie z objaśniami w poniższych sekcjach i najnowszą wersję Azure Backup Agent. Aby dowiedzieć się więcej na temat kopii zapasowej serwerów VMware Azure Backup Server, zobacz artykuł Use Azure Backup Server to back up a VMware server (Używanie programu Azure Backup Server do kopii [zapasowej serwera VMware).](backup-azure-backup-server-vmware.md) Aby dowiedzieć się więcej o możliwościach zabezpieczeń, [zapoznaj się z Azure Backup funkcji zabezpieczeń.](backup-azure-security-feature.md)
>
>

Usługa MABS wdrożona na maszynie wirtualnej platformy Azure może tworzyć kopie zapasowe maszyn wirtualnych na platformie Azure, ale powinny one być w tej samej domenie, aby umożliwić wykonywanie kopii zapasowej. Proces tworzenia kopii zapasowej maszyny wirtualnej platformy Azure pozostaje taki sam jak w przypadku tworzenia kopii zapasowych maszyn wirtualnych lokalnie, jednak wdrażanie usługi MABS na platformie Azure ma pewne ograniczenia. Aby uzyskać więcej informacji na temat ograniczeń, zobacz [DPM as an Azure virtual machine (Program DPM jako maszyna wirtualna platformy Azure)](/system-center/dpm/install-dpm#setup-prerequisites)

> [!NOTE]
> Platforma Azure ma dwa modele wdrażania do tworzenia zasobów i pracy z [Resource Manager klasycznym](../azure-resource-manager/management/deployment-models.md). Ten artykuł zawiera informacje i procedury dotyczące przywracania maszyn wirtualnych wdrożonych przy użyciu Resource Manager wirtualnej.
>
>

Azure Backup Server dziedziczy większość funkcji tworzenia kopii zapasowej obciążenia z Data Protection Manager (DPM). Ten artykuł zawiera linki do dokumentacji programu DPM w celu wyjaśnienia niektórych funkcji udostępnionych. Chociaż Azure Backup Server ma wiele takich samych funkcji jak program DPM, program Azure Backup Server kopii zapasowej nie jest back up na taśmie ani nie integruje się z System Center.

## <a name="choose-an-installation-platform"></a>Wybieranie platformy instalacji

Pierwszym krokiem w kierunku skonfigurowania Azure Backup Server jest skonfigurowanie systemu Windows Server. Serwer może być na platformie Azure lub w środowisku lokalnym.

* Aby chronić obciążenia lokalne, serwer USŁUGI MABS musi znajdować się w środowisku lokalnym.
* Aby chronić obciążenia uruchomione na maszynach wirtualnych platformy Azure, serwer USŁUGI MABS musi znajdować się na platformie Azure i działać jako maszyna wirtualna platformy Azure.

### <a name="using-a-server-in-azure"></a>Korzystanie z serwera na platformie Azure

Podczas wybierania serwera do uruchamiania Azure Backup Server zaleca się rozpoczęcie od obrazu galerii systemu Windows Server 2016 Datacenter lub Windows Server 2019 Datacenter. Artykuł Create your first Windows virtual machine in the Azure Portal (Tworzenie pierwszej maszyny wirtualnej z systemem Windows w usłudze [Azure Portal)](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)zawiera samouczek dotyczący rozpoczynania pracy z zalecaną maszyną wirtualną na platformie Azure, nawet jeśli nigdy wcześniej nie używaliśmy platformy Azure. Zalecane minimalne wymagania dotyczące maszyny wirtualnej serwera powinny być: Standard_A4_v2 z czterema rdzeniami i 8 GB pamięci RAM.

Ochrona obciążeń za pomocą Azure Backup Server ma wiele niuansów. Macierz [ochrony dla mabs](./backup-mabs-protection-matrix.md) pomaga wyjaśnić te niuanse. Przed wdrożeniem maszyny przeczytaj całkowicie ten artykuł.

### <a name="using-an-on-premises-server"></a>Korzystanie z serwera lokalnego

Jeśli nie chcesz uruchamiać serwera podstawowego na platformie Azure, możesz uruchomić serwer na maszynie wirtualnej funkcji Hyper-V, maszynie wirtualnej VMware lub hoście fizycznym. Zalecane minimalne wymagania dotyczące sprzętu serwera to dwa rdzenie i 8 GB pamięci RAM. Obsługiwane systemy operacyjne zostały wymienione w poniższej tabeli:

| System operacyjny | Platforma | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64-bitowa |Standard, Datacenter, Essentials |
| Windows Server 2016 i najnowsze wersje |64-bitowa |Standard, Datacenter, Essentials  |

Można deduplikować magazyn programu DPM przy użyciu deduplikacji systemu Windows Server. Dowiedz się więcej o tym, jak program DPM i [deduplikacja](/system-center/dpm/deduplicate-dpm-storage) współpracują ze sobą podczas wdrażania na maszynach wirtualnych funkcji Hyper-V.

> [!NOTE]
> Azure Backup Server jest przeznaczony do uruchamiania na dedykowanym serwerze jednego przeznaczenia. Nie można instalować Azure Backup Server na:
>
> * Komputer działa jako kontroler domeny
> * Komputer, na którym jest zainstalowana rola serwera aplikacji
> * Komputer, który jest serwerem System Center Operations Manager zarządzania
> * Komputer, na którym jest uruchomiony program Exchange Server
> * Komputer, który jest węzłem klastra
>
> Instalowanie Azure Backup Server nie jest obsługiwane w systemie Windows Server Core ani Microsoft Hyper-V Server.

Zawsze dołączaj Azure Backup Server do domeny. Jeśli planujesz przeniesienie serwera do innej domeny, najpierw Azure Backup Server, a następnie dołącz serwer do nowej domeny. Przenoszenie istniejącej maszyny Azure Backup Server do nowej domeny po wdrożeniu nie *jest obsługiwane.*

Niezależnie od tego, czy dane kopii zapasowej są wysyłane na platformę Azure, czy przechowywane lokalnie, Azure Backup Server być zarejestrowane w magazynie usługi Recovery Services.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Ustawianie replikacji magazynu

Dla opcji replikacji magazynu można wybrać magazynowanie nadmiarowe geograficznie lub lokalnie. Domyślnie magazyny usługi Recovery Services używają magazynu geograficznie nadmiarowego. Jeśli ten magazyn jest magazynem podstawowym, pozostaw opcję magazynu ustawioną na magazyn geograficznie nadmiarowy. Wybierz magazyn lokalnie nadmiarowy, jeśli chcesz skorzystać z tańszej, ale mniej trwałej opcji. Więcej informacji na [temat opcji magazynu geograficznie nadmiarowego,](../storage/common/storage-redundancy.md#geo-redundant-storage) [lokalnie nadmiarowego](../storage/common/storage-redundancy.md#locally-redundant-storage)i [strefowo nadmiarowego](../storage/common/storage-redundancy.md#zone-redundant-storage) można uzyskać w tesłudze [Azure Storage replication overview (Omówienie replikacji usługi Azure Storage).](../storage/common/storage-redundancy.md)

Aby edytować ustawienia replikacji magazynu:

1. W **okienku Magazyny usługi Recovery Services** wybierz nowy magazyn. W sekcji **Ustawienia** wybierz pozycję **Właściwości.**
2. W **obszarze Właściwości** w obszarze Konfiguracja kopii **zapasowej** wybierz pozycję **Aktualizuj**.

3. Wybierz typ replikacji magazynu, a następnie wybierz pozycję **Zapisz.**

     ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Pakiet oprogramowania

### <a name="downloading-the-software-package"></a>Pobieranie pakietu oprogramowania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Jeśli masz już otwarty magazyn usługi Recovery Services, przejdź do kroku 3. Jeśli nie masz otwartego magazynu usługi Recovery Services, ale jesteś w Azure Portal, w menu głównym wybierz pozycję **Przeglądaj.**

   * Na liście zasobów wpisz **Usługi odzyskiwania**.
   * Po rozpoczęciu pisania zawartość listy będzie filtrowana w oparciu o wpisywane dane. Po wyświetleniu **opcji Magazyny usługi Recovery Services** wybierz ją.

     ![Tworzenie magazynu usługi Recovery Services , krok 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Zostanie wyświetlona lista magazynów Usług odzyskiwania.
   * Wybierz magazyn z listy magazynów Usług odzyskiwania.

     Zostanie otwarty pulpit nawigacyjny wybranego magazynu.

     ![Pulpit nawigacyjny magazynu](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Domyślnie **zostanie** otwarte okienko Ustawienia. Jeśli okno jest zamknięte, wybierz pozycję **Ustawienia,** aby otworzyć okienko ustawień.

    ![Okienko Ustawienia](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Wybierz **pozycję Kopia** zapasowa, aby otworzyć Wprowadzenie kreatora.

    ![Wprowadzenie do tworzenia kopii zapasowej](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    W **okienku Wprowadzenie kopii** zapasowej,  które zostanie otwarte, zostanie automatycznie wybrana opcja Cele kopii zapasowej.

    ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. W **okienku Cel** kopii zapasowej z menu Where **is your workload running (Gdzie działa Twoje** obciążenie) wybierz pozycję **On-premises (Lokalnie).**

    ![lokalne i obciążenia jako cele](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Z menu **rozwijanego** Co chcesz wykonać kopię zapasową? wybierz obciążenia, które chcesz chronić przy użyciu Azure Backup Server, a następnie wybierz przycisk **OK.**

    Kreator **Wprowadzenie kopii zapasowej** przełącza opcję Przygotowywanie **infrastruktury** w celu tworzenia kopii zapasowych obciążeń na platformie Azure.

   > [!NOTE]
   > Jeśli chcesz tylko wrócić do kopii zapasowej plików i folderów, zalecamy użycie agenta programu Azure Backup i zastosowanie się do wskazówek w artykule Pierwsze spojrzenie: kopii zapasowej [plików i folderów.](./backup-windows-with-mars-agent.md) Jeśli zamierzasz chronić więcej niż pliki i foldery lub planujesz w przyszłości rozszerzyć wymagania dotyczące ochrony, wybierz te obciążenia.
   >
   >

    ![Wprowadzenie zmiany kreatora](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. W **okienku Przygotowywanie infrastruktury,**  które zostanie otwarte, wybierz linki Pobierz dla opcji Azure Backup Server i Pobierz poświadczenia magazynu. Poświadczenia magazynu są używane podczas rejestracji Azure Backup Server magazynu usługi Recovery Services. Linki te przejdą do Centrum pobierania, do którego można pobrać pakiet oprogramowania.

    ![Przygotowywanie infrastruktury na Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Zaznacz wszystkie pliki, a następnie wybierz pozycję **Dalej.** Pobierz wszystkie pliki pochodzące ze strony Microsoft Azure Backup pobierania i umieść wszystkie pliki w tym samym folderze.

    ![Centrum pobierania 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Ponieważ rozmiar pobierania wszystkich plików razem wynosi > 3 GB, ukończenie pobierania za > przy linku pobierania o rozmiarze 10 Mb/s może potrwać do 60 minut.

### <a name="extracting-the-software-package"></a>Wyodrębnianie pakietu oprogramowania

Po pobraniu wszystkich plików wybierz pozycję **MicrosoftAzureBackupInstaller.exe**. Spowoduje to uruchomienie **Microsoft Azure Backup Kreator instalacji** do wyodrębnienia plików instalacyjnych do lokalizacji określonej przez użytkownika. Kontynuuj pracę kreatora i wybierz przycisk **Wyodrębnij,** aby rozpocząć proces wyodrębniania.

> [!WARNING]
> Do wyodrębnienia plików instalacyjnych jest wymagane co najmniej 4 GB wolnego miejsca.
>
>

![Konfigurowanie wyodrębniania plików do instalacji](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Po zakończeniu procesu wyodrębniania zaznacz pole wyboru, aby uruchomić wyodrębnionysetup.exe, aby rozpocząć instalowanie Microsoft Azure Backup Server, a następnie wybierz **przycisk** Zakończ. 

### <a name="installing-the-software-package"></a>Instalowanie pakietu oprogramowania

1. Wybierz **Microsoft Azure Backup,** aby uruchomić kreatora instalacji.

    ![Microsoft Azure Backup Kreator instalacji](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Na ekranie powitalnym wybierz **przycisk** Dalej. W ten sposób zostanie przekierowynyny do *sekcji Sprawdzanie wymagań wstępnych.* Na tym ekranie wybierz pozycję **Sprawdź,** aby określić, czy wymagania wstępne dotyczące sprzętu i oprogramowania Azure Backup Server zostały spełnione. Jeśli wszystkie wymagania wstępne zostaną spełnione pomyślnie, zostanie wyświetlony komunikat informujący o tym, że maszyna spełnia wymagania. Kliknij przycisk **Next** (Dalej).

    ![Azure Backup Server — sprawdzanie powitania i wymagań wstępnych](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Pakiet Azure Backup Server jest dostarczany w pakiecie z odpowiednimi SQL Server wymaganymi plikami binarnymi. Podczas uruchamiania nowej Azure Backup Server instalacji wybierz opcję Zainstaluj nowe wystąpienie programu **SQL Server tej** instalacji, a następnie wybierz przycisk Sprawdź **i** zainstaluj. Po pomyślnym zainstalowaniu wymagań wstępnych wybierz pozycję **Dalej.**

    >[!NOTE]
    >Jeśli chcesz użyć własnego programu SQL Server, obsługiwane wersje SQL Server to SQL Server 2014 SP1 lub wyższa, 2016 i 2017.  Wszystkie SQL Server powinny być w wersji Standard lub Enterprise w wersji 64-bitowej.
    >Azure Backup Server nie będzie działać z wystąpieniem SQL Server zdalnego. Wystąpienie używane przez Azure Backup Server musi być lokalne. Jeśli używasz istniejącego serwera SQL dla usługi MABS, konfiguracja usługi MABS obsługuje tylko użycie nazwanych *wystąpień* programu SQL Server.

    ![Azure Backup Server — sprawdzanie sql](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Jeśli wystąpi błąd z zaleceniem ponownego uruchomienia komputera, zrób to i wybierz **pozycję Sprawdź ponownie.** Jeśli występują problemy z konfiguracją sql, skonfiguruj ponownie program SQL zgodnie z wytycznymi sql i spróbuj ponownie zainstalować/uaktualnić usługę MABS przy użyciu istniejącego wystąpienia programu SQL.

   **Konfiguracja ręczna**

   Jeśli używasz własnego wystąpienia programu SQL, upewnij się, że do głównej bazy danych dodasz rolę builtin\Administrators do roli sysadmin.

    **Konfiguracja usług SSRS z programem SQL 2017**

    Jeśli używasz własnego wystąpienia programu SQL 2017, musisz ręcznie skonfigurować usługi SSRS. Po skonfigurowaniu usług SSRS upewnij się, że właściwość *IsInitialized* usług SSRS ma wartość *True.* Jeśli ta wartość jest ustawiona na wartość True, mabs zakłada, że usługi SSRS są już skonfigurowane i pomijają konfigurację usług SSRS.

    Użyj następujących wartości dla konfiguracji usług SSRS:
    * Konto usługi: "Użyj wbudowanego konta" powinno być usługą sieciową
    * Adres URL usługi sieci Web: "Katalog wirtualny" powinien być ReportServer_\<SQLInstanceName>
    * Baza danych: DatabaseName powinna mieć wartość ReportServer$\<SQLInstanceName>
    * Adres URL portalu internetowego: "Katalog wirtualny" powinien być Reports_\<SQLInstanceName>

    [Dowiedz się więcej](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) o konfiguracji usług SSRS.

    > [!NOTE]
    > Licencjonowanie licencji SQL Server używanych jako baza danych usługi MABS podlega ustawie [Microsoft Postanowienia dotyczące Usług Online](https://www.microsoft.com/licensing/product-licensing/products) (OST). Zgodnie z OST SQL Server w pakiecie z usługą MABS mogą być używane tylko jako baza danych dla serwera MABS.

4. Podaj lokalizację instalacji plików serwera Microsoft Azure Backup wybierz pozycję **Dalej.**

    ![Podaj lokalizację instalacji plików](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Lokalizacja zapasowa jest wymaganiem do kopii zapasowej na platformie Azure. Upewnij się, że lokalizacja zapasowa to co najmniej 5% danych, których kopię zapasową planuje się wrócić do chmury. W celu ochrony dysku należy skonfigurować oddzielne dyski po zakończeniu instalacji. Aby uzyskać więcej informacji na temat pul magazynu, zobacz [Przygotowanie magazynu danych.](/system-center/dpm/plan-long-and-short-term-data-storage)

    Wymagania dotyczące pojemności magazynu dyskowego zależą przede wszystkim od rozmiaru chronionych danych, codziennego rozmiaru punktu odzyskiwania, oczekiwanego szybkości wzrostu ilości danych i celów zakresu przechowywania. Zalecamy, aby magazyn dyskowy był dwa razy rozmiarem chronionych danych. Wynika to z zakładanego codziennego rozmiaru punktu odzyskiwania na poziomie 10% rozmiaru chronionych danych oraz zakresu przechowywania równego 10 dni. Aby uzyskać dobre oszacowanie rozmiaru, zapoznaj się z tematem [DPM Planista wydajności](https://www.microsoft.com/download/details.aspx?id=54301). 

5. Podaj silne hasło dla kont użytkowników lokalnych z ograniczeniami, a następnie wybierz pozycję **Dalej.**

    ![Podaj silne hasło](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Wybierz, czy chcesz użyć programu *Microsoft Update,* aby sprawdzić aktualizacje, a następnie wybierz pozycję **Dalej.**

   > [!NOTE]
   > Zalecamy przekierowywanie Windows Update do usługi Microsoft Update, która oferuje zabezpieczenia i ważne aktualizacje dla systemu Windows i innych produktów, takich jak Microsoft Azure Backup Server.
   >
   >

    ![Microsoft Update Opt-In](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Przejrzyj podsumowanie *ustawień i wybierz* pozycję **Zainstaluj.**

    ![Podsumowanie ustawień](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Instalacja odbywa się w fazach. W pierwszej fazie na serwerze Microsoft Azure agent usług Recovery Services. Kreator sprawdza również łączność z Internetem. Jeśli łączność z Internetem jest dostępna, możesz kontynuować instalację. Jeśli nie, musisz podać szczegóły serwera proxy, aby nawiązać połączenie z Internetem.

    Następnym krokiem jest skonfigurowanie agenta Microsoft Azure Recovery Services. W ramach konfiguracji musisz podać poświadczenia magazynu, aby zarejestrować maszynę w magazynie usługi Recovery Services. Udostępnisz również hasło do szyfrowania/odszyfrowywania danych wysyłanych między platformą Azure i twoją siedzibą. Możesz automatycznie wygenerować hasło lub podać własne co najmniej 16-znakowe hasło. Kontynuuj pracę z kreatorem, dopóki agent nie zostanie skonfigurowany.

    ![Kreator rejestrowania serwera](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Po pomyślnym Microsoft Azure Backup serwera konfiguracji kreator instalacji ogólnej przechodzi do instalacji i konfiguracji serwera SQL Server i Azure Backup Server konfiguracji. Po SQL Server składników zostaną zainstalowane Azure Backup Server składników.

    ![Azure Backup Server konfiguracji](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Po zakończeniu kroku instalacji zostaną również utworzone ikony pulpitu produktu. Kliknij dwukrotnie ikonę, aby uruchomić produkt.

### <a name="add-backup-storage"></a>Dodawanie magazynu kopii zapasowych

Pierwsza kopia zapasowa jest przechowywana w magazynie dołączonym do Azure Backup Server wirtualnej. Aby uzyskać więcej informacji na temat dodawania dysków, zobacz [Konfigurowanie pul magazynów i magazynu dysków.](./backup-mabs-add-storage.md)

> [!NOTE]
> Musisz dodać magazyn kopii zapasowych, nawet jeśli planujesz wysłać dane na platformę Azure. W bieżącej architekturze usługi Azure Backup Server magazyn Azure Backup przechowuje  drugą kopię danych, a magazyn lokalny przechowuje pierwszą (i obowiązkową) kopię zapasową.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Instalowanie i aktualizowanie agenta Data Protection Manager ochrony

MaBS używa agenta System Center Data Protection Manager ochrony. [Poniżej znajdują się kroki instalacji](/system-center/dpm/deploy-dpm-protection-agent) agenta ochrony programu na serwerach ochrony.

W poniższych sekcjach opisano sposób aktualizowania agentów ochrony dla komputerów klienckich.

1. W chmurze Backup Server konsola administratora pozycję **Agenci**  >  **zarządzania.**

2. W okienku wyświetlania wybierz komputery klienckie, dla których chcesz zaktualizować agenta ochrony.

   > [!NOTE]
   > Kolumna **Aktualizacje agenta** wskazuje, kiedy aktualizacja agenta ochrony jest dostępna dla każdego komputera chronionego. W **okienku** Akcje akcja **Aktualizuj** jest dostępna tylko wtedy, gdy komputer chroniony jest zaznaczony, a aktualizacje są dostępne.
   >
   >

3. Aby zainstalować zaktualizowanych agentów ochrony na wybranych komputerach, w **okienku Akcje** wybierz pozycję **Aktualizuj.**

4. W przypadku komputera klienckiego, który nie jest połączony z siecią, dopóki komputer nie zostanie połączony z siecią, kolumna **Stan** agenta będzie zawierała stan **Oczekująca aktualizacja.**

   Po podłączeniu komputera klienckiego do sieci kolumna **Aktualizacje** agenta dla tego komputera klienckiego zawiera stan **Aktualizowanie**.

## <a name="move-mabs-to-a-new-server"></a>Przenoszenie usługi MABS na nowy serwer

Poniżej znajdują się kroki, które należy wykonać, aby przenieść usługę MABS na nowy serwer przy zachowaniu magazynu. Można to zrobić tylko wtedy, gdy wszystkie dane są Nowoczesny magazyn kopii zapasowych.

  > [!IMPORTANT]
  >
  > * Nazwa nowego serwera musi mieć taką samą nazwę jak oryginalna nazwa Azure Backup Server wystąpienia. Nie można zmienić nazwy nowego wystąpienia usługi Azure Backup Server, jeśli chcesz zachować punkty odzyskiwania przy użyciu poprzedniej puli magazynów i bazy danych MABS (DPMDB).
  > * Musisz mieć kopię zapasową bazy danych USŁUGI MABS (DPMDB). Będzie on potrzebny do przywrócenia bazy danych.

1. W okienku wyświetlania wybierz komputery klienckie, dla których chcesz zaktualizować agenta ochrony.
2. Zamknij oryginalny serwer Azure Backup lub przejmij go w tryb offline.
3. Zresetuj konto komputera w usłudze Active Directory.
4. Zainstaluj program Server 2016 na nowej maszynie i nadaj mu tę samą nazwę maszyny co oryginalny Azure Backup serwer.
5. Dołącz do domeny.
6. Zainstaluj Azure Backup Server wersji 3 lub nowszej (przenieś dyski puli magazynów USŁUGI MABS ze starego serwera i zaimportuj je).
7. Przywróć bazy danych DPMDB wykonane w kroku 1.
8. Dołącz magazyn z oryginalnego serwera kopii zapasowych do nowego serwera.
9. Z bazy danych SQL przywróć bazę danych DPMDB.
10. Uruchom polecenie CMD (jako administrator) na nowym serwerze. Przejdź do lokalizacji Microsoft Azure Backup folderu bin

    Przykład ścieżki: `C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"`

11. Aby nawiązać połączenie Azure Backup, uruchom `DPMSYNC -SYNC`

    Jeśli nowe dyski zostały **dodane** do puli magazynów programu DPM zamiast przenoszenia starych, uruchom `DPMSYNC -Reallocatereplica` .

## <a name="network-connectivity"></a>Łączność sieciowa

Azure Backup Server wymaga łączności z usługą Azure Backup, aby produkt działał pomyślnie. Aby sprawdzić, czy maszyna ma łączność z platformą Azure, użyj ```Get-DPMCloudConnection``` polecenia cmdlet Azure Backup Server programu PowerShell. Jeśli dane wyjściowe polecenia cmdlet mają wartość TRUE, łączność istnieje. W przeciwnym razie nie będzie łączności.

Jednocześnie subskrypcja platformy Azure musi być w dobrej kondycji. Aby sprawdzić stan subskrypcji i zarządzać jej, zaloguj się do [portalu subskrypcji.](https://account.windowsazure.com/Subscriptions)

Po poznaniu stanu łączności platformy Azure i subskrypcji platformy Azure możesz skorzystać z poniższej tabeli, aby dowiedzieć się, jaki wpływ ma na oferowaną funkcjonalność tworzenia/przywracania kopii zapasowych.

| Stan łączności | Subskrypcja platformy Azure | Tworzenie kopii zapasowej w systemie Azure | Back up to disk (Kopii zapasowej na dysku) | Przywracanie z platformy Azure | Przywracanie z dysku |
| --- | --- | --- | --- | --- | --- |
| Połączone |Aktywna |Dozwolone |Dozwolone |Dozwolone |Dozwolone |
| Połączone |Wygasłe |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Połączone |Coprowizowana |Zatrzymano |Zatrzymano |Zatrzymano i usunięto punkty odzyskiwania platformy Azure |Zatrzymano |
| Utracona łączność > 15 dni |Aktywna |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Utracona łączność > 15 dni |Wygasłe |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Utracona łączność > 15 dni |Coprowizowana |Zatrzymano |Zatrzymano |Zatrzymano i usunięto punkty odzyskiwania platformy Azure |Zatrzymano |

### <a name="recovering-from-loss-of-connectivity"></a>Odzyskiwanie po utracie łączności

Jeśli komputer ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na maszynie lub serwerze proxy zezwalają na następujące adresy URL i IP:

* Adresy URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Adresy IP
  * 20.190.128.0/18
  * 40.126.0.0/18

Jeśli używasz komunikacji równorzędnej firmy Microsoft z usługą ExpressRoute, wybierz następujące usługi/regiony:

* Azure Active Directory (12076:5060)
* Microsoft Azure regionu (zgodnie z lokalizacją magazynu usługi Recovery Services)
* Azure Storage (zgodnie z lokalizacją magazynu usługi Recovery Services)

Aby uzyskać więcej informacji, odwiedź stronę [ExpressRoute routing requirements (Wymagania dotyczące routingu usługi ExpressRoute).](../expressroute/expressroute-routing.md)

Po przywróceniu łączności z platformą Azure Azure Backup Server maszyny wirtualnej operacje, które można wykonać, są określane na podstawie stanu subskrypcji platformy Azure. W powyższej tabeli połączono się ze szczegółami operacji dozwolonych.

### <a name="handling-subscription-states"></a>Obsługa stanów subskrypcji

Można przejmować subskrypcję platformy Azure ze stanu *Wygasła* lub *Coprowizowana* do *stanu Aktywny.* Ma to jednak pewien wpływ na zachowanie produktu, gdy stan nie jest *aktywny:*

* Subskrypcja, dla których *anulowano* aprowizę, utraci funkcjonalność w okresie, w którym anulowano jej aprowizę. Po włączeniu *funkcji Active*(Aktywny) funkcja tworzenia/przywracania kopii zapasowej jest przywracana. Dane kopii zapasowej na dysku lokalnym można również pobrać, jeśli były przechowywane z wystarczająco dużym okresem przechowywania. Jednak dane kopii zapasowej na platformie Azure są nieodwracalnie utracone po wejściu subskrypcji w stan *Coprowizowanie.*
* *Wygasła* subskrypcja traci funkcjonalność tylko do momentu, gdy zostanie ponownie *aktywna.* Nie można uruchomić żadnych kopii zapasowych zaplanowanych na okres *wygaśnięcia* subskrypcji.

## <a name="upgrade-mabs"></a>Uaktualnianie mabs

Aby uaktualnić system MABS, należy użyć poniższych procedur.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Uaktualnianie z programu MABS w wersji 2 do wersji 3

> [!NOTE]
>
> Nie jest to wymaganie wstępne dotyczące instalowania programu MABS w wersji 3. Można jednak uaktualnić do wersji 3 programu MABS tylko z wersji 2.

Aby uaktualnić system MABS, należy wykonać następujące czynności:

1. Aby uaktualnić usługę MABS w wersji 2 do usługi MABS w wersji 3, w razie potrzeby uaktualnij system operacyjny do systemu Windows Server 2016 lub Windows Server 2019.

2. Uaktualnij serwer. Kroki są podobne do [instalacji](#install-and-upgrade-azure-backup-server). Jednak w przypadku ustawień sql będzie dostępna opcja uaktualnienia wystąpienia SQL do wersji SQL 2017 lub użycia własnego wystąpienia programu SQL Server 2017.

   > [!NOTE]
   >
   > Nie zamykaj podczas uaktualnienia wystąpienia SQL. Zamknięcie programu spowoduje odinstalowanie wystąpienia raportowania SQL, więc próba ponownego uaktualnienia usługi MABS zakończy się niepowodzeniem.

   > [!IMPORTANT]
   >
   >  W ramach uaktualnienia programu SQL 2017 kopię zapasową kluczy szyfrowania SQL i odinstalowywanie usług raportowania. Po uaktualnieniu programu SQL Server usługa raportowania (14.0.6827.4788) jest instalowana & kluczy szyfrowania.
   >
   > W przypadku ręcznego konfigurowania programu SQL 2017 zapoznaj się z sekcją Konfiguracja usług *SSRS w programie SQL 2017* w sekcji Instrukcje instalacji.

3. Zaktualizuj agentów ochrony na chronionych serwerach.
4. Tworzenie kopii zapasowych powinno być kontynuowane bez konieczności ponownego uruchamiania serwerów produkcyjnych.
5. Teraz możesz rozpocząć ochronę danych. W przypadku uaktualniania do wersji Nowoczesny magazyn kopii zapasowych podczas ochrony możesz również wybrać woluminy, w których chcesz przechowywać kopie zapasowe, i sprawdzić, czy są dostępne aprowizowane miejsce. [Dowiedz się więcej](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli Microsoft Azure Backup się z błędami podczas fazy instalacji (lub tworzenia kopii zapasowej lub przywracania), zapoznaj się z tym dokumentem z kodami [błędów,](https://support.microsoft.com/kb/3041338)  aby uzyskać więcej informacji.
Możesz również zapoznać się z [Azure Backup często zadawanych pytaniach](backup-azure-backup-faq.yml)

## <a name="next-steps"></a>Następne kroki

Szczegółowe informacje na temat przygotowywania środowiska dla programu DPM można znaleźć [tutaj.](/system-center/dpm/prepare-environment-for-dpm) Zawiera również informacje o obsługiwanych konfiguracjach, w których Azure Backup Server można wdrożyć i używać. Do wykonywania różnych operacji można użyć serii [poleceń cmdlet programu PowerShell.](/powershell/module/dataprotectionmanager/)

Możesz użyć tych artykułów, aby lepiej zrozumieć ochronę obciążeń przy użyciu Microsoft Azure Backup serwera.

* [SQL Server kopii zapasowej](backup-azure-backup-sql.md)
* [Kopia zapasowa programu SharePoint Server](backup-azure-backup-sharepoint.md)
* [Kopia zapasowa alternatywnego serwera](backup-azure-alternate-dpm-server.md)
