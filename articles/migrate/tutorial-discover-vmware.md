---
title: Odnajdywanie maszyn wirtualnych VMware za pomocą oceny serwera Azure Migrate
description: Informacje na temat odnajdywania lokalnych maszyn wirtualnych VMware za pomocą narzędzia do oceny Azure Migrate Server
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: c697a8a944c74d12202c4e9e148713c4c8433026
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541363"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>Samouczek: odnajdywanie maszyn wirtualnych VMware z oceną serwera

W ramach kursu migracji na platformę Azure można wykryć spisy i obciążenia lokalne.

W tym samouczku przedstawiono sposób odnajdywania lokalnych maszyn wirtualnych VMware przy użyciu Azure Migrate: narzędzia do oceny serwera za pomocą urządzenia uproszczonego Azure Migrate. Urządzenie można wdrożyć jako maszynę wirtualną VMware, aby ciągle wykrywać maszyny wirtualne i ich metadane wydajności, aplikacje działające na maszynach wirtualnych i zależności maszyn wirtualnych.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj konto platformy Azure.
> * Przygotuj środowisko VMware do odnajdowania.
> * Tworzenie projektu w usłudze Azure Migrate.
> * Skonfiguruj urządzenie Azure Migrate.
> * Rozpocznij odnajdowanie ciągłe.

> [!NOTE]
> Samouczki przedstawiają najszybszą ścieżkę w celu wypróbowania scenariusza i używają opcji domyślnych, tam gdzie to możliwe.  

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka zapoznaj się z wymaganiami wstępnymi.


**Wymaganie** | **Szczegóły**
--- | ---
**Host vCenter Server/ESXi** | Potrzebujesz vCenter Server w wersji 5,5, 6,0, 6,5 lub 6,7.<br/><br/> Maszyny wirtualne muszą być hostowane na hoście ESXi z systemem w wersji 5,5 lub nowszej.<br/><br/> Na vCenter Server Zezwalaj na połączenia przychodzące na porcie TCP 443, aby urządzenie mogły zbierać metadane konfiguracji i wydajności.<br/><br/> Urządzenie domyślnie łączy się z programem vCenter na porcie 443. Jeśli vCenter Server nasłuchuje na innym porcie, można zmodyfikować port, gdy podajesz vCenter Server szczegóły w Menedżerze konfiguracji urządzeń.<br/><br/> Na serwerze ESXi, który hostuje maszyny wirtualne, upewnij się, że na porcie TCP 443 jest dozwolony dostęp przychodzący do odnajdywania aplikacji zainstalowanych na maszynach wirtualnych i zależnościach maszyn wirtualnych.
**Wprowadzony** | vCenter Server wymaga zasobów do przydzielenia maszyny wirtualnej dla urządzenia Azure Migrate:<br/><br/> -32 GB pamięci RAM, 8 procesorów wirtualnych vCPU i około 80 GB miejsca na dysku.<br/><br/> — Zewnętrzny przełącznik wirtualny i dostęp do Internetu na maszynie wirtualnej urządzenia, bezpośrednio lub za pośrednictwem serwera proxy.
**Maszyny wirtualne** | Wszystkie wersje systemów operacyjnych Windows i Linux są obsługiwane na potrzeby odnajdywania metadanych konfiguracji i wydajności oraz odnajdywania aplikacji zainstalowanych na maszynach wirtualnych. <br/><br/> Sprawdź wersje systemu operacyjnego obsługiwane [w przypadku analizy](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) zależności bez agentów.<br/><br/> Aby odnaleźć zainstalowane aplikacje i zależności maszyn wirtualnych, należy zainstalować narzędzia VMware (nowsze niż 10.2.0) i uruchamiać je na maszynach wirtualnych, a maszyny wirtualne z systemem Windows muszą mieć zainstalowany program PowerShell w wersji 2,0 lub nowszej.


## <a name="prepare-an-azure-user-account"></a>Przygotowywanie konta użytkownika platformy Azure

