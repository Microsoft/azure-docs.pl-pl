---
title: Migrowanie maszyn wirtualnych VMware przy użyciu migracji Azure Migrate serwera opartego na agentach
description: Dowiedz się, jak uruchomić migrację maszyn wirtualnych VMware z użyciem agentów przy użyciu Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: 480051680ea98b82627a9a2b3ea004d9472f7124
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797098"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrowanie maszyn wirtualnych VMware na platformę Azure (oparte na agentach)

W tym artykule opisano sposób migrowania lokalnych maszyn wirtualnych programu VMware na platformę Azure przy użyciu narzędzia do [migracji Azure Migrate: serwera](migrate-services-overview.md#azure-migrate-server-migration-tool) z migracją opartą na agentach.  Można również migrować maszyny wirtualne VMware przy użyciu migracji bez agentów. [PORÓWNAJ](server-migrate-overview.md#compare-migration-methods) metody.


 Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> * Przygotuj platformę Azure do pracy z Azure Migrate.
> * Przygotuj się do migracji opartej na agencie. Skonfiguruj konto VMware, aby Azure Migrate mogły odnajdywać maszyny do migracji. Skonfiguruj konto, aby Agent usługi mobilności mógł zostać zainstalowany na maszynach, które mają zostać poddane migracji, i przygotuj komputer do działania jako urządzenie replikacji.
> * Dodawanie Azure Migrate: Narzędzia migracji serwera
> * Skonfiguruj urządzenie do replikacji.
> * Replikowanie maszyn wirtualnych.
> * Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację na platformę Azure.

> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Jeśli to możliwe, samouczki używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem [Przejrzyj](./agent-based-migration-architecture.md) architekturę migracji opartą na agencie VMware.

## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Wykonaj zadania w tabeli, aby przygotować platformę Azure do migracji opartej na agentach.

**Zadanie** | **Szczegóły**
--- | ---
**Tworzenie projektu usługi Azure Migrate** | Twoje konto platformy Azure wymaga uprawnień współautora lub właściciela do utworzenia projektu.
**Weryfikowanie uprawnień konta platformy Azure** | Twoje konto platformy Azure wymaga uprawnień do utworzenia maszyny wirtualnej i zapisu na dysku zarządzanym platformy Azure.
**Konfiguracja sieci platformy Azure** | Skonfiguruj sieć, do której będą dołączane maszyny wirtualne platformy Azure po migracji.

### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu
Jeśli nie masz projektu Azure Migrate, Sprawdź uprawnienia, aby je utworzyć.


1. W witrynie Azure Portal otwórz daną subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)** .
2. W obszarze **Sprawdź dostęp** Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Sprawdź, czy masz uprawnienia **współautora** lub **właściciela** .

    - Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, poproś właściciela o przypisanie roli.
    
### <a name="assign-azure-account-permissions"></a>Przypisywanie uprawnień konta platformy Azure

Przypisz rolę współautora maszyny wirtualnej do konta, aby mieć uprawnienia do:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapisz na dysku zarządzanym platformy Azure. 


### <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

