---
title: Instalowanie składnika Azure Backup Server w usłudze Azure Stack
description: W tym artykule dowiesz się, jak używać Azure Backup Server do ochrony lub kopii zapasowej obciążeń w Azure Stack.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: d7c685a16db50e51a54387dde49ffb137fcfd626
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519480"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Instalowanie składnika Azure Backup Server w usłudze Azure Stack

W tym artykule wyjaśniono, jak zainstalować Azure Backup Server na Azure Stack. Dzięki Azure Backup Server można chronić obciążenia infrastruktury jako usługi (IaaS), takie jak maszyny wirtualne uruchomione w Azure Stack. Zaletą używania Azure Backup Server do ochrony obciążeń jest możliwość zarządzania całą ochroną obciążeń z poziomu jednej konsoli.

> [!NOTE]
> Aby dowiedzieć się więcej na temat możliwości zabezpieczeń, zapoznaj [się Azure Backup dokumentacją funkcji zabezpieczeń](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Macierz ochrony usługi Azure Backup Server

Azure Backup Server chroni następujące obciążenia Azure Stack maszyny wirtualnej.

| Chronione źródło danych | Ochrona i odzyskiwanie |
| --------------------- | ----------------------- |
| Windows Server Semi Annual Channel — Datacenter/Enterprise/Standard | Woluminy, pliki, foldery |
| Windows Server 2016 — Datacenter/Enterprise/Standard | Woluminy, pliki, foldery |
| Windows Server 2012 R2 — Datacenter/Enterprise/Standard | Woluminy, pliki, foldery |
| Windows Server 2012 — Datacenter/Enterprise/Standard | Woluminy, pliki, foldery |
| Windows Server 2008 R2 — Datacenter/Enterprise/Standard | Woluminy, pliki, foldery |
| SQL Server 2016 | baza danych |
| SQL Server 2014 | baza danych |
| SQL Server 2012 SP1 | baza danych |
| SharePoint 2016 | Farma, baza danych, frontend, serwer internetowy |
| SharePoint 2013 | Farma, baza danych, frontend, serwer internetowy |
| SharePoint 2010 | Farma, baza danych, frontend, serwer internetowy |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Wymagania wstępne dotyczące Azure Backup Server środowiska

Podczas instalowania aplikacji w środowisku Azure Backup Server należy wziąć pod uwagę zalecenia Azure Stack tej sekcji. Instalator Azure Backup Server sprawdza, czy środowisko ma niezbędne wymagania wstępne, ale zaoszczędzisz czas, przygotowując się przed zainstalowaniem.

### <a name="determining-size-of-virtual-machine"></a>Określanie rozmiaru maszyny wirtualnej

Aby uruchomić Azure Backup Server na maszynie Azure Stack wirtualnej, użyj rozmiaru A2 lub większego. Aby uzyskać pomoc przy wyborze rozmiaru maszyny wirtualnej, pobierz kalkulator rozmiaru [Azure Stack wirtualnej.](https://www.microsoft.com/download/details.aspx?id=56832)

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Sieci wirtualne na Azure Stack wirtualnych

Wszystkie maszyny wirtualne używane w obciążeniu Azure Stack muszą należeć do tej samej sieci wirtualnej platformy Azure i subskrypcji platformy Azure.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server maszyny wirtualnej

W przypadku współużytkowania z innymi maszynami wirtualnymi rozmiar konta magazynu i limity IOPS mają wpływ Azure Backup Server wydajności maszyny wirtualnej. Z tego powodu należy użyć oddzielnego konta magazynu dla Azure Backup Server wirtualnej. Agent Azure Backup uruchomiony na Azure Backup Server wymaga magazynu tymczasowego na potrzeby:

- własne użycie (lokalizacja pamięci podręcznej),
- dane przywrócone z chmury (lokalny obszar przejściowy)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurowanie Azure Backup magazynu dyskowego

Każda Azure Stack wirtualna jest dostarczany z magazynem dysków tymczasowych, który jest dostępny dla użytkownika jako wolumin `D:\` . Lokalny obszar przejściowy wymagany przez program Azure Backup można skonfigurować tak, aby znajdował się w lokalizacji , a lokalizację pamięci podręcznej `D:\` można umieścić w lokalizacji `C:\` . W ten sposób żaden magazyn nie musi być odsejść od dysków danych dołączonych do Azure Backup Server wirtualnej.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Przechowywanie danych kopii zapasowej na dysku lokalnym i na platformie Azure

Azure Backup Server dane kopii zapasowej są przechowywane na dyskach platformy Azure dołączonych do maszyny wirtualnej w celu odzyskiwania operacyjnego. Po dołączeniu dysków i miejsca do magazynowania do maszyny wirtualnej Azure Backup Server zarządza magazynem. Ilość miejsca do magazynowania danych kopii zapasowej zależy od liczby i rozmiaru dysków dołączonych do [poszczególnych Azure Stack wirtualnej.](/azure-stack/user/azure-stack-storage-overview) Każdy rozmiar maszyny Azure Stack wirtualnej ma maksymalną liczbę dysków, które można dołączyć do maszyny wirtualnej. Na przykład A2 to cztery dyski. A3 to osiem dysków. A4 to 16 dysków. Ponownie rozmiar i liczba dysków określają łączną pulę magazynów kopii zapasowych.

> [!IMPORTANT]
> Nie należy **przechowywać danych** odzyskiwania operacyjnego (kopii zapasowej) na Azure Backup Server dołączonych przez więcej niż pięć dni.
>

Przechowywanie danych kopii zapasowej na platformie Azure zmniejsza infrastrukturę kopii zapasowych na Azure Stack. Jeśli dane mają więcej niż pięć dni, powinny być przechowywane na platformie Azure.

Aby przechowywać dane kopii zapasowej na platformie Azure, utwórz magazyn usługi Recovery Services lub użyj go. Podczas przygotowywania do kopii zapasowej Azure Backup Server obciążenia należy [skonfigurować magazyn usługi Recovery Services.](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault) Po skonfigurowaniu przy każdym uruchamianiu zadania tworzenia kopii zapasowej punkt odzyskiwania jest tworzony w magazynie. Każdy magazyn usługi Recovery Services zawiera maksymalnie 9999 punktów odzyskiwania. W zależności od liczby utworzonych punktów odzyskiwania i czasu ich przechowywania można przechowywać dane kopii zapasowej przez wiele lat. Można na przykład tworzyć miesięczne punkty odzyskiwania i przechowywać je przez pięć lat.

### <a name="scaling-deployment"></a>Skalowanie wdrożenia

Jeśli chcesz skalować wdrożenie, masz następujące opcje:

- Skalowanie w górę — zwiększ rozmiar maszyny wirtualnej Azure Backup Server z serii A do serii D i zwiększ magazyn lokalny zgodnie z Azure Stack [maszyny wirtualnej.](/azure-stack/user/azure-stack-manage-vm-disks)
- Odciążanie danych — wysyła starsze dane na platformę Azure i zachowuje tylko najnowsze dane w magazynie dołączonym do Azure Backup Server.
- Skalowanie w celu skalowania w Azure Backup serwerów w celu ochrony obciążeń.

### <a name="net-framework"></a>.NET Framework

.NET Framework na maszynie wirtualnej musi być zainstalowana .NET Framework 3.5 SP1 lub wyższa.

### <a name="joining-a-domain"></a>Przyłączanie do domeny

Maszyna Azure Backup Server wirtualna musi być przysłana do domeny. Użytkownik domeny z uprawnieniami administratora musi zainstalować Azure Backup Server na maszynie wirtualnej.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Korzystanie z maszyny wirtualnej IaaS w Azure Stack

Wybierając serwer dla programu Azure Backup Server, zacznij od obrazu galerii windows Server 2012 R2 Datacenter lub Windows Server 2016 Datacenter. Artykuł Tworzenie [pierwszej maszyny wirtualnej z](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)systemem Windows w Azure Portal zawiera samouczek dotyczący rozpoczynania pracy z zalecaną maszyną wirtualną. Zalecane minimalne wymagania dotyczące maszyny wirtualnej serwera powinny być: A2 Standardowa z dwoma rdzeniami i 3,5 GB pamięci RAM.

Ochrona obciążeń za pomocą Azure Backup Server ma wiele niuansów. Macierz [ochrony dla mabs](./backup-mabs-protection-matrix.md) pomaga wyjaśnić te niuanse. Przed wdrożeniem maszyny przeczytaj całkowicie ten artykuł.

> [!NOTE]
> Azure Backup Server jest przeznaczony do uruchamiania na dedykowanej maszynie wirtualnej jednego przeznaczenia. Nie można instalować Azure Backup Server na:
>
> - Komputer działa jako kontroler domeny
> - Komputer, na którym jest zainstalowana rola serwera aplikacji
> - Komputer, na którym jest uruchomiony program Exchange Server
> - Komputer, który jest węzłem klastra

Zawsze dołączaj Azure Backup Server do domeny. Jeśli musisz przenieść Azure Backup Server do innej domeny, najpierw zainstaluj Azure Backup Server, a następnie dołącz ją do nowej domeny. Po wdrożeniu Azure Backup Server nie można przenieść go do nowej domeny.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurowanie replikacji magazynu

Opcja replikacji magazynu usługi Recovery Services umożliwia wybór magazynu geograficznie nadmiarowego i magazynu lokalnie nadmiarowego. Domyślnie magazyny usługi Recovery Services używają magazynu geograficznie nadmiarowego. Jeśli ten magazyn jest magazynem podstawowym, pozostaw opcję magazynu ustawioną na magazyn geograficznie nadmiarowy. Wybierz magazyn lokalnie nadmiarowy, jeśli chcesz mieć tańszą opcję, która jest mniej trwała. Więcej informacji na [temat opcji magazynu geograficznie nadmiarowego,](../storage/common/storage-redundancy.md#geo-redundant-storage) [lokalnie nadmiarowego](../storage/common/storage-redundancy.md#locally-redundant-storage)i [strefowo nadmiarowego](../storage/common/storage-redundancy.md#zone-redundant-storage) można uzyskać w tesłudze [Azure Storage replication overview (Omówienie replikacji usługi Azure Storage).](../storage/common/storage-redundancy.md)

Aby edytować ustawienia replikacji magazynu:

1. Wybierz magazyn, aby otworzyć pulpit nawigacyjny magazynu i menu Ustawienia. Jeśli menu **Ustawienia** nie zostanie otwarte, wybierz pozycję **Wszystkie ustawienia na** pulpicie nawigacyjnym magazynu.
2. W menu **Ustawienia** wybierz pozycję Konfiguracja kopii zapasowej **infrastruktury**  >  **kopii zapasowej,** aby otworzyć menu **Konfiguracja kopii zapasowej.** W menu **Konfiguracja kopii zapasowej** wybierz opcję replikacji magazynu dla magazynu.

    ![Lista magazynów kopii zapasowych](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Pobieranie Azure Backup Server instalatora

Istnieją dwa sposoby pobrania instalatora Azure Backup Server instalatora. Instalatora aplikacji można Azure Backup Server z [Centrum pobierania Microsoft.](https://www.microsoft.com/download/details.aspx?id=55269) Możesz również pobrać Azure Backup Server podczas konfigurowania magazynu usługi Recovery Services. W poniższych krokach przedstawiono sposób pobierania instalatora z usługi Azure Portal podczas konfigurowania magazynu usługi Recovery Services.

1. Z Azure Stack wirtualnej zaloguj się do subskrypcji platformy Azure w [witrynie Azure Portal.](https://portal.azure.com/)
2. W menu po lewej stronie wybierz pozycję **Wszystkie usługi.**

    ![Wybierz opcję Wszystkie usługi w menu głównym](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. W **oknie dialogowym** Wszystkie usługi wpisz *Recovery Services*. Po rozpoczęciu wpisywania dane wejściowe filtruje listę zasobów. Po wyświetleniu tego widoku wybierz pozycję **Magazyny usługi Recovery Services.**

    ![Wpisz Recovery Services w oknie dialogowym Wszystkie usługi](./media/backup-mabs-install-azure-stack/all-services.png)

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.

4. Z listy magazynów usługi Recovery Services wybierz magazyn, aby otworzyć jego pulpit nawigacyjny.

    ![Wybieranie magazynu w celu otwarcia pulpitu nawigacyjnego](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. W menu konfiguracji magazynu Wprowadzenie **pozycję** Kopia zapasowa, aby otworzyć Wprowadzenie magazynu.

    ![Wprowadzenie do tworzenia kopii zapasowej](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Zostanie otwarte menu kopii zapasowej.

    ![Backup-goals-default-opened](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. W menu kopii zapasowej z menu Where is your workload running (Gdzie jest uruchomione **Twoje obciążenie)** **wybierz pozycję On-premises (Lokalne).** Z menu **rozwijanego Co chcesz** utworzyć kopię zapasową? wybierz obciążenia, które chcesz chronić przy użyciu Azure Backup Server. Jeśli nie masz pewności, które obciążenia wybrać, wybierz pozycję **Hyper-V** Virtual Machines a następnie wybierz pozycję Prepare Infrastructure (Przygotuj **infrastrukturę).**

    ![lokalne i obciążenia jako cele](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Zostanie otwarte menu **Przygotowywanie** infrastruktury.

7. W menu **Przygotowywanie infrastruktury** wybierz **pozycję** Pobierz, aby otworzyć stronę internetową w celu Azure Backup Server plików instalacyjnych.

    ![Wprowadzenie zmiany kreatora](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Zostanie otwarta strona internetowa firmy Microsoft, na Azure Backup Server pliki do pobrania.

8. Na stronie pobierania Microsoft Azure Backup Server wybierz język, a następnie wybierz pozycję **Pobierz.**

    ![Zostanie otwarte Centrum pobierania](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Instalator Azure Backup Server składa się z ośmiu plików — instalatora i siedmiu plików bin. Zaznacz **pole Nazwa pliku,** aby wybrać wszystkie wymagane pliki, a następnie wybierz przycisk **Dalej.** Pobierz wszystkie pliki do tego samego folderu.

    ![Centrum pobierania, wybrane pliki](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Rozmiar pobierania wszystkich plików instalacyjnych jest większy niż 3 GB. W przypadku linku pobierania o rozmiarze 10 Mb/s pobranie wszystkich plików instalacyjnych może potrwać do 60 minut. Pliki są pobierane do określonej lokalizacji pobierania.

## <a name="extract-azure-backup-server-install-files"></a>Wyodrębnianie Azure Backup Server plików instalacji

Po pobraniu wszystkich plików na maszynę wirtualną Azure Stack przejdź do lokalizacji pobierania. Pierwszą fazą instalowania Azure Backup Server jest wyodrębnianie plików.

![Pobieranie instalatora mabs](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Aby rozpocząć instalację, z listy pobranych plików wybierz pozycję **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Do wyodrębnienia plików instalacyjnych jest wymagane co najmniej 4 GB wolnego miejsca.
    >

2. W kreatorze Azure Backup Server wybierz przycisk **Dalej,** aby kontynuować.

    ![Microsoft Azure Backup Kreator instalacji](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Wybierz ścieżkę dla plików Azure Backup Server, a następnie wybierz pozycję **Dalej.**

   ![Wybieranie miejsca docelowego dla plików](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Sprawdź lokalizację wyodrębniania, a następnie wybierz pozycję **Wyodrębnij**.

   ![Weryfikowanie lokalizacji wyodrębniania](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Kreator wyodrębnia pliki i odczytuje proces instalacji.

   ![Kreator wyodrębnia pliki](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Po zakończeniu procesu wyodrębniania wybierz pozycję **Zakończ.** Domyślnie jest **zaznaczona opcja setup.exe** wykonania. Po wybraniu **przycisku Zakończ** program Setup.exe zainstaluje Microsoft Azure Backup Server w określonej lokalizacji.

   ![Instalator wyodrębnia Microsoft Azure Backup serwera](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Instalowanie pakietu oprogramowania

W poprzednim kroku wybierz pozycję **Zakończ,** aby zakończyć fazę wyodrębniania, a następnie uruchom Azure Backup Server konfiguracji.

![Microsoft Azure Backup Kreator instalacji uruchamia się](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server udostępnia kod Data Protection Manager. W instalatorze aplikacji Data Protection Manager odwołania do Azure Backup Server DPM. Chociaż Azure Backup Server i Data Protection Manager są oddzielnymi produktami, te produkty są ze sobą ściśle powiązane.

1. Aby uruchomić kreatora instalacji, wybierz pozycję **Microsoft Azure Backup Server.**

   ![Wybieranie Microsoft Azure Backup Serwera](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Na **ekranie powitalnym** wybierz pozycję **Dalej.**

    ![Azure Backup Server — Zapraszamy!](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Na **ekranie Sprawdzanie wymagań wstępnych** **wybierz** pozycję Sprawdź, aby określić, czy wymagania wstępne dotyczące sprzętu i oprogramowania Azure Backup Server zostały spełnione.

    ![Azure Backup Server — sprawdzanie wymagań wstępnych](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Jeśli środowisko ma niezbędne wymagania wstępne, zostanie wyświetlony komunikat informujący o tym, że maszyna spełnia wymagania. Wybierz opcję **Dalej**.  

    ![Azure Backup Server — sprawdzanie wymagań wstępnych minęło](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Jeśli środowisko nie spełnia niezbędnych wymagań wstępnych, zostaną określone problemy. Wymagania wstępne, które nie zostały spełnione, są również wymienione w pliku DpmSetup.log. Rozwiąż błędy wymagań wstępnych, a następnie uruchom **ponownie pozycję Sprawdź.** Nie można kontynuować instalacji, dopóki nie zostaną spełnione wszystkie wymagania wstępne.

    ![Azure Backup Server — wymagania wstępne instalacji nie zostały spełnione](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server wymaga SQL Server. Pakiet Azure Backup Server jest dostarczany w pakiecie z odpowiednimi SQL Server binarnymi. Jeśli chcesz użyć własnej instalacji sql, możesz to zrobić. Jednak zalecanym wyborem jest pozwolić instalatorowi na dodanie nowego wystąpienia SQL Server. Aby upewnić się, że wybór współpracuje ze środowiskiem, wybierz pozycję **Sprawdź i zainstaluj**.

   > [!NOTE]
   > Azure Backup Server nie będzie działać z wystąpieniem SQL Server zdalnego. Wystąpienie używane przez Azure Backup Server musi być lokalne.
   >

    ![Azure Backup Server — ustawienia SQL](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Po sprawdzeniu, czy maszyna wirtualna ma wymagania wstępne niezbędne do zainstalowania Azure Backup Server, wybierz pozycję **Dalej.**

    ![Azure Backup Server — spełnione wymagania](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Jeśli wystąpi awaria z zaleceniem ponownego uruchomienia maszyny, uruchom ją ponownie. Po ponownym uruchomieniu komputera uruchom ponownie instalatora, a po dojecheniu do ekranu **Ustawienia SQL** wybierz pozycję **Sprawdź ponownie.**

5. W **ustawieniach instalacji** podaj lokalizację instalacji plików serwera Microsoft Azure Backup i wybierz przycisk **Dalej.**

    ![Podaj lokalizację instalacji plików](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Lokalizacja na potrzeby kopii zapasowej jest wymagana do kopii zapasowej na platformie Azure. Upewnij się, że rozmiar lokalizacji dla plików scratch jest odpowiednikiem co najmniej 5% danych, których kopię zapasową planuje się wrócić na platformę Azure. W celu ochrony dysku należy skonfigurować oddzielne dyski po zakończeniu instalacji. Aby uzyskać więcej informacji na temat pul magazynów, zobacz [Przygotowanie magazynu danych.](/system-center/dpm/plan-long-and-short-term-data-storage)

6. Na **ekranie Ustawienia zabezpieczeń** podaj silne hasło dla kont użytkowników lokalnych z ograniczeniami, a następnie wybierz pozycję **Dalej.**

    ![Ekran Ustawień zabezpieczeń](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Na Microsoft Update **wybierz,** czy chcesz użyć programu *Microsoft Update,* aby sprawdzić aktualizacje, a następnie wybierz przycisk **Dalej.**

   > [!NOTE]
   > Zalecamy przekierowywanie Windows Update do usługi Microsoft Update, która oferuje zabezpieczenia i ważne aktualizacje dla systemu Windows i innych produktów, takich jak Microsoft Azure Backup Server.
   >

    ![Microsoft Update Opt-In ekranu](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Przejrzyj podsumowanie *ustawień i wybierz* pozycję **Zainstaluj.**

    ![Podsumowanie ustawień](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Po Azure Backup Server instalacji instalator natychmiast uruchamia instalatora agenta Microsoft Azure Recovery Services.

9. Zostanie otwarty Microsoft Azure Agent usługi Recovery Services i sprawdzi łączność z Internetem. Jeśli łączność z Internetem jest dostępna, kontynuuj instalację. Jeśli nie ma łączności, podaj szczegóły serwera proxy, aby nawiązać połączenie z Internetem. Po podano ustawienia serwera proxy, wybierz pozycję **Dalej.**

    ![Konfiguracja serwera proxy](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Aby zainstalować agenta Microsoft Azure Recovery Services, wybierz pozycję **Zainstaluj**.

    ![Instalacja agenta](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Agent Microsoft Azure Recovery Services, nazywany również agentem usługi Azure Backup, konfiguruje Azure Backup Server magazynu usługi Recovery Services. Po skonfigurowaniu Azure Backup Server kopii zapasowej danych w tym samym magazynie usługi Recovery Services.

11. Po zakończeniu Microsoft Azure usługi Recovery Services wybierz  przycisk Dalej, aby rozpocząć następną fazę: rejestrowanie Azure Backup Server w magazynie usługi Recovery Services.

    ![Instalacja agenta została ukończona pomyślnie](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Instalator uruchamia Kreatora **rejestrowania serwera**.

12. Przejdź do subskrypcji platformy Azure i magazynu usługi Recovery Services. W menu **Przygotowywanie infrastruktury** wybierz pozycję **Pobierz,** aby pobrać poświadczenia magazynu. Jeśli przycisk **Pobierz** w kroku 2 nie jest aktywny, wybierz pozycję Już pobrano lub za pomocą najnowszej **Azure Backup Server instalacji,** aby aktywować przycisk. Poświadczenia magazynu są pobierane do lokalizacji, w której są przechowywane pobrane pliki. Należy pamiętać o tej lokalizacji, ponieważ będzie ona potrzebna w następnym kroku.

    ![Pobieranie poświadczeń magazynu](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. W menu **Identyfikacja magazynu** wybierz pozycję **Przeglądaj,** aby znaleźć poświadczenia magazynu usługi Recovery Services.

    ![Menu identyfikacji magazynu](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    W **oknie dialogowym Select Vault Credentials** (Wybieranie poświadczeń magazynu) przejdź do lokalizacji pobierania, wybierz poświadczenia magazynu i wybierz pozycję **Open (Otwórz).**

    Ścieżka do poświadczeń zostanie wyświetlona w menu Identyfikacja magazynu. Wybierz **przycisk Dalej,** aby przejść do **ustawień szyfrowania.**

14. W **oknie dialogowym** Ustawienie szyfrowania podaj hasło szyfrowania kopii zapasowej oraz lokalizację do przechowywania hasła, a następnie wybierz pozycję **Dalej.**

    ![Ustawienia szyfrowania](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Możesz podać własne hasło lub użyć generatora haseł, aby je utworzyć. Hasło należy do Twoich, a firma Microsoft nie zapisuje tego hasła ani nie zarządza nim. Aby przygotować się na awarię, zapisz hasło w dostępnej lokalizacji.

    Po wybraniu **przycisku Dalej** Azure Backup Server w magazynie usługi Recovery Services. Instalator kontynuuje instalowanie SQL Server i Azure Backup Server.

    ![Instalator instaluje bazy danych SQL i Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Po zakończeniu pracy instalatora **stan** wskazuje, że całe oprogramowanie zostało pomyślnie zainstalowane.

    ![Oprogramowanie zostało pomyślnie zainstalowane](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Po zakończeniu instalacji na pulpicie serwera Azure Backup Server konsoli programu Azure Backup Server ikony programu PowerShell.

## <a name="add-backup-storage"></a>Dodawanie magazynu kopii zapasowych

Pierwsza kopia zapasowa jest przechowywana w magazynie dołączonym do Azure Backup Server wirtualnej. Aby uzyskać więcej informacji na temat dodawania dysków, [zobacz Dodawanie magazynu nowoczesnych kopii zapasowych.](/system-center/dpm/add-storage)

> [!NOTE]
> Musisz dodać magazyn kopii zapasowych, nawet jeśli planujesz wysłać dane na platformę Azure. W architekturze Azure Backup Server magazyn usługi Recovery  Services przechowuje drugą kopię danych, podczas gdy magazyn lokalny przechowuje pierwszą (i obowiązkową) kopię zapasową.
>
>

## <a name="network-connectivity"></a>Łączność sieciowa

Azure Backup Server do pomyślnego działania produktu Azure Backup łączności z usługą klienta. Aby sprawdzić, czy maszyna ma łączność z platformą Azure, użyj polecenia ```Get-DPMCloudConnection``` cmdlet Azure Backup Server programu PowerShell. Jeśli dane wyjściowe polecenia cmdlet mają wartość TRUE, łączność istnieje. W przeciwnym razie nie będzie łączności.

Jednocześnie subskrypcja platformy Azure musi być w dobrej kondycji. Aby sprawdzić stan subskrypcji i zarządzać jej, zaloguj się do [portalu subskrypcji.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)

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


Po przywróceniu łączności z platformą Azure Azure Backup Server stan subskrypcji platformy Azure określa operacje, które można wykonać. Gdy serwer będzie **połączony, użyj** tabeli w tece [Łączność sieciowa,](backup-mabs-install-azure-stack.md#network-connectivity) aby wyświetlić dostępne operacje.

### <a name="handling-subscription-states"></a>Obsługa stanów subskrypcji

Można zmienić stan subskrypcji platformy Azure z *Wygasła* lub *Coprowizowana* na *Stan* aktywny. Stan subskrypcji nie jest *aktywny:*

- Jeśli anulowano *aprowizę subskrypcji,* traci ona funkcjonalność. Przywrócenie subskrypcji do *aktywnej* przywraca funkcję tworzenia/przywracania kopii zapasowych. Jeśli dane kopii zapasowej na dysku lokalnym zostały zachowane z wystarczająco dużym okresem przechowywania, można pobrać te dane kopii zapasowej. Jednak dane kopii zapasowej na platformie Azure są nieodwracalnie utracone po wejściu subskrypcji w stan *Coprowizowanie.*
- Gdy subskrypcja *wygasła,* traci funkcjonalność. Zaplanowane kopie zapasowe nie są uruchamiane, gdy subskrypcja *wygasła.*

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli Microsoft Azure Backup się z błędami podczas fazy instalacji (lub tworzenia kopii zapasowej lub przywracania), zobacz dokument [kody błędów](https://support.microsoft.com/kb/3041338).
Możesz również zapoznać się z [Azure Backup często zadawanych pytaniach](backup-azure-backup-faq.yml)

## <a name="next-steps"></a>Następne kroki

Artykuł Przygotowywanie środowiska dla programu [DPM](/system-center/dpm/prepare-environment-for-dpm)zawiera informacje o obsługiwanych Azure Backup Server konfiguracji.

Poniższe artykuły mogą pomóc w głębszym zrozumieniu ochrony obciążeń przy użyciu Microsoft Azure Backup Server.

- [SQL Server kopii zapasowej](./backup-mabs-sql-azure-stack.md)
- [Kopia zapasowa programu SharePoint Server](./backup-mabs-sharepoint-azure-stack.md)
- [Kopia zapasowa alternatywnego serwera](backup-azure-alternate-dpm-server.md)
