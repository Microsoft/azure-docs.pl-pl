---
title: Migrowanie maszyn wirtualnych VMware przy użyciu migracji Azure Migrate serwera opartego na agentach
description: Dowiedz się, jak uruchomić migrację maszyn wirtualnych VMware z użyciem agentów przy użyciu Azure Migrate.
ms.topic: tutorial
ms.date: 03/09/2020
ms.custom: MVC
ms.openlocfilehash: 6855c3e81aece0358146608b6cf179fb923c54c8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81535336"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrowanie maszyn wirtualnych VMware na platformę Azure (oparte na agentach)

W tym artykule opisano sposób migrowania lokalnych maszyn wirtualnych programu VMware na platformę Azure przy użyciu migracji opartej na agentach za pomocą narzędzia migracji Azure Migrate Server.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj środowisko źródłowe i Wdróż Azure Migrate urządzenie do replikacji na potrzeby migracji na podstawie agenta.
> * Skonfiguruj środowisko docelowe do migracji.
> * Skonfiguruj zasady replikacji.
> * Włącz replikację.
> * Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację na platformę Azure.

> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z tematem Porady dotyczące oceny i migracji programu VMware.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) .

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed migracją maszyn wirtualnych na platformę Azure zalecamy wypróbowanie oceny maszyn wirtualnych VMware przy użyciu oceny serwera Azure Migrate. Skonfiguruj ocenę w następujący sposób:

1. Postępuj zgodnie z samouczkiem, aby [przygotować platformę Azure i oprogramowanie VMware](tutorial-prepare-vmware.md) do oceny.
2. Następnie postępuj zgodnie z [tym samouczkiem](tutorial-assess-vmware.md) , aby skonfigurować urządzenie Azure Migrate na potrzeby oceny oraz odnajdywać i oceniać maszyny wirtualne.


Chociaż zalecamy wypróbowanie oceny, nie trzeba wykonywać oceny przed migracją maszyn wirtualnych.

## <a name="migration-methods"></a>Metody migracji

Maszyny wirtualne programu VMware można migrować do platformy Azure za pomocą narzędzia migracji Azure Migrate Server. To narzędzie oferuje kilka opcji migracji maszyn wirtualnych VMware:

- Replikacja bez wykorzystania agentów. Migrowanie maszyn wirtualnych bez konieczności instalowania na nich jakichkolwiek elementów.
- Migracja oparta na agentach. lub replikacja. Zainstaluj agenta (agenta usług mobilności) na maszynie wirtualnej na potrzeby replikacji.

Aby zdecydować, czy chcesz korzystać z migracji opartej na agencie, czy na agencie, zapoznaj się z następującymi artykułami:

