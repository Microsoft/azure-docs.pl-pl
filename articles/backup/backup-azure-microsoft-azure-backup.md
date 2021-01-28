---
title: Użyj Azure Backup Server, aby utworzyć kopię zapasową obciążeń
description: W tym artykule dowiesz się, jak przygotować środowisko do ochrony i tworzenia kopii zapasowych obciążeń przy użyciu Microsoft Azure Backup Server (serwera usługi MAB).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: d476c228a619f03f798c1a2cd6854a8d603c3637
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987026"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Instalowanie i uaktualnianie Azure Backup Server

> [!div class="op_single_selector"]
>
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Dotyczy: serwera usługi MAB v3. (Serwera usługi MAB v2 nie jest już obsługiwany. Jeśli używasz wersji starszej niż serwera usługi MAB v3, przeprowadź uaktualnienie do najnowszej wersji.

W tym artykule wyjaśniono, jak przygotować środowisko do tworzenia kopii zapasowych obciążeń przy użyciu Microsoft Azure Backup Server (serwera usługi MAB). Za pomocą Azure Backup Server można chronić obciążenia aplikacji, takie jak maszyny wirtualne funkcji Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange i klienci z systemem Windows, z poziomu pojedynczej konsoli.

> [!NOTE]
> Azure Backup Server mogą teraz chronić maszyny wirtualne VMware i zapewniać ulepszone funkcje zabezpieczeń. Zainstaluj produkt zgodnie z opisem w poniższych sekcjach i najnowszym agentem Azure Backup. Aby dowiedzieć się więcej o tworzeniu kopii zapasowych serwerów VMware za pomocą Azure Backup Server, zobacz artykuł [Azure Backup Server do tworzenia kopii zapasowej serwera VMware](backup-azure-backup-server-vmware.md). Aby dowiedzieć się więcej o możliwościach zabezpieczeń, zapoznaj się z [dokumentacją dotyczącą funkcji zabezpieczeń Azure Backup](backup-azure-security-feature.md).
>
>

SERWERA usługi MAB wdrożone na maszynie wirtualnej platformy Azure mogą tworzyć kopie zapasowe maszyn wirtualnych na platformie Azure, ale powinny one znajdować się w tej samej domenie w celu włączenia operacji tworzenia kopii Proces tworzenia kopii zapasowej maszyny wirtualnej platformy Azure jest taki sam jak tworzenie kopii zapasowych maszyn wirtualnych w środowisku lokalnym, ale Wdrażanie serwera usługi MAB na platformie Azure ma pewne ograniczenia. Aby uzyskać więcej informacji o ograniczeniach, zobacz [program DPM jako maszynę wirtualną platformy Azure](/system-center/dpm/install-dpm#setup-prerequisites) .

> [!NOTE]
> Platforma Azure ma dwa modele wdrażania służące do tworzenia zasobów i pracy z nimi: [Menedżer zasobów i klasyczne](../azure-resource-manager/management/deployment-models.md). Ten artykuł zawiera informacje i procedury przywracania maszyn wirtualnych wdrożonych przy użyciu modelu Menedżer zasobów.
>
>

Azure Backup Server dziedziczy większość funkcji tworzenia kopii zapasowej obciążenia z Data Protection Manager (DPM). Ten artykuł zawiera linki do dokumentacji programu DPM w celu wyjaśnienia niektórych funkcji udostępnionych. Chociaż Azure Backup Server ma wiele takich samych funkcji co program DPM, Azure Backup Server nie tworzy kopii zapasowej na taśmie ani nie integruje się z programem System Center.

## <a name="choose-an-installation-platform"></a>Wybierz platformę instalacji

Pierwszym krokiem w celu uzyskania Azure Backup Server pracy jest skonfigurowanie systemu Windows Server. Serwer może być na platformie Azure lub lokalnie.

* Aby chronić obciążenia lokalne, serwer serwera usługi MAB musi znajdować się w środowisku lokalnym.
* Aby chronić obciążenia działające na maszynach wirtualnych platformy Azure, serwer serwera usługi MAB musi znajdować się na platformie Azure i działać jako maszyna wirtualna platformy Azure.

### <a name="using-a-server-in-azure"></a>Korzystanie z serwera na platformie Azure

W przypadku wybrania serwera do uruchamiania Azure Backup Server zaleca się rozpoczęcie od obrazu z galerii systemu Windows Server 2016 Datacenter lub Windows Server 2019 centrum danych. W tym artykule opisano [Tworzenie pierwszej maszyny wirtualnej z systemem Windows w Azure Portal, w](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)której znajduje się samouczek dotyczący rozpoczynania pracy z zalecaną maszyną wirtualną na platformie Azure, nawet jeśli wcześniej nie korzystano z platformy Azure. Zalecane minimalne wymagania dotyczące maszyny wirtualnej serwera: Standard_A4_v2 z czterema rdzeniami i 8 GB pamięci RAM.

Ochrona obciążeń za pomocą Azure Backup Server ma wiele wszystkie szczegóły. [Macierz ochrony dla programu serwera usługi MAB](./backup-mabs-protection-matrix.md) pomaga wyjaśnić te wszystkie szczegóły. Przed wdrożeniem maszyny zapoznaj się z tym artykułem całkowicie.

### <a name="using-an-on-premises-server"></a>Korzystanie z serwera lokalnego

Jeśli nie chcesz uruchamiać serwera podstawowego na platformie Azure, możesz uruchomić serwer programu na maszynie wirtualnej funkcji Hyper-V, maszynie wirtualnej VMware lub hoście fizycznym. Zalecane minimalne wymagania dotyczące sprzętu serwerowego są dwa rdzenie i 8 GB pamięci RAM. Obsługiwane systemy operacyjne są wymienione w poniższej tabeli:

| System operacyjny | Platforma | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64-bitowa |Standard, Datacenter, Essentials |
| Windows Server 2016 i najnowsze dodatki Service Pack |64-bitowa |Standard, Datacenter, Essentials  |

Magazyn programu DPM można deduplikowany przy użyciu funkcji deduplikacji systemu Windows Server. Dowiedz się więcej na temat tego [, jak program DPM i Deduplikacja](/system-center/dpm/deduplicate-dpm-storage) współpracują ze sobą w przypadku wdrożenia na maszynach wirtualnych funkcji Hyper

> [!NOTE]
> Azure Backup Server jest przeznaczony do działania na dedykowanym, wyznaczonym do tego celu serwerze. Nie można zainstalować Azure Backup Server na:
>
> * Komputer działa jako kontroler domeny
> * Komputer, na którym jest zainstalowana rola serwera aplikacji
> * Komputer, na którym jest System Center Operations Manager serwer zarządzania
> * Komputer, na którym jest uruchomiony program Exchange Server
> * Komputer, który jest węzłem klastra
>
> Instalowanie Azure Backup Server nie jest obsługiwane w systemie Windows Server Core lub Microsoft Hyper-V Server.

Zawsze dołączaj Azure Backup Server do domeny. Jeśli planujesz przeniesienie serwera do innej domeny, najpierw zainstaluj Azure Backup Server a następnie Przyłącz serwer do nowej domeny. Przeniesienie istniejącej maszyny Azure Backup Server do nowej domeny po wdrożeniu nie jest *obsługiwane*.

Niezależnie od tego, czy dane kopii zapasowej są wysyłane do platformy Azure, czy przechowywane lokalnie, Azure Backup Server należy zarejestrować w magazynie Recovery Services.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Ustaw replikację magazynu

Dla opcji replikacji magazynu można wybrać magazynowanie nadmiarowe geograficznie lub lokalnie. Domyślnie magazyny Recovery Services korzystają z magazynu geograficznie nadmiarowego. Jeśli magazyn jest magazynem podstawowym, pozostaw opcję magazynu ustawioną na magazyn Geograficznie nadmiarowy. Wybierz magazyn lokalnie nadmiarowy, jeśli chcesz skorzystać z tańszej, ale mniej trwałej opcji. Więcej informacji na temat opcji magazynu [geograficznie](../storage/common/storage-redundancy.md#geo-redundant-storage)nadmiarowego, [lokalnie nadmiarowego](../storage/common/storage-redundancy.md#locally-redundant-storage)i [strefowo nadmiarowe](../storage/common/storage-redundancy.md#zone-redundant-storage) można znaleźć w artykule [Omówienie replikacji usługi Azure Storage](../storage/common/storage-redundancy.md).

Aby edytować ustawienia replikacji magazynu:

1. W okienku **Recovery Services magazynów** wybierz nowy magazyn. W sekcji **Ustawienia** wybierz pozycję  **Właściwości**.
2. W obszarze **Właściwości** w obszarze **Konfiguracja kopii zapasowej** wybierz pozycję **Aktualizuj**.

3. Wybierz typ replikacji magazynu i wybierz pozycję **Zapisz**.

     ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Pakiet oprogramowania

### <a name="downloading-the-software-package"></a>Pobieranie pakietu oprogramowania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Jeśli masz już otwarty magazyn Recovery Services, przejdź do kroku 3. Jeśli nie masz otwartego magazynu Recovery Services, ale znajdują się one w Azure Portal, w menu głównym wybierz pozycję **Przeglądaj**.

   * Na liście zasobów wpisz **Usługi odzyskiwania**.
   * Po rozpoczęciu pisania zawartość listy będzie filtrowana w oparciu o wpisywane dane. Gdy widzisz **Recovery Services magazynów**, wybierz ją.

     ![Utwórz magazyn Recovery Services krok 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Zostanie wyświetlona lista magazynów Usług odzyskiwania.
   * Wybierz magazyn z listy magazynów Usług odzyskiwania.

     Zostanie otwarty pulpit nawigacyjny wybranego magazynu.

     ![Pulpit nawigacyjny magazynu](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Domyślnie zostanie otwarte okienko **ustawień** . Jeśli jest zamknięty, wybierz pozycję **Ustawienia** , aby otworzyć okienko ustawienia.

    ![Okienko ustawień](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Wybierz pozycję **kopia zapasowa** , aby otworzyć Kreatora wprowadzenie.

    ![Wprowadzenie do kopii zapasowej](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    W otwartym okienku **wprowadzenie z kopią zapasową** **cele tworzenia kopii zapasowych** będą wybierane.

    ![Tworzenie kopii zapasowych — cele domyślne — otwierane](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. W okienku **cel kopii zapasowej** w menu **gdzie jest uruchomione Twoje obciążenie** wybierz pozycję **lokalnie**.

    ![lokalne i obciążenia jako cele](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Z menu rozwijanego, **do którego chcesz utworzyć kopię zapasową?** wybierz obciążenia, które mają być chronione przy użyciu Azure Backup Server, a następnie wybierz przycisk **OK**.

    **Wprowadzenie Kreatora tworzenia kopii zapasowych** przełącza opcję **Przygotuj infrastrukturę** , aby utworzyć kopię zapasową obciążeń na platformie Azure.

   > [!NOTE]
   > Jeśli chcesz tylko tworzyć kopie zapasowe plików i folderów, zalecamy użycie agenta Azure Backup i postępując zgodnie ze wskazówkami zawartymi w artykule, [najpierw Zobacz: Tworzenie kopii zapasowych plików i folderów](./backup-windows-with-mars-agent.md). Jeśli zamierzasz chronić więcej niż pliki i foldery lub planujesz rozszerzyć potrzeby ochrony w przyszłości, wybierz te obciążenia.
   >
   >

    ![Zmiana kreatora Wprowadzenie](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. W otwartym okienku **Przygotuj infrastrukturę** wybierz linki do **pobrania** dla instalacji Azure Backup Server i Pobierz poświadczenia magazynu. Poświadczenia magazynu są używane podczas rejestrowania Azure Backup Server w magazynie Recovery Services. Linki prowadzą do centrum pobierania, w którym można pobrać pakiet oprogramowania.

    ![Przygotuj infrastrukturę dla Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Zaznacz wszystkie pliki i wybierz pozycję **dalej**. Pobierz wszystkie pliki przychodzące ze strony pobierania Microsoft Azure Backup i umieść wszystkie pliki w tym samym folderze.

    ![Centrum pobierania 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Ze względu na to, że pobieranie wszystkich plików jest > 3 GB, w łącznym rozmiarze 10 MB/s może upłynąć do 60 minut.

### <a name="extracting-the-software-package"></a>Wyodrębnianie pakietu oprogramowania

Po pobraniu wszystkich plików wybierz pozycję **MicrosoftAzureBackupInstaller.exe**. Spowoduje to uruchomienie **Kreatora instalacji Microsoft Azure Backup** w celu wyodrębnienia plików instalacyjnych do lokalizacji określonej przez użytkownika. Kontynuuj pracę kreatora i wybierz przycisk **Wyodrębnij** , aby rozpocząć proces wyodrębniania.

> [!WARNING]
> Do wyodrębnienia plików instalacyjnych wymagane jest co najmniej 4 GB wolnego miejsca.
>
>

![Instalator wyodrębniania plików do zainstalowania](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Po zakończeniu procesu wyodrębniania zaznacz pole wyboru w celu uruchomienia świeżo wyodrębnionego *setup.exe* , aby rozpocząć instalowanie serwera Microsoft Azure Backup i wybierz przycisk **Zakończ** .

### <a name="installing-the-software-package"></a>Instalowanie pakietu oprogramowania

1. Wybierz **Microsoft Azure Backup** , aby uruchomić Kreatora instalacji.

    ![Kreator instalacji Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Na ekranie powitalnym wybierz przycisk **dalej** . Spowoduje to przejście do sekcji *Sprawdzanie wymagań wstępnych* . Na tym ekranie wybierz pozycję **Sprawdź** , aby określić, czy zostały spełnione wymagania wstępne dotyczące sprzętu i oprogramowania Azure Backup Server. Jeśli wszystkie wymagania wstępne zostaną spełnione, zobaczysz komunikat informujący o tym, że komputer spełnia wymagania. Kliknij przycisk **Next** (Dalej).

    ![Azure Backup Server-Witamy i sprawdzanie wymagań wstępnych](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Pakiet instalacyjny Azure Backup Server jest powiązany z odpowiednimi SQL Server plikami binarnymi. Podczas uruchamiania nowej instalacji Azure Backup Server wybierz opcję **Zainstaluj nowe wystąpienie SQL Server z tą konfiguracją** i wybierz przycisk **Sprawdź i zainstaluj** . Po pomyślnym zainstalowaniu wymagań wstępnych wybierz pozycję **dalej**.

    >[!NOTE]
    >Jeśli chcesz użyć własnego programu SQL Server, obsługiwane wersje SQL Server są SQL Server 2014 SP1 lub nowsze, 2016 i 2017.  Wszystkie SQL Server wersje powinny mieć wersję Standard lub Enterprise 64-bit.
    >Azure Backup Server nie będzie działał z wystąpieniem SQL Server zdalnego. Wystąpienie używane przez Azure Backup Server musi być lokalne. Jeśli używasz istniejącego programu SQL Server dla programu serwera usługi MAB, Instalator serwera usługi MAB obsługuje tylko *nazwane wystąpienia* programu SQL Server.

    ![Sprawdzanie Azure Backup Server — SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Jeśli wystąpi błąd z zaleceniem ponownego uruchomienia maszyny, zrób to, a następnie wybierz pozycję **Sprawdź ponownie**. Jeśli występują problemy z konfiguracją SQL, skonfiguruj ponownie SQL zgodnie z wytycznymi SQL i spróbuj ponownie zainstalować/uaktualnić serwera usługi MAB przy użyciu istniejącego wystąpienia programu SQL Server.

   **Konfiguracja ręczna**

   Korzystając z własnego wystąpienia programu SQL Server, należy się upewnić, że dodano Builtin\administratorzy do roli sysadmin do bazy danych Master.

    **Konfiguracja usługi SSRS w programie SQL 2017**

    W przypadku korzystania z własnego wystąpienia programu SQL 2017 należy ręcznie skonfigurować usługi SSRS. Po skonfigurowaniu usług SSRS upewnij się, że właściwość *IsInitialized* usług SSRS ma *wartość true*. Gdy ta wartość jest równa true, serwera usługi MAB zakłada, że usługa SSRS jest już skonfigurowana i pominie konfigurację usług SSRS.

    W przypadku konfiguracji usług SSRS należy użyć następujących wartości:
    * Konto usługi: "Użyj wbudowanego konta" powinno być usługą sieciową
    * Adres URL usługi sieci Web: "katalog wirtualny" powinien być ReportServer_\<SQLInstanceName>
    * Baza danych: DatabaseName powinna być ReportServer $\<SQLInstanceName>
    * Adres URL portalu sieci Web: "katalog wirtualny" powinien być Reports_\<SQLInstanceName>

    [Dowiedz się więcej](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) o konfiguracji usług SSRS.

    > [!NOTE]
    > Licencjonowanie SQL Server używane jako baza danych dla serwera usługi MAB podlega [warunkom usług online firmy Microsoft](https://www.microsoft.com/licensing/product-licensing/products) (ost). Zgodnie z elementem OST SQL Server powiązany z serwera usługi MAB może być używany tylko jako baza danych serwera usługi MAB.

4. Podaj lokalizację instalacji plików serwera Microsoft Azure Backup i wybierz pozycję **dalej**.

    ![Podaj lokalizację instalacji plików](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Lokalizacja tymczasowa jest wymagana do utworzenia kopii zapasowej na platformie Azure. Upewnij się, że lokalizacja tymczasowa to co najmniej 5% danych, których kopię zapasową zaplanowano do chmury. W przypadku ochrony dysków należy skonfigurować oddzielne dyski po zakończeniu instalacji. Aby uzyskać więcej informacji na temat pul magazynów, zobacz [Przygotowywanie magazynu danych](/system-center/dpm/plan-long-and-short-term-data-storage).

    Wymagania dotyczące pojemności dla magazynu dyskowego są zależne przede wszystkim od rozmiaru chronionych danych, codziennego rozmiaru punktu odzyskiwania, oczekiwanego tempa wzrostu danych woluminu oraz docelowego zakresu przechowywania. Zalecamy, aby magazyn dyskowy był dwukrotnie większy od chronionych danych. Wynika to z zakładanego codziennego rozmiaru punktu odzyskiwania na poziomie 10% rozmiaru chronionych danych oraz zakresu przechowywania równego 10 dni. Aby uzyskać dobry szacunek rozmiaru, przejrzyj [planista wydajności programu DPM](https://www.microsoft.com/download/details.aspx?id=54301). 

5. Podaj silne hasło dla kont użytkowników lokalnych z ograniczeniami i wybierz pozycję **dalej**.

    ![Podaj silne hasło](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Wybierz, czy chcesz użyć *Microsoft Update* , aby sprawdzić dostępność aktualizacji, a następnie wybierz przycisk **dalej**.

   > [!NOTE]
   > Zalecamy, aby Windows Update przekierować do Microsoft Update, który oferuje bezpieczeństwo i ważne aktualizacje systemu Windows oraz innych produktów, takich jak Microsoft Azure Backup Server.
   >
   >

    ![Microsoft Update Opt-In](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Przejrzyj *Podsumowanie ustawień* i wybierz pozycję **Zainstaluj**.

    ![Podsumowanie ustawień](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Instalacja odbywa się w fazach. W pierwszej fazie agent Microsoft Azure Recovery Services jest zainstalowany na serwerze. Kreator sprawdza również połączenie z Internetem. Jeśli dostępna jest łączność z Internetem, można kontynuować instalację. W przeciwnym razie musisz podać szczegóły serwera proxy, aby połączyć się z Internetem.

    Następnym krokiem jest skonfigurowanie agenta Microsoft Azure Recovery Services. W ramach konfiguracji należy podać poświadczenia magazynu, aby zarejestrować maszynę w magazynie Recovery Services. Należy również podać hasło do szyfrowania/odszyfrowywania danych przesyłanych między platformą Azure i lokalnymi. Możesz automatycznie generować hasło lub podać własne, 16-znakowe hasło. Kontynuuj pracę z kreatorem, dopóki agent nie zostanie skonfigurowany.

    ![Zarejestruj kreatora serwera](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Po pomyślnym zakończeniu rejestracji serwera Microsoft Azure Backup, Kreator instalacji ogólnej przejdzie do instalacji i konfiguracji SQL Server i składników Azure Backup Server. Po zakończeniu instalacji składnika SQL Server składniki Azure Backup Server zostaną zainstalowane.

    ![Azure Backup Server postęp instalacji](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Po zakończeniu kroku instalacji zostaną również utworzone ikony pulpitu produktu. Kliknij dwukrotnie ikonę, aby uruchomić produkt.

### <a name="add-backup-storage"></a>Dodawanie magazynu kopii zapasowych

Pierwsza kopia zapasowa jest przechowywana w magazynie dołączonym do maszyny Azure Backup Server. Aby uzyskać więcej informacji na temat dodawania dysków, zobacz [Konfigurowanie pul magazynów i magazynu dyskowego](./backup-mabs-add-storage.md).

> [!NOTE]
> Należy dodać magazyn kopii zapasowych, nawet jeśli planujesz wysyłać dane na platformę Azure. W bieżącej architekturze Azure Backup Server Magazyn Azure Backup przechowuje *drugą* kopię danych, podczas gdy magazyn lokalny przechowuje pierwszą (i obowiązkową) kopię zapasową.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Zainstaluj i zaktualizuj agenta ochrony Data Protection Manager

SERWERA usługi MAB używa agenta ochrony programu System Center Data Protection Manager. [Poniżej przedstawiono procedurę](/system-center/dpm/deploy-dpm-protection-agent) instalowania agenta ochrony programu na serwerach ochrony.

W poniższych sekcjach opisano sposób aktualizowania agentów ochrony dla komputerów klienckich.

1. W Konsola administratora serwera kopii zapasowej wybierz pozycję  >  **agenci** zarządzania.

2. W okienku wyświetlania wybierz komputery klienckie, dla których chcesz zaktualizować agenta ochrony.

   > [!NOTE]
   > Kolumna **aktualizacje agenta** wskazuje, kiedy dla każdego komputera chronionego jest dostępna aktualizacja agenta ochrony. W okienku **Akcje** Akcja **Aktualizuj** jest dostępna tylko po wybraniu komputera chronionego i udostępnieniu aktualizacji.
   >
   >

3. Aby zainstalować zaktualizowanych agentów ochrony na wybranych komputerach, w okienku **Akcje** wybierz pozycję **Aktualizuj**.

4. W przypadku komputera klienckiego, który nie jest połączony z siecią, dopóki komputer nie jest podłączony do sieci, w kolumnie **stan agenta** zostanie wyświetlony stan **oczekujące aktualizacje**.

   Gdy komputer kliencki jest połączony z siecią, w kolumnie **aktualizacje agenta** dla komputera klienckiego zostanie wyświetlony stan **Aktualizacja**.

## <a name="move-mabs-to-a-new-server"></a>Przenieś serwera usługi MAB na nowy serwer

Poniżej przedstawiono kroki, które należy wykonać, jeśli chcesz przenieść serwera usługi MAB do nowego serwera, zachowując magazyn. Można to zrobić tylko wtedy, gdy wszystkie dane są na Nowoczesny magazyn kopii zapasowych.

  > [!IMPORTANT]
  >
  > * Nazwa nowego serwera musi mieć taką samą nazwę jak oryginalne wystąpienie Azure Backup Server. Nie można zmienić nazwy nowego wystąpienia Azure Backup Server, jeśli chcesz użyć poprzedniej puli magazynów i bazy danych serwera usługi MAB (DPMDB), aby zachować punkty odzyskiwania.
  > * Musisz mieć kopię zapasową bazy danych serwera usługi MAB (DPMDB). Będzie on potrzebny do przywrócenia bazy danych.

1. W okienku wyświetlania wybierz komputery klienckie, dla których chcesz zaktualizować agenta ochrony.
2. Zamknij oryginalny serwer Azure Backup lub Przełącz go w tryb offline.
3. Zresetuj konto komputera w Active Directory.
4. Zainstaluj serwer 2016 na nowym komputerze i nadaj mu taką samą nazwę komputera, jak oryginalny serwer Azure Backup.
5. Dołącz do domeny.
6. Zainstaluj Azure Backup Server wersja 3 lub nowszą (Przenieś dyski puli magazynu serwera usługi MAB ze starego serwera i zaimportować).
7. Przywróć DPMDB wykonaną w kroku 1.
8. Dołącz magazyn z oryginalnego serwera kopii zapasowej do nowego serwera.
9. W programie SQL Przywróć DPMDB.
10. Uruchom polecenie CMD (jako administrator) na nowym serwerze. Przejdź do lokalizacji instalacji Microsoft Azure Backup i folderu bin

    Przykład ścieżki: `C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"`

11. Aby nawiązać połączenie z Azure Backup, uruchom polecenie `DPMSYNC -SYNC`

    Jeśli **nowe** dyski zostały dodane do puli magazynów programu DPM, a nie przeniesione ze starych, uruchom polecenie `DPMSYNC -Reallocatereplica` .

## <a name="network-connectivity"></a>Łączność sieciowa

Azure Backup Server wymaga łączności z usługą Azure Backup, aby produkt działał pomyślnie. Aby sprawdzić, czy komputer ma łączność z platformą Azure, użyj ```Get-DPMCloudConnection``` polecenia cmdlet w konsoli programu PowerShell w Azure Backup Server. Jeśli dane wyjściowe polecenia cmdlet mają wartość TRUE, połączenie istnieje, w przeciwnym razie nie ma łączności.

W tym samym czasie subskrypcja platformy Azure musi być w dobrej kondycji. Aby sprawdzić stan subskrypcji i zarządzać nią, zaloguj się do [portalu subskrypcji](https://account.windowsazure.com/Subscriptions).

Po uzyskaniu informacji o stanie łączności z platformą Azure i subskrypcji platformy Azure Możesz użyć poniższej tabeli, aby dowiedzieć się, jak to miało wpływ na oferowane funkcje tworzenia kopii zapasowej/przywracania.

| Stan łączności | Subskrypcja platformy Azure | Tworzenie kopii zapasowej w systemie Azure | Utwórz kopię zapasową na dysku | Przywróć z platformy Azure | Przywracanie z dysku |
| --- | --- | --- | --- | --- | --- |
| Połączone |Aktywna |Dozwolone |Dozwolone |Dozwolone |Dozwolone |
| Połączone |Wygasłe |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Połączone |Anulowanie aprowizacji |Zatrzymano |Zatrzymano |Zatrzymane i usunięte punkty odzyskiwania platformy Azure |Zatrzymano |
| Utracono łączność > 15 dni |Aktywna |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Utracono łączność > 15 dni |Wygasłe |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Utracono łączność > 15 dni |Anulowanie aprowizacji |Zatrzymano |Zatrzymano |Zatrzymane i usunięte punkty odzyskiwania platformy Azure |Zatrzymano |

### <a name="recovering-from-loss-of-connectivity"></a>Odzyskiwanie po utracie łączności

Jeśli maszyna ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na komputerze lub serwerze proxy zezwalają na następujące adresy URL i adresy IP:

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

Jeśli używasz komunikacji równorzędnej firmy Microsoft ExpressRoute, wybierz następujące usługi/regiony:

* Azure Active Directory (12076:5060)
* Region Microsoft Azure (zgodnie z lokalizacją magazynu Recovery Services)
* Azure Storage (zgodnie z lokalizacją magazynu Recovery Services)

Aby uzyskać więcej informacji, odwiedź stronę [wymagania dotyczące routingu ExpressRoute](../expressroute/expressroute-routing.md).

Po przywróceniu łączności z platformą Azure do maszyny Azure Backup Server operacje, które można wykonać, są określane przez stan subskrypcji platformy Azure. Powyższa tabela zawiera szczegółowe informacje o operacjach dozwolonych, gdy maszyna jest "połączona".

### <a name="handling-subscription-states"></a>Obsługa stanów subskrypcji

Istnieje możliwość podjęcia subskrypcji platformy Azure ze stanu *wygasłego* lub *wstrzymanego* w stanie *aktywnym* . Jednak ma to wpływ na zachowanie produktu, gdy stan nie jest *aktywny*:

* Cofnięcie *aprowizacji subskrypcji powoduje* utratę funkcjonalności dla tego okresu. W przypadku włączania *aktywności* funkcja tworzenia kopii zapasowej/przywracania jest przywracana. Dane kopii zapasowej na dysku lokalnym można również pobrać, jeśli były utrzymywane w wystarczająco dużym okresie przechowywania. Jednak dane kopii zapasowej na platformie Azure są irretrievably utracone po przejściu subskrypcji w stan *anulowania* aprowizacji.
* *Wygasła* subskrypcja powoduje utratę funkcjonalności, dopóki nie zostanie ponownie *uaktywniona* . Wszystkie kopie zapasowe zaplanowane na okres *ważności* subskrypcji nie będą działać.

## <a name="upgrade-mabs"></a>SERWERA usługi MAB uaktualnienia

Użyj poniższych procedur, aby uaktualnić serwera usługi MAB.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Uaktualnienie z serwera usługi MAB v2 do wersji v3

> [!NOTE]
>
> SERWERA usługi MAB v2 nie jest wymaganiem wstępnym instalacji serwera usługi MAB v3. Można jednak uaktualnić do serwera usługi MAB v3 tylko z serwera usługi MAB v2.

Wykonaj następujące kroki, aby uaktualnić program serwera usługi MAB:

1. W razie potrzeby Uaktualnij system 2019 2016 operacyjny z wersji serwera usługi MAB v2 do serwera usługi MAB v3.

2. Uaktualnij serwer. Kroki są podobne do [instalacji](#install-and-upgrade-azure-backup-server). Jednak w przypadku ustawień SQL uzyskasz opcję uaktualnienia wystąpienia bazy danych SQL do wersji SQL 2017 lub do użycia własnego wystąpienia programu SQL Server 2017.

   > [!NOTE]
   >
   > Nie zamykaj, gdy wystąpienie programu SQL jest uaktualniane. Zakończenie spowoduje odinstalowanie wystąpienia programu SQL Reporting, dlatego próba ponownego uaktualnienia serwera usługi MAB zakończy się niepowodzeniem.

   > [!IMPORTANT]
   >
   >  W ramach uaktualnienia programu SQL 2017 należy utworzyć kopię zapasową kluczy szyfrowania SQL i odinstalować usługi Reporting Services. Po uaktualnieniu programu SQL Server usługa raportowania (14.0.6827.4788) jest zainstalowana & klucze szyfrowania są przywracane.
   >
   > Podczas ręcznego konfigurowania programu SQL 2017 zapoznaj się z sekcją *Konfiguracja usług SSRS i SQL 2017* w obszarze instrukcje instalacji.

3. Zaktualizuj agentów ochrony na serwerach chronionych.
4. Kopie zapasowe powinny być kontynuowane bez konieczności ponownego uruchamiania serwerów produkcyjnych.
5. Teraz możesz zacząć chronić dane. W przypadku uaktualniania do Nowoczesny magazyn kopii zapasowych, podczas ochrony, można również wybrać woluminy, w których mają być przechowywane kopie zapasowe, oraz sprawdzić w obszarze zainicjowane miejsce. [Dowiedz się więcej](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli w trakcie fazy instalacji (lub tworzenia kopii zapasowej lub przywracania) wystąpi błąd programu Microsoft Azure Backup Server, zapoznaj się z tym [dokumentem kodów błędów](https://support.microsoft.com/kb/3041338)  , aby uzyskać więcej informacji.
Możesz również odwoływać się do [Azure Backup powiązanych często zadawanych pytań](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Następne kroki

W tym miejscu możesz uzyskać szczegółowe informacje [na temat przygotowywania środowiska programu DPM](/system-center/dpm/prepare-environment-for-dpm). Zawiera również informacje o obsługiwanych konfiguracjach, w których Azure Backup Server można wdrożyć i użyć. Możesz użyć szeregu [poleceń cmdlet programu PowerShell](/powershell/module/dataprotectionmanager/) do wykonywania różnych operacji.

Te artykuły umożliwiają dokładniejsze zrozumienie ochrony obciążeń przy użyciu serwera Microsoft Azure Backup.

* [SQL Server kopia zapasowa](backup-azure-backup-sql.md)
* [Kopia zapasowa programu SharePoint Server](backup-azure-backup-sharepoint.md)
* [Alternatywna kopia zapasowa serwera](backup-azure-alternate-dpm-server.md)
