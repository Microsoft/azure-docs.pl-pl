---
title: Replikowanie maszyn wirtualnych usługi Azure Stack na platformę Azure przy użyciu usługi Azure Site Recovery | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla maszyn wirtualnych usługi Azure Stack za pomocą usługi Azure Site Recovery.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: ab35463ca8c3b29e6b4ae8abc781a7081091b214
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478515"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replikowanie maszyn wirtualnych usługi Azure Stack na platformie Azure

W tym artykule pokazano, jak skonfigurować odzyskiwanie po awarii maszyny wirtualne usługi Azure Stack na platformie Azure przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md)

Odzyskiwanie witryny przyczynia się do ciągłości biznesowej i strategii odzyskiwania po awarii (BCDR). Usługa zapewnia, że obciążenia maszyny Wirtualnej pozostają dostępne, gdy wystąpią oczekiwane i nieoczekiwane awarie.

- Usługa Site Recovery organizuje replikację maszyn wirtualnych do magazynu platformy Azure i zarządza nią.
- Gdy wystąpi awaria w lokacji głównej, użyj site recovery do pracy awaryjnej na platformie Azure.
- Po przemijeniu awaryjnym maszyny wirtualne platformy Azure są tworzone na podstawie przechowywanych danych maszyny wirtualnej, a użytkownicy mogą kontynuować uzyskiwanie dostępu do obciążeń uruchomionych na tych maszynach wirtualnych platformy Azure.
- Gdy wszystko jest ponownie uruchomione, możesz przywrócić maszyny wirtualne platformy Azure po awarii do lokacji głównej i ponownie rozpocząć replikację do magazynu platformy Azure.


W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * **Krok 1: Przygotowanie maszyn wirtualnych stosu platformy Azure do replikacji**. Sprawdź, czy maszyny wirtualne spełniają wymagania dotyczące odzyskiwania witryny i przygotuj się do instalacji usługi Mobilności odzyskiwania witryny. Ta usługa jest zainstalowana na każdej maszynie wirtualnej, którą chcesz replikować.
> * **Krok 2: Konfigurowanie magazynu usług odzyskiwania**. Skonfiguruj magazyn dla usługi Site Recovery i określ, co chcesz replikować. Składniki i akcje odzyskiwania lokacji są konfigurowane i zarządzane w przechowalni.
> * **Krok 3: Konfigurowanie środowiska replikacji źródłowej**. Konfigurowanie serwera konfiguracji odzyskiwania lokacji. Serwer konfiguracji to pojedyncza maszyna wirtualna usługi Azure Stack, która uruchamia wszystkie składniki wymagane przez usługę Site Recovery. Po skonfigurowaniu serwera konfiguracji można go zarejestrować w przechowalni.
> * **Krok 4: Konfigurowanie docelowego środowiska replikacji**. Wybierz swoje konto platformy Azure oraz konto i sieć magazynu platformy Azure, których chcesz użyć. Podczas replikacji dane maszyny Wirtualnej są kopiowane do magazynu platformy Azure. Po przełączeniu awaryjnym maszyny wirtualne platformy Azure są przyłączane do określonej sieci.
> * **Krok 5: Włącz replikację**. Skonfiguruj ustawienia replikacji i włącz replikację maszyn wirtualnych. Usługa mobilności zostanie zainstalowana na maszynie Wirtualnej, gdy replikacja jest włączona. Usługa Site Recovery wykonuje początkową replikację maszyny Wirtualnej, a następnie rozpoczyna się trwająca replikacja.
> * **Krok 6: Uruchom ćwiczenie odzyskiwania po awarii:** Po uruchomieniu replikacji, należy sprawdzić, czy praca awaryjna będzie działać zgodnie z oczekiwaniami, uruchamiając wiertło. Aby zainicjować wiertło, należy uruchomić test pracy awaryjnej w odzyskiwaniu lokacji. Test pracy awaryjnej nie ma wpływu na środowisko produkcyjne.

Po wykonaniu tych kroków można następnie uruchomić pełne przejście w tryb failover do platformy Azure, gdy trzeba.

## <a name="architecture"></a>Architektura

![Architektura](./media/azure-stack-site-recovery/architecture.png)