- [Dowiedz się więcej na temat](server-migrate-overview.md) opcji migracji programu VMware.
- [PORÓWNAJ metody migracji](server-migrate-overview.md#compare-migration-methods).
- [Postępuj zgodnie z tym artykułem](tutorial-migrate-vmware.md) , aby wypróbować migrację bez agentów.



## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. [Przejrzyj](migrate-architecture.md) architekturę migracji VMware.
2. Upewnij się, że Twoje konto platformy Azure ma przypisaną rolę współautor maszyny wirtualnej, dzięki czemu masz uprawnienia do:

    - Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
    - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
    - Zapisz na dysku zarządzanym platformy Azure. 

3. [Skonfiguruj sieć platformy Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Maszyny lokalne są replikowane do usługi Azure Managed Disks. Po przełączeniu w tryb failover na platformie Azure na potrzeby migracji maszyny wirtualne platformy Azure są tworzone na podstawie tych dysków zarządzanych i dołączane do sieci platformy Azure, którą określisz podczas konfigurowania migracji.


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Jeśli masz już uruchomioną ocenę przy użyciu Azure Migrate oceny serwera, możesz pominąć instrukcje przedstawione w tej sekcji, ponieważ zostały one już wykonane. 

Jeśli nie uruchomiono oceny, przed rozpoczęciem migracji z Azure Migrate migracji na serwer należy skonfigurować uprawnienia platformy Azure.

- **Utwórz projekt**: Twoje konto platformy Azure wymaga uprawnień do utworzenia projektu Azure Migrate. 
- **Zarejestruj urządzenie replikacji Azure Migrate**: urządzenie do replikacji tworzy i rejestruje aplikację Azure Active Directory na koncie platformy Azure. Delegowanie uprawnień dla tego elementu.
- **Tworzenie Key Vault**: Aby przeprowadzić migrację maszyn wirtualnych VMware przy użyciu migracji serwera Azure Migrate, Azure Migrate tworzy Key Vault w grupie zasobów, aby zarządzać kluczami dostępu do konta magazynu replikacji w ramach subskrypcji. Aby utworzyć magazyn, musisz mieć uprawnienia do przypisywania ról w grupie zasobów, w której znajduje się projekt Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W Azure Portal Otwórz subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)**.
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Należy mieć uprawnienia **współautora** lub **właściciela** .
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać rolę.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Przypisywanie uprawnień do rejestrowania urządzenia replikacji

W przypadku migracji opartej na agencie delegowanie uprawnień do migracji serwera Azure Migrate do tworzenia i rejestrowania aplikacji usługi Azure AD na Twoim koncie. Uprawnienia można przypisywać przy użyciu jednej z następujących metod:

- Dzierżawa/Administrator globalny może udzielić uprawnień użytkownikom w dzierżawie, aby tworzyć i rejestrować aplikacje usługi Azure AD.
- Dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji (z uprawnieniami) do konta.

Warto zauważyć, że:

- Aplikacje nie mają żadnych innych uprawnień dostępu do subskrypcji innych niż opisane powyżej.
- Te uprawnienia są potrzebne tylko podczas rejestrowania nowego urządzenia replikacji. Po skonfigurowaniu urządzenia replikacji można usunąć uprawnienia. 


#### <a name="grant-account-permissions"></a>Udziel uprawnień konta

Dzierżawy/Administrator globalny mogą udzielić uprawnień w następujący sposób

1. W usłudze Azure AD dzierżawca/Administrator globalny powinien przejść do**ustawień użytkownika** **Azure Active Directory** > **Użytkownicy** > .
2. Administrator powinien ustawić **rejestracje aplikacji** na **wartość tak**.

    ![Uprawnienia usługi Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Jest to ustawienie domyślne, które nie jest poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Przypisywanie roli Deweloper aplikacji 

Dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Przypisywanie uprawnień do tworzenia Key Vault

Przypisz uprawnienia do przypisywania ról do grupy zasobów, w której znajduje się projekt Azure Migrate, w następujący sposób:

1. W grupie zasobów w Azure Portal wybierz pozycję **Kontrola dostępu (IAM)**.
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia. Wymagane są uprawnienia **właściciela** (lub **współautora** oraz **administrator dostępu użytkowników**).
3. Jeśli nie masz wymaganych uprawnień, poproś ich od właściciela grupy zasobów. 


## <a name="prepare-on-premises-vmware"></a>Przygotowywanie lokalnego wdrożenia oprogramowania VMware

### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Migracja serwera Azure Migrate wymaga dostępu do serwerów VMware w celu:

- Automatyczne odnajdywanie maszyn wirtualnych. Wymagane jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Potrzebne jest konto, na którym można uruchamiać operacje, takie jak tworzenie i usuwanie dysków, a także włączanie maszyn wirtualnych.

Utwórz konto w następujący sposób:

1. Aby użyć dedykowanego konta, utwórz rolę na poziomie vCenter. Nadaj roli nazwę, taką jak **Azure_Site_Recovery**.
2. Przypisz do roli uprawnienia podsumowane w poniższej tabeli.
3. Utwórz użytkownika na serwerze vCenter lub hoście vSphere. Przypisz tę rolę temu użytkownikowi.

#### <a name="vmware-account-permissions"></a>Uprawnienia konta VMware

**Zadanie** | **Rola/uprawnienia** | **Szczegóły**
--- | --- | ---
**Odnajdowanie maszyn wirtualnych** | Co najmniej użytkownik tylko do odczytu<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z obiektem **Propaguj do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).
**Pełna replikacja, tryb failover i powrót po awarii** |  Utwórz rolę (Azure_Site_Recovery) z wymaganymi uprawnieniami, a następnie przypisz ją użytkownikowi lub grupie VMware<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = Azure_Site_Recovery<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z obiektem **Propaguj do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, VMsa, sieci ND).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługę Mobility należy zainstalować na każdej maszynie, która ma być replikowana.