Aby utworzyć projekt Azure Migrate i zarejestrować urządzenie Azure Migrate, musisz mieć konto z:
- Uprawnienia współautora lub właściciela w ramach subskrypcji platformy Azure
- Uprawnienia do rejestrowania aplikacji Azure Active Directory (AAD)
- Właściciel lub współautor oraz uprawnienia administratora dostępu użytkowników do subskrypcji platformy Azure w celu utworzenia Key Vault, używane podczas migracji VMware bez agenta

Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś właścicielem subskrypcji. Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać uprawnienia w następujący sposób:

1. W Azure Portal Wyszukaj "subskrypcje", a w obszarze **usługi** wybierz pozycję **subskrypcje**.

    ![Wyszukaj w polu wyszukiwania subskrypcję platformy Azure](./media/tutorial-discover-vmware/search-subscription.png)

2. Na stronie **subskrypcje** wybierz subskrypcję, w której chcesz utworzyć projekt Azure Migrate. 
3. W subskrypcji wybierz pozycję **Kontrola dostępu (IAM)**  >  **sprawdzanie dostępu**.
4. W obszarze **Sprawdź dostęp** Wyszukaj odpowiednie konto użytkownika.
5. W obszarze **Dodaj przypisanie roli** kliknij pozycję **Dodaj**.

    ![Wyszukaj konto użytkownika, aby sprawdzić dostęp i przypisać rolę](./media/tutorial-discover-vmware/azure-account-access.png)

6. W obszarze **Dodaj przypisanie roli** wybierz rolę współautor lub właściciela, a następnie wybierz konto (azmigrateuser w naszym przykładzie). Następnie kliknij przycisk **Zapisz**.

    ![Otwiera stronę Dodawanie przypisania roli w celu przypisania roli do konta](./media/tutorial-discover-vmware/assign-role.png)

1. Aby zarejestrować urządzenie, konto platformy Azure musi mieć **uprawnienia do rejestrowania aplikacji usługi AAD.**
1. W Azure Portal przejdź do **Azure Active Directory**  >  **użytkowników**  >  **Ustawienia użytkownika**.
1. W obszarze **Ustawienia użytkownika** Sprawdź, czy użytkownicy usługi Azure AD mogą rejestrować aplikacje (domyślnie ustawione na **wartość tak** ).

    ![Sprawdź ustawienia użytkownika, które użytkownicy mogą rejestrować Active Directory aplikacje](./media/tutorial-discover-vmware/register-apps.png)

