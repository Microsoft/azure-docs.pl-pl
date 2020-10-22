---
title: Replikowanie Azure Stack maszyn wirtualnych na platformę Azure przy użyciu Azure Site Recovery | Microsoft Docs
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla maszyn wirtualnych Azure Stack przy użyciu usługi Azure Site Recovery.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 36e11bfe5354644f9ef6603ffe20cb2e86074323
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370529"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replikowanie maszyn wirtualnych usługi Azure Stack na platformie Azure

W tym artykule opisano sposób konfigurowania odzyskiwania po awarii Azure Stack maszyn wirtualnych na platformie Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).

Site Recovery przyczynia się do strategii zachowania ciągłości działania i odzyskiwania po awarii (BCDR). Usługa zapewnia, że obciążenia maszyny wirtualnej pozostają dostępne po oczekiwaniu i nieoczekiwanym wystąpieniu awarii.

- Site Recovery organizuje replikację maszyn wirtualnych i zarządza nimi do usługi Azure Storage.
- W przypadku wystąpienia awarii w lokacji głównej należy użyć Site Recovery do przełączenia w tryb failover na platformie Azure.
- W trybie failover maszyny wirtualne platformy Azure są tworzone na podstawie przechowywanych danych maszyny wirtualnej, a użytkownicy mogą nadal uzyskiwać dostęp do obciążeń uruchomionych na tych maszynach wirtualnych platformy Azure.
- Po ponownym uruchomieniu i ponownym uruchomieniu maszyny wirtualne platformy Azure można powrócić do lokacji głównej, a następnie ponownie rozpocząć replikację do usługi Azure Storage.


W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * **Krok 1. Przygotowywanie maszyn wirtualnych usługi Azure Stack na potrzeby replikacji**. Sprawdź, czy maszyny wirtualne spełniają wymagania Site Recovery i przygotuj się do zainstalowania usługi mobilności Site Recovery. Ta usługa jest zainstalowana na każdej maszynie wirtualnej, którą chcesz replikować.
> * **Krok 2. Konfigurowanie magazynu Recovery Services**. Skonfiguruj magazyn dla Site Recovery i określ, co chcesz replikować. Składniki i akcje Site Recovery są konfigurowane i zarządzane w magazynie.
> * **Krok 3. Konfigurowanie środowiska replikacji źródłowej**. Skonfiguruj serwer konfiguracji Site Recovery. Serwer konfiguracji jest pojedynczą Azure Stack maszyną wirtualną, na której działają wszystkie składniki, które są używane przez Site Recovery. Po skonfigurowaniu serwera konfiguracji należy zarejestrować go w magazynie.
> * **Krok 4. Konfigurowanie docelowego środowiska replikacji**. Wybierz swoje konto platformy Azure oraz konto usługi Azure Storage i sieć, których chcesz użyć. Podczas replikacji dane maszyn wirtualnych są kopiowane do usługi Azure Storage. Po przejściu do trybu failover maszyny wirtualne platformy Azure są przyłączone do określonej sieci.
> * **Krok 5. włączanie replikacji**. Skonfiguruj ustawienia replikacji i Włącz replikację dla maszyn wirtualnych. Usługa mobilności zostanie zainstalowana na maszynie wirtualnej po włączeniu replikacji. Site Recovery wykonuje replikację początkową maszyny wirtualnej, a następnie rozpoczyna bieżącą replikację.
> * **Krok 6. przechodzenie do szczegółów odzyskiwania po awarii**: po uruchomieniu replikacji należy sprawdzić, czy tryb failover będzie działać zgodnie z oczekiwaniami, uruchamiając polecenie przechodzenia do szczegółów. Aby zainicjować przechodzenie do szczegółów, należy uruchomić test pracy w trybie failover w Site Recovery. Test pracy w trybie failover nie ma wpływu na środowisko produkcyjne.

Po wykonaniu tych kroków można uruchomić pełną pracę w trybie failover na platformie Azure, jak i w razie potrzeby.

## <a name="architecture"></a>Architektura

![Diagram przedstawia Recovery Services magazynów dla dwóch dzierżawców w chmurach skojarzonych z subskrypcjami dzierżawców zarówno w przypadku wspólnej infrastruktury Azure Stack.](./media/azure-stack-site-recovery/architecture.png)