**Lokalizacja** | **Składnik** |**Szczegóły**
--- | --- | ---
**Serwer konfiguracji** | Działa na jednej maszynie wirtualnej usługi Azure Stack. | W każdej subskrypcji skonfigurować maszynę wirtualną serwera konfiguracji. Ta maszyna wirtualna uruchamia następujące składniki odzyskiwania witryny:<br/><br/> - Serwer konfiguracji: koordynuje komunikację między lokalną a platformą Azure i zarządza replikacją danych. - Serwer przetwarzania: działa jako brama replikacji. Odbiera dane replikacji, optymalizuje za pomocą buforowania, kompresji i szyfrowania; i wysyła go do usługi Azure Storage.<br/><br/> Jeśli maszyny wirtualne, które chcesz replikować, przekraczają limity podane poniżej, można skonfigurować oddzielny serwer procesów autonomicznych. [Dowiedz się więcej](vmware-azure-set-up-process-server-scale.md).
**Usługa mobilności** | Zainstalowany na każdej maszynie wirtualnej, którą chcesz replikować. | W krokach w tym artykule przygotowujemy konto, dzięki czemu usługa mobilności jest instalowana automatycznie na maszynie Wirtualnej, gdy replikacja jest włączona. Jeśli nie chcesz instalować usługi automatycznie, istnieje wiele innych metod, których można użyć. [Dowiedz się więcej](vmware-azure-install-mobility-service.md).
**Azure** | Na platformie Azure potrzebujesz magazynu usług odzyskiwania, konta magazynu i sieci wirtualnej. |  Replikowane dane są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są dodawane do sieci platformy Azure po wystąpieniu pracy awaryjnej.


Replikacja działa w następujący sposób:

1. W przechowalni można określić źródło replikacji i obiekt docelowy, skonfigurować serwer konfiguracji, utworzyć zasady replikacji i włączyć replikację.
2. Usługa mobilności jest zainstalowana na komputerze (jeśli została użyta instalacja wypychania), a maszyny rozpoczynają replikację zgodnie z zasadami replikacji.
3. Początkowa kopia danych serwera jest replikowana do magazynu platformy Azure.
4. Po zakończeniu replikacji początkowej rozpoczyna się replikacja zmian różnicowych na platformie Azure. Śledzone zmiany dla maszyny są przechowywane w pliku hrl.
5. Serwer konfiguracji organizuje zarządzanie replikacją za pomocą platformy Azure (port HTTPS 443 wychodzących).
6. Serwer przetwarzania odbiera dane z komputerów źródłowych, optymalizuje je i szyfruje oraz wysyła do magazynu platformy Azure (port 443 wychodzących).
7. Maszyny replikowane komunikują się z serwerem konfiguracji (port HTTPS 443 przychodzący, do zarządzania replikacją. Maszyny wysyłają dane replikacji do serwera przetwarzania (port HTTPS 9443 przychodzące - mogą być modyfikowane).
8. Ruch jest replikowany do publicznych punktów końcowych usługi Azure Storage za pośrednictwem Internetu. Alternatywnie można użyć publicznej komunikacji równorzędnej usługi Azure ExpressRoute. Replikowanie ruchu za pośrednictwem sieci VPN typu lokacja-lokacja z lokacji lokalnej platformy Azure nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Oto, co jest potrzebne do skonfigurowania tego scenariusza.

**Wymaganie** | **Szczegóły**
--- | ---
**Konto subskrypcji platformy Azure** | Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
**Uprawnienia konta platformy Azure** | Konto platformy Azure, którego używasz, wymaga uprawnień do:<br/><br/> - Tworzenie magazynu usługi odzyskiwania<br/><br/> - Tworzenie maszyny wirtualnej w grupie zasobów i sieci wirtualnej, której używasz w scenariuszu<br/><br/> - Zapisz na konto magazynu, które określisz<br/><br/> Należy pamiętać, że:<br/><br/> -Jeśli utworzysz konto, jesteś administratorem subskrypcji i możesz wykonywać wszystkie czynności.<br/><br/> - Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz współpracować z administratorem, aby przypisać Ci uprawnienia właściciela lub współautora.<br/><br/> - Jeśli potrzebujesz bardziej szczegółowych uprawnień, przejrzyj [ten artykuł](site-recovery-role-based-linked-access-control.md).
**Maszyna wirtualna usługi Azure Stack** | Potrzebujesz maszyny Wirtualnej usługi Azure Stack w subskrypcji dzierżawy, która zostanie wdrożona jako serwer konfiguracji odzyskiwania lokacji.


### <a name="prerequisites-for-the-configuration-server"></a>Wymagania wstępne dla serwera konfiguracji

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]



