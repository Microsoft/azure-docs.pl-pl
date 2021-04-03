---
title: Przenoszenie maszyn wirtualnych platformy Azure między regionami rządowymi i publicznymi przy użyciu Azure Site Recovery
description: Użyj Azure Site Recovery, aby przenieść maszyny wirtualne platformy Azure między rządem i regionami publicznymi platformy Azure.
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: a76ebf95b92b6e1251a04daa9ffb48a9abe15b50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89425351"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Przenoszenie maszyn wirtualnych platformy Azure między usługą Azure Government i regionami publicznymi 

Możesz chcieć przenieść maszyny wirtualne IaaS między Azure Governmentami i publicznymi regionami, aby zwiększyć dostępność istniejących maszyn wirtualnych, poprawić możliwości zarządzania lub z powodów związanych z zarządzaniem, jak [opisano tutaj](azure-to-azure-move-overview.md).

Oprócz używania usługi [Azure Site Recovery](site-recovery-overview.md) do zarządzania odzyskiwaniem maszyn lokalnych i maszyn wirtualnych platformy Azure po awarii oraz ich organizowania dla celów zapewniania ciągłości działania i odzyskiwania po awarii (BCDR, business continuity and disaster recovery) można ją stosować również do zarządzania przenoszeniem maszyn wirtualnych platformy Azure do regionu pomocniczego.       

W tym samouczku pokazano, jak przenieść maszyny wirtualne platformy Azure między Azure Government a publicznymi regionami przy użyciu Azure Site Recovery. Ten sam można rozszerzyć, aby przenieść maszyny wirtualne między parami regionów, które nie znajdują się w tym samym klastrze geograficznym. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Weryfikacja wymagań wstępnych
> * Przygotowywanie źródłowych maszyn wirtualnych
> * Przygotowywanie regionu docelowego
> * Kopiowanie danych do regionu docelowego
> * Testowanie konfiguracji
> * Przenoszenie
> * Odrzucanie zasobów w regionie źródłowym