**Lokalizacja** | **Składnik** |**Szczegóły**
--- | --- | ---
**Serwer konfiguracji** | Działa na jednej maszynie wirtualnej Azure Stack. | W każdej subskrypcji można skonfigurować maszynę wirtualną serwera konfiguracji. Ta maszyna wirtualna uruchamia następujące składniki Site Recovery:<br/><br/> -Serwer konfiguracji: koordynuje komunikację między środowiskiem lokalnym i platformą Azure oraz zarządza replikacją danych. -Serwer przetwarzania: działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania; i wysyła je do usługi Azure Storage.<br/><br/> Jeśli maszyny wirtualne, które mają być replikowane, przekraczają limity podane poniżej, można skonfigurować oddzielny serwer przetwarzania autonomicznego. [Dowiedz się więcej](vmware-azure-set-up-process-server-scale.md).
**Usługa mobilności** | Zainstalowany na każdej maszynie wirtualnej, która ma być replikowana. | W krokach przedstawionych w tym artykule przygotowujemy konto, aby usługa mobilności została zainstalowana automatycznie na maszynie wirtualnej po włączeniu replikacji. Jeśli nie chcesz instalować usługi automatycznie, istnieje kilka innych metod, których można użyć. [Dowiedz się więcej](vmware-azure-install-mobility-service.md).
**Azure** | Na platformie Azure potrzebny jest magazyn Recovery Services, konto magazynu i Sieć wirtualna. |  Zreplikowane dane są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są dodawane do sieci platformy Azure w przypadku przełączenia w tryb failover.


Replikacja działa w następujący sposób:

1. W magazynie należy określić źródło i cel replikacji, skonfigurować serwer konfiguracji, utworzyć zasady replikacji i włączyć replikację.
2. Usługa mobilności jest zainstalowana na komputerze (Jeśli użyto instalacji wypychanej), a maszyny rozpoczynają replikację zgodnie z zasadami replikacji.
3. Początkowa kopia danych serwera jest replikowana do usługi Azure Storage.
4. Po zakończeniu replikacji początkowej zostanie rozpoczęta replikacja zmian różnicowych na platformie Azure. Śledzone zmiany dla maszyny są przechowywane w pliku hrl.
5. Serwer konfiguracji organizuje zarządzanie replikacją przy użyciu platformy Azure (port HTTPS 443).
6. Serwer przetwarzania odbiera dane z maszyn źródłowych, optymalizuje je i szyfruje i wysyła je do usługi Azure Storage (port 443 wychodzący).
7. Zreplikowane maszyny komunikują się z serwerem konfiguracji (port HTTPS 443 w ruchu przychodzącym w celu zarządzania replikacją. Maszyny wysyłają dane replikacji do serwera przetwarzania (port HTTPS 9443 ruchu przychodzącego — można modyfikować).
8. Ruch jest replikowany do publicznych punktów końcowych usługi Azure Storage za pośrednictwem Internetu. Alternatywnie można użyć publicznej komunikacji równorzędnej usługi Azure ExpressRoute. Replikowanie ruchu za pośrednictwem sieci VPN typu lokacja-lokacja z lokacji lokalnej platformy Azure nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Oto co należy zrobić, aby skonfigurować ten scenariusz.

**Wymaganie** | **Szczegóły**
--- | ---
**Konto subskrypcji platformy Azure** | Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
**Uprawnienia konta platformy Azure** | Używane konto platformy Azure wymaga uprawnień do:<br/><br/> -Utwórz magazyn usługi Recovery Service<br/><br/> -Utworzyć maszynę wirtualną w grupie zasobów i sieci wirtualnej używanej w tym scenariuszu<br/><br/> -Zapisz na określonym koncie magazynu<br/><br/> Należy pamiętać, że:<br/><br/> — Jeśli utworzysz konto, jesteś administratorem subskrypcji i możesz wykonać wszystkie akcje.<br/><br/> — Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem, aby przypisać uprawnienia właściciela lub współautora.<br/><br/> — Jeśli potrzebujesz bardziej szczegółowych uprawnień, zapoznaj się z [tym artykułem](site-recovery-role-based-linked-access-control.md).
**Azure Stack maszynę wirtualną** | W ramach subskrypcji dzierżawy jest potrzebna Azure Stack maszyna wirtualna, która zostanie wdrożona jako serwer konfiguracji Site Recovery.


### <a name="prerequisites-for-the-configuration-server"></a>Wymagania wstępne dotyczące serwera konfiguracji

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]



