---
title: Migrowanie maszyn wirtualnych VMware za pomocą migracji serwera migracji usługi Azure opartej na agentach
description: Dowiedz się, jak uruchomić migrację maszyn wirtualnych VMware opartą na agentach za pomocą usługi Azure Migrate.
ms.topic: tutorial
ms.date: 03/09/2020
ms.custom: MVC
ms.openlocfilehash: 6855c3e81aece0358146608b6cf179fb923c54c8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535336"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrowanie maszyn wirtualnych VMware na platformę Azure (oparte na agentach)

W tym artykule pokazano, jak migrować lokalne maszyny wirtualne VMware na platformę Azure przy użyciu migracji opartej na agentach za pomocą narzędzia migracji serwera migracji usługi Azure Migrate Server.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj środowisko źródłowe i wdrażaj urządzenie replikacji migracji platformy Azure do migracji opartej na agentach.
> * Skonfiguruj środowisko docelowe dla migracji.
> * Skonfiguruj zasady replikacji.
> * Włącz replikację.
> * Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację na platformę Azure.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza, dzięki czemu można szybko skonfigurować weryfikacji koncepcji. Samouczki używają opcji domyślnych tam, gdzie to możliwe, i nie pokazują wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z instrukcjami dotyczącymi oceny i migracji VMware.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) przed rozpoczęciem.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed migracją maszyn wirtualnych na platformę Azure zaleca się wypróbowanie oceny maszyn wirtualnych VMware za pomocą programu Azure Migrate Server Assessment. Skonfiguruj ocenę w następujący sposób:

1. Postępuj zgodnie z samouczkiem, aby [przygotować platformę Azure i VMware](tutorial-prepare-vmware.md) do oceny.
2. Następnie wykonaj [ten samouczek,](tutorial-assess-vmware.md) aby skonfigurować urządzenie migracji platformy Azure do oceny i odnajduj i oceń maszyny wirtualne.


Mimo że zaleca się wypróbowanie oceny, nie trzeba uruchamiać oceny przed migracją maszyn wirtualnych.

## <a name="migration-methods"></a>Metody migracji

Maszyny wirtualne VMware można migrować na platformę Azure za pomocą narzędzia migracji serwera migracji platformy Azure. To narzędzie oferuje kilka opcji migracji maszyn wirtualnych VMware:

- Replikacja bez agenta. Migrowanie maszyn wirtualnych bez konieczności instalowania na nich niczego.
- Migracja oparta na agentach. lub replikacji. Zainstaluj agenta (agenta usług mobilności) na maszynie wirtualnej do replikacji.

Aby zdecydować, czy chcesz używać migracji bez agenta czy na podstawie agenta, zapoznaj się z tymi artykułami:

- [Dowiedz się więcej o](server-migrate-overview.md) opcjach migracji VMware.
- [Porównaj metody migracji](server-migrate-overview.md#compare-migration-methods).
- [Postępuj zgodnie z tym artykułem,](tutorial-migrate-vmware.md) aby wypróbować migrację bez agenta.



## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. [Przejrzyj](migrate-architecture.md) architekturę migracji VMware.
2. Upewnij się, że twojemu kontu platformy Azure jest przypisana rola współautora maszyny wirtualnej, aby mieć uprawnienia do:

    - Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
    - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
    - Zapis na dysku zarządzanym platformy Azure. 

3. [Konfigurowanie sieci platformy Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Maszyny lokalne są replikowane na dyskach zarządzanych platformy Azure. Po przełączeniu na platformę Azure w celu migracji maszyny wirtualne platformy Azure są tworzone na podstawie tych dysków zarządzanych i dołączane do sieci platformy Azure, którą określisz podczas konfigurowania migracji.


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Jeśli ocena została już przeprowadzona za pomocą usługi Azure Migrate Server Assessment, można pominąć instrukcje opisane w tej sekcji, ponieważ te kroki zostały już wykonane. 

Jeśli nie uruchomisz oceny, musisz skonfigurować uprawnienia platformy Azure przed migracją za pomocą migracji serwera migracji usługi Azure Migrate Server.

- **Tworzenie projektu:** Twoje konto platformy Azure wymaga uprawnień do tworzenia projektu migracji platformy Azure. 
- **Zarejestruj urządzenie replikacji usługi Azure Migrate:** Urządzenie replikacji tworzy i rejestruje aplikację usługi Azure Active Directory na koncie platformy Azure. Delegować uprawnienia do tego.
- **Tworzenie magazynu kluczy:** Aby przeprowadzić migrację maszyn wirtualnych VMware przy użyciu migracji serwera migracji usługi Azure, usługa Azure Migrate tworzy magazyn kluczy w grupie zasobów, aby zarządzać kluczami dostępu do konta magazynu replikacji w ramach subskrypcji. Aby utworzyć magazyn, potrzebujesz uprawnień przypisania ról do grupy zasobów, w której znajduje się projekt migracji platformy Azure. 


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W witrynie Azure portal otwórz subskrypcję i wybierz **pozycję Kontrola dostępu (IAM)**.
2. W **obszarze Sprawdź dostęp**znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Powinieneś mieć uprawnienia **współautora** lub **właściciela.**
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, współpracuj z właścicielem, aby przypisać tę rolę.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Przypisywanie uprawnień do rejestrowania urządzenia replikacji

W przypadku migracji opartej na agentach należy delegować uprawnienia do migracji serwera migracji usługi Azure Migrate Server w celu utworzenia i zarejestrowania aplikacji usługi Azure AD na koncie. Uprawnienia można przypisać przy użyciu jednej z następujących metod:

- Dzierżawca/administrator globalny może udzielić uprawnień użytkownikom w dzierżawie, aby utworzyć i zarejestrować aplikacje usługi Azure AD.
- Dzierżawca/administrator globalny może przypisać rolę dewelopera aplikacji (która ma uprawnienia) do konta.

Warto zauważyć, że:

- Aplikacje nie mają żadnych innych uprawnień dostępu do subskrypcji innych niż te opisane powyżej.
- Te uprawnienia są potrzebne tylko podczas rejestrowania nowego urządzenia replikacji. Uprawnienia można usunąć po skonfigurowaniu urządzenia replikacji. 


#### <a name="grant-account-permissions"></a>Udziel uprawnień konta

Dzierżawca/administrator globalny może udzielić uprawnień w następujący sposób

1. W usłudze Azure AD administrator dzierżawy/globalnej powinien przejść do**ustawień użytkownika****użytkowników** > usługi Azure **Active Directory** > .
2. Administrator powinien ustawić **rejestracje aplikacji** na **Tak**.

    ![Uprawnienia usługi Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Jest to ustawienie domyślne, które nie jest poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Przypisywanie roli dewelopera aplikacji 

Dzierżawca/administrator globalny można przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Przypisywanie uprawnień do tworzenia magazynu kluczy

Przypisywanie uprawnień przypisania ról do grupy zasobów, w której znajduje się projekt migracji platformy Azure, w następujący sposób:

1. W grupie zasobów w witrynie Azure portal wybierz pozycję **Kontrola dostępu (IAM)**.
2. W **obszarze Sprawdź dostęp**znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia. Potrzebujesz uprawnień **Właściciela** (lub **Współautora** i **Administratora dostępu użytkownika).**
3. Jeśli nie masz wymaganych uprawnień, poproś o nie właściciela grupy zasobów. 


## <a name="prepare-on-premises-vmware"></a>Przygotowywanie lokalnego wdrożenia oprogramowania VMware

### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Migracja serwera migracji platformy Azure wymaga dostępu do serwerów VMware, aby:

- Automatyczne odnajdywanie maszyn wirtualnych. Wymagane jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Potrzebne jest konto, na którym można uruchamiać operacje, takie jak tworzenie i usuwanie dysków, a także włączanie maszyn wirtualnych.

Utwórz konto w następujący sposób:

1. Aby użyć dedykowanego konta, utwórz rolę na poziomie vCenter. Nadaj roli nazwę, taką jak **Azure_Site_Recovery**.
2. Przypisz do roli uprawnienia podsumowane w poniższej tabeli.
3. Utwórz użytkownika na serwerze vCenter lub hoście vSphere. Przypisz tę rolę temu użytkownikowi.

#### <a name="vmware-account-permissions"></a>Uprawnienia konta VMware

**Zadanie** | **Rola/uprawnienia** | **Szczegóły**
--- | --- | ---
**Odnajdowanie maszyn wirtualnych** | Co najmniej użytkownik tylko do odczytu<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z **propagate do** obiektu podrzędnego, do obiektów podrzędnych (hosty vSphere, magazyny danych, maszyny wirtualne i sieci).
**Pełna replikacja, tryb failover i powrót po awarii** |  Utwórz rolę (Azure_Site_Recovery) z wymaganymi uprawnieniami, a następnie przypisz ją użytkownikowi lub grupie VMware<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = Azure_Site_Recovery<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z **propagate do** obiektu podrzędnego, do obiektów podrzędnych (hosty vSphere, magazyny danych, VMsa, sieci nd).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługę Mobility należy zainstalować na każdej maszynie, która ma być replikowana.

- Urządzenie replikacji migracji platformy Azure może wykonać instalację wypychaną tej usługi po włączeniu replikacji komputera lub można zainstalować ją ręcznie lub przy użyciu narzędzi instalacyjnych.
- W tym samouczku zamierzamy zainstalować usługę Mobility przy użyciu instalacji typu push.
- Do instalacji wypychanej należy przygotować konto, którego migracja serwera migracji usługi Azure może używać do uzyskiwania dostępu do maszyny Wirtualnej. To konto jest używane tylko do instalacji wypychanej, jeśli usługa mobilności nie zostanie zainstalowana ręcznie.

Przygotuj konto w następujący sposób:

1. Przygotuj domenę lub konto lokalne z uprawnieniami do instalowania na maszynie wirtualnej.
2. Jeśli nie korzystasz z konta domeny, wyłącz kontrolę dostępu użytkownika zdalnego na komputerze lokalnym, dodając wpis DWORD **LocalAccountTokenFilterPolicy**, o wartości w rejestrze, w obszarze **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. W przypadku maszyn wirtualnych z systemem Linux należy przygotować konto główne na źródłowym serwerze Systemu Linux.


### <a name="check-vmware-requirements"></a>Sprawdzanie wymagań dotyczących programu VMware

Upewnij się, że serwery VMware i maszyny wirtualne spełniają wymagania dotyczące migracji na platformę Azure. 


> [!NOTE]
> Migracja oparta na agentach za pomocą migracji serwera migracji usługi Azure jest oparta na funkcjach usługi Azure Site Recovery. Niektóre wymagania mogą zawierać łącza do dokumentacji odzyskiwania witryny.

1. [Zweryfikuj](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) wymagania dotyczące serwerów VMware.
2. [Weryfikacja](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) Wymagania dotyczące obsługi maszyn wirtualnych dla migracji.
3. Sprawdź ustawienia maszyny Wirtualnej. Lokalne maszyny wirtualne, które replikujesz na platformie Azure, muszą spełniać [wymagania dotyczące maszyn wirtualnych platformy Azure.](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)



## <a name="add-the-azure-migrate-server-migration-tool"></a>Dodawanie narzędzia migracji serwera migracji platformy Azure

Jeśli nie po samouczku oceny maszyn wirtualnych VMware, skonfiguruj projekt migracji platformy Azure, a następnie dodaj narzędzie migracji serwera migracji usługi Azure:

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.

    ![Konfigurowanie migracji platformy Azure](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. W obszarze **Przegląd** kliknij pozycję **Ocena i migracja serwerów**.
4. W obszarze **Odnajdowanie oceniaj i migruj serwery**kliknij pozycję **Oceń i migruj serwery**.

    ! [Odkryj i oceń serwery] (./media/tutorial-migrate-vmware-agent/assess-migrate.png

1. W obszarze **Odnajdywanie, ocena i migracja serwerów** kliknij pozycję **Dodaj narzędzia**.
2. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.
3. W obszarze **Szczegóły projektu** określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt, a następnie kliknij przycisk **Dalej**. Przejrzyj obsługiwane obszary geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Tworzenie projektu migracji platformy Azure](./media/tutorial-migrate-vmware-agent/migrate-project.png)


4. W **obszarze Wybierz narzędzie oceny**wybierz pozycję **Pomiń dodanie narzędzia oceny na razie** > **Dalej**.
5. W **narzędziu Wybierz migrację**wybierz pozycję **Migracja platformy Azure: Migracja** > serwera**dalej**.
6. W obszarze **Przegląd i dodawanie narzędzi** przejrzyj ustawienia, a następnie kliknij pozycję **Dodaj narzędzia**
7. Po dodaniu narzędzia pojawia się w projekcie migracji platformy Azure >**narzędzia migracji** **serwerów** > .

## <a name="set-up-the-replication-appliance"></a>Konfigurowanie urządzenia replikacji

Pierwszym krokiem migracji jest skonfigurowanie urządzenia replikacji. Urządzenie replikacji jest pojedynczą, wysoce dostępną lokalną maszyną wirtualną VMware, która obsługuje te składniki:

- **Serwer konfiguracji:** Serwer konfiguracji koordynuje komunikację między lokalną a platformą Azure i zarządza replikacją danych.
- **Serwer przetwarzania:** Serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji; optymalizuje go za pomocą buforowania, kompresji i szyfrowania i wysyła go do konta magazynu pamięci podręcznej na platformie Azure. Serwer procesów instaluje również agenta usługi mobilności na maszynach wirtualnych, które chcesz replikować, i wykonuje automatyczne odnajdowanie lokalnych maszyn wirtualnych VMware.


Urządzenie replikacji można skonfigurować na kilka sposobów.

- Skonfiguruj za pomocą pobranego szablonu Otwartej aplikacji wirtualizacji (OVA). Szablon należy zaimportować do urządzenia VMware i utworzyć maszynę wirtualną urządzenia replikacji. Jest to metoda używana w tym samouczku.
- Skonfiguruj za pomocą skryptu.

### <a name="download-the-replication-appliance-template"></a>Pobieranie szablonu urządzenia replikacji

Pobierz szablon w następujący sposób:

1. W projekcie migracji platformy Azure kliknij pozycję **Serwery** w obszarze **Cele migracji**.
2. W **usłudze Azure Migrate — serwery** > **Migracji platformy Azure: Migracja serwera**kliknij przycisk **Odkryj**.

    ![Odnajdywanie maszyn wirtualnych](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. W **discover maszyny** > **Czy twoje maszyny są zwirtualizowane?**, kliknij **przycisk Tak, z VMWare vSphere hypervisor**.
4. W **obszarze Jak chcesz przeprowadzić migrację?** **Using agent-based replication**
5. W **regionie docelowym**wybierz region platformy Azure, do którego chcesz przeprowadzić migrację komputerów.
6. Wybierz **pozycję Potwierdź, że regionem docelowym migracji jest nazwa regionu**.
7. Kliknij **pozycję Utwórz zasoby**. Spowoduje to utworzenie magazynu usługi Azure Site Recovery w tle.
    - Po kliknięciu tego przycisku nie można zmienić regionu docelowego dla tego projektu.
    - Wszystkie kolejne migracje są do tego regionu.

    ![Tworzenie magazynu usługi Recovery Services](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. W **obszarze Czy chcesz zainstalować nowe urządzenie replikacji?** wybierz pozycję **Instalowanie urządzenia replikacji**.
9. Kliknij **przycisk Pobierz**, aby pobrać urządzenie replikacji. Spowoduje to pobranie szablonu OVF używanego do tworzenia nowej maszyny wirtualnej VMware, która uruchamia urządzenie.
    ![Pobierz OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Zanotuj nazwę grupy zasobów i magazynu usług odzyskiwania. Są one potrzebne podczas wdrażania urządzenia.


### <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware

Po pobraniu szablonu OVF należy zaimportować go do oprogramowania VMware, aby utworzyć aplikację replikacji na maszynie VMware VMware z systemem Windows Server 2016.

1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **Plik** wybierz polecenie **Wdrażanie szablonu OVF,** aby uruchomić **Kreatora wdrażania szablonu OVF**. 
3. W **obszarze Wybierz źródło**wprowadź lokalizację pobranego OVF.
4. W **obszarze Recenzja szczegóły**wybierz pozycję **Dalej**.
5. W **obszarze Wybierz nazwę i folder** oraz Wybierz **konfigurację**zaakceptuj ustawienia domyślne.
6. W **obszarze Wybierz pamięć masową** > Wybierz format dysku**wirtualnego**, aby uzyskać najlepszą wydajność, wybierz **opcję Gruby poboru Eager Wyzerowane**.
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. W **obszarze Gotowe do ukończenia**, aby skonfigurować maszynę wirtualną z ustawieniami domyślnymi, wybierz pozycję Włącz po **wdrożeniu** > **Zakończ**.

   > [!TIP]
   > Jeśli chcesz dodać dodatkową kartę sieciową, wyczyść **włącz zasilanie po wdrożeniu** > **Zakończ**. Domyślnie szablon zawiera jedną kartę sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.

### <a name="kick-off-replication-appliance-setup"></a>Rozpoczęcie konfiguracji urządzenia replikacji

1. Włącz maszynę wirtualną z poziomu konsoli klienta VMware vSphere.
2. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się do maszyny Wirtualnej jako administrator, używając hasła administratora.
4. Przy pierwszym logacji narzędzie do konfigurowania urządzenia replikacji (Narzędzie konfiguracji odzyskiwania witryny Azure) uruchamia się w ciągu kilku sekund.
5. Wprowadź nazwę używaną do rejestrowania urządzenia za pomocą migracji serwera migracji usługi Azure Migrate Server. Następnie kliknij przycisk **Dalej**.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure.
7. Poczekaj, aż narzędzie zakończy rejestrowanie aplikacji usługi Azure AD w celu zidentyfikowania urządzenia. Urządzenie zostanie ponownie uruchomione.
1. Ponownie zaloguj się do maszyny. W ciągu kilku sekund zostanie automatycznie uruchomiony kreator zarządzania serwerem konfiguracji.

### <a name="register-the-replication-appliance"></a>Rejestrowanie urządzenia replikacji

Zakończ konfigurowanie i rejestrowanie urządzenia replikacji.

1. W Kreatorze zarządzania serwerem konfiguracji wybierz pozycję **Łączność Instalatora**.
2. Wybierz kartę sieciową (domyślnie jest tylko jedna karta sieciowa), której urządzenie replikacji używa do odnajdowania maszyn wirtualnych, i wykonaj instalację wypychaną usługi mobilności na komputerach źródłowych.
3. Wybierz kartę sieciową używana przez urządzenie replikacji do łączności z platformą Azure. Następnie wybierz pozycję **Zapisz**. Nie można zmienić tego ustawienia po jego skonfigurowaniu.
4. Jeśli urządzenie znajduje się za serwerem proxy, należy określić ustawienia serwera proxy.
    - Określ nazwę **http://ip-address**serwera **http://FQDN**proxy jako , lub . Serwery proxy HTTPS nie są obsługiwane.
5. Po wyświetleniu monitu o subskrypcję, grupy zasobów i szczegóły przechowalni dodaj szczegóły, które zostały odnotowane podczas pobierania szablonu urządzenia.
6. Na stronie **Instalowanie oprogramowania innych firm** zaakceptuj umowę licencyjną. Wybierz pozycję **Pobierz i zainstaluj**, aby zainstalować program MySQL Server.
7. Wybierz pozycję **Zainstaluj interfejs PowerCLI programu VMware**. Przed wykonaniem tej czynności zamknij wszystkie okna przeglądarki. Następnie wybierz pozycję **Kontynuuj**.
8. W obszarze **Weryfikowanie konfiguracji urządzenia** zostaną zweryfikowane wymagania wstępne, a następnie będzie można kontynuować.
9. W obszarze **Skonfiguruj poświadczenia serwera vCenter Server/vSphere ESXi** wprowadź nazwę FQDN bądź adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
10. Wprowadź poświadczenia dla konta [utworzonego](#prepare-an-account-for-automatic-discovery) dla odnajdowania VMware. Wybierz **pozycję Dodaj** > **kontynuuj**.
11. W **obszarze Konfigurowanie poświadczeń maszyny wirtualnej**wprowadź poświadczenia [utworzone](#prepare-an-account-for-mobility-service-installation) do instalacji wypychanej usługi Mobilności po włączeniu replikacji maszyn wirtualnych.  
    - W przypadku maszyn z systemem Windows konto wymaga uprawnień administratora lokalnego na replikowanych maszynach.
    - W przypadku systemu Linux należy podać dane superużytkownika.
12. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**.


Po zarejestrowaniu urządzenia replikacji usługa Azure Migrate Server Assessment łączy się z serwerami VMware przy użyciu określonych ustawień i odnajduje maszyny wirtualne. Odnalezione maszyny wirtualne można wyświetlić w obszarze > **Zarządzanie odnalezionymi elementami**na karcie **Inne.** **Manage**


## <a name="replicate-vms"></a>Replikowanie maszyn wirtualnych

Teraz wybierz maszyny wirtualne do migracji.

> [!NOTE]
> Można replikować do 10 maszyn razem. Jeśli chcesz replikować więcej, a następnie replikuj je jednocześnie w partiach po 10.

1. W projekcie migracji platformy Azure > **serwery**, **Migracja platformy Azure: Migracja serwera**, kliknij przycisk **Replikuj**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. W obszarze **Replikacja** > **Ustawienia źródła** > **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**.
3. W **urządzeniu lokalnym**wybierz nazwę skonfigurowanego urządzenia migracji platformy Azure.
4. Na **serwerze vCenter**określ nazwę serwera vCenter zarządzającego maszynami wirtualnymi lub serwera vSphere, na którym są hostowane maszyny wirtualne.
5. W **obszarze Serwer przetwarzania**wybierz nazwę urządzenia replikacji.
6. W **poświadczeń gościa**określ konto administratora maszyny Wirtualnej, które będzie używane do instalacji wypychanej usługi mobilności. Następnie kliknij **przycisk Dalej: Maszyny wirtualne**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. W **obszarze Maszyny wirtualne**wybierz maszyny, które chcesz replikować.

    - Jeśli przeprowadzasz ocenę dla maszyn wirtualnych, możesz zastosować rekomendacje dotyczące rozmiarów maszyn wirtualnych i typów dysków (Premium/standardowy) w wynikach oceny. W tym celu w obszarze **Zaimportować ustawienia migracji z oceny usługi Azure Migrate?** wybierz opcję **Tak**.
    - Jeśli nie uruchomiono oceny lub nie chcesz używać ustawień oceny, wybierz opcję **Nie**.
    - W przypadku wybrania opcji korzystania z oceny wybierz grupę maszyn wirtualnych i nazwę oceny.

8. Sprawdź każdą maszynę wirtualną, którą chcesz przeprowadzić migrację. Następnie kliknij **przycisk Dalej: Ustawienia celu**.
9. W obszarze **Ustawienia elementu docelowego** wybierz subskrypcję i docelowy region migracji, a następnie określ grupę zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji.
10. W obszarze **Sieć wirtualna** wybierz sieć wirtualną/podsieć platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
11. W obszarze **Korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Dalej**.

12. W obszarze **Obliczenia** sprawdź nazwę, rozmiar, typ dysku systemu operacyjnego i zestaw dostępności maszyny wirtualnej. Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms).

    - **Rozmiar maszyny Wirtualnej:** Jeśli używasz zaleceń dotyczących oceny, lista rozwijana rozmiaru maszyny Wirtualnej będzie zawierać zalecany rozmiar. W przeciwnym razie usługa Azure Migrate wybierze rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (rozruchowego) maszyny Wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego. 
    - **Zestaw dostępności:** Jeśli maszyna wirtualna powinna znajdować się w zestawie dostępności platformy Azure po migracji, określ zestaw. Zestaw musi znajdować się w docelowej grupie zasobów określonej dla migracji.

13. W **obszarze Dyski**określ, czy dyski maszyn wirtualnych mają być replikowane na platformę Azure, i wybierz typ dysku (standardowy dysk SSD/HDD lub dyski zarządzane w wersji premium) na platformie Azure. Następnie kliknij przycisk **Dalej**.
    - Dyski można wykluczyć z replikacji.
    - Jeśli wykluczysz dyski, nie będą one znajdować się na maszynie wirtualnej platformy Azure po migracji. 

14. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można zaktualizować w dowolnym momencie przed rozpoczęciem replikacji, **Zarządzanie maszynami** > **replikacji**. Ustawień nie można zmienić po rozpoczęciu replikacji.




## <a name="track-and-monitor"></a>Śledzenie i monitorowanie

- Po **kliknięciu przycisku Replikacja** rozpoczyna się zadanie replikacji początkowej. 
- Po pomyślnym zakończeniu zadania replikacji początkowej maszyny rozpoczynają replikację początkową na platformie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja delta. Przyrostowe zmiany na dyskach lokalnych są okresowo replikowane na dyski repliki na platformie Azure.


Stan zadania można śledzić w powiadomieniach portalu.

![Śledzenie zadania](./media/tutorial-migrate-vmware-agent/jobs.png)

Stan replikacji można monitorować, klikając opcję **Replikowanie serwerów** w **programie Azure Migrate: Server Migration**.
![Monitorowanie replikacji](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po rozpoczęciu replikacji różnicowej można uruchomić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zaleca się, aby zrobić to co najmniej raz dla każdego komputera, przed migracją.

- Uruchomienie migracji testowej sprawdza, czy migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które pozostają operacyjne i kontynuują replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu replikowanych danych (zwykle migrując do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Za pomocą replikowanego testu maszyny Wirtualnej platformy Azure można sprawdzić poprawność migracji, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wykonaj migrację testową w następujący sposób:


1. W **celach** > migracji**Serwery** > **Usługi Azure Migrate: Migracja serwera**kliknij przycisk **Testuj zmigrowane serwery**.

     ![Serwery z przeprowadzoną migracją testową](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. W obszarze **Testowanie migracji** wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Zostanie uruchomione zadanie **Testowanie migracji**. Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji sprawdź zmigrowane maszyny wirtualne platformy Azure w obszarze **Maszyny wirtualne** w witrynie Azure Portal. Nazwa maszyny ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **Replikowanie maszyn**, a następnie kliknij pozycję **Wyczyść migrację testową**.

    ![Czyszczenie migracji](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po sprawdzeniu, że migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację komputerów lokalnych.

1. W projekcie migracji platformy Azure > **serwery** > **Usługi Azure Migrate: Migracja serwera**kliknij pozycję **Replikowanie serwerów**.

    ![Replikowanie serwerów](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. W **obszarze Migracja** > Zamknij**maszyny wirtualne i wykonaj planowaną migrację bez utraty danych**, wybierz tak **OK** > **OK**.
    - Domyślnie usługa Azure Migrate zamyka lokalną maszynę wirtualną, aby zapewnić minimalną utratę danych. 
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **Nie**
4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie **Maszyny wirtualne**.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną > **Zatrzymaj migrację**. Spowoduje to wykonanie następujących czynności:
    - Zatrzymuje replikację komputera lokalnego.
    - Usuwa komputer z licznika **serwerów replikacji** w usłudze Azure Migrate: Server Migration.
    - Czyści informacje o stanie replikacji dla maszyny Wirtualnej.
2. Zainstaluj agenta systemu [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) platformy Azure na zmigrowanych komputerach.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Przetnij ruch do zmigrowanego wystąpienia maszyny Wirtualnej platformy Azure.
6. Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
7. Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
8. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure. 

## <a name="post-migration-best-practices"></a>Najlepsze praktyki po migracji

- Lokalnie
    - Przenieś ruch aplikacji do aplikacji uruchomionej na zmigrowanym wystąpieniu maszyny wirtualnej platformy Azure.
    - Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
    - Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
    - Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure.
- Dostosuj ustawienia maszyny Wirtualnej platformy Azure po migracji:
    - [Agent maszyny wirtualnej platformy Azure](../virtual-machines/extensions/agent-windows.md) umożliwia zarządzanie interakcją maszyny wirtualnej z usługą Azure Fabric Controller. Jest to wymagane w przypadku niektórych usług platformy Azure, takich jak Azure Backup, Site Recovery i Azure Security. Podczas migracji maszyn wirtualnych VMare z migracją opartą na agentach instalator usługi mobilności instaluje agenta maszyn wirtualnych platformy Azure na komputerach z systemem Windows. Na maszynach wirtualnych z systemem Linux zaleca się zainstalowanie agenta po migracji.
    - Ręcznie odinstaluj usługę mobilności z maszyny Wirtualnej platformy Azure po migracji.
    - Ręcznie odinstaluj narzędzia VMware po migracji.
- Na platformie Azure:
    - Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
    - Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
- Ciągłość działania/odzyskiwanie po awarii
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Blokowanie i ograniczanie dostępu do ruchu przychodzącego za pomocą [usługi Azure Security Center — zarządzanie tylko w czasie](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Wdróż usługę [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
    - Rozważ wdrożenie usługi [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), aby monitorować użycie zasobu i wydatki.




 ## <a name="next-steps"></a>Następne kroki

Zbadaj [proces migracji](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) do chmury w ramach wdrażania chmury azure.