> [!IMPORTANT]
> W tym samouczku pokazano, jak przenieść maszyny wirtualne platformy Azure między Azure Governmentami i publicznymi regionami lub między parami regionów, które nie są obsługiwane przez zwykłe rozwiązanie do odzyskiwania po awarii dla maszyn wirtualnych platformy Azure. W przypadku, gdy pary regionów źródłowej i docelowej są [obsługiwane](./azure-to-azure-support-matrix.md#region-support), zapoznaj się z tym [dokumentem](azure-to-azure-tutorial-migrate.md) , aby przenieść. Jeśli wymagasz poprawy dostępności przez przeniesienie maszyn wirtualnych w zestawie dostępności do obszaru maszyny wirtualne przypięte do strefy w innym regionie, zapoznaj się z samouczkiem [tutaj](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Nie zaleca się korzystania z tej metody, aby skonfigurować odzyskiwanie po awarii między nieobsługiwanymi parami regionów, ponieważ pary są zdefiniowane, zachowując opóźnienie danych, co jest niezwykle ważne w przypadku scenariusza odzyskiwania po awarii.

## <a name="verify-prerequisites"></a>Weryfikacja wymagań wstępnych

> [!NOTE]
> Upewnij się, że rozumiesz [architekturę i składniki](physical-azure-architecture.md) w tym scenariuszu. Ta architektura będzie służyć do przenoszenia maszyn wirtualnych platformy Azure, **traktując maszyny wirtualne jako serwery fizyczne**.

- Zapoznaj się z wymaganiami dotyczącymi [obsługi](vmware-physical-secondary-support-matrix.md) wszystkich składników.
- Upewnij się, że serwery, które chcesz replikować, są zgodne z [wymaganiami maszyny wirtualnej platformy Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Przygotuj konto do automatycznej instalacji usługi mobilności na każdym serwerze, który chcesz replikować.

- Pamiętaj, że po przełączeniu w tryb failover do regionu docelowego na platformie Azure nie można bezpośrednio przeprowadzić powrotu po awarii do regionu źródłowego. Należy ponownie skonfigurować replikację do celu.

### <a name="verify-azure-account-permissions"></a>Weryfikowanie uprawnień konta platformy Azure

Upewnij się, że Twoje konto platformy Azure ma uprawnienia do replikacji maszyn wirtualnych na platformę Azure.

- Przejrzyj [uprawnienia](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) wymagane do replikowania maszyn na platformę Azure.
- Weryfikowanie i modyfikowanie uprawnień platformy [Azure kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md) . 

### <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Skonfiguruj docelową [Sieć platformy Azure](../virtual-network/quick-create-portal.md).

- Maszyny wirtualne platformy Azure są umieszczane w tej sieci, gdy są tworzone po przejściu w tryb failover.
- Sieć powinna znajdować się w tym samym regionie co magazyn Recovery Services


### <a name="set-up-an-azure-storage-account"></a>Konfigurowanie konta usługi Azure Storage

Skonfiguruj [konto usługi Azure Storage](../storage/common/storage-account-create.md).

- Site Recovery replikuje maszyny lokalne do usługi Azure Storage. Maszyny wirtualne platformy Azure są tworzone na podstawie magazynu po przejściu do trybu failover.
- Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.


## <a name="prepare-the-source-vms"></a>Przygotowywanie źródłowych maszyn wirtualnych

### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Na każdym serwerze, który ma zostać zreplikowany, musi być zainstalowana usługa mobilności. Site Recovery automatycznie instaluje tę usługę po włączeniu replikacji dla serwera. Aby automatycznie zainstalować program, należy przygotować konto, które będzie używane przez Site Recovery do uzyskiwania dostępu do serwera.

- Możesz użyć domeny lub konta lokalnego
- W przypadku maszyn wirtualnych z systemem Windows, jeśli nie korzystasz z konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na komputerze lokalnym. W tym celu w rejestrze w obszarze **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** Dodaj wpis DWORD **LocalAccountTokenFilterPolicy** o wartości 1.
- Aby dodać wpis rejestru w celu wyłączenia ustawienia z interfejsu wiersza polecenia, wpisz:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- W przypadku systemu Linux konto powinno być kontem głównym na źródłowym serwerze z systemem Linux.


## <a name="prepare-the-target-region"></a>Przygotowywanie regionu docelowego

1. Sprawdź, czy Twoja subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym używanym do odzyskiwania po awarii. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

2. Upewnij się, że zasoby subskrypcji są wystarczające do obsługi maszyn wirtualnych o rozmiarach odpowiadających źródłowym maszynom wirtualnym. Jeśli dane są kopiowane do miejsca docelowego przy użyciu usługi Site Recovery, usługa sama wybiera ten sam lub najbardziej zbliżony rozmiar dla docelowej maszyny wirtualnej.

3. Upewnij się, że utworzono zasób docelowy dla każdego składnika zidentyfikowanego w układzie sieci źródłowej. Jest to ważne, aby upewnić się, że po przeniesieniu do docelowego obszaru maszyny wirtualne mają wszystkie funkcje i funkcje, które były dostępne w źródle.

    > [!NOTE]
    > Usługa Azure Site Recovery automatycznie odnajduje i tworzy sieć wirtualną po włączeniu replikacji dla źródłowej maszyny wirtualnej. Można również wstępnie utworzyć sieć i przypisać ją do maszyny wirtualnej w ramach czynności włączania replikacji. Jednak pozostałe zasoby należy utworzyć ręcznie w regionie docelowym, jak wspomniano poniżej.

     Skorzystaj z poniższych dokumentów, aby utworzyć odpowiednie najczęściej używane zasoby sieciowe, na podstawie konfiguracji źródłowej maszyny wirtualnej.

    - [Grupy zabezpieczeń sieci](../virtual-network/manage-network-security-group.md)
    - [Moduły równoważenia obciążenia](../load-balancer/index.yml)
    - [Publiczny adres IP](../virtual-network/virtual-network-public-ip-address.md)
    
    Wszystkie inne składniki sieci można znaleźć w [dokumentacji](../index.yml?pivot=products&panel=network)dotyczącej sieci.

4. Ręcznie [utwórz sieć nieprodukcyjną](../virtual-network/quick-create-portal.md) w regionie docelowym, jeśli chcesz przetestować konfigurację przed wykonaniem końcowej migracji do regionu docelowego. Ogranicza to do minimum zaburzenia działania środowiska produkcyjnego i jest zalecane.

## <a name="copy-data-to-the-target-region"></a>Kopiowanie danych do regionu docelowego
Poniżej przedstawiono procedurę kopiowania danych do regionu docelowego przy użyciu usługi Azure Site Recovery.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Magazyn można utworzyć w dowolnym regionie, z wyjątkiem regionu źródłowego.

1. Zaloguj się do Recovery Services [Azure Portal](https://portal.azure.com)  >  .
2. Kliknij pozycję **Utwórz zasoby**  >  **Narzędzia do zarządzania** zasobami  >  **i Site Recovery**.
3. W polu **Nazwa** podaj przyjazną nazwę **ContosoVMVault**. Jeśli masz więcej niż jedną subskrypcję, wybierz odpowiednią z nich.
4. Utwórz grupę zasobów **ContosoRG**.
5. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz sekcję dotyczącą dostępności geograficznej w temacie [Szczegóły cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. W obszarze magazyny Recovery Services kliknij pozycję **Przegląd**  >  **ConsotoVMVault**  >  **+ replikacja**
7. Wybierz **platformę Azure, która**  >  **nie jest Zwirtualizowana/inna**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Skonfiguruj serwer konfiguracji w celu odnajdywania maszyn wirtualnych.


Skonfiguruj serwer konfiguracji, zarejestruj go w magazynie i odnajdź maszyny wirtualne.

1. Kliknij pozycję **Site Recovery**  >  **Przygotuj**  >  **Źródło** infrastruktury.
2. Jeśli nie masz serwera konfiguracji, kliknij przycisk **+ serwer konfiguracji**.
3. W obszarze **Dodawanie serwera** Sprawdź, czy **serwer konfiguracji** jest wyświetlany w polu **Typ serwera**.
4. Pobierz plik instalacyjny programu Site Recovery Unified Setup.
5. Pobierz klucz rejestracji magazynu. Jest on potrzebny po uruchomieniu ujednoliconej konfiguracji. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

   ![Konfiguracja źródła](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Rejestrowanie serwera konfiguracji w magazynie

Wykonaj następujące czynności przed rozpoczęciem: 

#### <a name="verify-time-accuracy"></a>Sprawdź dokładność czasu
Na komputerze serwera konfiguracji upewnij się, że zegar systemowy jest zsynchronizowany z [serwerem czasu](/windows-server/networking/windows-time-service/windows-time-service-top). Powinien być zgodny. Jeśli jest to 15 minut przed lub za, instalacja może zakończyć się niepowodzeniem.

#### <a name="verify-connectivity"></a>Sprawdź łączność
Upewnij się, że komputer może uzyskać dostęp do tych adresów URL w oparciu o środowisko: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Reguły zapory oparte na adresach IP powinny zezwalać na komunikację ze wszystkimi adresami URL platformy Azure wymienionymi powyżej za pośrednictwem portu HTTPS (443). Aby uprościć i ograniczyć zakres adresów IP, zalecane jest filtrowanie adresów URL.

- **Komercyjne adresy** IP — Zezwalaj na [przedziały adresów IPv4 centrum danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)oraz port HTTPS (443). Zezwalaj na zakresy adresów IP dla regionu platformy Azure Twojej subskrypcji, aby obsługiwać adresy URL usługi AAD, kopii zapasowej, replikacji i magazynu.  
- **Adresy IP instytucji rządowych** — zezwalaj na [Azure Government zakresy adresów URL centrum](https://www.microsoft.com/en-us/download/details.aspx?id=57063)danych oraz port HTTPS (443) dla wszystkich regionów USGov (Wirginia, Texas, Arizona i Iowa) do obsługi usługi AAD, kopii zapasowych, replikacji i adresów URL magazynu.  

#### <a name="run-setup"></a>Uruchamianie instalatora
Uruchom ujednoliconą konfigurację jako administrator lokalny, aby zainstalować serwer konfiguracji. Serwer przetwarzania oraz główny serwer docelowy są również instalowane domyślnie na serwerze konfiguracji.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Po zakończeniu rejestracji serwer konfiguracji zostanie wyświetlony na stronie **Ustawienia**  >  **serwery** w magazynie.

### <a name="configure-target-settings-for-replication"></a>Skonfiguruj ustawienia docelowe dla replikacji

Wybierz i zweryfikuj zasoby docelowe.

1. Kliknij pozycję **Przygotuj**  >  **miejsce docelowe** infrastruktury i wybierz subskrypcję platformy Azure, której chcesz użyć.
2. Określ docelowy model wdrażania.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.

   ![Cel](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. Aby utworzyć nowe zasady replikacji, kliknij kolejno pozycje **Site Recovery infrastruktura**  >  **zasady replikacji**  >  **+ zasady replikacji**.
2. W obszarze **Tworzenie zasad replikacji** określ nazwę zasad.
3. W obszarze **Wartość progowa celu punktu odzyskiwania** określ limit celu punktu odzyskiwania. Ta wartość określa, jak często są tworzone punkty odzyskiwania danych. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
4. W obszarze **Przechowywanie punktu odzyskiwania** określ (w godzinach), jak długie jest okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie. Przechowywanie do 24 godzin jest obsługiwane dla maszyn replikowanych do magazynu w warstwie Premium, zaś do 72 godzin dla magazynu w warstwie Standardowa.
5. W obszarze **częstotliwość tworzenia migawek** na poziomie aplikacji Określ, jak często (w minutach) będą tworzone punkty odzyskiwania zawierające migawki spójne z aplikacjami. Kliknij przycisk **OK**, aby utworzyć zasady.

    ![Zasady replikacji](./media/physical-azure-disaster-recovery/replication-policy.png)


Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. Domyślnie są też automatycznie tworzone odpowiadające im zasady powrotu po awarii. Na przykład jeśli zasady replikacji są typu **"przedstawiciel"** , zostanie utworzona zasada powrotu po awarii nr **przedstawiciel-zasady-powrót** po awarii. Te zasady są używane wyłącznie po zainicjowaniu powrotu po awarii z platformy Azure.

### <a name="enable-replication"></a>Włączanie replikacji

- Site Recovery zainstaluje usługę mobilności po włączeniu replikacji.
- Po włączeniu replikacji dla serwera może upłynąć 15 minut lub dłużej, aby zmiany zaczęły obowiązywać i pojawiają się w portalu.

1. Kliknij pozycję **Replikuj**  >  **Źródło** aplikacji.
2. W obszarze **Źródło** wybierz serwer konfiguracji.
3. W obszarze **Typ maszyny** wybierz pozycję **maszyny fizyczne**.
4. Wybierz serwer przetwarzania (serwer konfiguracji). Następnie kliknij przycisk **OK**.
5. W obszarze **cel** wybierz subskrypcję i grupę zasobów, w której chcesz utworzyć maszyny wirtualne platformy Azure po przejściu do trybu failover. Wybierz model wdrażania, którego chcesz użyć na platformie Azure (wersja klasyczna lub zarządzanie zasobami).
6. Wybierz konto usługi Azure Storage, którego chcesz użyć na potrzeby replikacji danych. 
7. Wybierz sieć platformy Azure i podsieć, z którą nawiążą połączenie maszyny wirtualne platformy Azure, gdy zostaną uruchomione po przejściu do trybu failover.
8. Wybierz pozycję **Konfiguruj teraz dla wybranych maszyn**, aby zastosować ustawienia sieci do wszystkich maszyn wybranych do ochrony. Wybierz pozycję **Konfiguruj później** , aby wybrać sieć platformy Azure na maszynę. 
9. W obszarze **maszyny fizyczne** i kliknij pozycję **+ maszyna fizyczna**. Określ nazwę i adres IP. Wybierz system operacyjny maszyny, którą chcesz replikować. Odnajdywanie i wykrycie serwerów może potrwać kilka minut. 

   > [!WARNING]
   > Należy wprowadzić adres IP maszyny wirtualnej platformy Azure, która ma zostać przeniesiona

10. W obszarze **Właściwości**  >  **Konfigurowanie właściwości** wybierz konto, które będzie używane przez serwer przetwarzania w celu automatycznego zainstalowania usługi mobilności na maszynie.
11. W obszarze **Ustawienia replikacji**  >  **Skonfiguruj ustawienia replikacji** Sprawdź, czy wybrano odpowiednie zasady replikacji. 
12. Kliknij pozycję **Włącz replikację**. Postęp zadania **Włącz ochronę** można śledzić w obszarze **Ustawienia**  >  **zadania**  >  **Site Recovery zadania**. Po uruchomieniu zadania **finalizowania ochrony** maszyna jest gotowa do pracy w trybie failover.


Aby monitorować dodawane serwery, można sprawdzić czas ostatniego wykrycia dla nich w obszarze **serwery konfiguracji**  >  **ostatni kontakt na stronie**. Aby dodać maszyny bez oczekiwania na zaplanowaną godzinę odnajdywania, wyróżnij serwer konfiguracji (nie klikaj go), a następnie kliknij przycisk **Odśwież**.

## <a name="test-the-configuration"></a>Testowanie konfiguracji


1. Przejdź do magazynu, w obszarze **Ustawienia**  >  **zreplikowane elementy** kliknij maszynę wirtualną, która ma zostać przeniesiona do regionu docelowego, a następnie kliknij ikonę **test pracy w trybie failover** .
2. W obszarze **Test pracy w trybie failover** wybierz punkt odzyskiwania, którego chcesz użyć podczas pracy w trybie failover:

   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.

3. Wybierz docelową sieć wirtualną platformy Azure, do której chcesz przenieść maszyny wirtualne platformy Azure, aby przetestować konfigurację. 

   > [!IMPORTANT]
   > Zalecamy używanie oddzielnej sieci maszyn wirtualnych platformy Azure do testowania pracy w trybie failover, a nie sieci produkcyjnej, w której chcesz przenieść maszyny wirtualne ostatecznie skonfigurowane po włączeniu replikacji.

4. Aby rozpocząć testowanie przenoszenia, kliknij przycisk **OK**. Aby śledzić postępy, kliknij maszynę wirtualną w celu otwarcia jej właściwości. Możesz też kliknąć zadanie **Test pracy w trybie failover** w obszarze nazwy magazynu > **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
6. Aby usunąć maszynę wirtualną utworzoną podczas testowania przeniesienia, kliknij pozycję **Wyczyść test pracy w trybie failover** replikowanego elementu. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Wykonaj przeniesienie do regionu docelowego i potwierdź.

1. Przejdź do magazynu, w obszarze **Ustawienia**  >  **zreplikowane elementy**, kliknij maszynę wirtualną, a następnie kliknij pozycję **tryb failover**.
2. W obszarze **Tryb failover** wybierz pozycję **Najnowsze**. 
3. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłową maszynę wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy w trybie failover można wykonać na stronie **zadań** . 
4. Po ukończeniu zadania sprawdź, czy maszyna wirtualna jest wyświetlana w regionie docelowym platformy Azure zgodnie z oczekiwaniami.
5. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie kliknij pozycję **Zatwierdź**. Spowoduje to zakończenie procesu przenoszenia do regionu docelowego. Zaczekaj na ukończenie zadania zatwierdzania.

## <a name="discard-the-resource-in-the-source-region"></a>Odrzucanie zasobu w regionie źródłowym 

- Przejdź do maszyny wirtualnej.  Kliknij pozycję **Wyłącz replikację**.  Spowoduje to zatrzymanie procesu kopiowania danych maszyny wirtualnej.  

   > [!IMPORTANT]
   > Wykonanie powyższego kroku jest ważne w celu uniknięcia naliczenia opłaty za replikację przy użyciu usługi ASR.

Jeśli nie zamierzasz już używać zasobów źródłowych, przejdź do następnego zestawu kroków.

1. Usuń wszystkie odpowiednie zasoby sieciowe w regionie źródłowym wyszczególnione w kroku 4 w sekcji [Przygotowywanie źródłowych maszyn wirtualnych](#prepare-the-source-vms) 
2. Usuń odpowiednie konto magazynu w regionie źródłowym.



## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono maszynę wirtualną platformy Azure do innego regionu świadczenia usługi Azure. Teraz możesz skonfigurować odzyskiwanie po awarii dla przeniesionej maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii po migracji](azure-to-azure-quickstart.md)
