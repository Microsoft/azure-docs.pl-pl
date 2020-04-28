---
title: Instalowanie składnika Azure Backup Server w usłudze Azure Stack
description: W tym artykule dowiesz się, jak chronić lub wykonać kopię zapasową obciążeń w usłudze Azure Stack za pomocą usługi Azure Backup Server.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: b78e5a662bdcf23ad38cb33292658d4d2455e579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583439"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Instalowanie składnika Azure Backup Server w usłudze Azure Stack

W tym artykule wyjaśniono, jak zainstalować usługę Azure Backup Server w usłudze Azure Stack. Za pomocą usługi Azure Backup Server można chronić obciążenia infrastruktury jako usługi (IaaS), takie jak maszyny wirtualne uruchomione w usłudze Azure Stack. Zaletą korzystania z usługi Azure Backup Server w celu ochrony obciążeń jest zarządzanie całą ochroną obciążenia z jednej konsoli.

> [!NOTE]
> Aby dowiedzieć się więcej o funkcjach zabezpieczeń, zapoznaj się z [dokumentacją funkcji zabezpieczeń usługi Azure Backup](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Macierz ochrony usługi Azure Backup Server

Usługa Azure Backup Server chroni następujące obciążenia maszyn wirtualnych usługi Azure Stack.

| Chronione źródło danych | Ochrona i odzyskiwanie |
| --------------------- | ----------------------- |
| Półroczny kanał systemu Windows Server — centrum danych/przedsiębiorstwo/standard | Woluminy, pliki, foldery |
| Windows Server 2016 — centrum danych/przedsiębiorstwo/standard | Woluminy, pliki, foldery |
| Windows Server 2012 R2 — centrum danych/przedsiębiorstwo/standard | Woluminy, pliki, foldery |
| Windows Server 2012 — centrum danych/przedsiębiorstwo/standard | Woluminy, pliki, foldery |
| Windows Server 2008 R2 — centrum danych/przedsiębiorstwo/standard | Woluminy, pliki, foldery |
| SQL Server 2016 | baza danych |
| SQL Server 2014 | baza danych |
| SQL Server 2012 SP1 | baza danych |
| SharePoint 2016 | Farma, baza danych, frontend, serwer www |
| SharePoint 2013 | Farma, baza danych, frontend, serwer www |
| SharePoint 2010 | Farma, baza danych, frontend, serwer www |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Wymagania wstępne dla środowiska serwera kopii zapasowych platformy Azure

Należy wziąć pod uwagę zalecenia zawarte w tej sekcji podczas instalowania usługi Azure Backup Server w środowisku usługi Azure Stack. Instalator serwera kopii zapasowej platformy Azure sprawdza, czy w środowisku są wymagane wymagania wstępne, ale zaoszczędzisz czas, przygotowując się przed zainstalowaniem.

### <a name="determining-size-of-virtual-machine"></a>Określanie rozmiaru maszyny wirtualnej

Aby uruchomić usługę Azure Backup Server na maszynie wirtualnej usługi Azure Stack, należy użyć rozmiaru A2 lub większego. Aby uzyskać pomoc dotyczącą wyboru rozmiaru maszyny wirtualnej, pobierz [kalkulator rozmiaru maszyny wirtualnej usługi Azure Stack](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Sieci wirtualne na maszynach wirtualnych usługi Azure Stack

Wszystkie maszyny wirtualne używane w obciążeniu usługi Azure Stack muszą należeć do tej samej sieci wirtualnej platformy Azure i subskrypcji platformy Azure.

### <a name="azure-backup-server-vm-performance"></a>Wydajność maszyny Wirtualnej serwera kopii zapasowych platformy Azure

Jeśli są współużytkowane z innymi maszynami wirtualnymi, rozmiar konta magazynu i limity we/wy mają wpływ na wydajność maszyny wirtualnej usługi Azure Backup Server. Z tego powodu należy użyć osobnego konta magazynu dla maszyny wirtualnej usługi Azure Backup Server. Agent kopii zapasowej platformy Azure uruchomiony na serwerze kopii zapasowej platformy Azure potrzebuje magazynu tymczasowego dla:

- własnego użytku (lokalizacja pamięci podręcznej),
- dane przywrócone z chmury (lokalny obszar przemieszczania)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurowanie tymczasowego magazynu dysku usługi Azure Backup

Każda maszyna wirtualna usługi Azure Stack jest wyposażona w `D:\`magazyn dysku tymczasowego, który jest dostępny dla użytkownika jako wolumin. Lokalny obszar przemieszczania potrzebny usługi Azure Backup można `D:\`skonfigurować do przebywania `C:\`w programie , a lokalizację pamięci podręcznej można umieścić na . W ten sposób nie magazynu musi być wyrzeźbione z dala od dysków danych dołączonych do maszyny wirtualnej serwera kopii zapasowych platformy Azure.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Przechowywanie danych kopii zapasowej na dysku lokalnym i na platformie Azure

Usługa Azure Backup Server przechowuje dane kopii zapasowych na dyskach platformy Azure dołączonych do maszyny wirtualnej w celu odzyskiwania operacyjnego. Gdy dyski i miejsce do magazynowania są podłączone do maszyny wirtualnej, usługa Azure Backup Server zarządza magazynem. Ilość magazynu danych kopii zapasowej zależy od liczby i rozmiaru dysków dołączonych do każdej [maszyny wirtualnej usługi Azure Stack.](/azure-stack/user/azure-stack-storage-overview) Każdy rozmiar maszyny wirtualnej usługi Azure Stack ma maksymalną liczbę dysków, które mogą być dołączone do maszyny wirtualnej. Na przykład A2 to cztery dyski. A3 to osiem dysków. A4 to 16 dysków. Ponownie rozmiar i liczba dysków określa całkowitą pulę magazynu kopii zapasowych.

> [!IMPORTANT]
> Nie **należy** przechowywać danych odzyskiwania operacyjnego (kopii zapasowej) na dyskach dołączonych do serwera kopii zapasowych platformy Azure przez okres dłużej niż pięć dni.
>

Przechowywanie danych kopii zapasowej na platformie Azure zmniejsza infrastrukturę tworzenia kopii zapasowych w usłudze Azure Stack. Jeśli dane mają więcej niż pięć dni, powinny być przechowywane na platformie Azure.

Aby przechowywać dane kopii zapasowej na platformie Azure, utwórz lub użyj magazynu usług odzyskiwania. Przygotowując się do utworzenia kopii zapasowej obciążenia serwera kopii zapasowych platformy Azure, [należy skonfigurować magazyn usług odzyskiwania](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Po skonfigurowaniu, za każdym razem, gdy zadanie tworzenia kopii zapasowej jest uruchamiane, punkt odzyskiwania jest tworzony w przechowalni. Każdy magazyn usług odzyskiwania posiada do 9999 punktów odzyskiwania. W zależności od liczby utworzonych punktów odzyskiwania i czasu ich przechowywania można zachować dane kopii zapasowej przez wiele lat. Na przykład można utworzyć miesięczne punkty odzyskiwania i zachować je przez pięć lat.

### <a name="scaling-deployment"></a>Skalowanie wdrożenia

Jeśli chcesz skalować wdrożenie, masz następujące opcje:

- Skalowanie w górę — zwiększ rozmiar maszyny wirtualnej usługi Azure Backup Server z serii A do D i zwiększ magazyn lokalny [zgodnie z instrukcjami maszyny wirtualnej usługi Azure Stack.](/azure-stack/user/azure-stack-manage-vm-disks)
- Odciążanie danych — wysyłaj starsze dane na platformę Azure i przechowuj tylko najnowsze dane w magazynie dołączonym do serwera kopii zapasowych platformy Azure.
- Skaluj w poziomie — dodaj więcej serwerów kopii zapasowych platformy Azure, aby chronić obciążenia.

### <a name="net-framework"></a>.NET Framework

.NET Framework 3.5 SP1 lub nowsza musi być zainstalowana na maszynie wirtualnej.

### <a name="joining-a-domain"></a>Dołączanie do domeny

Maszyna wirtualna serwera kopii zapasowych platformy Azure musi być przyłączona do domeny. Użytkownik domeny z uprawnieniami administratora musi zainstalować usługę Azure Backup Server na maszynie wirtualnej.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Korzystanie z maszyny Wirtualnej IaaS w usłudze Azure Stack

Wybierając serwer dla serwera Azure Backup Server, należy rozpocząć od obrazu galerii centrum danych systemu Windows Server 2012 R2 lub centrum danych systemu Windows Server 2016. W tym artykule [Utwórz pierwszą maszynę wirtualną systemu Windows w portalu Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), zawiera samouczek, aby rozpocząć pracę z zalecaną maszyną wirtualną. Zalecane minimalne wymagania dla maszyny wirtualnej serwera (VM) powinny być: A2 Standard z dwoma rdzeniami i 3,5 GB pamięci RAM.

Ochrona obciążeń za pomocą usługi Azure Backup Server ma wiele niuansów. Artykuł [Zainstaluj program DPM jako maszynę wirtualną platformy Azure,](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12))pomaga wyjaśnić te niuanse. Przed wdrożeniem komputera przeczytaj ten artykuł całkowicie.

> [!NOTE]
> Usługa Azure Backup Server jest przeznaczona do uruchamiania na dedykowanej, jednoelkątej maszynie wirtualnej. Nie można zainstalować usługi Azure Backup Server na:
>
> - Komputer działa jako kontroler domeny
> - Komputer, na którym jest zainstalowana rola serwera aplikacji
> - Komputer, na którym jest uruchomiony program Exchange Server
> - Komputer, który jest węzłem klastra

Zawsze dołącz do usługi Azure Backup Server do domeny. Jeśli musisz przenieść usługę Azure Backup Server do innej domeny, najpierw zainstaluj usługę Azure Backup Server, a następnie dołącz do nowej domeny. Po wdrożeniu serwera kopii zapasowej platformy Azure nie można przenieść go do nowej domeny.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurowanie replikacji magazynu

The Recovery Services vault storage replication option allows you to choose between geo-redundant storage and locally redundant storage. Domyślnie magazyny usług odzyskiwania używają magazynu geograficznie nadmiarowego. Jeśli ten magazyn jest magazynem podstawowym, pozostaw opcję magazynu ustawioną na magazyn geograficznie nadmiarowy. Wybierz lokalnie nadmiarową pamięć masową, jeśli chcesz tańszą opcję, która jest mniej trwała. Dowiedz się więcej o opcjach magazynu [geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md) i [lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) w [omówieniu replikacji usługi Azure Storage](../storage/common/storage-redundancy.md).

Aby edytować ustawienia replikacji magazynu:

1. Wybierz przechowalnię, aby otworzyć pulpit nawigacyjny przechowalni i menu Ustawienia. Jeśli menu **Ustawienia** nie zostanie otwarte, kliknij pozycję **Wszystkie ustawienia** na pulpicie nawigacyjnym przechowalni.
2. W menu **Ustawienia** kliknij polecenie**Konfiguracja kopii zapasowej** infrastruktury >  **kopii zapasowych,** aby otworzyć menu **Konfiguracja kopii zapasowej.** W menu **Konfiguracja kopii zapasowej** wybierz opcję replikacji magazynu dla przechowalni.

    ![Lista magazynów kopii zapasowych](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Pobierz instalator usługi Azure Backup Server

Istnieją dwa sposoby pobrania instalatora serwera kopii zapasowych platformy Azure. Instalator serwera kopii zapasowych platformy Azure można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=55269). Można również pobrać instalatora usługi Azure Backup Server podczas konfigurowania magazynu usług odzyskiwania. Poniższe kroki przebiegają przez pobieranie instalatora z witryny Azure portal podczas konfigurowania magazynu usług odzyskiwania.

1. Z maszyny wirtualnej usługi Azure Stack [zaloguj się do subskrypcji platformy Azure w witrynie Azure portal.](https://portal.azure.com/)
2. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**.

    ![Wybierz opcję Wszystkie usługi w menu głównym](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. W oknie dialogowym **Wszystkie usługi** wpisz polecenie *Usługi odzyskiwania*. Po rozpoczęciu pisania dane wejściowe filtrują listę zasobów. Po jego wyświetleniu wybierz pozycję **Magazyny usług odzyskiwania**.

    ![Wpisz usługi odzyskiwania w oknie dialogowym Wszystkie usługi](./media/backup-mabs-install-azure-stack/all-services.png)

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.

4. Z listy magazynów usług odzyskiwania wybierz magazyn, aby otworzyć jej pulpit nawigacyjny.

    ![Wpisz usługi odzyskiwania w oknie dialogowym Wszystkie usługi](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. W menu Wprowadzenie do przechowalni kliknij polecenie **Kopia zapasowa,** aby otworzyć kreatora Wprowadzenie.

    ![Rozpoczęcie tworzenia kopii zapasowej](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Zostanie otwarte menu kopii zapasowej.

    ![Kopia zapasowa-cele-domyślnie otwarty](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. W menu kopia zapasowa z menu **Gdzie jest uruchomione obciążenie,** wybierz opcję **Lokalne**. Z menu **rozwijanego Co chcesz wykonać kopię zapasową?** wybierz obciążenia, które chcesz chronić za pomocą serwera kopii zapasowej platformy Azure. Jeśli nie masz pewności, które obciążenia wybrać, wybierz pozycję **Maszyny wirtualne funkcji Hyper-V,** a następnie kliknij przycisk **Przygotuj infrastrukturę**.

    ![lokalnie i obciążeń jako cele](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Zostanie otwarte menu **Przygotowywanie infrastruktury.**

7. W menu **Przygotowywanie infrastruktury** kliknij polecenie **Pobierz,** aby otworzyć stronę sieci Web w celu pobrania plików instalacyjnych usługi Azure Backup Server.

    ![Zmiana kreatora wprowadzenie](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Zostanie otwarta strona sieci Web firmy Microsoft zawierająca pliki do pobrania dla serwera kopii zapasowej platformy Azure.

8. Na stronie pobierania serwera kopii zapasowych platformy Microsoft Azure wybierz język i kliknij przycisk **Pobierz**.

    ![Otwiera się centrum pobierania](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Instalator serwera kopii zapasowych platformy Azure składa się z ośmiu plików — instalatora i siedmiu plików .bin. Zaznacz **nazwę pliku,** aby zaznaczyć wszystkie wymagane pliki, a następnie kliknij przycisk **Dalej**. Pobierz wszystkie pliki do tego samego folderu.

    ![Centrum pobierania 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Rozmiar pobierania wszystkich plików instalacyjnych jest większy niż 3 GB. Na łącze do pobierania o rozmiarze 10 Mb/s pobranie wszystkich plików instalacyjnych może potrwać do 60 minut. Pliki są pobierane do określonej lokalizacji pobierania.

## <a name="extract-azure-backup-server-install-files"></a>Wyodrębnianie plików instalacyjnych serwera kopii zapasowych platformy Azure

Po pobraniu wszystkich plików do maszyny wirtualnej usługi Azure Stack przejdź do lokalizacji pobierania. Pierwszą fazą instalacji usługi Azure Backup Server jest wyodrębnienie plików.

![Centrum pobierania 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Aby rozpocząć instalację, z listy pobranych plików kliknij przycisk **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Do wyodrębnienia plików instalacyjnych wymagane jest co najmniej 4 GB wolnego miejsca.
    >

2. W kreatorze serwera kopii zapasowych platformy Azure kliknij przycisk **Dalej,** aby kontynuować.

    ![Kreator instalacji kopii zapasowej platformy Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Wybierz ścieżkę dla plików serwera kopii zapasowych platformy Azure i kliknij przycisk **Dalej**.

   ![Kreator instalacji kopii zapasowej platformy Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Sprawdź lokalizację wyodrębniania i kliknij przycisk **Wyodrębnij**.

   ![Kreator instalacji kopii zapasowej platformy Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Kreator wyodrębnia pliki i odczytuje proces instalacji.

   ![Kreator instalacji kopii zapasowej platformy Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Po zakończeniu procesu ekstrakcji kliknij przycisk **Zakończ**. Domyślnie wybrano **opcję Execute setup.exe.** Po **kliknięciu przycisku Zakończ**program Setup.exe instaluje serwer kopii zapasowej platformy Microsoft Azure w określonej lokalizacji.

   ![Kreator instalacji kopii zapasowej platformy Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Zainstaluj pakiet oprogramowania

W poprzednim kroku kliknięno **przycisk Zakończ,** aby zakończyć fazę wyodrębniania i uruchomić kreatora konfiguracji serwera kopii zapasowych platformy Azure.

![Kreator instalacji kopii zapasowej platformy Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Usługa Azure Backup Server udostępnia kod menedżerowi ochrony danych. Odwołania do Menedżera ochrony danych i programu DPM zostaną wyświetlone w instalatorze serwera kopii zapasowej platformy Azure. Chociaż usługa Azure Backup Server i Data Protection Manager są oddzielnymi produktami, produkty te są ze sobą ściśle powiązane.

1. Aby uruchomić kreatora konfiguracji, kliknij pozycję **Serwer kopii zapasowej platformy Microsoft Azure**.

   ![Kreator instalacji kopii zapasowej platformy Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Na ekranie **Zapraszamy** naciśnij przycisk **Dalej**.

    ![Serwer kopii zapasowych platformy Azure — sprawdzanie powitalnym i wymagań wstępnych](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Na ekranie **Testy wymagań wstępnych** kliknij przycisk **Sprawdź,** aby ustalić, czy zostały spełnione wymagania wstępne dotyczące sprzętu i oprogramowania dla serwera kopii zapasowej platformy Azure.

    ![Serwer kopii zapasowych platformy Azure — sprawdzanie powitalnym i wymagań wstępnych](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Jeśli środowisko ma niezbędne wymagania wstępne, zostanie wyświetlony komunikat informujący, że urządzenie spełnia wymagania. Kliknij przycisk **Dalej**.  

    ![Serwer kopii zapasowej platformy Azure — sprawdzanie wymagań wstępnych nieprzekazano](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Jeśli środowisko nie spełnia niezbędnych wymagań wstępnych, zostaną określone problemy. Wymagania wstępne, które nie zostały spełnione są również wymienione w DpmSetup.log. Rozwiąż błędy wariowania, a następnie uruchom **program Sprawdź ponownie**. Instalacja nie może być kontynuowana, dopóki nie zostaną spełnione wszystkie wymagania wstępne.

    ![Usługa Azure Backup Server — wymagania wstępne instalacji nie zostały spełnione](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Program Microsoft Azure Backup Server wymaga programu SQL Server. Pakiet instalacyjny serwera kopii zapasowych platformy Azure jest dostarczany w pakiecie z odpowiednimi plikami binarnymi programu SQL Server. Jeśli chcesz użyć własnej instalacji SQL, możesz. Jednak zalecanym wyborem jest umożliwienie instalatorowi dodania nowego wystąpienia programu SQL Server. Aby upewnić się, że wybór działa z twoim środowiskiem, kliknij przycisk **Sprawdź i zainstaluj**.

   > [!NOTE]
   > Usługa Azure Backup Server nie będzie działać ze zdalnym wystąpieniem programu SQL Server. Wystąpienie używane przez usługę Azure Backup Server musi być lokalne.
   >

    ![Serwer kopii zapasowych platformy Azure — sprawdzanie powitalnym i wymagań wstępnych](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Po sprawdzeniu, jeśli maszyna wirtualna ma niezbędne wymagania wstępne do zainstalowania usługi Azure Backup Server, kliknij przycisk **Dalej**.

    ![Serwer kopii zapasowych platformy Azure — sprawdzanie powitalnym i wymagań wstępnych](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Jeśli wystąpi błąd z zaleceniem ponownego uruchomienia komputera, uruchom ponownie komputer. Po ponownym uruchomieniu komputera uruchom ponownie instalatora, a po przejściu do ekranu **Ustawienia SQL** kliknij przycisk **Sprawdź ponownie**.

5. W **ustawieniach instalacji**podaj lokalizację instalacji plików serwera kopii zapasowej platformy Microsoft Azure i kliknij przycisk **Dalej**.

    ![Prereq2 kopii zapasowej platformy Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Lokalizacja zarysowania jest wymagana do utworzenia kopii zapasowej na platformie Azure. Upewnij się, że rozmiar lokalizacji podstaw jest odpowiednikiem co najmniej 5% danych planowanych do utworzenia kopii zapasowej na platformie Azure. Aby chronić dysk, po zakończeniu instalacji należy skonfigurować oddzielne dyski. Aby uzyskać więcej informacji dotyczących pul magazynowania, zobacz [Konfigurowanie pul magazynowania i magazynu dysków](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).

6. Na ekranie **Ustawienia zabezpieczeń** podaj silne hasło dla ograniczonych kont użytkowników lokalnych i kliknij przycisk **Dalej**.

    ![Prereq2 kopii zapasowej platformy Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Na ekranie **Microsoft Update Opt-In** wybierz, czy chcesz używać *usługi Microsoft Update* do sprawdzania dostępności aktualizacji, a następnie kliknij przycisk **Dalej**.

   > [!NOTE]
   > Zalecamy przekierowanie usługi Windows Update do witryny Microsoft Update, która oferuje zabezpieczenia i ważne aktualizacje dla systemu Windows i innych produktów, takich jak Microsoft Azure Backup Server.
   >

    ![Prereq2 kopii zapasowej platformy Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Przejrzyj *podsumowanie ustawień* i kliknij pozycję **Zainstaluj**.

    ![Prereq2 kopii zapasowej platformy Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Po zakończeniu instalowania serwera kopii zapasowej usługi Azure Instalator natychmiast uruchamia instalator agenta usług Microsoft Azure Recovery Services.

9. Zostanie otwarty instalator programu Microsoft Azure Recovery Services Agent i sprawdza dostępność łączności z Internetem. Jeśli połączenie z Internetem jest dostępne, przejdź do instalacji. Jeśli nie ma łączności, podaj szczegóły serwera proxy, aby połączyć się z Internetem. Po określeniu ustawień serwera proxy kliknij przycisk **Dalej**.

    ![Prereq2 kopii zapasowej platformy Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Aby zainstalować agenta usług odzyskiwania platformy Microsoft Azure, kliknij przycisk **Zainstaluj**.

    ![PreReq2 serwera kopii zapasowych platformy Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Agent usług odzyskiwania platformy Microsoft Azure, nazywany również agentem usługi Azure Backup, konfiguruje serwer kopii zapasowej platformy Azure w magazynie usług odzyskiwania. Po skonfigurowaniu usługa Azure Backup Server zawsze będzie dokonywać kopii zapasowych danych w tym samym magazynie usług odzyskiwania.

11. Po zakończeniu instalowania agenta usług odzyskiwania platformy Microsoft Azure kliknij przycisk **Dalej,** aby rozpocząć następną fazę: rejestrowanie usługi Azure Backup Server w magazynie usług odzyskiwania.

    ![PreReq2 serwera kopii zapasowych platformy Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Instalator uruchamia **Kreatora rejestracji serwera**.

12. Przełącz się do subskrypcji platformy Azure i magazynu usług odzyskiwania. W menu **Przygotowywanie infrastruktury** kliknij polecenie **Pobierz,** aby pobrać poświadczenia przechowalni. Jeśli przycisk **Pobierz** w kroku 2 nie jest aktywny, wybierz **pozycję Już pobrane lub przy użyciu najnowszej instalacji serwera kopii zapasowych platformy Azure,** aby aktywować przycisk. Poświadczenia magazynu są pobierane do lokalizacji, w której są przechowywane pliki do pobrania. Pamiętaj o tej lokalizacji, ponieważ będzie potrzebna do następnego kroku.

    ![PreReq2 serwera kopii zapasowych platformy Azure](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. W menu **Identyfikacja przechowalni** kliknij przycisk **Przeglądaj,** aby znaleźć poświadczenia magazynu usług recovery Services.

    ![PreReq2 serwera kopii zapasowych platformy Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    W oknie dialogowym **Wybieranie poświadczeń przechowalni** przejdź do lokalizacji pobierania, wybierz poświadczenia przechowalni i kliknij przycisk **Otwórz**.

    Ścieżka do poświadczeń pojawi się w menu Identyfikacja przechowalni. Kliknij **przycisk Dalej,** aby przejść do ustawienia szyfrowania.

14. W oknie dialogowym **Ustawienia szyfrowania** podaj hasło do szyfrowania kopii zapasowej i lokalizację do przechowywania hasła, a następnie kliknij przycisk **Dalej**.

    ![PreReq2 serwera kopii zapasowych platformy Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Możesz podać własne hasło lub użyć generatora haseł, aby utworzyć je dla Ciebie. Hasło należy do Twoich i firma Microsoft nie zapisuje tego hasła ani nie zarządza nim. Aby przygotować się na awarię, zapisz hasło w dostępnej lokalizacji.

    Po kliknięciu przycisku **Dalej**serwer kopii zapasowej platformy Azure jest rejestrowany w magazynie usług odzyskiwania. Instalator kontynuuje instalowanie programu SQL Server i serwera kopii zapasowych platformy Azure.

    ![PreReq2 serwera kopii zapasowych platformy Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Po zakończeniu instalacji stan pokazuje, że całe oprogramowanie zostało pomyślnie zainstalowane.

    ![PreReq2 serwera kopii zapasowych platformy Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Po zakończeniu instalacji konsola serwera azure backup server i ikony programu PowerShell usługi Azure Backup Server są tworzone na pulpicie serwera.

## <a name="add-backup-storage"></a>Dodawanie magazynu kopii zapasowych

Pierwsza kopia zapasowa jest przechowywana w magazynie dołączonym do komputera serwera kopii zapasowych platformy Azure. Aby uzyskać więcej informacji na temat dodawania dysków, zobacz [Dodawanie nowoczesnego magazynu kopii zapasowych](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Należy dodać magazyn kopii zapasowych, nawet jeśli planujesz wysyłać dane na platformę Azure. W architekturze usługi Azure Backup Server magazyn usług odzyskiwania przechowuje *drugą* kopię danych, podczas gdy magazyn lokalny zawiera pierwszą (i obowiązkową) kopię zapasową.
>
>

## <a name="network-connectivity"></a>Łączność sieciowa

Usługa Azure Backup Server wymaga łączności z usługą Azure Backup, aby produkt działał pomyślnie. Aby sprawdzić, czy komputer ma łączność z ```Get-DPMCloudConnection``` platformą Azure, użyj polecenia cmdlet w konsoli programu PowerShell programu Azure Backup Server. Jeśli dane wyjściowe polecenia cmdlet jest true, a następnie łączność istnieje, w przeciwnym razie nie ma łączności.

W tym samym czasie subskrypcja platformy Azure musi być w dobrej kondycji. Aby dowiedzieć się o stanie subskrypcji i zarządzać nią, zaloguj się do [portalu subskrypcji](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Gdy znasz stan łączności platformy Azure i subskrypcji platformy Azure, możesz użyć poniższej tabeli, aby dowiedzieć się, jaki ma wpływ na oferowaną funkcję tworzenia kopii zapasowej/przywracania.

| Stan łączności | Subskrypcja platformy Azure | Tworzenie kopii zapasowej w systemie Azure | Zapasowy zapas na dysku | Przywracanie z platformy Azure | Przywracanie z dysku |
| --- | --- | --- | --- | --- | --- |
| Połączone |Aktywne |Dozwolone |Dozwolone |Dozwolone |Dozwolone |
| Połączone |Wygasłe |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Połączone |Anulowanie obsługi administracyjnej |Zatrzymano |Zatrzymano |Zatrzymane i punkty odzyskiwania platformy Azure usunięte |Zatrzymano |
| Utrata łączności > 15 dni |Aktywne |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Utrata łączności > 15 dni |Wygasłe |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Utrata łączności > 15 dni |Anulowanie obsługi administracyjnej |Zatrzymano |Zatrzymano |Zatrzymane i punkty odzyskiwania platformy Azure usunięte |Zatrzymano |

### <a name="recovering-from-loss-of-connectivity"></a>Odzyskiwanie po utracie łączności

Jeśli zapora lub serwer proxy uniemożliwia dostęp do platformy Azure, dodaj następujące adresy domen na liście dozwolonych zapory/serwera proxy:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Po przywróceniu łączności z platformą Azure na serwerze kopii zapasowej usługa Azure stan subskrypcji platformy Azure określa operacje, które można wykonać. Gdy serwer jest **połączony,** użyj tabeli w [łączności sieciowej,](backup-mabs-install-azure-stack.md#network-connectivity) aby wyświetlić dostępne operacje.

### <a name="handling-subscription-states"></a>Obsługa stanów subskrypcji

Jest możliwe, aby zmienić subskrypcję platformy Azure ze *stanu wygasłe* lub *deprowizowane* na stan *aktywny.* Podczas gdy stan subskrypcji nie jest *aktywny:*

- Podczas gdy subskrypcja jest *deprovisioned*, traci funkcjonalność. Przywracanie subskrypcji do *Active*, przywraca funkcję tworzenia kopii zapasowych/przywracania. Jeśli dane kopii zapasowej na dysku lokalnym zostały zachowane z wystarczająco dużym okresem przechowywania, dane kopii zapasowej mogą być pobierane. Jednak dane kopii zapasowej na platformie Azure jest nieodwracalnie utracone, gdy subskrypcja wejdzie w stan *Deprovisioned.*
- Gdy subskrypcja *wygasła,* traci funkcjonalność. Zaplanowane kopie zapasowe nie są uruchamiane, gdy *subskrypcja wygasła*.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli serwer kopii zapasowej platformy Microsoft Azure ulegnie awarii z powodu błędów podczas fazy instalacji (lub kopii zapasowej lub przywrócenia), zobacz [dokument kodów błędów](https://support.microsoft.com/kb/3041338).
Można również zapoznać się [z często zadawanymi pytaniami dotyczącymi usługi Azure Backup](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Następne kroki

Artykuł [Przygotowywanie środowiska dla programu DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801)zawiera informacje o obsługiwanych konfiguracjach serwera kopii zapasowych platformy Azure.

Poniższe artykuły można użyć, aby uzyskać głębsze zrozumienie ochrony obciążenia przy użyciu programu Microsoft Azure Backup Server.

- [Kopia zapasowa programu SQL Server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [Kopia zapasowa serwera programu SharePoint](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Alternatywna kopia zapasowa serwera](backup-azure-alternate-dpm-server.md)
