---
title: Migrowanie maszyn wirtualnych usług AWS na platformę Azure za pomocą usługi Azure Site Recovery | Microsoft Docs
description: W tym artykule opisano sposób migrowania maszyn wirtualnych z systemem Windows działających w usługach Amazon Web Services (AWS) na platformę Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 929bc0695bda2e64f77f7e9286e06cee787822ba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80388971"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrowanie maszyn wirtualnych usług Amazon Web Services (AWS) na platformę Azure

W tym samouczku pokazano, jak migrować maszyny wirtualne Amazon Web Services (AWS) do maszyn wirtualnych platformy Azure przy użyciu Azure Site Recovery. Podczas migrowania wystąpień usługi EC2 usług AWS na platformę Azure maszyny wirtualne są traktowane tak, jakby były fizycznymi komputerami lokalnymi. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:


> [!TIP]
> Teraz należy używać usługi Azure Migrate do migrowania maszyn wirtualnych AWS na platformę Azure, a nie usługi Azure Site Recovery. [Dowiedz się więcej](../migrate/tutorial-migrate-physical-virtual-machines.md).


> [!div class="checklist"]
> * Weryfikowanie wymagań wstępnych
> * Przygotowywanie zasobów platformy Azure
> * Przygotowywanie wystąpień usługi EC2 usług AWS do migracji
> * Wdrażanie serwera konfiguracji
> * Włączanie replikacji maszyn wirtualnych
> * Testowanie trybu failover w celu upewnienia się, że wszystkie funkcje działają
> * Uruchamianie jednokrotnego przejścia w tryb failover na platformie Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) .


## <a name="prerequisites"></a>Wymagania wstępne
- Upewnij się, że na maszynach wirtualnych, które mają zostać zmigrowane, jest uruchomiona obsługiwana wersja systemu operacyjnego. Obsługiwane wersje to: 
  - Windows Server 2016 
  - Windows Server 2012 R2
  - Windows Server 2012 
  - 64-bitowa wersja systemu Windows Server 2008 R2 z dodatkiem SP1 lub nowszym
  - Red Hat Enterprise Linux wersja od 6.4 do 6.10, od 7.1 do 7.6 (tylko wystąpienia z wirtualizacją sprzętową) *(Wystąpienia ze sterownikami RedHat PV nie są obsługiwane).*
  - CentOS wersja od 6.4 do 6.10, od 7.1 do 7.6 (tylko wystąpienia z wirtualizacją sprzętową)
 
- Na każdej maszynie wirtualnej, która ma być replikowana, musi być zainstalowana usługa mobilności. 

    > [!IMPORTANT]
    > Usługa Site Recovery automatycznie instaluje tę usługę po włączeniu replikacji dla danej maszyny wirtualnej. Instalacja automatyczna wymaga przygotowania na wystąpieniach usługi EC2 konta, za pomocą którego usługa Site Recovery będzie uzyskiwać dostęp do maszyny wirtualnej. Możesz użyć domeny lub konta lokalnego. 
    > - W przypadku maszyn wirtualnych z systemem Linux konto powinno być użytkownikiem głównym na serwerze źródłowym z systemem Linux. 
    > - W przypadku maszyn wirtualnych z systemem Windows, jeśli nie korzystasz z konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na maszynie lokalnej:
    >
    >      W kluczu rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** dodaj wpis DWORD **LocalAccountTokenFilterPolicy** i ustaw wartość na **1**.

- Oddzielne wystąpienie usługi EC2, którego można używać jako serwera konfiguracji usługi Site Recovery. Na tym wystąpieniu musi być uruchomiony system Windows Server 2012 R2.

## <a name="prepare-azure-resources"></a>Przygotowywanie zasobów platformy Azure

Należy mieć przygotowanych kilka zasobów na platformie Azure do użytku przez zmigrowane wystąpienia usługi EC2, takich jak konto magazynu, magazyn i sieć wirtualna.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Obrazy replikowanych maszyn są przechowywane w usłudze Microsoft Azure Storage. Maszyny wirtualne Azure są tworzone na podstawie magazynu w momencie przejścia w tryb failover ze środowiska lokalnego do platformy Azure.

