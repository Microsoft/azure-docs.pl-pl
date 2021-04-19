---
title: Migrowanie maszyn wirtualnych VMware za pomocą migracji serwera Azure Migrate agentów
description: Dowiedz się, jak uruchomić migrację maszyn wirtualnych VMware opartą na agentach przy użyciu Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: 60b58f7cf67a22e019ff186e4e1811ff5b001d84
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714456"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrowanie maszyn wirtualnych VMware na platformę Azure (oparte na agentach)

W tym artykule przedstawiono sposób migrowania lokalnych maszyn wirtualnych VMware na platformę Azure przy użyciu [narzędzia Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) z migracją opartą na agentach.  Można również migrować maszyny wirtualne VMware przy użyciu migracji bez agenta. [Porównaj](server-migrate-overview.md#compare-migration-methods) metody.


 Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Przygotowanie platformy Azure do pracy z Azure Migrate.
> * Przygotowanie do migracji opartej na agentach. Skonfiguruj konto VMware, aby Azure Migrate odnajdywać maszyny do migracji. Skonfiguruj konto tak, aby agent usługa mobilności programu można było zainstalować na maszynach, które chcesz migrować, i przygotuj maszynę do działania jako urządzenie replikacji.
> * Dodawanie narzędzia Azure Migrate:Server Migration
> * Skonfiguruj urządzenie replikacji.
> * Replikowanie maszyn wirtualnych.
> * Uruchom migrację testów, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację na platformę Azure.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Jeśli to możliwe, samouczki używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka zapoznaj [się z architekturą](./agent-based-migration-architecture.md) migracji opartą na agentach VMware.

## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Wykonaj zadania w tabeli, aby przygotować platformę Azure do migracji opartej na agentach.

**Zadanie** | **Szczegóły**
--- | ---
**Tworzenie projektu usługi Azure Migrate** | Twoje konto platformy Azure wymaga uprawnień Współautor lub Właściciel, aby utworzyć projekt.
**Weryfikowanie uprawnień konta platformy Azure** | Konto platformy Azure musi mieć uprawnienia do tworzenia maszyny wirtualnej i zapisu na dysku zarządzanym platformy Azure.
**Konfiguracja sieci platformy Azure** | Skonfiguruj sieć, do których maszyny wirtualne platformy Azure będą dołączać po migracji.

### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu
Jeśli nie masz projektu aplikacji Azure Migrate, sprawdź uprawnienia, aby go utworzyć.


1. W witrynie Azure Portal otwórz daną subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)** .
2. W **sprawdź dostęp**, znajdź odpowiednie konto, a następnie kliknij go, aby wyświetlić uprawnienia.
3. Sprawdź, czy masz **uprawnienia Współautor** **lub** Właściciel.

    - Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, poproś właściciela o przypisanie roli.
    
### <a name="assign-azure-account-permissions"></a>Przypisywanie uprawnień konta platformy Azure

Przypisz do konta rolę Współautor maszyny wirtualnej, aby mieć uprawnienia do:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapis na dysku zarządzanym platformy Azure. 


### <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

[Skonfiguruj sieć platformy Azure.](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Maszyny lokalne są replikowane do dysków zarządzanych platformy Azure. Po zakończeniu pracy awaryjnej na platformie Azure w celu migracji maszyny wirtualne platformy Azure są tworzone na podstawie tych dysków zarządzanych i przyłączone do sieci platformy Azure, która została przez Ciebie ustawiona.

## <a name="prepare-for-migration"></a>Przygotowanie do migracji

Sprawdź wymagania i uprawnienia dotyczące obsługi oraz przygotuj się do wdrożenia urządzenia replikacji. 

### <a name="prepare-an-account-to-discover-vms"></a>Przygotowywanie konta do odnajdywania maszyn wirtualnych

Azure Migrate Server Migration wymaga dostępu do serwerów VMware w celu odnajdywania maszyn wirtualnych, które chcesz migrować. Utwórz konto w następujący sposób:

1. Aby użyć dedykowanego konta, utwórz rolę na poziomie vCenter. Nadaj roli nazwę, taką **jak Azure_Migrate**.
2. Przypisz do roli uprawnienia podsumowane w poniższej tabeli.
3. Utwórz użytkownika na serwerze vCenter lub hoście vSphere. Przypisz tę rolę temu użytkownikowi.

#### <a name="vmware-account-permissions"></a>Uprawnienia konta VMware

**Zadanie** | **Rola/uprawnienia** | **Szczegóły**
--- | --- | ---
**Odnajdowanie maszyn wirtualnych** | Co najmniej użytkownik tylko do odczytu<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp,  przypisz rolę  Bez dostępu z propagacja do obiektu podrzędnego do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).
**Replikacja** |  Utwórz rolę (Azure_Site_Recovery) z wymaganymi uprawnieniami, a następnie przypisz ją użytkownikowi lub grupie VMware<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = Azure_Site_Recovery<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp,  przypisz rolę  Bez dostępu z propagacja do obiektu podrzędnego do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługę Mobility należy zainstalować na każdej maszynie, która ma być replikowana.