## <a name="step-1-prepare-azure-stack-vms"></a>Krok 1: Przygotowywanie maszyn wirtualnych usługi Azure Stack

### <a name="verify-the-operating-system"></a>Sprawdź system operacyjny

Upewnij się, że maszyny wirtualne są uruchomione jeden z systemów operacyjnych podsumowane w tabeli.


**System operacyjny** | **Szczegóły**
--- | ---
**64-bitowy system Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (z dodatku SP1)
**CentOS** | 5,2 do 5,11, 6,1 do 6,9, od 7,0 do 7,3
**Ubuntu** | 14.04 Serwer LTS, serwer 16.04 LTS. Przeglądanie [obsługiwanych jąder](vmware-physical-azure-support-matrix.md#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Przygotowanie do instalacji usługi mobilności

Każda maszyna wirtualna, którą chcesz replikować, musi mieć zainstalowaną usługę mobilności. Aby serwer przetwarzania automatycznie instalował usługę na maszynie Wirtualnej po włączeniu replikacji, sprawdź ustawienia maszyny Wirtualnej.

#### <a name="windows-machines"></a>Maszyny z systemem Windows

- Potrzebna jest łączność sieciowa między maszyną wirtualną, na której ma zostać włączona replikacja, a komputerem z serwerem przetwarzania (domyślnie jest to maszyna wirtualna serwera konfiguracji).
- Potrzebne jest konto z uprawnieniami administratora (domena lub lokalna) na komputerze, dla którego można włączyć replikację.
    - To konto należy określić podczas konfigurowania usługi Site Recovery. Następnie serwer przetwarzania używa tego konta do zainstalowania usługi mobilności, gdy replikacja jest włączona.
    - To konto będzie używane tylko przez usługę Site Recovery do instalacji wypychanej i do aktualizacji usługi mobilności.
    - Jeśli nie używasz konta domeny, musisz wyłączyć kontrolę dostępu użytkownika zdalnego na maszynie Wirtualnej:
        - W rejestrze utwórz wartość DWORD **LocalAccountTokenFilterPolicy** w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Jako wartość wpisz 1.
        - Aby to zrobić w wierszu polecenia, wpisz następujące polecenie: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- W Zaporze systemu Windows na maszynie wirtualnej, którą chcesz replikować, zezwalaj na udostępnianie plików i drukarek oraz usługę WMI.
    - Aby to zrobić, uruchom **program wf.msc,** aby otworzyć konsolę Zapory systemu Windows. Kliknij prawym przyciskiem myszy **pozycję Reguły** > przychodzące**Nowa reguła**. Wybierz **wstępnie zdefiniowane**i wybierz pozycję **Udostępnianie plików i drukarek** z listy. Ukończ kreatora, wybierz, aby zezwolić na połączenie > **Zakończ**.
    - W przypadku komputerów domeny można użyć obiektu zasad grupy.


#### <a name="linux-machines"></a>Maszyny linuzownie

- Upewnij się, że istnieje łączność sieciowa między komputerem z systemem Linux a serwerem przetwarzania.
- Na komputerze, dla którego można włączyć replikację, potrzebujesz konta, które jest użytkownikiem głównym na źródłowym serwerze Linux:
    - To konto należy określić podczas konfigurowania usługi Site Recovery. Następnie serwer przetwarzania używa tego konta do zainstalowania usługi mobilności, gdy replikacja jest włączona.
    - To konto będzie używane tylko przez usługę Site Recovery do instalacji wypychanej i do aktualizacji usługi mobilności.
- Sprawdź, czy plik /etc/hosts na źródłowym serwerze z systemem Linux zawiera wpisy mapujące lokalną nazwę hosta na adres IP skojarzony ze wszystkimi kartami sieciowymi.
- Zainstaluj najnowsze pakiety openssh, openssh-server i openssl na komputerze, który chcesz replikować.
- Upewnij się, że protokół Secure Shell (SSH) jest włączony i uruchomiony na porcie 22.
- Włącz podsystem SFTP i uwierzytelnianie hasłem w pliku sshd_config:
    1. Aby to zrobić, zaloguj się jako root.
    2. Znajdź wiersz, który zaczyna się od **PasswordAuthentication**, w pliku /etc/ssh/sshd_config. Usuń znaczniki komentarza dla wiersza i zmień wartość na **yes**.
    3. Znajdź wiersz zaczynający się od ciągu **Subsystem** i usuń znaczniki komentarza.

        ![Usługa Mobilności Linuksa](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Uruchom ponownie usługę sshd.


### <a name="note-the-vm-private-ip-address"></a>Zanotuj prywatny adres IP maszyny Wirtualnej

Dla każdego komputera, który chcesz replikować, znajdź adres IP:

1. W portalu stosu Azure kliknij maszynę wirtualną.
2. W menu **Zasób** kliknij polecenie **Interfejsy sieciowe**.
3. Zanotuj prywatny adres IP.

    ![Prywatny adres IP](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Krok 2: Tworzenie przechowalni i wybieranie celu replikacji

1. W witrynie Azure portal wybierz pozycję Utwórz kopię **zapasową** > narzędzi do > **zarządzania zasobami****i odzyskiwanie witryny**.
2. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn.
3. W **grupie zasobów**utwórz lub wybierz grupę zasobów. Używamy **contosoRG**.
4. W **obszarze Lokalizacja**wprowadź region platformy Azure. Użyj wartości **Europa Zachodnia**.
5. Aby szybko uzyskać dostęp do repozytorium z pulpitu nawigacyjnego, wybierz **pozycję Przypnij do pulpitu nawigacyjnego** > **Utwórz**.

   ![Tworzenie nowego magazynu](./media/azure-stack-site-recovery/new-vault-settings.png)

   Nowy magazyn pojawi się na **pulpicie nawigacyjnym** > **Wszystkie zasoby**i na głównej stronie **magazynów usług odzyskiwania.**

### <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

1. W **magazynach usług odzyskiwania** > określić nazwę przechowalni. Używamy **ContosoVMVault**.
2. W obszarze **Wprowadzenie** wybierz pozycję Site Recovery. Następnie wybierz pozycję **Przygotowywanie infrastruktury**.
3. W **celu** > Ochrona**Gdzie znajdują się twoje maszyny**, wybierz opcję **Lokalnie**.
4. W obszarze **Gdzie chcesz zreplikować maszyny** wybierz pozycję **Na platformę Azure**.
5. W **Czy maszyny zwirtualizowane**, wybierz **nie zwirtualizowane /Inne**. Następnie wybierz przycisk **OK**.

    ![Cel ochrony](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Krok 3: Konfigurowanie środowiska źródłowego

Skonfiguruj komputer serwera konfiguracji, zarejestruj go w przechowalni i odnajduj maszyny, które chcesz replikować.

1. Kliknij przycisk Przygotuj**źródło** **infrastruktury** > .
2. W obszarze **Przygotowywanie źródła** kliknij pozycję **+ Serwer konfiguracji**.

    ![Konfiguracja źródła](./media/azure-stack-site-recovery/plus-config-srv.png)

3. W **programie Add Server**sprawdź, czy serwer **konfiguracji** jest wyświetlany w **typie serwera**.
5. Pobierz plik instalacyjny ujednoliconej instalacji odzyskiwania witryny.
6. Pobierz klucz rejestracji magazynu. Klucz rejestracji jest potrzebny po uruchomieniu Instalatora ujednoliconego. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Konfiguracja źródła](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Uruchamianie ujednoliconej instalacji usługi Azure Site Recovery

Aby zainstalować i zarejestrować serwer konfiguracji, wykonaj połączenie RDP z maszyną wirtualną, której chcesz użyć dla serwera konfiguracji, i uruchom Instalatora ujednoliconego.

Przed rozpoczęciem upewnij się, że zegar jest [synchronizowany z serwerem czasu](/windows-server/networking/windows-time-service/windows-time-service-top) na maszynie Wirtualnej przed rozpoczęciem. Instalacja kończy się niepowodzeniem, jeśli czas jest więcej niż pięć minut czasu lokalnego.

Teraz zainstaluj serwer konfiguracji:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Serwer konfiguracji można również zainstalować z wiersza polecenia. [Dowiedz się więcej](physical-manage-configuration-server.md#install-from-the-command-line).
>
> Zanim nazwa konta pojawi się w portalu, może minąć 15 minut lub więcej. Aby natychmiast zaktualizować, wybierz pozycję **Serwery** > konfiguracji***nazwa*** > serwera**Odświeżyć serwer**.

## <a name="step-4-set-up-the-target-environment"></a>Krok 4: Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. W **obszarze Przygotowanie infrastruktury** > **docelowej**wybierz subskrypcję platformy Azure, której chcesz użyć.
2. Określ docelowy model wdrażania.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure. Jeśli ich nie znajdzie, należy utworzyć co najmniej jedno konto magazynu i sieć wirtualną, aby ukończyć kreatora.


## <a name="step-5-enable-replication"></a>Krok 5. Włączanie replikacji

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. Kliknij pozycję Przygotuj**ustawienia replikacji** **infrastruktury** > .
2. W obszarze **Tworzenie zasad replikacji** określ nazwę zasad.
3. W obszarze **Wartość progowa celu punktu odzyskiwania** określ limit celu punktu odzyskiwania.
    - Punkty odzyskiwania dla zreplikowanych danych są tworzone zgodnie z ustawionym czasem.
    - To ustawienie nie ma wpływu na replikację, która jest ciągła. Po prostu wydaje alert, jeśli limit progu zostanie osiągnięty bez tworzonego punktu odzyskiwania.
4. W **obszarze Przechowywanie punktów odzyskiwania**określ, jak długo każdy punkt odzyskiwania jest przechowywany. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w określonym przedziale czasu.
5. W **aplikacji spójne częstotliwości migawki**, określ, jak często migawki spójne z aplikacją są tworzone.

    - Migawka spójna z aplikacją jest migawką punktu w czasie danych aplikacji wewnątrz maszyny Wirtualnej.
    - Usługa kopiowania woluminów w tle (VSS) zapewnia, że aplikacje na maszynie Wirtualnej są w spójnym stanie po wykonaniu migawki.
6. Wybierz pozycję **OK**, aby utworzyć zasady.


### <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

Możesz teraz pominąć ten krok. W menu rozwijanym **Planowanie wdrażania** kliknij przycisk **Tak, zrobiłem to**.



### <a name="enable-replication"></a>Włączanie replikacji

Upewnij się, że wszystkie zadania zostały wykonane w [kroku 1: Przygotowanie maszyny](#step-1-prepare-azure-stack-vms). Następnie włącz replikację w następujący sposób:

1. Wybierz **opcję Replikuj** > **źródło**aplikacji .
2. W obszarze **Źródło** wybierz serwer konfiguracji.
3. W **obszarze Typ maszyny**wybierz opcję Komputery **fizyczne**.
4. Wybierz serwer przetwarzania (serwer konfiguracji). Następnie kliknij przycisk **OK**.
5. W **obszarze Docelowy**wybierz subskrypcję i grupę zasobów, w której chcesz utworzyć maszyny wirtualne po przemienniu w stan failover. Wybierz model wdrażania, który ma być używany dla maszyn wirtualnych po awarii.
6. Wybierz konto magazynu platformy Azure, na którym chcesz przechowywać zreplikowane dane.
7. Wybierz sieć platformy Azure i podsieć, z którą nawiążą połączenie maszyny wirtualne platformy Azure, gdy zostaną uruchomione po przejściu do trybu failover.
8. Wybierz opcję **Konfiguruj teraz dla wybranych maszyn**, aby zastosować ustawienia sieci do wszystkich maszyn wybranych do ochrony. Wybierz **pozycję Konfiguruj później,** jeśli chcesz wybrać sieć platformy Azure oddzielnie dla każdego komputera.
9. W **programie Physical Machines**kliknij pozycję **+Maszyna fizyczna**. Określ nazwę, adres IP i typ systemu operacyjnego każdego komputera, który ma zostać zreplikowane.

    - Użyj wewnętrznego adresu IP urządzenia.
    - Jeśli określisz publiczny adres IP, replikacja może nie działać zgodnie z oczekiwaniami.

10. W **właściwościach** > **Konfiguracja właściwości**wybierz konto, którego serwer przetwarzania będzie używał do automatycznego instalowania usługi mobilności na komputerze.
11. W **obszarze Ustawienia** > replikacji**Konfigurowanie ustawień replikacji**sprawdź, czy zaznaczono prawidłowe zasady replikacji.
12. Kliknij pozycję **Włącz replikację**.
13. Track progress of the **Enable Protection** job in **Settings** > **Jobs** > **Site Recovery Jobs**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.

> [!NOTE]
> Po włączeniu replikacji maszyny wirtualnej usługa Site Recovery instaluje usługę mobilności.
>
> Zastosowanie zmian i wyświetlenie ich w portalu może potrwać 15 minut lub dłużej.
>
> Aby monitorować dodawanych maszyn wirtualnych, sprawdź ostatnio odnaleziony czas dla maszyn wirtualnych w ostatnim**kontakcie z**serwerami >  **konfiguracyjnymi**w . Aby dodać maszyny wirtualne, nie czekając na zaplanowane odnajdywanie, wyróżnij serwer konfiguracji (nie wybieraj go), a następnie wybierz pozycję **Odśwież**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Krok 6: Uruchamianie ćwiczenia odzyskiwania po awarii

Uruchom test pracy awaryjnej na platformie Azure, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami. Ta przewija się w stan failover nie wpłynie na środowisko produkcyjne.

### <a name="verify-machine-properties"></a>Weryfikowanie właściwości maszyny

Przed uruchomieniem testowego trybu failover sprawdź właściwości komputera i upewnij się, że są one zgodne z [wymaganiami platformy Azure.](vmware-physical-azure-support-matrix.md#azure-vm-requirements) Można wyświetlać i modyfikować właściwości w następujący sposób:

1. W obszarze **Chronione elementy** kliknij kolejno pozycje **Zreplikowane elementy** > Maszyna wirtualna.
2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.
3. W **obszarze Obliczenia i Sieć**zmodyfikuj ustawienia w razie potrzeby.

    - Można zmodyfikować nazwę maszyny Wirtualnej platformy Azure, grupę zasobów, rozmiar [docelowy, zestaw dostępności](/azure/virtual-machines/windows/tutorial-availability-sets)i ustawienia dysku zarządzanego.
    - Można również wyświetlać i modyfikować ustawienia sieciowe. Należą do nich sieci/podsieci, do której maszyna wirtualna platformy Azure jest przyłączona po pracy awaryjnej i adres IP, który zostanie przypisany do maszyny Wirtualnej.
1. W **programie Disks**można wyświetlić informacje o systemie operacyjnym i dyskach danych na maszynie wirtualnej.


### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Po uruchomieniu próby przejścia w tryb failover wykonywane są następujące operacje:

1. Uruchamiane jest sprawdzanie wymagań wstępnych, aby upewnić się, że zostały spełnione wszystkie warunki przejścia w tryb failover.
2. Przewija się w przypadku pracy awaryjnej przy użyciu określonego punktu odzyskiwania:
    - **Najnowsze przetworzone:** Komputer działa pod wpływem najnowszego punktu odzyskiwania przetwarzanego przez program Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
    - **Najnowsze spójne z aplikacjami:** komputer działa w stan fail, do najnowszego punktu odzyskiwania spójnego z aplikacją.
    - **Niestandardowe**: Wybierz punkt odzyskiwania używany do pracy awaryjnej.

3. Maszyna wirtualna platformy Azure jest tworzona przy użyciu przetworzonych danych.
4. Testowanie pracy awaryjnej można automatycznie oczyścić maszyn wirtualnych platformy Azure utworzonych podczas drążenia.

Uruchom test pracy awaryjnej dla maszyny Wirtualnej w następujący sposób:

1. W **obszarze Ustawienia** > **replikowane elementy**kliknij maszynę wirtualną > **+Test failover**.
2. W tym instruktażu wybierzemy opcję użycia punktu odzyskiwania **najnowszego przetworzonego.**
3. W **testowanie trybu failover**wybierz docelową sieć platformy Azure.
4. Kliknij przycisk **OK**, aby rozpocząć tryb failover.
5. Śledź postęp, klikając maszynę wirtualną, aby otworzyć jej właściwości. Można też kliknąć zadanie **testowania trybu failover** w *zadaniach* > odzyskiwania**witryny Ustawienia magazynu Ustawienia** > **zadań.** >**Site Recovery jobs**
6. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Sprawdź, czy maszyna wirtualna ma odpowiedni rozmiar, jest podłączona do prawej sieci i uruchomiona.
7. Powinno być teraz możliwe nawiązanie połączenia ze zreplikowaną maszyną wirtualną na platformie Azure. [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
8. Aby usunąć maszyny wirtualne platformy Azure utworzone podczas testowego przełączania w tryb failover, kliknij pozycję **Wyczyść test pracy w trybie failover** na maszynie wirtualnej. W **uwagach**zapisz wszystkie obserwacje skojarzone z testem w wersji failover.

## <a name="fail-over-and-fail-back"></a>Przechodzenie do trybu failover i powrót po awarii

Po skonfigurowaniu replikacji i uruchomieniu wiertła, aby upewnić się, że wszystko działa, możesz w razie potrzeby przewinąć maszyny w stan fail do platformy Azure.

Przed uruchomieniem pracy awaryjnej, jeśli chcesz połączyć się z komputerem na platformie Azure po przełączeniu w pracę awaryjną, a następnie [przygotować się do połączenia](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) przed uruchomieniem.

Następnie uruchom przełęknienie awaryjne w następujący sposób:


1. W **obszarze Ustawienia** > **z replikowane elementy**kliknij komputer > pracy **awaryjnej**.
2. Wybierz punkt odzyskiwania, którego chcesz użyć.
3. W **testowanie trybu failover**wybierz docelową sieć platformy Azure.
4. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Za pomocą tego ustawienia usługa Site Recovery próbuje zamknąć komputer źródłowy przed rozpoczęciem pracy awaryjnej. Jednak tryb failover nadal, nawet jeśli zamknięcie nie powiedzie się.
5. Kliknij przycisk **OK**, aby rozpocząć tryb failover. Postęp pracy awaryjnej można śledzić na stronie **Zadania.**
6. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Jeśli przygotowano do połączenia po pracy awaryjnej, sprawdź, czy maszyna wirtualna jest odpowiedni rozmiar, podłączony do prawej sieci i działa.
7. Po zweryfikowaniu maszyny Wirtualnej kliknij przycisk **Zaśliwij,** aby zakończyć przetwórt pracy awaryjnej. Spowoduje to usunięcie wszystkich dostępnych punktów odzyskiwania.

> [!WARNING]
> Nie anuluj trybu failover po rozpoczęciu przełączania: przed uruchomieniem trybu failover replikacja maszyny wirtualnej zostanie zatrzymana. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.


### <a name="fail-back-to-azure-stack"></a>Powrót po awarii do usługi Azure Stack

Gdy twoja lokacja główna jest uruchomiona ponownie, możesz wrócić z platformy Azure po awarii do usługi Azure Stack. Aby to zrobić, należy pobrać wirtualną wirtualną platformę Azure vhd i przekazać ją do usługi Azure Stack.

1. Zamknij maszynę wirtualną platformy Azure, aby można było pobrać dysk VHD.
2. Aby rozpocząć pobieranie dysku VHD, zainstaluj [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).
3. Przejdź do maszyny Wirtualnej w witrynie Azure Portal (przy użyciu nazwy maszyny Wirtualnej).
4. W **obszarze Dyski**kliknij nazwę dysku i zdejmij ustawienia.

    - Na przykład identyfikator URI VHD używany `https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd` w naszym teście: można podzielić, aby uzyskać następujące parametry wejściowe, które są używane do pobierania vhd.
        - Konto magazynu: 502055westcentralus
        - Kontener: wahv9b8d2ceb284fb59287
        - Nazwa VHD: skopiowany-3676553984.vhd

5. Teraz użyj Usługi Azure Storage Explorer, aby pobrać dysk VHD.
6. Przekaż dysk VHD do usługi Azure Stack za pomocą [tych kroków](/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-disks-to-a-vm).
7. W istniejącej maszynie wirtualnej lub nowej maszynie wirtualnej dołącz przekazane wirtualne identyfikatory WIRTUALNE.
8. Sprawdź, czy dysk systemu operacyjnego jest poprawny i uruchom maszynę wirtualną.


Na tym etapie powrót po awarii jest zakończona.


## <a name="conclusion"></a>Podsumowanie

W tym artykule zreplikowaliśmy maszyny wirtualne usługi Azure Stack na platformę Azure. Po uruchomieniu replikacji w miejscu uruchomiliśmy wiertło odzyskiwania po awarii, aby upewnić się, że praca awaryjna na platformie Azure działała zgodnie z oczekiwaniami. W tym artykule uwzględniono również kroki dotyczące uruchamiania pełnego trybu failover na platformie Azure i powrotu do usługi Azure Stack.

## <a name="next-steps"></a>Następne kroki

Po niepowodzeniu po awarii, można ponownie wykonać proces wirtualny i rozpocząć replikowanie go na platformie Azure, aby to zrobić, powtórz kroki opisane w tym artykule.