- Urządzenie replikacji Azure Migrate może przeprowadzić instalację wypychaną tej usługi po włączeniu replikacji dla maszyny lub można ją zainstalować ręcznie lub za pomocą narzędzi instalacyjnych.
- W tym samouczku zamierzamy zainstalować usługę Mobility przy użyciu instalacji typu push.
- W przypadku instalacji wypychanej należy przygotować konto, za pomocą którego Migracja serwera Azure Migrate może być używana do uzyskiwania dostępu do maszyny wirtualnej. To konto jest używane tylko w przypadku instalacji wypychanej, jeśli usługa mobilności nie zostanie zainstalowana ręcznie.

Przygotuj konto w następujący sposób:

1. Przygotuj domenę lub konto lokalne z uprawnieniami do instalowania na maszynie wirtualnej.
2. W przypadku maszyn wirtualnych z systemem Windows, jeśli nie korzystasz z konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na maszynie lokalnej przez dodanie wpisu DWORD **LocalAccountTokenFilterPolicy**z wartością w rejestrze w obszarze **HKEY_LOCAL_MACHINE \software\microsoft\windows\currentversion\policies\system**
3. W przypadku maszyn wirtualnych z systemem Linux Przygotuj konto główne na źródłowym serwerze z systemem Linux.


### <a name="check-vmware-requirements"></a>Sprawdzanie wymagań dotyczących programu VMware

Upewnij się, że serwery VMware i maszyny wirtualne spełniają wymagania dotyczące migracji na platformę Azure. 


> [!NOTE]
> Migracja oparta na agencie z migracją Azure Migrate Server jest oparta na funkcjach Azure Site Recovery usługi. Niektóre wymagania mogą być połączone z dokumentacją Site Recovery.