1. W lewym menu witryny [Azure Portal](https://portal.azure.com) wybierz kolejno opcje **Utwórz zasób** > **Magazyn** > **Konto magazynu**.
2. Wprowadź nazwę konta magazynu. W tych samouczkach użyjemy nazwy **awsmigrated2017**. Nazwa musi:
    - Być unikatowa na platformie Azure
    - Zawierać od 3 do 24 znaków
    - Zawierać tylko cyfry i małe litery
3. Zachowaj wartości domyślne dla ustawień **Model wdrażania**, **Rodzaj konta**, **Wydajność** i **Wymagany bezpieczny transfer**.
5. W polu **Replikacja** wybierz wartość domyślną **RA-GRS**.
6. Wybierz subskrypcję, której chcesz używać w tym samouczku.
7. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**. W tym przykładzie używamy wartości **migrationRG** jako nazwy grupy zasobów.
8. W obszarze **Lokalizacja** wybierz pozycję **Europa Zachodnia**.
9. Wybierz pozycję **Utwórz**, aby utworzyć konto magazynu.

### <a name="create-a-vault"></a>Tworzenie magazynu

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Wszystkie usługi**. Wyszukaj, a następnie wybierz pozycję **Magazyny usług Recovery Services**.
2. Na stronie magazynów usługi Azure Recovery Services wybierz pozycję **Dodaj**.
3. W polu **Nazwa** wprowadź wartość **myVault**.
4. W polu **Subskrypcja** wybierz subskrypcję, która ma być używana.
4. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **migrationRG**.
5. W obszarze **Lokalizacja** wybierz pozycję **Europa Zachodnia**.
5. Aby mieć możliwość szybkiego uzyskania dostępu do nowego magazynu z poziomu pulpitu nawigacyjnego, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.
7. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

Aby wyświetlić nowy magazyn, przejdź do pozycji **pulpit nawigacyjny** > **wszystkie zasoby**. Nowy magazyn będzie wyświetlany także na stronie głównej **Magazyny usług Recovery Services**.

### <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Gdy po migracji (przejściu w tryb failover) zostaną utworzone maszyny wirtualne platformy Azure, zostaną one przyłączone do tej sieci Azure.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz kolejno pozycje **Utwórz zasób** > **Sieć** >
   **Sieć wirtualna**.
3. W polu **Nazwa** wprowadź wartość **myMigrationNetwork**.
4. Pozostaw wartość domyślną **przestrzeni adresowej** (należy wprowadzić wartość).
5. W polu **Subskrypcja** wybierz subskrypcję, która ma być używana.
6. W obszarze **Grupa zasobów**wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **migrationRG**.
7. W obszarze **Lokalizacja** wybierz pozycję **Europa Zachodnia**.
8. W obszarze **podsieć**pozostaw wartości domyślne w polu **Nazwa** i **zakres adresów IP (należy wprowadzić wartość)**.
9. Dodaj instrukcje dotyczące ustawień ochrony DDoS.
10. Pozostaw wyłączoną opcję **Punkty końcowe usługi**.
11. Dodaj instrukcje dotyczące ustawień zapory.
12. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

## <a name="prepare-the-infrastructure"></a>Przygotowywanie infrastruktury

Na stronie swojego magazynu w witrynie Azure Portal w sekcji **Wprowadzenie** wybierz pozycję **Site Recovery**, a następnie wybierz opcję **Przygotowywanie infrastruktury**. Wykonaj poniższe czynności.

### <a name="1-protection-goal"></a>1. Cel ochrony

Na stronie **Cel ochrony** wybierz następujące wartości:

|    |  |
|---------|-----------|
| Gdzie znajdują się maszyny? |Wybierz pozycję **Lokalnie**.|
| Gdzie chcesz zreplikować maszyny? |Wybierz pozycję **Do platformy Azure**.|
| Czy przeprowadzasz migrację? | Wybierz pozycję **tak**, a następnie zaznacz pole wyboru obok pozycji **underdstand, ale chcę kontynuować Azure Site Recovery.**
| Czy maszyny są zwirtualizowane? |Wybierz pozycję **Bez wirtualizacji / inne**.|

Gdy wszystko będzie gotowe, wybierz przycisk **OK**, aby przejść do następnej sekcji.

### <a name="2-select-deployment-planning"></a>2: Wybierz Planowanie wdrożenia

W obszarze **Czy ukończono planowanie wdrożenia** wybierz pozycję **Zrobię to później**, a następnie wybierz opcję **OK**.

### <a name="3-prepare-source"></a>3: przygotowywanie źródła

Na stronie **Przygotowywanie źródła** wybierz pozycję **+ Serwer konfiguracji**.

1. Skorzystaj z wystąpienia usługi EC2 z uruchomionym systemem Windows Server 2012 R2, aby utworzyć serwer konfiguracji i zarejestrować go w magazynie usług odzyskiwania.
2. Skonfiguruj serwer proxy na maszynie wirtualnej wystąpienia usługi EC2, której używasz w roli serwera konfiguracji, aby możliwe było uzyskanie dostępu do [adresów URL usługi](site-recovery-support-matrix-to-azure.md).
3. Pobierz program [Microsoft Azure Site Recovery Unified Setup](https://aka.ms/unifiedinstaller_wus). Możesz pobrać go na maszynę lokalną, a następnie skopiować na maszynę wirtualną, której używasz jako serwera konfiguracji.
4. Wybierz przycisk **Pobierz**, aby pobrać klucz rejestracji magazynu. Skopiuj pobrany plik na maszynę wirtualną, której używasz jako serwera konfiguracji.
5. Na maszynie wirtualnej kliknij prawym przyciskiem myszy pobrany plik instalatora programu Microsoft Azure Site Recovery Unified Setup, a następnie wybierz polecenie **Uruchom jako administrator**.

    1. W obszarze **Przed rozpoczęciem** wybierz pozycję **Zainstaluj serwer konfiguracji i serwer przetwarzania**, a następnie wybierz opcję **Dalej**.
    2. W obszarze **Licencja oprogramowania innej firmy** zaznacz opcję **Akceptuję umowę licencyjną innej firmy**, a następnie wybierz opcję **Dalej**.
    3. W obszarze **Rejestracja** wybierz pozycję **Przeglądaj** i przejdź do lokalizacji, w której umieszczono plik klucza rejestracji magazynu. Wybierz pozycję **Dalej**.
    4. W obszarze **Ustawienia internetowe** wybierz pozycję **Połącz z usługą Azure Site Recovery bez serwera proxy**, a następnie wybierz opcję **Dalej**.
    5. Na stronie **Sprawdzanie wymagań wstępnych** zostanie uruchomione sprawdzanie kilku elementów. Po zakończeniu wybierz opcję **Dalej**.
    6. W obszarze **Konfiguracja programu MySQL** podaj wymagane hasła, a następnie wybierz opcję **Dalej**.
    7. Dla opcji **Szczegóły środowiska** wybierz wartość **Nie**. Maszyny VMware nie wymagają ochrony. Następnie wybierz przycisk **dalej**.
    8. Na stronie **Lokalizacja instalacji** wybierz opcję **Dalej**, aby zaakceptować wartości domyślne.
    9. Na stronie **Wybór sieci** wybierz opcję **Dalej**, aby zaakceptować wartości domyślne.
    10. Na stronie **Podsumowanie** wybierz pozycję **Zainstaluj**.
    11. W obszarze **Postęp instalacji** wyświetlane są informacje dotyczące procesu instalacji. Po zakończeniu wybierz opcję **Zakończ**. Zostanie wyświetlone okno z komunikatem o konieczności ponownego uruchomienia. Wybierz przycisk **OK**. Następnie w oknie zostanie wyświetlony komunikat dotyczący hasła wymaganego do połączenia z serwerem konfiguracji. Skopiuj hasło do schowka i zapisz je w bezpiecznym miejscu.
6. Na maszynie wirtualnej uruchom plik cspsconfigtool.exe, aby utworzyć co najmniej jedno konto zarządzania na serwerze konfiguracji. Upewnij się, że konta zarządzania mają uprawnienia administratora na wystąpieniach usługi EC2, które chcesz zmigrować.

Po zakończeniu konfigurowania serwera konfiguracji wróć do portalu i wybierz utworzony przez siebie serwer jako **Serwer konfiguracji**. Wybierz opcję **OK**, aby przejść do kroku 3: Przygotowywanie celu.

### <a name="4-prepare-target"></a>4: przygotowywanie elementu docelowego

W tej sekcji wprowadzisz informacje dotyczące zasobów utworzonych w sekcji [Przygotowywanie zasobów platformy Azure](#prepare-azure-resources) we wcześniejszej części tego samouczka.

1. W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, której użyto w samouczku [Przygotowywanie platformy Azure](tutorial-prepare-azure.md).
2. Wybierz pozycję **Resource Manager** jako model wdrażania.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure. Powinny to być zasoby utworzone w sekcji [Przygotowywanie zasobów platformy Azure](#prepare-azure-resources) we wcześniejszej części tego samouczka.
4. Gdy skończysz, wybierz opcję **OK**.

### <a name="5-prepare-replication-settings"></a>5: przygotowywanie ustawień replikacji

Aby móc włączyć replikację, musisz najpierw utworzyć zasady replikacji.

1. Wybierz pozycję **Utwórz i skojarz**.
2. W obszarze **Nazwa** wprowadź wartość **myReplicationPolicy**.
3. Pozostaw resztę ustawień domyślnych bez zmian i kliknij przycisk **OK**, aby utworzyć zasady. Nowe zasady zostaną automatycznie skojarzone z serwerem konfiguracji.

Po zakończeniu wszystkich pięciu sekcji w obszarze **Przygotowanie infrastruktury**, wybierz opcję **OK**.

## <a name="enable-replication"></a>Włączanie replikacji

Włącz replikację dla każdej maszyny wirtualnej, która ma zostać zmigrowana. Po włączeniu replikacji usługa Site Recovery automatycznie zainstaluje usługę mobilności.

1. Przejdź do [Azure Portal](https://portal.azure.com).
1. Na stronie swojego magazynu w obszarze **Wprowadzenie** wybierz pozycję **Site Recovery**.
2. W obszarze **Dla maszyn lokalnych i maszyn wirtualnych platformy Azure** wybierz pozycję **Krok 1. Replikowanie aplikacji**. Przejdź do końca przez strony kreatora, używając poniższych informacji. Gdy wszystko będzie gotowe, na każdej stronie wybierz opcję **OK**:
   - 1. Konfigurowanie źródła

     |  |  |
     |-----|-----|
     | Źródło: | Wybierz pozycję **Lokalnie**.|
     | Lokalizacja źródła:| Wprowadź nazwę wystąpienia usługi EC2 Twojego serwera konfiguracji.|
     |Typ maszyny: | Wybierz pozycję **Maszyny fizyczne**.|
     | Serwer przetwarzania: | Wybierz serwer konfiguracji z listy rozwijanej.|

   - 2. Konfigurowanie celu

     |  |  |
     |-----|-----|
     | Cel: | Pozostaw wartość domyślną.|
     | Subskrypcja: | Wybierz subskrypcję, której używasz.|
     | Grupa zasobów po przełączeniu w tryb failover:| Użyj grupy zasobów utworzonej w sekcji [Przygotowywanie zasobów platformy Azure](#prepare-azure-resources).|
     | Model wdrażania po przełączeniu w tryb failover: | Wybierz opcję **Menedżer zasobów**.|
     | Konto magazynu: | Wybierz konto magazynu utworzone w sekcji [Przygotowywanie zasobów platformy Azure](#prepare-azure-resources).|
     | Sieć platformy Azure: | Wybierz pozycję **Konfiguruj teraz dla wybranych maszyn**.|
     | Sieć platformy Azure po przełączeniu w tryb failover: | Wybierz sieć utworzoną w sekcji [Przygotowywanie zasobów platformy Azure](#prepare-azure-resources).|
     | Podsieć: | Wybierz z listy rozwijanej pozycję **domyślna**.|

   - 3. Wybieranie maszyn fizycznych

     Wybierz pozycję **Maszyna fizyczna**, a następnie wprowadź wartości dla pól **Nazwa**, **Adres IP** i **Typ systemu operacyjnego** dotyczące wystąpienia usługi EC2, które ma zostać zmigrowane. Wybierz przycisk **OK**.

   - 4. Konfigurowanie właściwości

     Wybierz konto utworzone na serwerze konfiguracji, a następnie wybierz opcję **OK**.

   - 5. Konfigurowanie ustawień replikacji

     Upewnij się, że wybrane z listy rozwijanej zasady replikacji to zasady **myReplicationPolicy**, a następnie wybierz opcję **OK**.

3. Po zakończeniu pracy kreatora wybierz pozycję **Włącz replikację**.

Aby śledzić postępy zadania **Włącz ochronę**, przejdź do obszaru **Monitorowanie i raporty** > **Zadania** > **Zadania usługi Site Recovery**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.        

Po włączeniu replikacji maszyny wirtualnej zastosowanie zmian i wyświetlenie ich w portalu może potrwać 15 minut lub dłużej.

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Po uruchomieniu próby przejścia w tryb failover występują następujące zdarzenia:

- Uruchamiane jest sprawdzanie wymagań wstępnych, aby upewnić się, że zostały spełnione wszystkie warunki przejścia w tryb failover.
- Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli wybierzesz najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
- Przy użyciu danych przetworzonych w poprzednim kroku utworzona zostaje maszyna wirtualna platformy Azure.

W portalu wykonaj próbę przejścia w tryb failover:

1. Na stronie swojego magazynu przejdź do **pozycji chronione elementy** > **zreplikowane elementy**. Wybierz maszynę wirtualną, a następnie wybierz pozycję **Test pracy w trybie failover**.
2. Wybierz punkt odzyskiwania, którego chcesz użyć podczas pracy w trybie failover:
    - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
    - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
    - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.

3. W obszarze **Test pracy w trybie failover** wybierz sieć platformy Azure, z którą maszyny wirtualne platformy Azure zostaną połączone po przejściu w tryb failover. Powinna to być sieć utworzona w sekcji [Przygotowywanie zasobów platformy Azure](#prepare-azure-resources).
4. Wybierz opcję **OK**, aby rozpocząć tryb failover. Aby śledzić postępy, wybierz maszynę wirtualną w celu wyświetlenia jej właściwości. Możesz również wybrać zadanie **Test pracy w trybie failover** na stronie dla Twojego magazynu. W tym celu wybierz kolejno pozycje **monitorowanie i raporty** > **zadania** >  **Site Recovery zadania**.
5. Po zakończeniu trybu failover w witrynie Azure Portal będzie widoczna replika maszyny wirtualnej platformy Azure. Aby wyświetlić maszynę wirtualną, wybierz opcję **Maszyny wirtualne**. Upewnij się, że maszyna wirtualna ma prawidłowy rozmiar, jest połączona z odpowiednią siecią i jest uruchomiona.
6. Powinno być teraz możliwe nawiązanie połączenia ze zreplikowaną maszyną wirtualną na platformie Azure.
7. Aby usunąć maszyny wirtualne platformy Azure utworzone podczas testu pracy w trybie failover, w planie odzyskiwania wybierz pozycję **Wyczyść test pracy w trybie failover**. W obszarze **uwagi**Zarejestruj i Zapisz wszelkie obserwacje związane z testem pracy w trybie failover.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania. Do ukończenia przetwarzania wymagane jest od 8 do 10 minut.

## <a name="migrate-to-azure"></a>Migracja na platformę Azure

Uruchom rzeczywisty tryb failover dla wystąpień usługi EC2, aby zmigrować je do maszyn wirtualnych platformy Azure:

1. W obszarze **chronione elementy** > **zreplikowane elementy**wybierz wystąpienia AWS, a następnie wybierz pozycję **tryb failover**.
2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, z którego chcesz skorzystać do przełączenia w tryb failover. Wybierz najnowszy punkt odzyskiwania, a następnie uruchom tryb failover. Postęp pracy w trybie failover można wykonać na stronie **zadań** .
1. Upewnij się, że maszyna wirtualna jest wyświetlana w obszarze **Zreplikowane elementy**.
2. Kliknij prawym przyciskiem myszy każdą maszynę wirtualną, a następnie wybierz polecenie **Zakończ migrację**. Spowoduje to wykonanie następujących czynności:

   - Spowoduje to zakończenie procesu migracji, zatrzymanie replikacji maszyny wirtualnej usług AWS oraz zatrzymanie naliczania opłat za usługę Site Recovery dla maszyny wirtualnej.
   - W tym kroku oczyszczane są dane replikacji. Nie są jednak usuwane migrowane maszyny wirtualne. 

     ![Kończenie migracji](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> *Nie anuluj trybu failover, który jest w toku*. Przed rozpoczęciem pracy w trybie failover zatrzymywana jest replikacja maszyny wirtualnej. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie on zatrzymany, ale maszyna wirtualna nie zostanie ponownie zreplikowana.  


## <a name="next-steps"></a>Następne kroki

W tym artykule opisano migrowanie wystąpień usługi EC2 usług AWS do maszyn wirtualnych platformy Azure. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczków dotyczących maszyn wirtualnych z systemem Windows.

> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Windows](../virtual-machines/windows/tutorial-manage-vm.md)