- Urządzenie Azure Migrate replikacji może wykonać instalację wypychaną tej usługi po włączeniu replikacji dla maszyny lub zainstalować ją ręcznie lub przy użyciu narzędzi instalacyjnych.
- W tym samouczku zamierzamy zainstalować usługę Mobility przy użyciu instalacji typu push.
- W przypadku instalacji wypychanej należy przygotować konto, za pomocą Azure Migrate Server Migration w celu uzyskania dostępu do maszyny wirtualnej. To konto jest używane tylko w przypadku instalacji wypychaowej, jeśli nie zainstalujesz aplikacji usługa mobilności ręcznie.

Przygotuj konto w następujący sposób:

1. Przygotuj domenę lub konto lokalne z uprawnieniami do instalowania na maszynie wirtualnej.
2. W przypadku maszyn wirtualnych z systemem Windows, jeśli nie używasz konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na maszynie lokalnej, dodając wpis DWORD **LocalAccountTokenFilterPolicy** z wartością w rejestrze w obszarze **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. W przypadku maszyn wirtualnych z systemem Linux przygotuj konto główne na źródłowym serwerze z systemem Linux.


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Przygotowywanie maszyny dla urządzenia replikacji

Urządzenie jest używane do replikacji maszyn na platformę Azure. Urządzenie jest pojedynczą lokalną maszyną wirtualną VMware o wysokiej dostępie, która hostuje następujące składniki:

- **Serwer konfiguracji:** serwer konfiguracji koordynuje komunikację między środowiskiem lokalnym i platformą Azure oraz zarządza replikacją danych.
- **Serwer przetwarzania:** serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji; optymalizuje je za pomocą buforowania, kompresji i szyfrowania oraz wysyła je do konta magazynu pamięci podręcznej na platformie Azure. Serwer przetwarzania instaluje również agenta usługi Mobility Service na maszynach wirtualnych, które mają być replikowane, i wykonuje automatyczne odnajdywanie lokalnych maszyn wirtualnych VMware.

Przygotuj urządzenie w następujący sposób:

- [Zapoznaj się z wymaganiami urządzenia.](migrate-replication-appliance.md#appliance-requirements) Ogólnie rzecz biorąc, urządzenie replikacji należy skonfigurować jako maszynę wirtualną VMware przy użyciu pobranego pliku OVA. Szablon tworzy urządzenie, które spełnia wszystkie wymagania.
- Na urządzeniu musi być zainstalowany program MySQL. [Przejrzyj](migrate-replication-appliance.md#mysql-installation) metody instalacji.
- Przejrzyj adresy [URL chmury publicznej](migrate-replication-appliance.md#url-access)i [Azure Government URL,](migrate-replication-appliance.md#azure-government-url-access) do których maszyna urządzenia musi uzyskać dostęp.
- [Sprawdź porty, do](migrate-replication-appliance.md#port-access) których maszyna replikacji musi uzyskać dostęp.



### <a name="check-vmware-requirements"></a>Sprawdzanie wymagań dotyczących programu VMware

Upewnij się, że serwery VMware i maszyny wirtualne są zgodne z wymaganiami migracji na platformę Azure. 

1. [Zweryfikuj](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) wymagania dotyczące serwerów VMware.
2. [Weryfikowanie](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) Wymagania dotyczące maszyny wirtualnej na potrzeby migracji.
3. Sprawdź ustawienia platformy Azure. Lokalne maszyny wirtualne replikowane na platformę Azure muszą być zgodne z wymaganiami [maszyny wirtualnej platformy Azure.](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)
4. Przed migracją maszyn wirtualnych na platformę Azure należy wprowadzić pewne zmiany.
    - Przed rozpoczęciem migracji należy wprowadzić te zmiany. Jeśli zmigrujesz maszynę wirtualną przed zmianą, maszyna wirtualna może nie zostać uruchomiony na platformie Azure.
    - Przejrzyj [zmiany w](prepare-for-migration.md#windows-machines) systemach Windows i [Linux,](prepare-for-migration.md#linux-machines) które należy wprowadzić.

> [!NOTE]
> Migracja oparta na agentach Azure Migrate Server jest oparta na funkcjach usługi Azure Site Recovery Service. Niektóre wymagania mogą być linkiem do Site Recovery dokumentacji.

## <a name="set-up-the-replication-appliance"></a>Konfigurowanie urządzenia replikacji

W tej procedurze opisano sposób skonfigurowania urządzenia przy użyciu pobranego szablonu open virtualization application (OVA). Jeśli nie możesz użyć tej metody, możesz skonfigurować urządzenie przy [użyciu skryptu](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). 

### <a name="download-the-replication-appliance-template"></a>Pobieranie szablonu urządzenia replikacji

Pobierz szablon w następujący sposób:

1. W projekcie Azure Migrate serwery **w** obszarze **Cele migracji.**
2. W **Azure Migrate — Serwery**  >  **Azure Migrate: Migracja serwera** kliknij pozycję **Odnajdz.**

    ![Odnajdywanie maszyn wirtualnych](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. W **odnajdywania**  >  **maszyn czy maszyny są zwirtualizowane?**, kliknij przycisk **Tak, VMware vSphere funkcji hypervisor.**
4. Na **stronie Jak przeprowadzić migrację?** wybierz pozycję Korzystanie z **replikacji opartej na agentach.**
5. W **obszarze Region docelowy** wybierz region platformy Azure, do którego chcesz migrować maszyny.
6. Wybierz **pozycję Potwierdź, że regionem docelowym migracji jest nazwa regionu**.
7. Kliknij **pozycję Utwórz zasoby.** Powoduje to utworzenie Azure Site Recovery magazynu w tle. Po kliknięciu tego przycisku nie można zmienić regionu docelowego dla tego projektu, a wszystkie kolejne migracje będą dotyczyć tego regionu.

    ![Tworzenie magazynu usługi Recovery Services](./media/tutorial-migrate-vmware-agent/create-resources.png)  

    > [!NOTE]
    > Jeśli podczas tworzenia projektu usługi Azure Migrate wybrano prywatny punkt końcowy jako metodę łączności, magazyn usługi Recovery Services zostanie również skonfigurowany do łączności z prywatnym punktem końcowym. Upewnij się, że prywatne punkty końcowe są dostępne z urządzenia replikacji: [ **Dowiedz się więcej**](how-to-use-azure-migrate-with-private-endpoints.md#troubleshoot-network-connectivity)


8. Na **stronie Czy chcesz zainstalować nowe urządzenie replikacji?** wybierz pozycję Zainstaluj urządzenie **replikacji.**
9. Kliknij pozycję **Pobierz**. W ten sposób zostanie pobrany szablon OVF.
    ![Pobieranie pliku OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Zanotuj nazwę grupy zasobów i magazyn usługi Recovery Services. Będą one potrzebne podczas wdrażania urządzenia.


### <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware

Po pobraniu szablonu OVF należy zaimportować go do programu VMware w celu utworzenia aplikacji replikacji na maszynie wirtualnej VMware z systemem Windows Server 2016.

1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu VMware vSphere klienta.
2. W menu **Plik** wybierz pozycję **Deploy OVF Template (Wd wdrażaj szablon OVF),** aby uruchomić Kreatora wdrażania **szablonu OVF.** 
3. W **pozycji Wybierz** źródło wprowadź lokalizację pobranego pliku OVF.
4. Na **stronie Przeglądanie szczegółów** wybierz pozycję **Dalej.**
5. W **polach Wybierz nazwę i folder** oraz Wybierz **konfigurację** zaakceptuj ustawienia domyślne.
6. W **wybieranie magazynu** Wybierz format dysku  >  **wirtualnego**, aby uzyskać najlepszą wydajność wybierz opcję Thick Provision Eager **Zeroed**.
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. Na **stronie Gotowe do ukończenia,** aby skonfigurować maszynę wirtualną z ustawieniami domyślnymi, wybierz pozycję **Wł. po wdrożeniu**  >  **Zakończ.**

   > [!TIP]
   > Jeśli chcesz dodać dodatkową kartę sieciową, wyczyść pozycję **Wł. po wdrożeniu**  >  **Zakończ.** Domyślnie szablon zawiera jedną kartę sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.

### <a name="start-appliance-setup"></a>Uruchamianie konfiguracji urządzenia

1. W konsoli VMware vSphere klienta włącz maszynę wirtualną. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016.
2. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się do maszyny wirtualnej jako administrator, używając hasła administratora. Po pierwszym zalogowaniu się narzędzie konfiguracji urządzenia replikacji (Azure Site Recovery Configuration Tool) zostanie uruchomiony w ciągu kilku sekund.
5. Wprowadź nazwę, która ma być używane do rejestrowania urządzenia w funkcji migracji serwera. Następnie kliknij przycisk **Dalej**.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure.
7. Poczekaj, aż narzędzie zakończy rejestrowanie aplikacji usługi Azure AD w celu zidentyfikowania urządzenia. Urządzenie jest ponownie uruchamiane.
1. Ponownie zaloguj się do maszyny. W ciągu kilku sekund zostanie automatycznie uruchomiony kreator zarządzania serwerem konfiguracji.

### <a name="register-the-replication-appliance"></a>Rejestrowanie urządzenia replikacji

Zakończ konfigurowanie i rejestrowanie urządzenia replikacji.

1. W instalatorze urządzenia wybierz pozycję **Skonfiguruj łączność.**
2. Wybierz kartę sieciową (domyślnie jest tylko jedna karta sieciowa), która jest używana przez urządzenie replikacji do odnajdywania maszyn wirtualnych, i wykonaj instalację wypychaną usługa mobilności na maszynach źródłowych.
3. Wybierz kartę sieciową używaną przez urządzenie replikacji do łączności z platformą Azure. Następnie wybierz pozycję **Zapisz**. Nie można zmienić tego ustawienia po jego skonfigurowaniu.
4. Jeśli urządzenie znajduje się za serwerem proxy, należy określić ustawienia serwera proxy.
    - Określ nazwę serwera proxy jako **http://ip-address** , lub **http://FQDN** . Serwery proxy HTTPS nie są obsługiwane.
5. Po wyświetleniu monitu o subskrypcję, grupy zasobów i szczegóły magazynu dodaj szczegóły zanotowane podczas pobierania szablonu urządzenia.
6. Na stronie **Instalowanie oprogramowania innych firm** zaakceptuj umowę licencyjną. Wybierz pozycję **Pobierz i zainstaluj**, aby zainstalować program MySQL Server.
7. Wybierz pozycję **Zainstaluj interfejs PowerCLI programu VMware**. Przed wykonaniem tej czynności zamknij wszystkie okna przeglądarki. Następnie wybierz pozycję **Kontynuuj**.
8. W obszarze **Weryfikowanie konfiguracji urządzenia** zostaną zweryfikowane wymagania wstępne, a następnie będzie można kontynuować.
9. W obszarze **Skonfiguruj poświadczenia serwera vCenter Server/vSphere ESXi** wprowadź nazwę FQDN bądź adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
10. Wprowadź poświadczenia dla konta utworzonego na [podstawie odnajdywania](#prepare-an-account-to-discover-vms) VMware. Wybierz **pozycję Dodaj**  >  **kontynuuj.**
11. W **konfigurować poświadczenia maszyny wirtualnej** [](#prepare-an-account-for-mobility-service-installation) , wprowadź poświadczenia utworzone dla instalacji wypychane usługi usługa mobilności, po włączeniu replikacji dla maszyn wirtualnych.  
    - W przypadku maszyn z systemem Windows konto wymaga uprawnień administratora lokalnego na replikowanych maszynach.
    - W przypadku systemu Linux należy podać dane superużytkownika.
12. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**.


Po zarejestrowaniu urządzenia replikacji program Azure Migrate Server Assessment łączy się z serwerami VMware przy użyciu określonych ustawień i odnajduje maszyny wirtualne. Odnalezione maszyny wirtualne można wyświetlić w obszarze **Zarządzanie**  >  **odnalezionych elementami** na **karcie** Inne.



## <a name="replicate-vms"></a>Replikowanie maszyn wirtualnych

Wybierz maszyny wirtualne do migracji.

> [!NOTE]
> W portalu można wybrać maksymalnie 10 maszyn jednocześnie do replikacji. Jeśli chcesz replikować więcej, pogrupuj je w partie po 10.

1. W projekcie Azure Migrate serwery > **,** **Azure Migrate: Migracja serwera** kliknij pozycję **Replikuj.**

    ![Zrzut ekranu przedstawiający ekran Serwery w Azure Migrate. Przycisk Replikuj jest wybrany w obszarze Azure Migrate: Migracja serwera w obszarze Narzędzia migracji.](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. W obszarze **Replikacja** > **Ustawienia źródła** > **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**.
3. W **chmurze Urządzenie** lokalne wybierz nazwę Azure Migrate, które zostało przez Ciebie ustawione.
4. Na **serwerze vCenter** określ nazwę serwera vCenter zarządzającego maszynami wirtualnych lub serwera vSphere, na którym są hostowane maszyny wirtualne.
5. Na **serwerze przetwarzania** wybierz nazwę urządzenia replikacji.
6. W **pozycji Poświadczenia gościa** określ konto administratora maszyny wirtualnej, które będzie używane do instalacji wypychaowej usługa mobilności. Następnie kliknij przycisk **Dalej: Maszyny wirtualne.**

    ![Zrzut ekranu przedstawiający kartę Ustawienia źródła na ekranie Replikowanie. Pole Poświadczenia gościa jest wyróżnione, a wartość jest ustawiona na vm-admin-account.](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. W **Virtual Machines** wybierz maszyny, które chcesz replikować.

    - Jeśli przeprowadzasz ocenę dla maszyn wirtualnych, możesz zastosować rekomendacje dotyczące rozmiarów maszyn wirtualnych i typów dysków (Premium/standardowy) w wynikach oceny. W tym celu w obszarze **Zaimportować ustawienia migracji z oceny usługi Azure Migrate?** wybierz opcję **Tak**.
    - Jeśli nie uruchomiono oceny lub nie chcesz używać ustawień oceny, wybierz opcję **Nie**.
    - W przypadku wybrania opcji korzystania z oceny wybierz grupę maszyn wirtualnych i nazwę oceny.
8. W **opcje dostępności**, wybierz:
    -  Strefa dostępności do przypiętych migrowanych maszyn do określonej strefy dostępności w regionie. Ta opcja umożliwia dystrybucję serwerów tworzących warstwę aplikacji z wieloma węzłami między Strefy dostępności. Jeśli wybierzesz tę opcję, musisz określić strefę dostępności do użycia dla każdej wybranej maszyny na karcie Obliczenia. Ta opcja jest dostępna tylko wtedy, gdy region docelowy wybrany do migracji obsługuje Strefy dostępności
    -  Zestaw dostępności, aby umieścić zmigrowany komputer w zestawie dostępności. Docelowa grupa zasobów, która została wybrana, musi mieć co najmniej jeden zestaw dostępności, aby można było użyć tej opcji.
    - Jeśli nie potrzebujesz żadnej z tych konfiguracji dostępności dla migrowanych maszyn, nie jest wymagana żadna z tych opcji nadmiarowości infrastruktury.
9. Sprawdź każdą maszynę wirtualną, którą chcesz migrować. Następnie kliknij przycisk **Dalej: Ustawienia docelowe.**
10. W obszarze **Ustawienia elementu docelowego** wybierz subskrypcję i docelowy region migracji, a następnie określ grupę zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji.
11. W obszarze **Sieć wirtualna** wybierz sieć wirtualną/podsieć platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.  
12. Na  **stronie Konto magazynu pamięci podręcznej** zachowaj domyślną opcję używania konta magazynu pamięci podręcznej, które jest automatycznie tworzone dla projektu. Użyj listy rozwijanej, jeśli chcesz określić inne konto magazynu do użycia jako konto magazynu pamięci podręcznej na użytek replikacji. <br/> 
    > [!NOTE]
    >
    > - Jeśli jako metodę łączności dla projektu usługi Azure Migrate wybrano prywatny punkt końcowy, przyznaj magazynowi usługi Recovery Services dostęp do konta magazynu pamięci podręcznej. [**Dowiedz się więcej**](how-to-use-azure-migrate-with-private-endpoints.md#grant-access-permissions-to-the-recovery-services-vault)
    > - Aby replikować przy użyciu usługi ExpressRoute z prywatną komunikacji równorzędnej, utwórz prywatny punkt końcowy dla konta magazynu pamięci podręcznej. [**Dowiedz się więcej**](how-to-use-azure-migrate-with-private-endpoints.md#create-a-private-endpoint-for-the-storage-account-optional) 
13. W **opcje dostępności**, wybierz:
    -  Strefa dostępności do przypiętych migrowanych maszyn do określonej strefy dostępności w regionie. Ta opcja umożliwia dystrybucję serwerów tworzących warstwę aplikacji z wieloma węzłami między Strefy dostępności. W przypadku wybrania tej opcji należy określić strefę dostępności do użycia dla każdej wybranej maszyny na karcie Obliczenia. Ta opcja jest dostępna tylko wtedy, gdy region docelowy wybrany do migracji obsługuje Strefy dostępności
    -  Zestaw dostępności, aby umieścić zmigrowany komputer w zestawie dostępności. Docelowa grupa zasobów, która została wybrana, musi mieć co najmniej jeden zestaw dostępności, aby można było użyć tej opcji.
    - Jeśli nie potrzebujesz żadnej z tych konfiguracji dostępności dla migrowanych maszyn, nie jest wymagana żadna z tych opcji nadmiarowości infrastruktury.
14. W **typie Szyfrowanie dysków** wybierz pozycję:
    - Szyfrowanie danych spoczynku przy użyciu klucza zarządzanego przez platformę
    - Szyfrowanie danych spoczynku przy użyciu klucza zarządzanego przez klienta
    - Podwójne szyfrowanie za pomocą kluczy zarządzanych przez platformę i zarządzanych przez klienta

   > [!NOTE]
   > Aby replikować maszyny wirtualne za pomocą klucza cmk, należy utworzyć zestaw szyfrowania [dysków w](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) docelowej grupie zasobów. Obiekt zestawu szyfrowania dysków mapuje Dyski zarządzane na Key Vault, który zawiera klucz CMK do użycia na użytek funkcji SSE.
  
15. W obszarze **Korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Dalej**.

    ![Ustawienia docelowe](./media/tutorial-migrate-vmware/target-settings.png)

16. W **jęz.** obliczeniowym przejrzyj nazwę maszyny wirtualnej, rozmiar, typ dysku systemu operacyjnego i konfigurację dostępności (jeśli wybrano w poprzednim kroku). Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

   - **Rozmiar maszyny wirtualnej:** jeśli używasz zaleceń dotyczących oceny, lista rozwijana Rozmiar maszyny wirtualnej zawiera zalecany rozmiar. W przeciwnym razie usługa Azure Migrate wybierze rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**. 
    - **Dysk systemu operacyjnego:** określ dysk systemu operacyjnego (rozruchowy) dla maszyny wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego. 
    - **Strefa dostępności:** określ strefę dostępności do użycia.
    - **Zestaw dostępności:** określ zestaw dostępności do użycia.

17. W **witrynie Dyski** określ, czy dyski maszyny wirtualnej mają być replikowane na platformie Azure, i wybierz typ dysku (dyski ssd/HDD w warstwie Standardowa lub dyski zarządzane w warstwie Premium) na platformie Azure. Następnie kliknij przycisk **Dalej**.
    - Dyski można wykluczyć z replikacji.
    - Jeśli wykluczysz dyski, nie będą one znajdować się na maszynie wirtualnej platformy Azure po migracji. 

18. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można zaktualizować w dowolnym momencie przed rozpoczęciem **replikacji: Zarządzanie**  >  **replikami maszyn.** Ustawień nie można zmienić po rozpoczęciu replikacji.


## <a name="track-and-monitor"></a>Śledzenie i monitorowanie

1. Śledź stan zadania w powiadomieniach portalu. 

    ![Śledzenie zadania](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. Aby monitorować stan replikacji, kliknij pozycję **Replikowanie serwerów** **w Azure Migrate: Migracja serwera.**

    ![Monitorowanie replikacji](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

Replikacja odbywa się w następujący sposób:
- Po pomyślnym zakończeniu zadania uruchamiania replikacji maszyny rozpoczynają replikację początkową na platformie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany na dyskach lokalnych są okresowo replikowane do dysków repliki na platformie Azure.


## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po uruchomieniu replikacji różnicowej można uruchomić migrację testowa dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zaleca się przeprowadzenie tej migracji co najmniej raz dla każdej maszyny przed jej migracją.

- Uruchomienie testu migracji sprawdza, czy migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które nadal działają, i kontynuować replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu replikowanych danych (zazwyczaj migrując do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Replikowanej testowej maszyny wirtualnej platformy Azure można użyć do weryfikowania migracji, testowania aplikacji i rozwiązania wszelkich problemów przed pełną migracją.

Przetestuj migrację w następujący sposób:


1. W **cele migracji**  >  **serwerów**  >  **Azure Migrate: migracja serwera,** kliknij przycisk Test **zmigrowane serwery.**

     ![Serwery z przeprowadzoną migracją testową](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. W obszarze **Testowanie migracji** wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Zostanie uruchomione zadanie **Testowanie migracji**. Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji sprawdź zmigrowane maszyny wirtualne platformy Azure w obszarze **Maszyny wirtualne** w witrynie Azure Portal. Nazwa maszyny ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **Replikowanie maszyn**, a następnie kliknij pozycję **Wyczyść migrację testową**.

    ![Czyszczenie migracji](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po sprawdzeniu, czy migracja testowa działa zgodnie z oczekiwaniami, możesz przeprowadzić migrację maszyn lokalnych.

1. W projekcie Azure Migrate serwery >: migracja serwera Azure Migrate kliknij pozycję  >   **Replikowanie serwerów.**

    ![Replikowanie serwerów](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. Na **stronie**  >  **Migruj Zamknij** maszyny wirtualne i wykonaj planowaną migrację bez utraty danych wybierz **pozycję Tak**  >  **OK.**
    - Domyślnie program Azure Migrate lokalną maszynę wirtualną w celu zapewnienia minimalnej utraty danych. 
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **Nie**
4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie **Maszyny wirtualne**.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną, aby > **zatrzymać migrację.** Spowoduje to wykonanie następujących czynności:
    - Zatrzymuje replikację maszyny lokalnej.
    - Usuwa maszynę z liczby serwerów **replikowania** w Azure Migrate: Migracja serwera.
    - Czyści informacje o stanie replikacji dla maszyny wirtualnej.
2. Zainstaluj agenta maszyny wirtualnej platformy Azure z systemem [Windows](../virtual-machines/extensions/agent-windows.md) lub [Linux](../virtual-machines/extensions/agent-linux.md) na zmigrowanych maszynach.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Przetnij ruch do zmigrowanych wystąpień maszyn wirtualnych platformy Azure.
6. Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
7. Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
8. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure. 

## <a name="post-migration-best-practices"></a>Najlepsze rozwiązania po migracji

- Lokalnie
    - Przenieś ruch aplikacji do aplikacji uruchomionej na zmigrowanym wystąpieniu maszyny wirtualnej platformy Azure.
    - Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
    - Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
    - Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure.
- Dostosuj ustawienia maszyny wirtualnej platformy Azure po migracji:
    - [Agent maszyny wirtualnej platformy Azure](../virtual-machines/extensions/agent-windows.md) umożliwia zarządzanie interakcją maszyny wirtualnej z usługą Azure Fabric Controller. Jest to wymagane w przypadku niektórych usług platformy Azure, takich jak Azure Backup, Site Recovery i Azure Security. Podczas migrowania maszyn wirtualnych VMare za pomocą migracji opartej na agentach instalator usługi mobilności instaluje agenta maszyny wirtualnej platformy Azure na maszynach z systemem Windows. Na komputerach wirtualnych z systemem Linux zalecamy zainstalowanie agenta po migracji.
    - Po migracji ręcznie odinstaluj usługa mobilności z maszyny wirtualnej platformy Azure.
    - Ręcznie odinstaluj narzędzia VMware po migracji.
- Na platformie Azure:
    - Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
    - Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
- Ciągłość działania/odzyskiwanie po awarii
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Blokowanie i ograniczanie dostępu do ruchu przychodzącego za [pomocą Azure Security Center — administrowanie just in time.](../security-center/security-center-just-in-time.md)
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md).
    - Wdróż usługę [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
    - Rozważ wdrożenie usługi [Azure Cost Management](../cost-management-billing/cloudyn/overview.md), aby monitorować użycie zasobu i wydatki.




 ## <a name="next-steps"></a>Następne kroki

Zbadaj [podróż po migracji do chmury](/azure/architecture/cloud-adoption/getting-started/migrate) w usłudze Azure Cloud Adoption Framework.