1. [Zweryfikuj](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) wymagania dotyczące serwerów VMware.
2. [Sprawdź](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) Wymagania dotyczące migracji maszyn wirtualnych.
3. Sprawdź ustawienia maszyny wirtualnej. Lokalne maszyny wirtualne replikowane na platformę Azure muszą być zgodne z [wymaganiami maszyny wirtualnej platformy Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Dodawanie narzędzia migracji Azure Migrate Server

Jeśli nie korzystasz z samouczka do oceny maszyn wirtualnych VMware, skonfiguruj projekt Azure Migrate, a następnie Dodaj narzędzie do migracji Azure Migrate Server:

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.

    ![Skonfiguruj Azure Migrate](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. W obszarze **Przegląd** kliknij pozycję **Ocena i migracja serwerów**.
4. W obszarze **odnajdywanie, ocenianie i Migrowanie serwerów**kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.

    ! [Odnajdywanie i ocenianie serwerów] (./media/tutorial-migrate-vmware-agent/assess-migrate.png

1. W obszarze **Odnajdywanie, ocena i migracja serwerów** kliknij pozycję **Dodaj narzędzia**.
2. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.
3. W obszarze **Szczegóły projektu** określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt, a następnie kliknij przycisk **Dalej**. Przejrzyj obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Tworzenie projektu Azure Migrate](./media/tutorial-migrate-vmware-agent/migrate-project.png)


4. W **narzędziu Wybierz ocenę**wybierz pozycję **Pomiń Dodawanie narzędzia do oceny teraz** > **.**
5. W obszarze **Wybieranie narzędzia migracji**wybierz **Azure Migrate: migracja** > serwera**dalej**.
6. W obszarze **Przegląd i dodawanie narzędzi** przejrzyj ustawienia, a następnie kliknij pozycję **Dodaj narzędzia**
7. Po dodaniu narzędzia pojawia się ono w Azure Migrate**Narzędzia migracji** **serwerów** > > projektu.

## <a name="set-up-the-replication-appliance"></a>Konfigurowanie urządzenia do replikacji

Pierwszym krokiem migracji jest skonfigurowanie urządzenia do replikacji. Urządzenie do replikacji to jedna maszyna wirtualna VMware o wysokiej dostępności, która obsługuje następujące składniki:

- **Serwer konfiguracji**: serwer konfiguracji koordynuje komunikację między środowiskiem lokalnym i platformą Azure oraz zarządza replikacją danych.
- **Serwer przetwarzania**: serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do konta magazynu pamięci podręcznej na platformie Azure. Serwer przetwarzania instaluje również agenta usługi mobilności na maszynach wirtualnych, które mają być replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.


Urządzenie do replikacji można skonfigurować na kilka sposobów.

- Skonfiguruj przy użyciu pobranego szablonu Open Virtualization Application (komórki jajowe). Należy zaimportować szablon do programu VMware i utworzyć maszynę wirtualną urządzenia replikacji. Ta metoda jest używana w tym samouczku.
- Skonfiguruj przy użyciu skryptu.

### <a name="download-the-replication-appliance-template"></a>Pobierz szablon urządzenia replikacji

Pobierz szablon w następujący sposób:

1. W projekcie Azure Migrate kliknij pozycję **serwery** w obszarze **cele migracji**.
2. W **Azure Migrate serwery** > **Azure Migrate: Migracja serwera**, kliknij przycisk **odkryj**.

    ![Odnajdywanie maszyn wirtualnych](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. W obszarze **odnajdywanie** > maszyn**są zwirtualizowane maszyny?** kliknij przycisk **tak, z funkcją hypervisor programu VMware vSphere**.
4. W **jaki sposób chcesz przeprowadzić migrację?** wybierz pozycję **przy użyciu replikacji opartej na agentach**.
5. W **obszarze region docelowy**wybierz region świadczenia usługi Azure, do którego chcesz przeprowadzić migrację maszyn.
6. Wybierz pozycję **Potwierdź, że docelowy region migracji to nazwa regionu**.
7. Kliknij pozycję **Utwórz zasoby**. Spowoduje to utworzenie magazynu Azure Site Recovery w tle.
    - Po kliknięciu tego przycisku nie można zmienić regionu docelowego dla tego projektu.
    - Wszystkie kolejne migracje znajdują się w tym regionie.

    ![Tworzenie magazynu usługi Recovery Services](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. W **czy chcesz zainstalować nowe urządzenie do replikacji?** wybierz pozycję **Zainstaluj urządzenie replikacji**.
9. Kliknij pozycję **Pobierz**, aby pobrać urządzenie do replikacji. Spowoduje to pobranie szablonu OVF, który zostanie użyty do utworzenia nowej maszyny wirtualnej VMware, na której działa urządzenie.
    ![Pobierz komórki jajowe](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Zanotuj nazwę grupy zasobów i magazynu Recovery Services. Są one potrzebne podczas wdrażania urządzenia.


### <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware

Po pobraniu szablonu OVF należy zaimportować go do programu VMware, aby utworzyć aplikację replikacji na maszynie wirtualnej VMware z systemem Windows Server 2016.

1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **plik** wybierz polecenie **Wdróż szablon OVF** , aby uruchomić **Kreatora wdrażania szablonu OVF**. 
3. W obszarze **Wybierz źródło**wprowadź lokalizację pobranego OVF.
4. W obszarze **szczegóły przeglądu**wybierz pozycję **dalej**.
5. W obszarze **Wybierz nazwę i folder** i **Wybierz pozycję Konfiguracja**, zaakceptuj ustawienia domyślne.
6. W obszarze **Wybieranie magazynu** > **Wybierz pozycję Format dysku wirtualnego**, aby uzyskać najlepszą wydajność, wybierz opcję **grube udostępnianie eager zero**.
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. Aby skonfigurować maszynę wirtualną z ustawieniami domyślnymi, wybierz pozycję **Włącz po** > **zakończeniu**wdrażania. **Ready to complete**

   > [!TIP]
   > Jeśli chcesz dodać dodatkową kartę sieciową, usuń zaznaczenie pola wyboru **Włącz po** > **zakończeniu**wdrażania. Domyślnie szablon zawiera jedną kartę sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.

### <a name="kick-off-replication-appliance-setup"></a>Uruchamianie konfiguracji urządzenia replikacji

1. Włącz maszynę wirtualną z poziomu konsoli klienta VMware vSphere.
2. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się do maszyny wirtualnej jako administrator przy użyciu hasła administratora.
4. Gdy logujesz się po raz pierwszy, narzędzie konfiguracji urządzenia do replikacji (Narzędzie konfiguracji Azure Site Recovery) rozpocznie się w ciągu kilku sekund.
5. Wprowadź nazwę, która ma zostać użyta do zarejestrowania urządzenia w ramach migracji Azure Migrate Server. Następnie kliknij przycisk **Dalej**.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure.
7. Zaczekaj, aż narzędzie zakończy rejestrowanie aplikacji usługi Azure AD w celu zidentyfikowania urządzenia. Urządzenie zostanie ponownie uruchomione.
1. Ponownie zaloguj się do maszyny. W ciągu kilku sekund zostanie automatycznie uruchomiony kreator zarządzania serwerem konfiguracji.

### <a name="register-the-replication-appliance"></a>Rejestrowanie urządzenia replikacji

Zakończ Konfigurowanie i rejestrowanie urządzenia replikacji.

1. W Kreatorze zarządzania serwerem konfiguracji wybierz pozycję **Instalacja łączność**.
2. Wybierz kartę sieciową (domyślnie istnieje tylko jedna karta sieciowa), którą urządzenie replikacji używa do odnajdywania maszyn wirtualnych, a następnie wykonaj instalację wypychaną usługi mobilności na maszynach źródłowych.
3. Wybierz kartę sieciową używaną przez urządzenie do replikacji na potrzeby łączności z platformą Azure. Następnie wybierz pozycję **Zapisz**. Po skonfigurowaniu tego ustawienia nie można go zmienić.
4. Jeśli urządzenie znajduje się za serwerem proxy, należy określić ustawienia serwera proxy.
    - Określ nazwę serwera proxy jako **http://ip-address**lub **http://FQDN**. Serwery proxy HTTPS nie są obsługiwane.
5. Po wyświetleniu monitu o subskrypcję, grupy zasobów i szczegóły magazynu należy dodać szczegóły zanotowane podczas pobraniu szablonu urządzenia.
6. Na stronie **Instalowanie oprogramowania innych firm** zaakceptuj umowę licencyjną. Wybierz pozycję **Pobierz i zainstaluj**, aby zainstalować program MySQL Server.
7. Wybierz pozycję **Zainstaluj interfejs PowerCLI programu VMware**. Przed wykonaniem tej czynności zamknij wszystkie okna przeglądarki. Następnie wybierz pozycję **Kontynuuj**.
8. W obszarze **Weryfikowanie konfiguracji urządzenia** zostaną zweryfikowane wymagania wstępne, a następnie będzie można kontynuować.
9. W obszarze **Skonfiguruj poświadczenia serwera vCenter Server/vSphere ESXi** wprowadź nazwę FQDN bądź adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
10. Wprowadź poświadczenia dla konta [utworzonego](#prepare-an-account-for-automatic-discovery) na potrzeby odnajdywania oprogramowania VMware. Wybierz pozycję **Dodaj** > **Kontynuuj**.
11. W obszarze **Konfiguruj poświadczenia maszyny wirtualnej**wprowadź poświadczenia [utworzone](#prepare-an-account-for-mobility-service-installation) na potrzeby instalacji wypychanej usługi mobilności po włączeniu replikacji dla maszyn wirtualnych.  
    - W przypadku maszyn z systemem Windows konto wymaga uprawnień administratora lokalnego na replikowanych maszynach.
    - W przypadku systemu Linux należy podać dane superużytkownika.
12. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**.


Po zarejestrowaniu urządzenia replikacji Azure Migrate oceny serwera nawiązuje połączenie z serwerami VMware przy użyciu określonych ustawień i odnajduje maszyny wirtualne. Odnalezione maszyny wirtualne można wyświetlić w obszarze **Zarządzanie** > **odnalezionymi elementami**na karcie **inne** .


## <a name="replicate-vms"></a>Replikowanie maszyn wirtualnych

Teraz wybierz maszyny wirtualne do migracji.

> [!NOTE]
> Można replikować do 10 maszyn jednocześnie. Jeśli chcesz replikować więcej, a następnie Replikuj je jednocześnie w partiach 10.

1. Na **serwerach**Azure Migrate project > **Azure Migrate: Migracja serwera**, kliknij przycisk **replikacja**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. W obszarze **Replikacja** > **Ustawienia źródła** > **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**.
3. W **urządzeniu lokalnym**wybierz nazwę skonfigurowanego urządzenia Azure Migrate.
4. W programie **vCenter Server**Określ nazwę serwera vCenter do zarządzania maszynami wirtualnymi lub serwer vSphere, na którym są hostowane maszyny wirtualne.
5. W obszarze **serwer przetwarzania**wybierz nazwę urządzenia replikacji.
6. W obszarze **poświadczenia gościa**Określ konto administratora maszyny wirtualnej, które będzie używane do instalacji wypychanej usługi mobilności. Następnie kliknij przycisk **Dalej: maszyny wirtualne**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. W **Virtual Machines**wybierz maszyny, które chcesz replikować.

    - Jeśli przeprowadzasz ocenę dla maszyn wirtualnych, możesz zastosować rekomendacje dotyczące rozmiarów maszyn wirtualnych i typów dysków (Premium/standardowy) w wynikach oceny. W tym celu w obszarze **Zaimportować ustawienia migracji z oceny usługi Azure Migrate?** wybierz opcję **Tak**.
    - Jeśli nie uruchomiono oceny lub nie chcesz używać ustawień oceny, wybierz opcję **Nie**.
    - W przypadku wybrania opcji korzystania z oceny wybierz grupę maszyn wirtualnych i nazwę oceny.

8. Sprawdź wszystkie maszyny wirtualne, które chcesz zmigrować. Następnie kliknij przycisk **Dalej: ustawienia docelowe**.
9. W obszarze **Ustawienia elementu docelowego** wybierz subskrypcję i docelowy region migracji, a następnie określ grupę zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji.
10. W obszarze **Sieć wirtualna** wybierz sieć wirtualną/podsieć platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
11. W obszarze **Korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Dalej**.

12. W obszarze **Obliczenia** sprawdź nazwę, rozmiar, typ dysku systemu operacyjnego i zestaw dostępności maszyny wirtualnej. Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms).

    - **Rozmiar maszyny wirtualnej**: Jeśli korzystasz z zaleceń dotyczących oceny, lista rozwijana rozmiaru maszyny wirtualnej będzie zawierać zalecany rozmiar. W przeciwnym razie usługa Azure Migrate wybierze rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (Boot) dla maszyny wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego. 
    - **Zestaw dostępności**: Jeśli maszyna wirtualna powinna znajdować się w zestawie dostępności platformy Azure po migracji, określ zestaw. Zestaw musi znajdować się w docelowej grupie zasobów określonej dla migracji.

13. W obszarze **dyski**Określ, czy dyski maszyn wirtualnych mają być replikowane na platformę Azure, a następnie wybierz typ dysku (standardowy dysk SSD lub dyski w warstwie Premium) na platformie Azure. Następnie kliknij przycisk **Dalej**.
    - Dyski można wykluczyć z replikacji.
    - Jeśli wykluczysz dyski, nie będą one znajdować się na maszynie wirtualnej platformy Azure po migracji. 

14. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można aktualizować w dowolnym momencie przed rozpoczęciem replikacji, **Zarządzanie** > **maszynami replikowanymi**. Ustawień nie można zmienić po rozpoczęciu replikacji.




## <a name="track-and-monitor"></a>Śledzenie i monitorowanie

- Po kliknięciu przycisku **Replikuj** rozpocznie się zadanie uruchamiania replikacji. 
- Po pomyślnym zakończeniu zadania Rozpocznij replikację maszyny rozpoczynają replikację początkową na platformę Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany w dyskach lokalnych są okresowo replikowane do dysków repliki na platformie Azure.


Stan zadania można śledzić w powiadomieniach portalu.

![Śledzenie zadania](./media/tutorial-migrate-vmware-agent/jobs.png)

Aby monitorować stan replikacji, należy kliknąć opcję **replikowanie serwerów** w **Azure Migrate: Migracja serwera**.
![Monitorowanie replikacji](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po rozpoczęciu replikacji różnicowej można przeprowadzić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zalecamy wykonanie tej czynności co najmniej raz na każdym komputerze, przed przeprowadzeniem migracji.

- Podczas przeprowadzania migracji testów migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które pozostają w działaniu i kontynuują replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu zreplikowanych danych (zazwyczaj migrujesz do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Możesz użyć zreplikowanego testowej maszyny wirtualnej platformy Azure, aby zweryfikować migrację, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wykonaj migrację testową w następujący sposób:


1. W obszarze**serwery** >  **celów** > migracji**Azure Migrate: Migracja serwera**, kliknij przycisk **Testuj zmigrowane serwery**.

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

1. W Azure Migrate **serwery** > > Project**Azure Migrate: Migracja serwera**, kliknij przycisk **replikowanie serwerów**.

    ![Replikowanie serwerów](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. W obszarze **Migrowanie** > **Zamknij maszyny wirtualne i przeprowadź planowaną migrację bez utraty danych**wybierz pozycję **tak** > **OK**.
    - Domyślnie Azure Migrate zamyka lokalną maszynę wirtualną, aby zapewnić minimalną utratę danych. 
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **Nie**
4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie **Maszyny wirtualne**.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną > **Zatrzymaj migrację**. Spowoduje to wykonanie następujących czynności:
    - Wyłącza replikację maszyny lokalnej.
    - Usuwa maszynę z liczby **serwerów replikowania** w Azure Migrate: Migracja serwera.
    - Czyści informacje o stanie replikacji maszyny wirtualnej.
2. Zainstaluj agenta maszyny wirtualnej platformy Azure dla [systemu Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) na zmigrowanych maszynach.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Obcinaj ruch do zmigrowanego wystąpienia maszyny wirtualnej platformy Azure.
6. Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
7. Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
8. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure. 

## <a name="post-migration-best-practices"></a>Najlepsze rozwiązania po migracji

- Lokalnie
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
    - Zablokuj i Ogranicz dostęp do ruchu przychodzącego za pomocą [administracji Azure Security Center w czasie](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Wdróż usługę [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
    - Rozważ wdrożenie usługi [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), aby monitorować użycie zasobu i wydatki.




 ## <a name="next-steps"></a>Następne kroki

Zbadaj [podróż migracji w chmurze](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) w strukturze wdrażania w chmurze platformy Azure.