## <a name="step-1-prepare-azure-stack-vms"></a>Krok 1. Przygotowanie Azure Stack maszyn wirtualnych

### <a name="verify-the-operating-system"></a>Weryfikowanie systemu operacyjnego

Upewnij się, że na maszynach wirtualnych jest uruchomiony jeden z systemów operacyjnych zestawionych w tabeli.


**System operacyjny** | **Szczegóły**
--- | ---
**64 — bit systemu Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (z programu SP1)
**CentOS** | 5,2 do 5,11, 6,1 do 6,9, 7,0 do 7,3
**Ubuntu** | 14,04 LTS Server, 16,04 LTS serwer. Przegląd [obsługiwanych jądra](vmware-physical-azure-support-matrix.md#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Przygotowanie do instalacji usługi mobilności

Dla każdej maszyny wirtualnej, która ma zostać zreplikowana, musi być zainstalowana usługa mobilności. Aby serwer przetwarzania automatycznie instalował usługę na maszynie wirtualnej po włączeniu replikacji, sprawdź ustawienia maszyny wirtualnej.

#### <a name="windows-machines"></a>Maszyny z systemem Windows

- Wymagana jest łączność sieciowa między maszyną wirtualną, na której chcesz włączyć replikację, a maszyną z uruchomionym serwerem przetwarzania (domyślnie jest to maszyna wirtualna serwera konfiguracji).
- Wymagane jest konto z uprawnieniami administratora (domeny lub lokalne) na komputerze, na którym ma zostać włączona replikacja.
    - To konto należy określić podczas konfigurowania Site Recovery. Następnie serwer przetwarzania korzysta z tego konta, aby zainstalować usługę mobilności po włączeniu replikacji.
    - To konto będzie używane tylko przez Site Recovery do instalacji wypychanej oraz do aktualizowania usługi mobilności.
    - Jeśli nie korzystasz z konta domeny, musisz wyłączyć kontrolę dostępu użytkowników zdalnych na maszynie wirtualnej:
        - W rejestrze utwórz wartość DWORD **LocalAccountTokenFilterPolicy** w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Jako wartość wpisz 1.
        - W tym celu w wierszu polecenia wpisz następujące polecenie: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System/V LocalAccountTokenFilterPolicy/t REG_DWORD/d 1**.
- W zaporze systemu Windows na maszynie wirtualnej, którą chcesz replikować, Zezwól na udostępnianie plików i drukarek oraz usługę WMI.
    - Aby to zrobić, uruchom program **WF. msc** , aby otworzyć konsolę zapory systemu Windows. Kliknij prawym przyciskiem myszy pozycję **reguły ruchu przychodzącego**  >  **Nowa reguła**. Wybierz pozycję **wstępnie zdefiniowane**, a następnie z listy wybierz pozycję **udostępnianie plików i drukarek** . Ukończ pracę kreatora, wybierając opcję zezwalania > na **zakończenie**połączenia.
    - W przypadku komputerów z domeną można użyć obiektu zasad grupy.


#### <a name="linux-machines"></a>Maszyny z systemem Linux

- Upewnij się, że istnieje połączenie sieciowe między komputerem z systemem Linux a serwerem przetwarzania.
- Na komputerze, na którym zostanie włączona replikacja, potrzebujesz konta, które jest kontem użytkownika głównego na źródłowym serwerze z systemem Linux:
    - To konto należy określić podczas konfigurowania Site Recovery. Następnie serwer przetwarzania korzysta z tego konta, aby zainstalować usługę mobilności po włączeniu replikacji.
    - To konto będzie używane tylko przez Site Recovery do instalacji wypychanej oraz do aktualizowania usługi mobilności.
- Sprawdź, czy plik /etc/hosts na źródłowym serwerze z systemem Linux zawiera wpisy mapujące lokalną nazwę hosta na adres IP skojarzony ze wszystkimi kartami sieciowymi.
- Zainstaluj najnowsze pakiety openssh, openssh-server i openssl na komputerze, który chcesz replikować.
- Upewnij się, że protokół Secure Shell (SSH) jest włączony i uruchomiony na porcie 22.
- Włącz podsystem SFTP i uwierzytelnianie hasłem w pliku sshd_config:
    1. W tym celu zaloguj się jako element główny.
    2. Znajdź wiersz zaczynający się od **PasswordAuthentication**w pliku/etc/ssh/sshd_config. Usuń znaczniki komentarza dla wiersza i zmień wartość na **yes**.
    3. Znajdź wiersz zaczynający się od ciągu **Subsystem** i usuń znaczniki komentarza.

        ![Usługa mobilności dla systemu Linux](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Uruchom ponownie usługę sshd.


### <a name="note-the-vm-private-ip-address"></a>Zanotuj prywatny adres IP maszyny wirtualnej

Znajdź adres IP dla każdej maszyny, która ma zostać zreplikowana:

1. W portalu Azure Stack kliknij maszynę wirtualną.
2. W menu **zasób** kliknij pozycję **interfejsy sieciowe**.
3. Zanotuj prywatny adres IP.

    ![Prywatny adres IP](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Krok 2. Tworzenie magazynu i Wybieranie celu replikacji

1. W Azure Portal wybierz pozycję **Utwórz zasoby**  >  **Narzędzia do zarządzania**zasobami  >  **i Site Recovery**.
2. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn.
3. W obszarze **Grupa zasobów**Utwórz lub wybierz grupę zasobów. Korzystamy z usługi **contosoRG**.
4. W polu **Lokalizacja**wprowadź region świadczenia usługi Azure. Użyj wartości **Europa Zachodnia**.
5. Aby szybko uzyskać dostęp do magazynu z pulpitu nawigacyjnego, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**  >  **Utwórz**.

   ![Tworzenie nowego magazynu](./media/azure-stack-site-recovery/new-vault-settings.png)

   Nowy magazyn będzie wyświetlany na **pulpicie nawigacyjnym**  >  **wszystkie zasoby**i na stronie **magazyny główne Recovery Services** .

### <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

1. W **Recovery Services magazynach** > określić nazwę magazynu. Korzystamy z usługi **ContosoVMVault**.
2. W obszarze **Wprowadzenie** wybierz pozycję Site Recovery. Następnie wybierz pozycję **Przygotowywanie infrastruktury**.
3. W obszarze **cel ochrony**, gdzie znajdują się  >  **maszyny**, wybierz pozycję **lokalna**.
4. W obszarze **Gdzie chcesz zreplikować maszyny** wybierz pozycję **Na platformę Azure**.
5. W obszarze **czy maszyny są zwirtualizowane**, wybierz pozycję **niezwirtualizowane/inne**. Następnie wybierz przycisk **OK**.

    ![Cel ochrony](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Krok 3. Konfigurowanie środowiska źródłowego

Skonfiguruj maszynę serwera konfiguracji, zarejestruj ją w magazynie i Wykryj maszyny, które chcesz replikować.

1. Kliknij pozycję **Przygotuj**  >  **Źródło**infrastruktury.
2. W obszarze **Przygotowywanie źródła** kliknij pozycję **+ Serwer konfiguracji**.

    ![Zrzut ekranu okna dialogowego + serwera konfiguracji z komunikatem "kliknij pozycję + Serwer konfiguracji na pasku poleceń powyżej, aby skonfigurować jeden...".](./media/azure-stack-site-recovery/plus-config-srv.png)

3. W obszarze **Dodawanie serwera**Sprawdź, czy **serwer konfiguracji** jest wyświetlany w polu **Typ serwera**.
5. Pobierz plik instalacyjny programu Site Recovery Unified Setup.
6. Pobierz klucz rejestracji magazynu. Po uruchomieniu ujednoliconej konfiguracji wymagany jest klucz rejestracji. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Zrzut ekranu okna dialogowego Dodawanie serwera z ustawioną pozycją serwer konfiguracji i przycisk Pobierz klucz rejestracji magazynu.](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Uruchom Azure Site Recovery ujednoliconą konfigurację

Aby zainstalować i zarejestrować serwer konfiguracji, należy nawiązać połączenie RDP z maszyną wirtualną, która ma być używana przez serwer konfiguracji, i uruchomić ujednoliconą konfigurację.

Przed rozpoczęciem upewnij się, że zegar jest [zsynchronizowany z serwerem czasu](/windows-server/networking/windows-time-service/windows-time-service-top) na maszynie wirtualnej przed rozpoczęciem. Instalacja kończy się niepowodzeniem, jeśli czas jest większy niż pięć minut na czas lokalny.

Teraz Zainstaluj serwer konfiguracji:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Serwer konfiguracji można również zainstalować z poziomu wiersza polecenia. [Dowiedz się więcej](physical-manage-configuration-server.md#install-from-the-command-line).
>
> Zanim nazwa konta pojawi się w portalu, może minąć 15 minut lub więcej. Aby natychmiast zaktualizować, wybierz pozycję **serwery konfiguracji**  >  **_Nazwa serwera_*_ > _* Odśwież serwer**.

## <a name="step-4-set-up-the-target-environment"></a>Krok 4. Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. W obszarze **Przygotowanie infrastruktury**  >  **docelowej**wybierz subskrypcję platformy Azure, której chcesz użyć.
2. Określ docelowy model wdrażania.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure. Jeśli ich nie znajdziesz, należy utworzyć co najmniej jedno konto magazynu i sieć wirtualną, aby zakończyć pracę kreatora.


## <a name="step-5-enable-replication"></a>Krok 5. Włączanie replikacji

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. Kliknij pozycję **Przygotuj**  >  **Ustawienia replikacji**infrastruktury.
2. W obszarze **Tworzenie zasad replikacji** określ nazwę zasad.
3. W obszarze **Wartość progowa celu punktu odzyskiwania** określ limit celu punktu odzyskiwania.
    - Punkty odzyskiwania replikowanych danych są tworzone zgodnie z ustawionym czasem.
    - To ustawienie nie ma wpływu na replikację, która jest ciągła. Po prostu generuje alert w przypadku osiągnięcia limitu progu bez tworzenia punktu odzyskiwania.
4. W obszarze **przechowywanie punktu odzyskiwania**Określ czas przechowywania poszczególnych punktów odzyskiwania. Zreplikowane maszyny wirtualne można odzyskać do dowolnego punktu w określonym przedziale czasu.
5. W obszarze częstotliwość wykonywania migawek na poziomie **aplikacji**Określ, jak często są tworzone migawki spójne z aplikacjami.

    - Migawka spójna na poziomie aplikacji to migawka danych aplikacji znajdujących się w danym momencie w ramach maszyny wirtualnej.
    - Usługa kopiowania woluminów w tle (VSS) gwarantuje, że aplikacje na maszynie wirtualnej są w stanie spójnym podczas tworzenia migawki.
6. Wybierz pozycję **OK**, aby utworzyć zasady.


### <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

Możesz pominąć ten krok teraz. Na liście rozwijanej **Planowanie wdrożenia** kliknij przycisk **tak. zostało to zrobione**.



### <a name="enable-replication"></a>Włączanie replikacji

Upewnij się, że wszystkie zadania zostały wykonane w [kroku 1: Prepare Machine](#step-1-prepare-azure-stack-vms). Następnie Włącz replikację w następujący sposób:

1. Wybierz pozycję **Replikuj**  >  **Źródło**aplikacji.
2. W obszarze **Źródło** wybierz serwer konfiguracji.
3. W obszarze **Typ maszyny**wybierz pozycję **maszyny fizyczne**.
4. Wybierz serwer przetwarzania (serwer konfiguracji). Następnie kliknij przycisk **OK**.
5. W obszarze **cel**wybierz subskrypcję i grupę zasobów, w której chcesz utworzyć maszyny wirtualne po przejściu do trybu failover. Wybierz model wdrażania, którego chcesz użyć dla maszyn wirtualnych w trybie failover.
6. Wybierz konto usługi Azure Storage, w którym chcesz przechowywać zreplikowane dane.
7. Wybierz sieć platformy Azure i podsieć, z którą nawiążą połączenie maszyny wirtualne platformy Azure, gdy zostaną uruchomione po przejściu do trybu failover.
8. Wybierz opcję **Konfiguruj teraz dla wybranych maszyn**, aby zastosować ustawienia sieci do wszystkich maszyn wybranych do ochrony. Wybierz pozycję **Skonfiguruj później** , jeśli chcesz wybrać sieć platformy Azure osobno dla każdej maszyny.
9. W obszarze **maszyny fizyczne**kliknij pozycję **+ maszyna fizyczna**. Określ nazwę, adres IP i typ systemu operacyjnego każdej maszyny, która ma być replikowana.

    - Użyj wewnętrznego adresu IP maszyny.
    - W przypadku określenia publicznego adresu IP replikacja może nie zadziałała zgodnie z oczekiwaniami.

10. W obszarze **Właściwości**  >  **Konfigurowanie właściwości**wybierz konto, które będzie używane przez serwer przetwarzania do automatycznej instalacji usługi mobilności na maszynie.
11. W obszarze **Ustawienia replikacji**  >  **Skonfiguruj ustawienia replikacji**Sprawdź, czy wybrano odpowiednie zasady replikacji.
12. Kliknij pozycję **Włącz replikację**.
13. Śledź postęp zadania **Włącz ochronę** w obszarze **Ustawienia**  >  **zadania**  >  **Site Recovery zadania**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.

> [!NOTE]
> Po włączeniu replikacji maszyny wirtualnej usługa Site Recovery instaluje usługę mobilności.
>
> Zastosowanie zmian i wyświetlenie ich w portalu może potrwać 15 minut lub dłużej.
>
> Aby monitorować dodawane maszyny wirtualne, sprawdź ostatni znaleziony czas dla maszyn wirtualnych w obszarze **serwery konfiguracji**  >  **ostatni kontakt na stronie**. Aby dodać maszyny wirtualne, nie czekając na zaplanowane odnajdywanie, wyróżnij serwer konfiguracji (nie wybieraj go), a następnie wybierz pozycję **Odśwież**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Krok 6. przechodzenie do szczegółów odzyskiwania po awarii

Przeprowadzasz test pracy w trybie failover na platformie Azure, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami. Ta praca w trybie failover nie będzie miała wpływu na środowisko produkcyjne.

### <a name="verify-machine-properties"></a>Sprawdź właściwości maszyny

Przed uruchomieniem testowego przejścia w tryb failover Sprawdź właściwości komputera i upewnij się, że są one zgodne z [wymaganiami platformy Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements). Właściwości można wyświetlać i modyfikować w następujący sposób:

1. W obszarze **Chronione elementy** kliknij kolejno pozycje **Zreplikowane elementy** > Maszyna wirtualna.
2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.
3. W obszarze **obliczenia i sieć**zmodyfikuj ustawienia zgodnie z wymaganiami.

    - Możesz zmodyfikować nazwę maszyny wirtualnej platformy Azure, grupę zasobów, rozmiar docelowy, [zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md)i ustawienia dysku zarządzanego.
    - Możesz również wyświetlać i modyfikować ustawienia sieci. Obejmują one sieć/podsieć, do której jest dołączona maszyna wirtualna platformy Azure po przejściu w tryb failover, oraz adres IP, który zostanie przypisany do maszyny wirtualnej.
1. W obszarze **dyski**Przejrzyj informacje o systemie operacyjnym i dyskach z danymi na maszynie wirtualnej.


### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Po uruchomieniu próby przejścia w tryb failover wykonywane są następujące operacje:

1. Uruchamiane jest sprawdzanie wymagań wstępnych, aby upewnić się, że zostały spełnione wszystkie warunki przejścia w tryb failover.
2. Tryb failover przetwarza dane przy użyciu określonego punktu odzyskiwania:
    - **Ostatnie przetwarzanie**: komputer przechodzi w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
    - **Najnowsza spójna dla aplikacji**: komputer przechodzi w tryb failover do najnowszego punktu odzyskiwania spójnego z aplikacją.
    - **Niestandardowy**: Wybierz punkt odzyskiwania używany do pracy w trybie failover.

3. Maszyna wirtualna platformy Azure jest tworzona przy użyciu przetworzonych danych.
4. Test pracy w trybie failover może automatycznie czyścić maszyny wirtualne platformy Azure utworzone podczas przechodzenia do szczegółów.

Uruchom test pracy w trybie failover dla maszyny wirtualnej w następujący sposób:

1. W obszarze **Ustawienia**  >  **zreplikowane elementy**kliknij maszynę wirtualną > **i przetestuj tryb failover**.
2. W tym instruktażu wybierzemy użycie **najnowszego przetworzonego** punktu odzyskiwania.
3. W obszarze **test pracy w trybie failover**wybierz docelową sieć platformy Azure.
4. Kliknij przycisk **OK**, aby rozpocząć tryb failover.
5. Śledź postęp, klikając maszynę wirtualną, aby otworzyć jej właściwości. Można też kliknąć zadanie **test pracy w trybie failover** w obszarze Ustawienia *nazw magazynu*  >  **Settings**  >  **zadania**  > **Site Recovery zadania**.
6. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Sprawdź, czy maszyna wirtualna ma odpowiedni rozmiar, jest połączona z odpowiednią siecią i uruchomiona.
7. Powinno być teraz możliwe nawiązanie połączenia ze zreplikowaną maszyną wirtualną na platformie Azure. [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
8. Aby usunąć maszyny wirtualne platformy Azure utworzone podczas testowego przełączania w tryb failover, kliknij pozycję **Wyczyść test pracy w trybie failover** na maszynie wirtualnej. W obszarze **uwagi**Zapisz wszelkie obserwacje związane z testem pracy w trybie failover.

## <a name="fail-over-and-fail-back"></a>Przechodzenie do trybu failover i powrót po awarii

Po skonfigurowaniu replikacji i przejściu do szczegółów, aby upewnić się, że wszystko działa, możesz przejść do trybu failover na platformie Azure, jeśli jest to wymagane.

Przed uruchomieniem trybu failover, jeśli chcesz nawiązać połączenie z maszyną na platformie Azure po przejściu do trybu failover, [Przygotuj się do nawiązania połączenia](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) przed rozpoczęciem.

Następnie uruchom tryb failover w następujący sposób:


1. W obszarze **Ustawienia**  >  **zreplikowane elementy**kliknij maszynę > **tryb failover**.
2. Wybierz punkt odzyskiwania, którego chcesz użyć.
3. W obszarze **test pracy w trybie failover**wybierz docelową sieć platformy Azure.
4. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. W przypadku tego ustawienia Site Recovery próbuje zamknąć maszynę źródłową przed rozpoczęciem pracy w trybie failover. Jednak tryb failover kontynuuje działanie nawet w przypadku niepowodzenia wyłączenia.
5. Kliknij przycisk **OK**, aby rozpocząć tryb failover. Postęp pracy w trybie failover można wykonać na stronie **zadań** .
6. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Jeśli przygotowano do nawiązania połączenia po przejściu w tryb failover, sprawdź, czy maszyna wirtualna ma odpowiedni rozmiar, jest połączona z odpowiednią siecią i uruchomiona.
7. Po sprawdzeniu maszyny wirtualnej kliknij pozycję **Zatwierdź** , aby zakończyć pracę w trybie failover. Spowoduje to usunięcie wszystkich dostępnych punktów odzyskiwania.

> [!WARNING]
> Nie anuluj trybu failover po rozpoczęciu przełączania: przed uruchomieniem trybu failover replikacja maszyny wirtualnej zostanie zatrzymana. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.


### <a name="fail-back-to-azure-stack"></a>Powrót po awarii do Azure Stack

Gdy lokacja główna zostanie uruchomiona ponownie, możesz wrócić z powrotem z platformy Azure do Azure Stack. Aby to zrobić, wykonaj kroki opisane w [tym miejscu](/azure-stack/operator/site-recovery-failback?view=azs-2005).

## <a name="conclusion"></a>Podsumowanie

W tym artykule zostały zreplikowane Azure Stack maszyny wirtualne na platformie Azure. W przypadku replikacji na miejscu zostało wykonane przechodzenie do trybu failover odzyskiwania po awarii, aby upewnić się, że w systemie Azure działa zgodnie z oczekiwaniami. W tym artykule uwzględniono również procedurę uruchamiania pełnej pracy w trybie failover na platformie Azure i powrotu po awarii do Azure Stack.

## <a name="next-steps"></a>Następne kroki

Po powrocie po awarii można ponownie włączyć ochronę maszyny wirtualnej i rozpocząć replikację na platformę Azure, aby to zrobić, powtórz kroki opisane w tym artykule.