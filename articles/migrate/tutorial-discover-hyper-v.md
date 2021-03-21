---
title: Odnajdywanie maszyn wirtualnych funkcji Hyper-V za pomocą oceny serwera Azure Migrate
description: Dowiedz się, jak odnajdywać lokalne maszyny wirtualne funkcji Hyper-V za pomocą narzędzia do oceny Azure Migrate Server.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 8b46d08da87565d133962c23e8281b221544d9ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99092520"
---
# <a name="tutorial-discover-hyper-v-vms-with-server-assessment"></a>Samouczek: odnajdywanie maszyn wirtualnych funkcji Hyper-V z oceną serwera

W ramach kursu migracji na platformę Azure można wykryć spisy i obciążenia lokalne. 

W tym samouczku pokazano, jak odnajdywać lokalne maszyny wirtualne funkcji Hyper-V za pomocą narzędzia do oceny serwera Azure Migrate, korzystającego z uproszczonego urządzenia Azure Migrate. Urządzenie można wdrożyć jako maszynę wirtualną funkcji Hyper-V w celu ciągłego odnajdywania metadanych maszyn i wydajności.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj konto platformy Azure
> * Przygotuj środowisko funkcji Hyper-V do odnajdowania.
> * Tworzenie projektu w usłudze Azure Migrate.
> * Skonfiguruj urządzenie Azure Migrate.
> * Rozpocznij odnajdowanie ciągłe.

> [!NOTE]
> Samouczki pokazują najszybszą ścieżkę w celu wypróbowania scenariusza i używają opcji domyślnych.  

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka zapoznaj się z wymaganiami wstępnymi.


**Wymaganie** | **Szczegóły**
--- | ---
**Host funkcji Hyper-V** | Hosty funkcji Hyper-V, na których znajdują się maszyny wirtualne, mogą być autonomiczne lub w klastrze.<br/><br/> Na hoście musi być uruchomiony system Windows Server 2019, Windows Server 2016 lub Windows Server 2012 R2.<br/><br/> Sprawdź, czy połączenia przychodzące są dozwolone na porcie WinRM 5985 (HTTP), tak aby urządzenie mogło nawiązać połączenie w celu ściągnięcia metadanych maszyny wirtualnej i danych wydajności przy użyciu sesji model wspólnych informacji (CIM).
**Wdrażanie urządzenia** | Host funkcji Hyper-V wymaga zasobów do przydzielenia maszyny wirtualnej dla urządzenia:<br/><br/> -16 GB pamięci RAM, 8 procesorów wirtualnych vCPU i około 80 GB miejsca na dysku.<br/><br/> — Zewnętrzny przełącznik wirtualny i dostęp do Internetu na maszynie wirtualnej urządzenia, bezpośrednio lub za pośrednictwem serwera proxy.
**Maszyny wirtualne** | Na maszynach wirtualnych może działać dowolny system operacyjny Windows lub Linux. 

## <a name="prepare-an-azure-user-account"></a>Przygotowywanie konta użytkownika platformy Azure

Aby utworzyć projekt Azure Migrate i zarejestrować urządzenie Azure Migrate, musisz mieć konto z:
- Uprawnienia współautora lub właściciela w ramach subskrypcji platformy Azure.
- Uprawnienia do rejestrowania aplikacji Azure Active Directory (AAD).

Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś właścicielem subskrypcji. Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać uprawnienia w następujący sposób:


1. W Azure Portal Wyszukaj "subskrypcje", a w obszarze **usługi** wybierz pozycję **subskrypcje**.

    ![Wyszukaj w polu wyszukiwania subskrypcję platformy Azure](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Na stronie **subskrypcje** wybierz subskrypcję, w której chcesz utworzyć projekt Azure Migrate. 
3. W subskrypcji wybierz pozycję **Kontrola dostępu (IAM)**  >  **sprawdzanie dostępu**.
4. W obszarze **Sprawdź dostęp** Wyszukaj odpowiednie konto użytkownika.
5. W obszarze **Dodaj przypisanie roli** kliknij pozycję **Dodaj**.

    ![Wyszukaj konto użytkownika, aby sprawdzić dostęp i przypisać rolę](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. W obszarze **Dodaj przypisanie roli** wybierz rolę współautor lub właściciela, a następnie wybierz konto (azmigrateuser w naszym przykładzie). Następnie kliknij przycisk **Zapisz**.

    ![Otwiera stronę Dodawanie przypisania roli w celu przypisania roli do konta](./media/tutorial-discover-hyper-v/assign-role.png)

1. Aby zarejestrować urządzenie, konto platformy Azure musi mieć **uprawnienia do rejestrowania aplikacji usługi AAD.**
1. W Azure Portal przejdź do **Azure Active Directory**  >  **użytkowników**  >  **Ustawienia użytkownika**.
1. W obszarze **Ustawienia użytkownika** Sprawdź, czy użytkownicy usługi Azure AD mogą rejestrować aplikacje (domyślnie ustawione na **wartość tak** ).

    ![Sprawdź ustawienia użytkownika, które użytkownicy mogą rejestrować Active Directory aplikacje](./media/tutorial-discover-hyper-v/register-apps.png)

9. Jeśli ustawienia "Rejestracje aplikacji" są ustawione na wartość "nie", zażądaj dzierżawy/administratora globalnego, aby przypisał wymagane uprawnienie. Alternatywnie, dzierżawa/Administrator globalny może przypisać rolę **dewelopera aplikacji** do konta, aby umożliwić rejestrację aplikacji usługi AAD. [Dowiedz się więcej](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-hyper-v-hosts"></a>Przygotuj hosty funkcji Hyper-V

Hosty funkcji Hyper-V można przygotować ręcznie lub za pomocą skryptu. Kroki przygotowania zostały podsumowane w tabeli. Skrypt przygotuje je automatycznie.

**Krok** | **Skrypt** | **Ręczne**
--- | --- | ---
Weryfikuj wymagania dotyczące hosta | Sprawdza, czy na hoście jest uruchomiona obsługiwana wersja funkcji Hyper-V i rola funkcji Hyper-V.<br/><br/>Włącza usługę WinRM i otwiera porty 5985 (HTTP) i 5986 (HTTPS) na hoście (wymaganym do zbierania metadanych). | Na hoście musi być uruchomiony system Windows Server 2019, Windows Server 2016 lub Windows Server 2012 R2.<br/><br/> Sprawdź, czy połączenia przychodzące są dozwolone na porcie WinRM 5985 (HTTP), tak aby urządzenie mogło nawiązać połączenie w celu ściągnięcia metadanych maszyny wirtualnej i danych wydajności przy użyciu sesji model wspólnych informacji (CIM).<br/><br/> Skrypt nie jest obecnie obsługiwany na hostach z ustawieniami regionalnymi innymi niż angielski.  
Weryfikuj wersję programu PowerShell | Sprawdza, czy skrypt jest uruchamiany w obsługiwanej wersji programu PowerShell. | Sprawdź, czy na hoście funkcji Hyper-V jest uruchomiony program PowerShell w wersji 4,0 lub nowszej.
Tworzenie konta | Sprawdza, czy masz odpowiednie uprawnienia na hoście funkcji Hyper-V.<br/><br/> Umożliwia utworzenie konta użytkownika lokalnego z odpowiednimi uprawnieniami. | Opcja 1: Przygotuj konto z dostępem administratora do komputera hosta funkcji Hyper-V.<br/><br/> Opcja 2: przygotowywanie konta administratora lokalnego lub konta administratora domeny i Dodawanie konta do tych grup: Użytkownicy zarządzania zdalnego, Administratorzy funkcji Hyper-V i użytkownicy monitora wydajności.
Włącz obsługę zdalną programu PowerShell | Włącza obsługę zdalną programu PowerShell na hoście, dzięki czemu urządzenie Azure Migrate może uruchamiać polecenia programu PowerShell na hoście przy użyciu połączenia usługi WinRM. | Aby skonfigurować program, na każdym hoście Otwórz konsolę programu PowerShell jako administrator i uruchom następujące polecenie: ``` powershell Enable-PSRemoting -force ```
Konfigurowanie usług integracji funkcji Hyper-V | Sprawdza, czy usługi integracji funkcji Hyper-V są włączone na wszystkich maszynach wirtualnych zarządzanych przez hosta. | [Włącz usługi integracji funkcji Hyper-V](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) na każdej maszynie wirtualnej.<br/><br/> Jeśli korzystasz z systemu Windows Server 2003, [postępuj zgodnie z tymi instrukcjami](prepare-windows-server-2003-migration.md).
Delegowanie poświadczeń, jeśli dyski maszyny wirtualnej znajdują się na zdalnych udziałach SMB | Delegowanie poświadczeń | Uruchom to polecenie, aby umożliwić dostawcy CredSSP Delegowanie poświadczeń na hostach z uruchomionymi maszynami wirtualnymi funkcji Hyper-V z dyskami w udziałach SMB: ```powershell Enable-WSManCredSSP -Role Server -Force ```<br/><br/> To polecenie można uruchomić zdalnie na wszystkich hostach funkcji Hyper-V.<br/><br/> Jeśli dodasz nowe węzły hosta do klastra, są one automatycznie dodawane do odnajdowania, ale musisz włączyć dostawcę CredSSP ręcznie.<br/><br/> Po skonfigurowaniu urządzenia należy zakończyć konfigurowanie dostawcy CredSSP, [włączając je na urządzeniu](#delegate-credentials-for-smb-vhds). 

### <a name="run-the-script"></a>Uruchamianie skryptu

1. Pobierz skrypt z [Centrum pobierania Microsoft](https://aka.ms/migrate/script/hyperv). Skrypt jest kryptograficznie podpisany przez firmę Microsoft.
2. Zweryfikuj integralność skryptu przy użyciu plików MD5 lub SHA256 skrótów. Wartości hasztagów są poniżej. Uruchom to polecenie, aby wygenerować skrót dla skryptu:

    ```powershell
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Przykład użycia: 

    ```powershell
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```
3. Po zweryfikowaniu integralności skryptu Uruchom skrypt na każdym hoście funkcji Hyper-V za pomocą tego polecenia programu PowerShell:

    ```powershell
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```
Wartości skrótu to:

**Skrót** |  **Wartość**
--- | ---
MD5 | 0ef418f31915d01f896ac42a80dc414e
SHA256 | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2

## <a name="set-up-a-project"></a>Konfigurowanie projektu

Skonfiguruj nowy projekt Azure Migrate.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Przegląd** wybierz pozycję **Utwórz projekt**.
5. W obszarze **Utwórz projekt** wybierz swoją subskrypcję platformy Azure i grupę zasobów. Utwórz grupę zasobów, jeśli jej nie masz.
6. W obszarze **szczegóły projektu** Określ nazwę projektu i geografię, w której chcesz utworzyć projekt. Przejrzyj obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pola nazwy i regionu projektu](./media/tutorial-discover-hyper-v/new-project.png)

7. Wybierz przycisk **Utwórz**.
8. Zaczekaj kilka minut, aż projekt Azure Migrate zostanie wdrożony. **Azure Migrate: Narzędzie do oceny serwera** jest domyślnie dodawane do nowego projektu.

![Zostanie wyświetlona strona narzędzia do oceny serwera, która jest domyślnie dodawana](./media/tutorial-discover-hyper-v/added-tool.png)

> [!NOTE]
> Jeśli projekt został już utworzony, możesz użyć tego samego projektu do zarejestrowania dodatkowych urządzeń w celu odnalezienia i oceny większej liczby maszyn wirtualnych.[Dowiedz się więcej](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Konfigurowanie urządzenia

Azure Migrate: Ocena serwera używa urządzenia uproszczonego Azure Migrate. Urządzenie wykonuje odnajdywanie maszyn wirtualnych i wysyła do Azure Migrate metadane dotyczące konfiguracji maszyny wirtualnej i wydajności. Urządzenie można skonfigurować przez wdrożenie pliku VHD, który można pobrać z projektu Azure Migrate.

> [!NOTE]
> Jeśli z jakiegoś powodu nie można skonfigurować urządzenia przy użyciu szablonu, można skonfigurować go za pomocą skryptu programu PowerShell na istniejącym serwerze z systemem Windows Server 2016. [Dowiedz się więcej](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).

Ten samouczek konfiguruje urządzenie na maszynie wirtualnej funkcji Hyper-V w następujący sposób:

1. Podaj nazwę urządzenia i Wygeneruj klucz projektu Azure Migrate w portalu.
1. Pobierz skompresowany wirtualny dysk twardy funkcji Hyper-V z Azure Portal.
1. Utwórz urządzenie i sprawdź, czy może nawiązać połączenie z oceną serwera Azure Migrate.
1. Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate przy użyciu klucza projektu Azure Migrate.

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Wygeneruj klucz projektu Azure Migrate

1. W obszarze **Cele migracji** > **Serwery** > **Azure Migrate: Server Assessment** wybierz pozycję **Odnajdź**.
2. W obszarze **odnajdywanie** maszyn  >  **są zwirtualizowane maszyny?** wybierz pozycję **tak, używając funkcji Hyper-V**.
3. W **1: Wygeneruj klucz projektu Azure Migrate**, podaj nazwę urządzenia Azure Migrate, które zostanie skonfigurowane do odnajdywania maszyn wirtualnych funkcji Hyper-V. nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
1. Kliknij pozycję **Generuj klucz** , aby rozpocząć tworzenie wymaganych zasobów platformy Azure. Nie zamykaj strony odnajdywanie maszyn podczas tworzenia zasobów.
1. Po pomyślnym utworzeniu zasobów platformy Azure zostanie wygenerowany **klucz projektu Azure Migrate** .
1. Skopiuj klucz, ponieważ będzie on potrzebny do ukończenia rejestracji urządzenia podczas jego konfiguracji.

### <a name="2-download-the-vhd"></a>2. Pobierz dysk VHD

W **2: Pobierz urządzenie Azure Migrate**, wybierz opcję. Plik VHD i kliknij pozycję **Pobierz**.

### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.

2. Uruchom następujące polecenie programu PowerShell, aby wygenerować skrót dla pliku ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Przykład użycia: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  Sprawdź najnowsze wersje urządzeń i wartości skrótu:

    - W przypadku chmury publicznej platformy Azure:

        **Scenariusz** | **Pobieranie** | **SHA256**
        --- | --- | ---
        Funkcja Hyper-V (8,91 GB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2140422) |  40aa037987771794428b1c6ebee2614b092e6d69ac56d48a2bbc75eeef86c99a

    - Dla Azure Government:

        **Scenariusz** _ | _ *Pobieranie** | **SHA256**
        --- | --- | ---
        Funkcja Hyper-V (85,8 MB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="3-create-the-appliance-vm"></a>3. Utwórz maszynę wirtualną urządzenia

Zaimportuj pobrany plik i Utwórz maszynę wirtualną.

1. Wyodrębnij plik skompresowanego dysku VHD do folderu na hoście funkcji Hyper-V, który będzie obsługiwał maszynę wirtualną urządzenia. Trzy foldery są wyodrębniane.
2. Otwórz Menedżera funkcji Hyper-V. W obszarze **Akcje** kliknij pozycję **Importuj maszynę wirtualną**.
2. Po **rozpoczęciu pracy** Kreatora importu maszyny wirtualnej > kliknij przycisk **dalej**.
3. W obszarze **lokalizowanie folderu** określ folder zawierający wyodrębniony wirtualny dysk twardy. Następnie kliknij przycisk **Dalej**.
1. W obszarze **Wybierz maszynę wirtualną** kliknij przycisk **dalej**.
2. W obszarze **Wybierz typ importu** kliknij pozycję **Kopiuj maszynę wirtualną (Utwórz nowy unikatowy identyfikator)**. Następnie kliknij przycisk **Dalej**.
3. W obszarze **Wybierz lokalizację docelową** pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
4. W obszarze **foldery magazynu** pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
5. W obszarze **Wybierz sieć** Określ przełącznik wirtualny, który będzie używany przez maszynę wirtualną. Przełącznik wymaga połączenia z Internetem, aby wysyłać dane do platformy Azure.
6. W obszarze **Podsumowanie** przejrzyj ustawienia. Następnie kliknij przycisk **Zakończ**.
7. W Menedżerze funkcji Hyper-V > **Virtual Machines** Uruchom maszynę wirtualną.


### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może połączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i dla [instytucji rządowych](migrate-appliance.md#government-cloud-urls) .

### <a name="4-configure-the-appliance"></a>4. Skonfiguruj urządzenie

Skonfiguruj urządzenie po raz pierwszy.

> [!NOTE]
> Jeśli urządzenie zostanie skonfigurowane przy użyciu [skryptu programu PowerShell](deploy-appliance-script.md) zamiast pobranego wirtualnego dysku twardego, pierwsze dwa kroki tej procedury nie są istotne.

1. W Menedżerze funkcji Hyper-V > **Virtual Machines** kliknij prawym przyciskiem myszy maszynę wirtualną > **Połącz**.
2. Podaj język, strefę czasową i hasło dla urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną wirtualną, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację na pulpicie urządzenia, klikając skrót do aplikacji.
1. Zaakceptuj **postanowienia licencyjne** i przeczytaj informacje o innych firmach.
1. W aplikacji internetowej > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
    - **Łączność**: aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
      - Kliknij pozycję **Skonfiguruj serwer proxy** , aby określić adres serwera proxy (w postaci http://ProxyIPAddress lub na http://ProxyFQDN) porcie nasłuchu.
      - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
      - Obsługiwane są tylko serwery proxy HTTP.
      - Jeśli dodano szczegóły serwera proxy lub wyłączono serwer proxy i/lub uwierzytelnianie, kliknij przycisk **Zapisz** , aby ponownie uruchomić sprawdzanie łączności.
    - **Synchronizacja czasu**: godzina została zweryfikowana. Czas na urządzeniu powinien być zsynchronizowany z czasem Internetu, aby funkcja odnajdywania maszyn wirtualnych działała prawidłowo.
    - **Instalowanie aktualizacji**: ocena serwera Azure Migrate sprawdza, czy na urządzeniu zainstalowano najnowsze aktualizacje. Po zakończeniu sprawdzania można kliknąć pozycję **Wyświetl usługi urządzenia** , aby zobaczyć stan i wersje składników uruchomionych na urządzeniu.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Wklej **klucz projektu Azure Migrate** skopiowany z portalu. Jeśli nie masz klucza, przejdź do pozycji **Ocena serwera> odkryj> zarządzanie istniejącymi urządzeniami**, wybierz nazwę urządzenia podaną w momencie generowania klucza i skopiuj odpowiedni klucz.
1. Do uwierzytelnienia w systemie Azure potrzebny będzie kod urządzenia. Kliknięcie przycisku **Zaloguj** spowoduje otwarcie modalnego kodu urządzenia, jak pokazano poniżej.

    ![Modalne wyświetlanie kodu urządzenia](./media/tutorial-discover-vmware/device-code.png)

1. Kliknij pozycję **Kopiuj kod & login** , aby skopiować kod urządzenia i otworzyć monit logowania platformy Azure na nowej karcie przeglądarki. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
1. Na nowej karcie Wklej kod urządzenia i zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure.
   
   Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. W przypadku nagłego zamknięcia karty logowania bez logowania, należy odświeżyć kartę przeglądarki w Menedżerze konfiguracji urządzenia, aby ponownie włączyć przycisk Zaloguj.
1. Po pomyślnym zalogowaniu Wróć do poprzedniej karty przy użyciu Menedżera konfiguracji urządzeń.
4. Jeśli konto użytkownika platformy Azure używane do rejestrowania ma odpowiednie uprawnienia do zasobów platformy Azure utworzonych podczas generowania klucza, Rejestracja urządzenia zostanie zainicjowana.
1. Po pomyślnym zarejestrowaniu urządzenia można wyświetlić szczegóły rejestracji, klikając pozycję **Wyświetl szczegóły**.

### <a name="delegate-credentials-for-smb-vhds"></a>Delegowanie poświadczeń dla wirtualnych dysków twardych SMB

Jeśli używasz dysków VHD w systemie technologii, musisz włączyć delegowanie poświadczeń z urządzenia do hostów funkcji Hyper-V. W tym celu z urządzenia:

1. Na maszynie wirtualnej urządzenia Uruchom to polecenie. HyperVHost1/HyperVHost2 są przykładowymi nazwami hostów.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Alternatywnie należy to zrobić w Edytor lokalnych zasad grupy na urządzeniu:
    - W obszarze Konfiguracja komputera **zasad komputera lokalnego**  >  kliknij pozycję **Szablony administracyjne**  >    >  **delegowania poświadczeń** systemowych.
    - Kliknij dwukrotnie pozycję **Zezwól na delegowanie świeżych poświadczeń** i wybierz pozycję **włączone**.
    - W obszarze **Opcje** kliknij pozycję **Pokaż**, a następnie na liście Dodaj każdego hosta funkcji Hyper-V, który ma zostać odnajdowany, przy użyciu **usługi WSMAN/** jako prefiksu.
    - W obszarze  **Delegowanie poświadczeń** kliknij dwukrotnie pozycję **Zezwól na delegowanie świeżych poświadczeń z uwierzytelnianiem serwera tylko NTLM**. Ponownie Dodaj każdy host funkcji Hyper-V, który ma zostać odnalezienie do listy, przy użyciu **usługi WSMAN/** jako prefiksu.

## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Połącz się z urządzeniem z hostami lub klastrami funkcji Hyper-V, a następnie Uruchom odnajdywanie maszyn wirtualnych.

1. W **kroku 1: podaj poświadczenia hosta funkcji Hyper-V**, kliknij pozycję **Dodaj poświadczenia** , aby określić przyjazną nazwę dla poświadczeń, Dodaj **nazwę użytkownika** i **hasło** dla hosta lub klastra funkcji Hyper-v, którego urządzenie będzie używało do odnajdywania maszyn wirtualnych. Kliknij pozycję **Zapisz**.
1. Jeśli chcesz dodać jednocześnie wiele poświadczeń, kliknij pozycję **Dodaj więcej** , aby zapisać i dodać więcej poświadczeń. Funkcja odnajdywania maszyn wirtualnych funkcji Hyper-V obsługuje wiele poświadczeń.
1. W **kroku 2: Podaj szczegóły dotyczące hosta lub klastra funkcji Hyper-v**, kliknij pozycję **Dodaj źródło odnajdowania** , aby określić hosta funkcji Hyper-v/ **adres IP/FQDN** klastra i przyjazną nazwę dla poświadczeń do nawiązania połączenia z hostem/klastrem.
1. Możesz **dodać pojedynczy element** naraz lub **dodać wiele elementów** w jednym miejscu. Istnieje również możliwość zapewnienia szczegółowych informacji o hoście lub klastrze funkcji Hyper-V za poorednictwem **importowania woluminu CSV**.


    - W przypadku wybrania opcji **Dodaj pojedynczy element** należy określić przyjazną nazwę dla poświadczeń i hosta funkcji Hyper-V/ **adresu IP klastra/nazwy FQDN** , a następnie kliknąć przycisk **Zapisz**.
    - W przypadku wybrania opcji **Dodaj wiele elementów** _(wybrane domyślnie)_ można dodać wiele rekordów jednocześnie, określając w polu tekstowym opcję Host/klaster **/nazwa FQDN** klastra funkcji Hyper-V z przyjazną nazwą. **Sprawdź** dodane rekordy i kliknij pozycję **Zapisz**.
    - W przypadku wybrania opcji **Importuj woluminy CSV** można pobrać plik szablonu CSV, wypełnić go hostem/ **adresem IP klastra/nazwą FQDN** i przyjazną nazwą dla poświadczeń. Następnie zaimportuj plik do urządzenia, **Sprawdź** rekordy w pliku i kliknij przycisk **Zapisz**.

1. Po kliknięciu przycisku Zapisz Urządzenie spróbuje sprawdzić poprawność połączenia z hostami/klastrami funkcji Hyper-V, które zostały dodane, i wyświetlić **stan sprawdzania poprawności** w tabeli dla każdego hosta/klastra.
    - Aby sprawdzić poprawność hostów/klastrów, można wyświetlić więcej szczegółów, klikając ich adres IP/nazwę FQDN.
    - Jeśli walidacja nie powiedzie się dla hosta, przejrzyj błąd, klikając opcję **Walidacja nie powiodła się** w kolumnie Stan tabeli. Usuń problem i ponownie sprawdź poprawność.
    - Aby usunąć hosty lub klastry, kliknij pozycję **Usuń**.
    - Nie można usunąć określonego hosta z klastra. Można usunąć tylko cały klaster.
    - Można dodać klaster, nawet jeśli występują problemy z konkretnymi hostami w klastrze.
1. Możesz ponownie **sprawdzić poprawność** łączności z hostami/klastrami przed rozpoczęciem odnajdywania.
1. Kliknij przycisk **Rozpocznij odnajdywanie**, aby uruchomić odnajdywanie maszyn wirtualnych od pomyślnie zweryfikowanych hostów/klastrów. Po pomyślnym zainicjowaniu odnajdywania można sprawdzić stan odnajdywania dla każdego hosta/klastra w tabeli.

Spowoduje to uruchomienie odnajdywania. Aby metadane odnalezionych serwerów były wyświetlane w Azure Portal, zajmie około 2 minut na każdy host.

## <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po zakończeniu odnajdywania możesz sprawdzić, czy maszyny wirtualne są widoczne w portalu.

1. Otwórz pulpit nawigacyjny usługi Azure Migrate.
2. W **Azure Migrate serwery**  >  **Azure Migrate: Strona Ocena serwera** kliknij ikonę, która wyświetla liczbę **odnalezionych serwerów**.

## <a name="next-steps"></a>Następne kroki

- [Oceń maszyny wirtualne funkcji Hyper-V](tutorial-assess-hyper-v.md) do migracji na maszyny wirtualne platformy Azure.
- [Przejrzyj dane](migrate-appliance.md#collected-data---hyper-v) zbierane przez urządzenie podczas odnajdywania.