[Skonfiguruj sieć platformy Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Maszyny lokalne są replikowane do usługi Azure Managed Disks. Po przełączeniu w tryb failover na platformie Azure na potrzeby migracji maszyny wirtualne platformy Azure są tworzone na podstawie tych dysków zarządzanych i przyłączone do skonfigurowanej sieci platformy Azure.

## <a name="prepare-for-migration"></a>Przygotowanie do migracji

Sprawdź wymagania i uprawnienia dotyczące pomocy technicznej i przygotuj się do wdrożenia urządzenia replikacji. 

### <a name="prepare-an-account-to-discover-vms"></a>Przygotowywanie konta do odnajdywania maszyn wirtualnych

Migracja serwera Azure Migrate wymaga dostępu do serwerów VMware w celu odnalezienia maszyn wirtualnych, które mają zostać zmigrowane. Utwórz konto w następujący sposób:

1. Aby użyć dedykowanego konta, utwórz rolę na poziomie vCenter. Nadaj roli nazwę, taką jak **Azure_Migrate**.
2. Przypisz do roli uprawnienia podsumowane w poniższej tabeli.
3. Utwórz użytkownika na serwerze vCenter lub hoście vSphere. Przypisz tę rolę temu użytkownikowi.

#### <a name="vmware-account-permissions"></a>Uprawnienia konta VMware

**Zadanie** | **Rola/uprawnienia** | **Szczegóły**
--- | --- | ---
**Odnajdowanie maszyn wirtualnych** | Co najmniej użytkownik tylko do odczytu<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z obiektem **Propaguj do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).
**Replikacja** |  Utwórz rolę (Azure_Site_Recovery) z wymaganymi uprawnieniami, a następnie przypisz ją użytkownikowi lub grupie VMware<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = Azure_Site_Recovery<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z obiektem **Propaguj do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, VMsa, sieci ND).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługę Mobility należy zainstalować na każdej maszynie, która ma być replikowana.

- Urządzenie replikacji Azure Migrate może przeprowadzić instalację wypychaną tej usługi po włączeniu replikacji dla maszyny lub można ją zainstalować ręcznie lub za pomocą narzędzi instalacyjnych.
- W tym samouczku zamierzamy zainstalować usługę Mobility przy użyciu instalacji typu push.
- W przypadku instalacji wypychanej należy przygotować konto, za pomocą którego Migracja serwera Azure Migrate może być używana do uzyskiwania dostępu do maszyny wirtualnej. To konto jest używane tylko w przypadku instalacji wypychanej, jeśli usługa mobilności nie zostanie zainstalowana ręcznie.

Przygotuj konto w następujący sposób:

1. Przygotuj domenę lub konto lokalne z uprawnieniami do instalowania na maszynie wirtualnej.
2. W przypadku maszyn wirtualnych z systemem Windows, jeśli nie korzystasz z konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na maszynie lokalnej przez dodanie wpisu DWORD **LocalAccountTokenFilterPolicy** z wartością w rejestrze w obszarze **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. W przypadku maszyn wirtualnych z systemem Linux Przygotuj konto główne na źródłowym serwerze z systemem Linux.


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Przygotuj komputer do urządzenia replikacji

Urządzenie jest używane do replikacji maszyn na platformę Azure. Urządzenie jest jedną maszyną wirtualną VMware o wysokiej dostępności, która obsługuje te składniki:

- **Serwer konfiguracji**: serwer konfiguracji koordynuje komunikację między środowiskiem lokalnym i platformą Azure oraz zarządza replikacją danych.
- **Serwer przetwarzania**: serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do konta magazynu pamięci podręcznej na platformie Azure. Serwer przetwarzania instaluje również agenta usługi mobilności na maszynach wirtualnych, które mają być replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.

Przygotuj urządzenie w następujący sposób:

- Zapoznaj się z [wymaganiami dotyczącymi urządzeń](migrate-replication-appliance.md#appliance-requirements). Ogólnie rzecz biorąc, należy skonfigurować urządzenie do replikacji maszyny wirtualnej VMware przy użyciu pobranego pliku komórek jajowych. Szablon tworzy urządzenie, które jest zgodne ze wszystkimi wymaganiami.
- Na urządzeniu musi być zainstalowany pakiet MySQL. [Przejrzyj](migrate-replication-appliance.md#mysql-installation) metody instalacji.
- Przejrzyj [adresy URL chmury publicznej](migrate-replication-appliance.md#url-access)i [adresy URL Azure Government](migrate-replication-appliance.md#azure-government-url-access) , do których komputer musi mieć dostęp.
- [Zapoznaj się z portami](migrate-replication-appliance.md#port-access) , do których maszyna replikacji musi uzyskać dostęp.



### <a name="check-vmware-requirements"></a>Sprawdzanie wymagań dotyczących programu VMware

Upewnij się, że serwery VMware i maszyny wirtualne spełniają wymagania dotyczące migracji na platformę Azure. 

1. [Zweryfikuj](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) wymagania dotyczące serwerów VMware.
2. [Sprawdź](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) Wymagania maszyny wirtualnej dotyczące migracji.
3. Sprawdź ustawienia platformy Azure. Lokalne maszyny wirtualne replikowane na platformę Azure muszą być zgodne z [wymaganiami maszyny wirtualnej platformy Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).
4. Przed przeprowadzeniem migracji na platformę Azure potrzebne są pewne zmiany na maszynach wirtualnych.
    - Ważne jest, aby wprowadzić te zmiany przed rozpoczęciem migracji. W przypadku migrowania maszyny wirtualnej przed wprowadzeniem zmiany, maszyna wirtualna może nie zostać uruchomiona na platformie Azure.
    - Przejrzyj zmiany w [systemach Windows](prepare-for-migration.md#windows-machines) i [Linux](prepare-for-migration.md#linux-machines) , które należy wprowadzić.

> [!NOTE]
> Migracja oparta na agencie z migracją Azure Migrate Server jest oparta na funkcjach Azure Site Recovery usługi. Niektóre wymagania mogą być połączone z dokumentacją Site Recovery.

## <a name="set-up-the-replication-appliance"></a>Konfigurowanie urządzenia do replikacji

W tej procedurze opisano sposób konfigurowania urządzenia przy użyciu pobranego szablonu Open Virtualization Application (komórki jajowe). Jeśli nie można użyć tej metody, można skonfigurować urządzenie [przy użyciu skryptu](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). 

### <a name="download-the-replication-appliance-template"></a>Pobierz szablon urządzenia replikacji

Pobierz szablon w następujący sposób:

1. W projekcie Azure Migrate kliknij pozycję **serwery** w obszarze **cele migracji**.
2. W **Azure Migrate serwery**  >  **Azure Migrate: Migracja serwera**, kliknij przycisk **odkryj**.

    ![Odnajdywanie maszyn wirtualnych](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. W obszarze **odnajdywanie** maszyn  >  **są zwirtualizowane maszyny?** kliknij przycisk **tak, za pomocą funkcji hypervisor VMware vSphere**.
4. W **jaki sposób chcesz przeprowadzić migrację?** wybierz pozycję **przy użyciu replikacji opartej na agentach**.
5. W **obszarze region docelowy** wybierz region świadczenia usługi Azure, do którego chcesz przeprowadzić migrację maszyn.
6. Wybierz pozycję **Potwierdź, że docelowy region migracji to nazwa regionu**.
7. Kliknij pozycję **Utwórz zasoby**. Spowoduje to utworzenie magazynu Azure Site Recovery w tle. Po kliknięciu tego przycisku nie można zmienić regionu docelowego dla tego projektu, a wszystkie kolejne migracje są do tego regionu.

    ![Tworzenie magazynu usługi Recovery Services](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. W **czy chcesz zainstalować nowe urządzenie do replikacji?** wybierz pozycję **Zainstaluj urządzenie replikacji**.
9. Kliknij pozycję **Pobierz**. Spowoduje to pobranie szablonu OVF.
    ![Pobierz komórki jajowe](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Zanotuj nazwę grupy zasobów i magazynu Recovery Services. Są one potrzebne podczas wdrażania urządzenia.


### <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware

Po pobraniu szablonu OVF należy zaimportować go do programu VMware, aby utworzyć aplikację replikacji na maszynie wirtualnej VMware z systemem Windows Server 2016.

1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **plik** wybierz polecenie **Wdróż szablon OVF** , aby uruchomić **Kreatora wdrażania szablonu OVF**. 
3. W obszarze **Wybierz źródło** wprowadź lokalizację pobranego OVF.
4. W obszarze **szczegóły przeglądu** wybierz pozycję **dalej**.
5. W obszarze **Wybierz nazwę i folder** i **Wybierz pozycję Konfiguracja**, zaakceptuj ustawienia domyślne.
6. W obszarze **Wybieranie magazynu**  >  **Wybierz pozycję Format dysku wirtualnego**, aby uzyskać najlepszą wydajność, wybierz opcję **grube udostępnianie eager zero**.
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. Aby skonfigurować maszynę wirtualną z ustawieniami domyślnymi **, wybierz** pozycję **Włącz po**  >  **zakończeniu** wdrażania.

   > [!TIP]
   > Jeśli chcesz dodać dodatkową kartę sieciową, usuń zaznaczenie pola wyboru **Włącz po**  >  **zakończeniu** wdrażania. Domyślnie szablon zawiera jedną kartę sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.

### <a name="start-appliance-setup"></a>Uruchom konfigurację urządzenia

1. W konsoli klienta VMware vSphere Włącz maszynę wirtualną. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016.
2. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się do maszyny wirtualnej jako administrator przy użyciu hasła administratora. Gdy logujesz się po raz pierwszy, narzędzie konfiguracji urządzenia do replikacji (Narzędzie konfiguracji Azure Site Recovery) rozpocznie się w ciągu kilku sekund.
5. Wprowadź nazwę, która ma zostać użyta do zarejestrowania urządzenia z migracją serwera. Następnie kliknij przycisk **Dalej**.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure.
7. Zaczekaj, aż narzędzie zakończy rejestrowanie aplikacji usługi Azure AD w celu zidentyfikowania urządzenia. Urządzenie zostanie ponownie uruchomione.
1. Ponownie zaloguj się do maszyny. W ciągu kilku sekund zostanie automatycznie uruchomiony kreator zarządzania serwerem konfiguracji.

### <a name="register-the-replication-appliance"></a>Rejestrowanie urządzenia replikacji

Zakończ Konfigurowanie i rejestrowanie urządzenia replikacji.

1. W obszarze Konfiguracja urządzenia wybierz pozycję **Instalacja łączność**.
2. Wybierz kartę sieciową (domyślnie istnieje tylko jedna karta sieciowa), którą urządzenie replikacji używa do odnajdywania maszyn wirtualnych, a następnie wykonaj instalację wypychaną usługi mobilności na maszynach źródłowych.
3. Wybierz kartę sieciową używaną przez urządzenie do replikacji na potrzeby łączności z platformą Azure. Następnie wybierz pozycję **Zapisz**. Po skonfigurowaniu tego ustawienia nie można go zmienić.
4. Jeśli urządzenie znajduje się za serwerem proxy, należy określić ustawienia serwera proxy.
    - Określ nazwę serwera proxy jako **http://ip-address** lub **http://FQDN** . Serwery proxy HTTPS nie są obsługiwane.
5. Po wyświetleniu monitu o subskrypcję, grupy zasobów i szczegóły magazynu należy dodać szczegóły zanotowane podczas pobraniu szablonu urządzenia.
6. Na stronie **Instalowanie oprogramowania innych firm** zaakceptuj umowę licencyjną. Wybierz pozycję **Pobierz i zainstaluj**, aby zainstalować program MySQL Server.
7. Wybierz pozycję **Zainstaluj interfejs PowerCLI programu VMware**. Przed wykonaniem tej czynności zamknij wszystkie okna przeglądarki. Następnie wybierz pozycję **Kontynuuj**.
8. W obszarze **Weryfikowanie konfiguracji urządzenia** zostaną zweryfikowane wymagania wstępne, a następnie będzie można kontynuować.
9. W obszarze **Skonfiguruj poświadczenia serwera vCenter Server/vSphere ESXi** wprowadź nazwę FQDN bądź adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
10. Wprowadź poświadczenia dla konta [utworzonego](#prepare-an-account-to-discover-vms) na potrzeby odnajdywania oprogramowania VMware. Wybierz pozycję **Dodaj**  >  **Kontynuuj**.
11. W obszarze **Konfiguruj poświadczenia maszyny wirtualnej** wprowadź poświadczenia [utworzone](#prepare-an-account-for-mobility-service-installation) na potrzeby instalacji wypychanej usługi mobilności po włączeniu replikacji dla maszyn wirtualnych.  
    - W przypadku maszyn z systemem Windows konto wymaga uprawnień administratora lokalnego na replikowanych maszynach.
    - W przypadku systemu Linux należy podać dane superużytkownika.
12. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**.


Po zarejestrowaniu urządzenia replikacji Azure Migrate oceny serwera nawiązuje połączenie z serwerami VMware przy użyciu określonych ustawień i odnajduje maszyny wirtualne. Odnalezione maszyny wirtualne można wyświetlić w obszarze **Zarządzanie**  >  **odnalezionymi elementami** na karcie **inne** .



## <a name="replicate-vms"></a>Replikowanie maszyn wirtualnych

Wybierz Maszyny wirtualne do migracji.

> [!NOTE]
> W portalu można wybrać maksymalnie 10 maszyn na potrzeby replikacji. Jeśli chcesz przeprowadzić replikację więcej, następnie pogrupuj je w partie 10.

1. Na **serwerach** Azure Migrate project > **Azure Migrate: Migracja serwera**, kliknij przycisk **replikacja**.

    ![Zrzut ekranu przedstawiający ekran serwery w Azure Migrate. Przycisk replikacja została wybrana w Azure Migrate: Migracja serwera w obszarze Narzędzia migracji.](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. W obszarze **Replikacja** > **Ustawienia źródła** > **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**.
3. W **urządzeniu lokalnym** wybierz nazwę skonfigurowanego urządzenia Azure Migrate.
4. W programie **vCenter Server** Określ nazwę serwera vCenter do zarządzania maszynami wirtualnymi lub serwer vSphere, na którym są hostowane maszyny wirtualne.
5. W obszarze **serwer przetwarzania** wybierz nazwę urządzenia replikacji.
6. W obszarze **poświadczenia gościa** Określ konto administratora maszyny wirtualnej, które będzie używane do instalacji wypychanej usługi mobilności. Następnie kliknij przycisk **Dalej: maszyny wirtualne**.

    ![Zrzut ekranu przedstawiający kartę Ustawienia źródła na ekranie replikacja. Zostanie wyróżnione pole poświadczenia gościa, a wartość jest ustawiona na VM-admin-account.](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. W **Virtual Machines** wybierz maszyny, które chcesz replikować.

    - Jeśli przeprowadzasz ocenę dla maszyn wirtualnych, możesz zastosować rekomendacje dotyczące rozmiarów maszyn wirtualnych i typów dysków (Premium/standardowy) w wynikach oceny. W tym celu w obszarze **Zaimportować ustawienia migracji z oceny usługi Azure Migrate?** wybierz opcję **Tak**.
    - Jeśli nie uruchomiono oceny lub nie chcesz używać ustawień oceny, wybierz opcję **Nie**.
    - W przypadku wybrania opcji korzystania z oceny wybierz grupę maszyn wirtualnych i nazwę oceny.
8. W obszarze **Opcje dostępności** wybierz pozycję:
    -  Strefa dostępności służąca do przypinania zmigrowanej maszyny do określonej strefy dostępności w regionie. Użyj tej opcji, aby dystrybuować serwery tworzące wielowęzłową warstwę aplikacji między Strefy dostępności. W przypadku wybrania tej opcji należy określić strefę dostępności, która ma być używana dla każdej z wybranych maszyn na karcie obliczenia. Ta opcja jest dostępna tylko wtedy, gdy region docelowy wybrany do migracji obsługuje Strefy dostępności
    -  Zestaw dostępności umożliwiający umieszczenie zmigrowanej maszyny w zestawie dostępności. Docelowa Grupa zasobów, która została wybrana, musi mieć co najmniej jeden zestaw dostępności, aby można było użyć tej opcji.
    - Nie jest wymagana opcja nadmiarowości infrastruktury, jeśli nie potrzebujesz żadnej z tych konfiguracji dostępności dla zmigrowanych maszyn.
9. Sprawdź wszystkie maszyny wirtualne, które chcesz zmigrować. Następnie kliknij przycisk **Dalej: ustawienia docelowe**.
10. W obszarze **Ustawienia elementu docelowego** wybierz subskrypcję i docelowy region migracji, a następnie określ grupę zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji.
11. W obszarze **Sieć wirtualna** wybierz sieć wirtualną/podsieć platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
12. W obszarze **Opcje dostępności** wybierz pozycję:
    -  Strefa dostępności służąca do przypinania zmigrowanej maszyny do określonej strefy dostępności w regionie. Użyj tej opcji, aby dystrybuować serwery tworzące wielowęzłową warstwę aplikacji między Strefy dostępności. W przypadku wybrania tej opcji należy określić strefę dostępności, która ma być używana dla każdej z wybranych maszyn na karcie obliczenia. Ta opcja jest dostępna tylko wtedy, gdy region docelowy wybrany do migracji obsługuje Strefy dostępności
    -  Zestaw dostępności umożliwiający umieszczenie zmigrowanej maszyny w zestawie dostępności. Docelowa Grupa zasobów, która została wybrana, musi mieć co najmniej jeden zestaw dostępności, aby można było użyć tej opcji.
    - Nie jest wymagana opcja nadmiarowości infrastruktury, jeśli nie potrzebujesz żadnej z tych konfiguracji dostępności dla zmigrowanych maszyn.
    
13. W obszarze **Korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Dalej**.

14. W obszarze **obliczenia** Sprawdź nazwę maszyny wirtualnej, rozmiar, typ dysku systemu operacyjnego i konfigurację dostępności (w przypadku wybrania w poprzednim kroku). Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

   - **Rozmiar maszyny wirtualnej**: Jeśli korzystasz z zaleceń dotyczących oceny, rozmiar maszyny wirtualnej zostanie wyświetlony na liście rozwijanej rozmiar zalecane. W przeciwnym razie usługa Azure Migrate wybierze rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (Boot) dla maszyny wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego. 
    - **Strefa dostępności**: Określ strefę dostępności, która ma zostać użyta.
    - **Zestaw dostępności**: Określ zestaw dostępności, który ma być używany.

15. W obszarze **dyski** Określ, czy dyski maszyn wirtualnych mają być replikowane na platformę Azure, a następnie wybierz typ dysku (standardowy dysk SSD lub dyski w warstwie Premium) na platformie Azure. Następnie kliknij przycisk **Dalej**.
    - Dyski można wykluczyć z replikacji.
    - Jeśli wykluczysz dyski, nie będą one znajdować się na maszynie wirtualnej platformy Azure po migracji. 

16. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można aktualizować w dowolnym momencie przed rozpoczęciem replikacji, **Zarządzanie**  >  **maszynami replikowanymi**. Ustawień nie można zmienić po rozpoczęciu replikacji.


## <a name="track-and-monitor"></a>Śledzenie i monitorowanie

1. Śledź stan zadania w powiadomieniach portalu. 

    ![Śledzenie zadania](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. Aby monitorować stan replikacji, kliknij pozycję **replikowanie serwerów** w **Azure Migrate: Migracja serwera**.

    ![Monitorowanie replikacji](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

Replikacja odbywa się w następujący sposób:
- Po pomyślnym zakończeniu zadania Rozpocznij replikację maszyny rozpoczynają replikację początkową na platformę Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany w dyskach lokalnych są okresowo replikowane do dysków repliki na platformie Azure.


## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po rozpoczęciu replikacji różnicowej można przeprowadzić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zalecamy wykonanie tej czynności co najmniej raz na każdym komputerze, przed przeprowadzeniem migracji.

- Podczas przeprowadzania migracji testów migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które pozostają w działaniu i kontynuują replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu zreplikowanych danych (zazwyczaj migrujesz do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Możesz użyć zreplikowanego testowej maszyny wirtualnej platformy Azure, aby zweryfikować migrację, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wykonaj migrację testową w następujący sposób:


1. W obszarze serwery **celów migracji**  >    >  **Azure Migrate: Migracja serwera**, kliknij przycisk **Testuj zmigrowane serwery**.

     ![Serwery z przeprowadzoną migracją testową](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. W obszarze **Testowanie migracji** wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Zostanie uruchomione zadanie **Testowanie migracji**. Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji sprawdź zmigrowane maszyny wirtualne platformy Azure w obszarze **Maszyny wirtualne** w witrynie Azure Portal. Nazwa maszyny ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **Replikowanie maszyn**, a następnie kliknij pozycję **Wyczyść migrację testową**.

    ![Czyszczenie migracji](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację maszyn lokalnych.

1. W Azure Migrate serwery > Project   >  **Azure Migrate: Migracja serwera**, kliknij przycisk **replikowanie serwerów**.

    ![Replikowanie serwerów](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. W obszarze **Migrowanie**  >  **Zamknij maszyny wirtualne i przeprowadź planowaną migrację bez utraty danych** wybierz pozycję **tak**  >  **OK**.
    - Domyślnie Azure Migrate zamyka lokalną maszynę wirtualną, aby zapewnić minimalną utratę danych. 
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **Nie**
4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie **Maszyny wirtualne**.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną > **Zatrzymaj migrację**. Spowoduje to wykonanie następujących czynności:
    - Wyłącza replikację maszyny lokalnej.
    - Usuwa maszynę z liczby **serwerów replikowania** w Azure Migrate: Migracja serwera.
    - Czyści informacje o stanie replikacji maszyny wirtualnej.
2. Zainstaluj agenta maszyny wirtualnej platformy Azure dla [systemu Windows](../virtual-machines/extensions/agent-windows.md) lub [Linux](../virtual-machines/extensions/agent-linux.md) na zmigrowanych maszynach.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Obcinaj ruch do zmigrowanego wystąpienia maszyny wirtualnej platformy Azure.
6. Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
7. Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
8. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure. 

## <a name="post-migration-best-practices"></a>Najlepsze rozwiązania po migracji

- Magazyn lokalny
    - Przenieś ruch aplikacji do aplikacji uruchomionej na zmigrowanym wystąpieniu maszyny wirtualnej platformy Azure.
    - Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
    - Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
    - Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure.
- Dostosowywanie ustawień maszyny wirtualnej platformy Azure po migracji:
    - [Agent maszyny wirtualnej platformy Azure](../virtual-machines/extensions/agent-windows.md) umożliwia zarządzanie interakcją maszyny wirtualnej z usługą Azure Fabric Controller. Jest to wymagane w przypadku niektórych usług platformy Azure, takich jak Azure Backup, Site Recovery i Azure Security. Podczas migrowania maszyn wirtualnych VMare z migracją opartą na agentach Instalator usługi mobilności instaluje agenta maszyny wirtualnej platformy Azure na maszynach z systemem Windows. Na maszynach wirtualnych z systemem Linux zalecamy zainstalowanie agenta po migracji.
    - Ręcznie odinstaluj usługę mobilności z maszyny wirtualnej platformy Azure po migracji.
    - Ręcznie odinstaluj narzędzia VMware po migracji.
- Na platformie Azure:
    - Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
    - Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
- Ciągłość działania i odzyskiwanie po awarii
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Zablokuj i Ogranicz dostęp do ruchu przychodzącego za pomocą [administracji Azure Security Center w czasie](../security-center/security-center-just-in-time.md).
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md).
    - Wdróż usługę [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
    - Rozważ wdrożenie usługi [Azure Cost Management](../cost-management-billing/cloudyn/overview.md), aby monitorować użycie zasobu i wydatki.




 ## <a name="next-steps"></a>Następne kroki

Zbadaj [podróż migracji w chmurze](/azure/architecture/cloud-adoption/getting-started/migrate) w strukturze wdrażania w chmurze platformy Azure.