9. Jeśli ustawienia "Rejestracje aplikacji" są ustawione na wartość "nie", zażądaj dzierżawy/administratora globalnego, aby przypisał wymagane uprawnienie. Alternatywnie, dzierżawa/Administrator globalny może przypisać rolę **dewelopera aplikacji** do konta, aby umożliwić rejestrację aplikacji usługi AAD. [Dowiedz się więcej](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>Przygotuj oprogramowanie VMware

Na vCenter Server sprawdź, czy Twoje konto ma uprawnienia do tworzenia maszyny wirtualnej przy użyciu pliku komórek jajowych. Jest to konieczne w przypadku wdrożenia urządzenia Azure Migrate jako maszyny wirtualnej VMware przy użyciu pliku komórki jajowe.

Ocena serwera wymaga vCenter Server konta tylko do odczytu na potrzeby odnajdywania i oceny maszyn wirtualnych VMware. Jeśli chcesz również odnajdywać zainstalowane aplikacje i zależności maszyn wirtualnych, konto wymaga uprawnień włączonych do **Virtual Machines > operacji gościa**.

### <a name="create-an-account-to-access-vcenter"></a>Utwórz konto, aby uzyskać dostęp do programu vCenter

W programie klient sieci Web vSphere Skonfiguruj konto w następujący sposób:

1. Korzystając z konta z uprawnieniami administratora, w > klienta sieci Web programu vSphere wybierz pozycję **Administracja**.
2. **Dostęp**, wybierz pozycję **Użytkownicy i grupy logowania jednokrotnego**.
3. W obszarze **Użytkownicy** Dodaj nowego użytkownika.
4. W obszarze **nowy użytkownik** wpisz szczegóły konta. Następnie kliknij przycisk **OK**.
5. W obszarze **uprawnienia globalne** wybierz konto użytkownika i przypisz rolę tylko do **odczytu** do konta. Następnie kliknij przycisk **OK**.
6. Jeśli chcesz również odnajdywać zainstalowane aplikacje i zależności maszyn wirtualnych, przejdź do pozycji **role** > wybierz rolę **tylko do odczytu** i w obszarze **uprawnienia** wybierz pozycję **operacje gościa**. Można propagować uprawnienia do wszystkich obiektów w vCenter Server, zaznaczając pole wyboru Propaguj do elementów podrzędnych.
 
    ![Pole wyboru zezwalające na operacje gościa w roli tylko do odczytu](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>Utwórz konto, aby uzyskać dostęp do maszyn wirtualnych

Aby odnajdywać zainstalowane aplikacje i zależności maszyny wirtualnej, musisz mieć konto użytkownika z niezbędnymi uprawnieniami na maszynach wirtualnych. Konto użytkownika można podać w Menedżerze konfiguracji urządzenia. Urządzenie nie instaluje żadnych agentów na maszynach wirtualnych.

1. W przypadku maszyn wirtualnych z systemem Windows utwórz konto (lokalne lub domeny) z uprawnieniami administracyjnymi na maszynach wirtualnych.
2. W przypadku maszyn wirtualnych z systemem Linux Utwórz konto z uprawnieniami głównymi. Alternatywnie można utworzyć konto z tymi uprawnieniami dla plików/bin/netstat i/bin/ls: CAP_DAC_READ_SEARCH i CAP_SYS_PTRACE.

> [!NOTE]
> Obecnie Azure Migrate obsługuje jedno konto użytkownika dla maszyn wirtualnych z systemem Windows i jedno konto użytkownika dla maszyn wirtualnych z systemem Linux, które można dostarczyć na urządzeniu w celu odnajdywania zainstalowanych aplikacji i zależności maszyn wirtualnych.


## <a name="set-up-a-project"></a>Konfigurowanie projektu

Skonfiguruj nowy projekt Azure Migrate.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Przegląd** wybierz pozycję **Utwórz projekt**.
5. W obszarze **Utwórz projekt** wybierz swoją subskrypcję platformy Azure i grupę zasobów. Utwórz grupę zasobów, jeśli jej nie masz.
6. W obszarze **szczegóły projektu** Określ nazwę projektu i geografię, w której chcesz utworzyć projekt. Przejrzyj obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pola nazwy i regionu projektu](./media/tutorial-discover-vmware/new-project.png)

7. Wybierz przycisk **Utwórz**.
8. Zaczekaj kilka minut, aż projekt Azure Migrate zostanie wdrożony. **Azure Migrate: Narzędzie do oceny serwera** jest domyślnie dodawane do nowego projektu.

![Zostanie wyświetlona strona narzędzia do oceny serwera, która jest domyślnie dodawana](./media/tutorial-discover-vmware/added-tool.png)

> [!NOTE]
> Jeśli projekt został już utworzony, możesz użyć tego samego projektu do zarejestrowania dodatkowych urządzeń w celu odnalezienia i oceny większej liczby maszyn wirtualnych.[Dowiedz się więcej](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Konfigurowanie urządzenia

Azure Migrate: Ocena serwera używa urządzenia uproszczonego Azure Migrate. Urządzenie wykonuje odnajdywanie maszyn wirtualnych i wysyła do Azure Migrate metadane dotyczące konfiguracji maszyny wirtualnej i wydajności. Urządzenie można skonfigurować przez wdrożenie szablonu komórki jajowe, który można pobrać z projektu Azure Migrate.

> [!NOTE]
> Jeśli z jakiegoś powodu nie można skonfigurować urządzenia przy użyciu szablonu, można skonfigurować go za pomocą skryptu programu PowerShell na istniejącym serwerze z systemem Windows Server 2016. [Dowiedz się więcej](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="deploy-with-ova"></a>Wdrażanie przy użyciu komórek jajowych

Aby skonfigurować urządzenie przy użyciu szablonu komórki jajowe:
1. Podaj nazwę urządzenia i Wygeneruj klucz projektu Azure Migrate w portalu
1. Pobierz plik szablonu komórki jajowe i zaimportuj go do vCenter Server. Sprawdź, czy komórki jajowe są bezpieczne.
1. Utwórz urządzenie i sprawdź, czy może nawiązać połączenie z oceną serwera Azure Migrate.
1. Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate przy użyciu klucza projektu Azure Migrate.

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Wygeneruj klucz projektu Azure Migrate

1. W obszarze **Cele migracji** > **Serwery** > **Azure Migrate: Server Assessment** wybierz pozycję **Odnajdź**.
2. W obszarze **odnajdywanie** maszyn  >  **są zwirtualizowane maszyny?** wybierz pozycję **tak, aby uzyskać VMware vSphere funkcji hypervisor**.
3. W obszarze **1: generowanie klucza projektu Azure Migrate** Podaj nazwę urządzenia Azure Migrate, które zostanie skonfigurowane do odnajdywania maszyn wirtualnych VMware. nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
1. Kliknij pozycję **Generuj klucz** , aby rozpocząć tworzenie wymaganych zasobów platformy Azure. Nie zamykaj strony odnajdywanie maszyn podczas tworzenia zasobów.
1. Po pomyślnym utworzeniu zasobów platformy Azure zostanie wygenerowany **klucz projektu Azure Migrate** .
1. Skopiuj klucz, ponieważ będzie on potrzebny do ukończenia rejestracji urządzenia podczas jego konfiguracji.

### <a name="2-download-the-ova-template"></a>2. Pobierz szablon komórki jajowe

W **2: Pobierz urządzenie Azure Migrate**, wybierz opcję. Plik komórki jajowe i kliknij pozycję **Pobierz**.

### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy plik komórki jajowe jest bezpieczny:

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla pliku komórki jajowe:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Przykład użycia: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Sprawdź najnowsze wersje urządzeń i wartości skrótu:

    - W przypadku chmury publicznej platformy Azure:
    
        **Algorytm** | **Pobieranie** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Dla Azure Government:
    
        **Algorytm** | **Pobieranie** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

### <a name="3-create-the-appliance-vm"></a>3. Utwórz maszynę wirtualną urządzenia

Zaimportuj pobrany plik i Utwórz maszynę wirtualną.

1. W konsoli klienta vSphere kliknij pozycję **plik**  >  **Wdróż OVF szablon**.
2. W Kreatorze wdrażania szablonu OVF > **Źródło** Określ lokalizację pliku komórek jajowych.
3. W polu **Nazwa** i **Lokalizacja** Określ przyjazną nazwę maszyny wirtualnej. Wybierz obiekt spisu, w którym będzie hostowana maszyna wirtualna.
5. W obszarze **host/klaster** Określ hosta lub klaster, na którym będzie URUCHAMIANA maszyna wirtualna.
6. W obszarze **Magazyn** określ miejsce docelowe magazynu dla maszyny wirtualnej.
7. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
8. W polu **mapowanie sieci** Określ sieć, z którą zostanie nawiązane połączenie z maszyną wirtualną. Sieć wymaga łączności z Internetem, aby można było wysyłać metadane do oceny serwera Azure Migrate.
9. Sprawdź poprawność ustawień, a następnie kliknij pozycję **Finish** (Zakończ).


### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może połączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i dla [instytucji rządowych](migrate-appliance.md#government-cloud-urls) .


### <a name="4-configure-the-appliance"></a>4. Skonfiguruj urządzenie

Skonfiguruj urządzenie po raz pierwszy.

> [!NOTE]
> Jeśli urządzenie zostanie skonfigurowane przy użyciu [skryptu programu PowerShell](deploy-appliance-script.md) zamiast pobranych komórek jajowych, pierwsze dwa kroki tej procedury nie są istotne.

1. W konsoli klienta vSphere kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie wybierz polecenie **Otwórz konsolę**.
2. Podaj język, strefę czasową i hasło dla urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną wirtualną, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację na pulpicie urządzenia, wybierając skrót do aplikacji.
1. Zaakceptuj **postanowienia licencyjne** i przeczytaj informacje o innych firmach.
1. W aplikacji internetowej > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
   - **Łączność**: aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
     - Kliknij pozycję **Skonfiguruj serwer proxy** , aby określić adres serwera proxy (w postaci http://ProxyIPAddress lub na http://ProxyFQDN) porcie nasłuchu.
     - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
     - Obsługiwane są tylko serwery proxy HTTP.
     - Jeśli dodano szczegóły serwera proxy lub wyłączono serwer proxy i/lub uwierzytelnianie, kliknij przycisk **Zapisz** , aby ponownie uruchomić sprawdzanie łączności.
   - **Synchronizacja czasu**: czas na urządzeniu powinien być zsynchronizowany z czasem Internetu, aby odnajdywanie działało prawidłowo.
   - **Zainstaluj aktualizacje**: urządzenie zapewnia zainstalowanie najnowszych aktualizacji. Po zakończeniu sprawdzania można kliknąć pozycję **Wyświetl usługi urządzenia** , aby zobaczyć stan i wersje składników uruchomionych na urządzeniu.
   - **Zainstaluj VDDK**: Urządzenie sprawdza, czy zainstalowano pakiet VMware vSphere Virtual Disk Development Kit (VDDK). Jeśli nie jest zainstalowany, Pobierz VDDK 6,7 z programu VMware i Wyodrębnij zawartość pliku zip do określonej lokalizacji na urządzeniu, zgodnie z **instrukcjami dotyczącymi instalacji**.

     Migracja serwera Azure Migrate przy użyciu programu VDDK umożliwia replikowanie maszyn podczas migracji na platformę Azure. 
1. Jeśli chcesz, możesz **ponownie uruchomić wymagania wstępne** w dowolnym momencie podczas konfigurowania urządzenia, aby sprawdzić, czy urządzenie spełnia wszystkie wymagania wstępne.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Wklej **klucz projektu Azure Migrate** skopiowany z portalu. Jeśli nie masz klucza, przejdź do pozycji **Ocena serwera> odkryj> zarządzanie istniejącymi urządzeniami**, wybierz nazwę urządzenia podaną w momencie generowania klucza i skopiuj odpowiedni klucz.
1. Do uwierzytelnienia w systemie Azure potrzebny będzie kod urządzenia. Kliknięcie przycisku **Zaloguj** spowoduje otwarcie modalnego kodu urządzenia, jak pokazano poniżej.

    ![Modalne wyświetlanie kodu urządzenia](./media/tutorial-discover-vmware/device-code.png)

1. Kliknij pozycję **Kopiuj kod & login** , aby skopiować kod urządzenia i otworzyć monit logowania platformy Azure na nowej karcie przeglądarki. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
1. Na nowej karcie Wklej kod urządzenia i zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure.
   
   Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. W przypadku nagłego zamknięcia karty logowania bez logowania, należy odświeżyć kartę przeglądarki w Menedżerze konfiguracji urządzenia, aby ponownie włączyć przycisk Zaloguj.
1. Po pomyślnym zalogowaniu Wróć do poprzedniej karty przy użyciu Menedżera konfiguracji urządzeń.
1. Jeśli konto użytkownika platformy Azure używane do rejestrowania ma odpowiednie uprawnienia do zasobów platformy Azure utworzonych podczas generowania klucza, Rejestracja urządzenia zostanie zainicjowana.
1. Po pomyślnym zarejestrowaniu urządzenia można wyświetlić szczegóły rejestracji, klikając pozycję **Wyświetl szczegóły**.



## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Urządzenie musi połączyć się z vCenter Server, aby odnaleźć dane dotyczące konfiguracji i wydajności maszyn wirtualnych.

1. W **kroku 1: podaj poświadczenia vCenter Server**, kliknij pozycję **Dodaj poświadczenia** , aby określić przyjazną nazwę dla poświadczeń, Dodaj **nazwę użytkownika** i **hasło** do konta vCenter Server, które będzie używane przez urządzenie do odnajdywania maszyn wirtualnych w wystąpieniu vCenter Server.
    - Należy skonfigurować konto z uprawnieniami wymaganymi w poprzednim samouczku.
    - Jeśli chcesz przeznaczyć zakres odnajdywania do określonych obiektów VMware (vCenter Server centrach danych, klastrów, folderu klastrów, hostów, folderu hostów lub poszczególnych maszyn wirtualnych), zapoznaj się z instrukcjami w [tym artykule](set-discovery-scope.md) , aby ograniczyć konto używane przez Azure Migrate.
1. W **kroku 2: podaj vCenter Server Szczegóły**, kliknij pozycję **Dodaj źródło odnajdywania** , aby wybrać przyjazną nazwę dla poświadczeń z listy rozwijanej, określ **adres IP/nazwę FQDN** wystąpienia vCenter Server. Możesz pozostawić **port** domyślny (443) lub określić port niestandardowy, dla którego vCenter Server nasłuchiwanie i kliknąć przycisk **Zapisz**.
1. Po kliknięciu przycisku Zapisz Urządzenie spróbuje sprawdzić poprawność połączenia z vCenter Server przy użyciu podanych poświadczeń i wyświetlić **stan weryfikacji** w tabeli dotyczącej VCENTER Server adres IP/nazwa FQDN.
1. Przed rozpoczęciem odnajdywania można ponownie **sprawdzić poprawność** łączności, aby vCenter Server w dowolnym momencie.
1. W **kroku 3: podaj poświadczenia maszyny wirtualnej w celu odnalezienia zainstalowanych aplikacji i przeprowadzenia mapowania zależności bez agenta**, kliknij przycisk **Dodaj poświadczenia** i określ system operacyjny, dla którego podano poświadczenia, przyjazną nazwę dla poświadczeń oraz nazwę **użytkownika** i **hasło**. Następnie kliknij przycisk **Zapisz**.

    - Opcjonalnie możesz dodać poświadczenia tutaj, jeśli utworzono konto do użycia na potrzeby [odnajdywania aplikacji](how-to-discover-applications.md)lub [analizy zależności bez agenta](how-to-create-group-machine-dependencies-agentless.md).
    - Jeśli nie chcesz korzystać z tych funkcji, możesz kliknąć suwak, aby pominąć ten krok. Zamiar można zmienić w dowolnym momencie później.
    - Zapoznaj się z uprawnieniami wymaganymi na koncie [odnajdowania aplikacji](migrate-support-matrix-vmware.md#application-discovery-requirements)lub [analizą zależności bez agenta](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Kliknij przycisk **Rozpocznij odnajdywanie**, aby uruchomić odnajdywanie maszyn wirtualnych. Po pomyślnym zainicjowaniu odnajdywania można sprawdzić stan odnajdywania dla vCenter Server adres IP/nazwa FQDN w tabeli.

Odnajdywanie działa w następujący sposób:
- Odnalezienie metadanych maszyny wirtualnej w portalu może potrwać około 15 minut.
- Odnajdywanie zainstalowanych aplikacji, ról i funkcji zajmuje trochę czasu. Czas trwania zależy od liczby wykrytych maszyn wirtualnych. W przypadku maszyn wirtualnych 500 ilość spisu aplikacji do wyświetlenia w portalu Azure Migrate trwa około godzinę.
- Po zakończeniu odnajdywania maszyn wirtualnych można włączyć analizę zależności bez agenta na wybranych maszynach wirtualnych z poziomu portalu.


## <a name="next-steps"></a>Następne kroki

- [Ocenianie maszyn wirtualnych VMware](./tutorial-assess-vmware-azure-vm.md) na potrzeby migracji do maszyn wirtualnych platformy Azure.
- [Przejrzyj dane](migrate-appliance.md#collected-data---vmware) zbierane przez urządzenie podczas odnajdywania.