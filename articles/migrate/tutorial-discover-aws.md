---
title: Odkryj wystąpienia AWS z odnajdywaniem i oceną Azure Migrate
description: Dowiedz się, jak odnajdywać wystąpienia AWS za pomocą odnajdywania i oceny Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/11/2021
ms.custom: mvc
ms.openlocfilehash: 295cd5a6831cb64d146bb92bca74b82ff7ab29df
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771485"
---
# <a name="tutorial-discover-aws-instances-with-azure-migrate-discovery-and-assessment"></a>Samouczek: odnajdywanie wystąpień AWS z Azure Migrate: odnajdywanie i Ocena

W ramach przeprowadzonej migracji na platformę Azure można odnaleźć serwery do oceny i migracji.

W tym samouczku przedstawiono sposób odnajdywania wystąpień Amazon Web Services (AWS) za pomocą narzędzia Azure Migrate do odnajdywania i oceny przy użyciu uproszczonego urządzenia Azure Migrate. Urządzenie jest wdrażane jako serwer fizyczny w celu ciągłego odnajdywania metadanych maszyn i wydajności.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj konto platformy Azure.
> * Przygotuj wystąpienia AWS do odnajdywania.
> * Utwórz projekt.
> * Skonfiguruj urządzenie Azure Migrate.
> * Rozpocznij odnajdowanie ciągłe.

> [!NOTE]
> Samouczki pokazują najszybszą ścieżkę w celu wypróbowania scenariusza i używają opcji domyślnych.  

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka zapoznaj się z wymaganiami wstępnymi.

**Wymaganie** | **Szczegóły**
--- | ---
**Wprowadzony** | Wymagana jest maszyna wirtualna EC2, na której będzie uruchamiane urządzenie Azure Migrate. Maszyna powinna mieć następujące:<br/><br/> — Zainstalowano system Windows Server 2016.<br/> _Uruchamianie urządzenia na komputerze z systemem Windows Server 2019 nie jest obsługiwane_.<br/><br/> -16 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB miejsca na dysku i zewnętrznym przełączniku wirtualnym.<br/><br/> — Statyczny lub dynamiczny adres IP, z dostępem do Internetu, bezpośrednio lub za pomocą serwera proxy.
**Wystąpienia systemu Windows** | Zezwalaj na połączenia przychodzące na porcie WinRM 5985 (HTTP), aby urządzenie mogły ściągnąć konfigurację i metadane wydajności.
**Wystąpienia systemu Linux** | Zezwalaj na połączenia przychodzące na porcie 22 (TCP).<br/><br/> Wystąpienia powinny używać `bash` jako powłoka domyślna; w przeciwnym razie odnajdywanie nie powiedzie się.

## <a name="prepare-an-azure-user-account"></a>Przygotowywanie konta użytkownika platformy Azure

Aby utworzyć projekt i zarejestrować urządzenie Azure Migrate, musisz mieć konto z:

* Uprawnienia współautora lub właściciela w ramach subskrypcji platformy Azure.
* Uprawnienia do rejestrowania aplikacji Azure Active Directory (AAD).

Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś właścicielem subskrypcji. Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać uprawnienia w następujący sposób:

1. W Azure Portal Wyszukaj "subskrypcje", a w obszarze **usługi** wybierz pozycję **subskrypcje**.

    ![Wyszukaj w polu wyszukiwania subskrypcję platformy Azure](./media/tutorial-discover-aws/search-subscription.png)

2. Na stronie **subskrypcje** wybierz subskrypcję, w której chcesz utworzyć projekt.
3. W subskrypcji wybierz pozycję **Kontrola dostępu (IAM)**  >  **sprawdzanie dostępu**.
4. W obszarze **Sprawdź dostęp** Wyszukaj odpowiednie konto użytkownika.
5. W obszarze **Dodaj przypisanie roli** kliknij pozycję **Dodaj**.

    ![Wyszukaj konto użytkownika, aby sprawdzić dostęp i przypisać rolę](./media/tutorial-discover-aws/azure-account-access.png)

6. W obszarze **Dodaj przypisanie roli** wybierz rolę współautor lub właściciela, a następnie wybierz konto (azmigrateuser w naszym przykładzie). Następnie kliknij przycisk **Zapisz**.

    ![Otwiera stronę Dodawanie przypisania roli w celu przypisania roli do konta](./media/tutorial-discover-aws/assign-role.png)

1. Aby zarejestrować urządzenie, konto platformy Azure musi mieć **uprawnienia do rejestrowania aplikacji usługi AAD.**
1. W Azure Portal przejdź do **Azure Active Directory**  >  **użytkowników**  >  **Ustawienia użytkownika**.
1. W obszarze **Ustawienia użytkownika** Sprawdź, czy użytkownicy usługi Azure AD mogą rejestrować aplikacje (domyślnie ustawione na **wartość tak** ).

    ![Sprawdź ustawienia użytkownika, które użytkownicy mogą rejestrować Active Directory aplikacje](./media/tutorial-discover-aws/register-apps.png)

1. Jeśli ustawienia "Rejestracje aplikacji" są ustawione na wartość "nie", zażądaj dzierżawy/administratora globalnego, aby przypisał wymagane uprawnienie. Alternatywnie, dzierżawa/Administrator globalny może przypisać rolę **dewelopera aplikacji** do konta, aby umożliwić rejestrację aplikacji usługi AAD. [Dowiedz się więcej](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-aws-instances"></a>Przygotuj wystąpienia AWS

Skonfiguruj konto, za pomocą którego urządzenie może uzyskiwać dostęp do wystąpień AWS.

- W przypadku **serwerów z systemem Windows** należy skonfigurować konto użytkownika lokalnego na wszystkich serwerach z systemem Windows, które mają zostać uwzględnione w odnajdywaniu. Dodaj konto użytkownika do następujących grup: — Użytkownicy zarządzania zdalnego — użytkownicy monitora wydajności — Użytkownicy dzienników wydajności.
 - W przypadku **serwerów z systemem Linux** wymagane jest konto główne na serwerach z systemem Linux, które mają zostać odnajdywane. Zapoznaj się z instrukcjami w [macierzy obsługi](migrate-support-matrix-physical.md#physical-server-requirements) , aby zapoznać się z alternatywą.
- Azure Migrate używa uwierzytelniania hasła podczas odnajdywania wystąpień AWS. Wystąpienia AWS domyślnie nie obsługują uwierzytelniania hasła. Aby można było odnaleźć wystąpienie, należy włączyć uwierzytelnianie hasła.
    - W przypadku serwerów z systemem Windows należy zezwolić na port WinRM 5985 (HTTP). Pozwala to na zdalne wywołania WMI.
    - Dla serwerów z systemem Linux:
        1. Zaloguj się do każdej maszyny z systemem Linux.
        2. Otwórz plik sshd_config: VI/etc/ssh/sshd_config
        3. W pliku Znajdź linię **PasswordAuthentication** i zmień wartość na **tak**.
        4. Zapisz plik i zamknij go. Uruchom ponownie usługę SSH.
    - Jeśli używasz użytkownika root do odnajdywania serwerów z systemem Linux, upewnij się, że na serwerach jest dozwolone logowanie główne.
        1. Zaloguj się do każdej maszyny z systemem Linux
        2. Otwórz plik sshd_config: VI/etc/ssh/sshd_config
        3. W pliku Znajdź linię **PermitRootLogin** i zmień wartość na **tak**.
        4. Zapisz plik i zamknij go. Uruchom ponownie usługę SSH.

## <a name="set-up-a-project"></a>Konfigurowanie projektu

Skonfiguruj nowy projekt.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Przegląd** wybierz pozycję **Utwórz projekt**.
5. W obszarze **Utwórz projekt** wybierz swoją subskrypcję platformy Azure i grupę zasobów. Utwórz grupę zasobów, jeśli jej nie masz.
6. W obszarze **szczegóły projektu** Określ nazwę projektu i geografię, w której chcesz utworzyć projekt. Przejrzyj obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pola nazwy i regionu projektu](./media/tutorial-discover-aws/new-project.png)

7. Wybierz przycisk **Utwórz**.
8. Zaczekaj kilka minut, aż projekt zostanie wdrożony. **Azure Migrate: narzędzie odnajdywania i oceny** jest domyślnie dodawane do nowego projektu.

![Zostanie wyświetlona strona narzędzia do oceny serwera, która jest domyślnie dodawana](./media/tutorial-discover-aws/added-tool.png)

> [!NOTE]
> Jeśli projekt został już utworzony, można użyć tego samego projektu do zarejestrowania dodatkowych urządzeń w celu odnalezienia i oceny większej liczby serwerów. [Dowiedz się więcej](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Konfigurowanie urządzenia

Urządzenie Azure Migrate jest lekkim urządzeniem używanym przez Azure Migrate: odnajdywanie i ocenianie, aby wykonać następujące czynności:

- Odnajdywanie serwerów lokalnych.
- Wysyłanie metadanych i danych wydajności dla odnalezionych serwerów do Azure Migrate: odnajdywania i oceny.

[Dowiedz się więcej](migrate-appliance.md) o urządzeniu Azure Migrate.

Aby skonfigurować urządzenie:

1. Podaj nazwę urządzenia i Wygeneruj klucz projektu w portalu.
1. Pobierz spakowany plik ze skryptem Instalatora Azure Migrate z Azure Portal.
1. Wyodrębnij zawartość z pliku spakowanego. Uruchom konsolę programu PowerShell z uprawnieniami administracyjnymi.
1. Wykonaj skrypt programu PowerShell, aby uruchomić aplikację sieci Web urządzenia.
1. Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie przy użyciu klucza projektu.

### <a name="1-generate-the-project-key"></a>1. Generowanie klucza projektu

1. W obszarze **cele migracji**  >  **systemy Windows, Linux i SQL Server**  >  **Azure Migrate: odnajdywanie i Ocena** wybierz pozycję **odkryj**.
2. W obszarze **odnajdowanie serwerów**  >  **są zwirtualizowane serwery?** wybierz opcję **fizyczne lub inne (AWS, GCP, Xen itp.)**.
3. W obszarze **1: generowanie klucza projektu** Podaj nazwę urządzenia Azure Migrate, które zostanie skonfigurowane do odnajdywania serwerów fizycznych lub wirtualnych. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
1. Kliknij pozycję **Generuj klucz** , aby rozpocząć tworzenie wymaganych zasobów platformy Azure. Nie zamykaj strony odnajdywanie serwerów podczas tworzenia zasobów.
1. Po pomyślnym utworzeniu zasobów platformy Azure zostanie wygenerowany **klucz projektu** .
1. Skopiuj klucz, ponieważ będzie on potrzebny do ukończenia rejestracji urządzenia podczas jego konfiguracji.

### <a name="2-download-the-installer-script"></a>2. Pobierz skrypt Instalatora

W **2: Pobierz urządzenie Azure Migrate**, kliknij pozycję **Pobierz**.

### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla pliku spakowanego:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykładowe użycie chmury publicznej: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Przykładowe użycie w chmurze dla instytucji rządowych: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Sprawdź najnowsze wersje urządzeń i wartości skrótu:
    - W przypadku chmury publicznej:

        **Scenariusz** | **Pobierz** _ | _ *Wartość skrótu**
        --- | --- | ---
        Fizyczne (85 MB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2140334) | 207157bab39303dca1c2b93562d6f1deaa05aa7c992f480138e17977641163fb

    - Dla Azure Government:

        **Scenariusz** | **Pobierz** _ | _ *Wartość skrótu**
        --- | --- | ---
        Fizyczne (85 MB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2140338) | ca67e8dbe21d113ca93bfe94c1003ab7faba50472cb03972d642be8a466f78ce
 

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Uruchom skrypt Instalatora Azure Migrate
Skrypt Instalatora wykonuje następujące czynności:

- Instaluje agentów i aplikację sieci Web na potrzeby odnajdywania i oceny serwera fizycznego.
- Zainstaluj role systemu Windows, w tym usługi aktywacji systemu Windows, usług IIS i programu PowerShell ISE.
- Pobierz i zainstaluj moduł, który ma zostać przezapisywalny usług IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM) z trwałymi ustawieniami ustawień dla Azure Migrate.
- Tworzy następujące pliki w ścieżce:
    - **Pliki konfiguracji**:%ProgramData%\Microsoft Azure\Config
    - **Pliki dziennika**:%ProgramData%\Microsoft Azure\Logs

Uruchom skrypt w następujący sposób:

1. Wyodrębnij spakowany plik do folderu na serwerze, który będzie hostować urządzenie.  Upewnij się, że skrypt nie jest uruchamiany na komputerze na istniejącym urządzeniu Azure Migrate.
2. Uruchom program PowerShell na powyższym serwerze z uprawnieniami administracyjnymi (z podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell do folderu, w którym zawartość została wyodrębniona z pobranego pliku spakowanego.
4. Uruchom skrypt o nazwie **AzureMigrateInstaller.ps1** , uruchamiając następujące polecenie:

    - W przypadku chmury publicznej: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Dla Azure Government: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    Po pomyślnym zakończeniu działania skryptu zostanie uruchomiona aplikacja sieci Web urządzenia.

Jeśli występują problemy, możesz uzyskać dostęp do dzienników skryptów w witrynie C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log w celu rozwiązywania problemów.

### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że urządzenie może połączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i dla [instytucji rządowych](migrate-appliance.md#government-cloud-urls) .

### <a name="4-configure-the-appliance"></a>4. Skonfiguruj urządzenie

Skonfiguruj urządzenie po raz pierwszy.

1. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z urządzeniem, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację z poziomu pulpitu, klikając skrót do aplikacji.
2. Zaakceptuj **postanowienia licencyjne** i przeczytaj informacje o innych firmach.
1. W aplikacji internetowej > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
    - **Łączność**: aplikacja sprawdza, czy serwer ma dostęp do Internetu. Jeśli serwer używa serwera proxy:
        - Kliknij pozycję **Skonfiguruj serwer proxy** , aby określić adres serwera proxy (w postaci http://ProxyIPAddress lub na http://ProxyFQDN) porcie nasłuchu.
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
        - Jeśli dodano szczegóły serwera proxy lub wyłączono serwer proxy i/lub uwierzytelnianie, kliknij przycisk **Zapisz** , aby ponownie uruchomić sprawdzanie łączności.
    - **Synchronizacja czasu**: godzina została zweryfikowana. Czas na urządzeniu powinien być zsynchronizowany z czasem internetowym w celu poprawnego działania funkcji odnajdywania serwerów.
    - **Zainstaluj aktualizacje**: Azure Migrate: Funkcja odnajdywania i oceny sprawdza, czy na urządzeniu zainstalowano najnowsze aktualizacje. Po zakończeniu sprawdzania można kliknąć pozycję **Wyświetl usługi urządzenia** , aby zobaczyć stan i wersje składników uruchomionych na urządzeniu.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Wklej **klucz projektu** skopiowany z portalu. Jeśli nie masz klucza, przejdź do **Azure Migrate: odnajdywania i oceny> odkryj> zarządzanie istniejącymi urządzeniami**, wybierz nazwę urządzenia podaną w momencie generowania klucza i skopiuj odpowiedni klucz.
1. Do uwierzytelnienia w systemie Azure potrzebny będzie kod urządzenia. Kliknięcie przycisku **Zaloguj** spowoduje otwarcie modalnego kodu urządzenia, jak pokazano poniżej.

    ![Modalne wyświetlanie kodu urządzenia](./media/tutorial-discover-vmware/device-code.png)

1. Kliknij pozycję **Kopiuj kod & login** , aby skopiować kod urządzenia i otworzyć monit logowania platformy Azure na nowej karcie przeglądarki. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
1. Na nowej karcie Wklej kod urządzenia i zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure.
   
   Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. W przypadku nagłego zamknięcia karty logowania bez logowania, należy odświeżyć kartę przeglądarki w Menedżerze konfiguracji urządzenia, aby ponownie włączyć przycisk Zaloguj.
1. Po pomyślnym zalogowaniu Wróć do poprzedniej karty przy użyciu Menedżera konfiguracji urządzeń.
4. Jeśli konto użytkownika platformy Azure używane do rejestrowania ma odpowiednie [uprawnienia](./tutorial-discover-physical.md) do zasobów platformy Azure utworzonych podczas generowania klucza, Rejestracja urządzenia zostanie zainicjowana.
1. Po pomyślnym zarejestrowaniu urządzenia można wyświetlić szczegóły rejestracji, klikając pozycję **Wyświetl szczegóły**.


## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Teraz nawiąż połączenie z urządzeniem z serwerami fizycznymi, które mają zostać odnalezione, i Uruchom odnajdywanie.

1. W **kroku 1: podaj poświadczenia do odnajdywania serwerów fizycznych lub wirtualnych z systemami Windows i Linux**, kliknij pozycję **Dodaj poświadczenia**.
1. W przypadku systemu Windows Server wybierz typ źródła jako **Windows Server**, określ przyjazną nazwę dla poświadczeń, Dodaj nazwę użytkownika i hasło. Kliknij pozycję **Zapisz**.
1. Jeśli używasz uwierzytelniania opartego na hasłach dla serwera z systemem Linux, wybierz typ źródła jako serwer z systemem **Linux (oparte na hasłach)**, podaj przyjazną nazwę dla poświadczeń, Dodaj nazwę użytkownika i hasło. Kliknij pozycję **Zapisz**.
1. Jeśli używasz uwierzytelniania opartego na kluczu SSH dla serwera z systemem Linux, możesz wybrać typ źródła jako serwer z systemem **Linux (oparty na klucz SSH)**, określić przyjazną nazwę dla poświadczeń, dodać nazwę użytkownika, przeglądać i wybrać plik klucza prywatnego SSH. Kliknij pozycję **Zapisz**.

    * Azure Migrate obsługuje klucz prywatny SSH generowany przez polecenie ssh-keygen przy użyciu algorytmów RSA, DSA, ECDSA i ed25519.
    * Obecnie Azure Migrate nie obsługuje klucza SSH opartego na hasłach. Użyj klucza SSH bez hasła.
    * Obecnie Azure Migrate nie obsługuje pliku klucza prywatnego SSH wygenerowanego przez program.
    * Azure Migrate obsługuje format OpenSSH pliku prywatnego klucza SSH, jak pokazano poniżej:
    
    ![Format obsługiwanego klucza prywatnego SSH](./media/tutorial-discover-physical/key-format.png)


1. Jeśli chcesz dodać jednocześnie wiele poświadczeń, kliknij pozycję **Dodaj więcej** , aby zapisać i dodać więcej poświadczeń. W przypadku odnajdywania serwerów fizycznych obsługiwane są wiele poświadczeń.
1. W **kroku 2: Podaj informacje o serwerze fizycznym lub wirtualnym**, kliknij pozycję **Dodaj źródło odnajdowania** , aby określić **adres IP/nazwę FQDN** serwera i przyjazną nazwę dla poświadczeń do nawiązania połączenia z serwerem.
1. Możesz **dodać pojedynczy element** naraz lub **dodać wiele elementów** w jednym miejscu. Istnieje również możliwość zapewnienia informacji o serwerze za poorednictwem **importowania woluminu CSV**.


    - Jeśli wybierzesz opcję **Dodaj pojedynczy element**, możesz wybrać typ systemu operacyjnego, określić przyjazną nazwę dla poświadczeń, dodać **adres IP/nazwę FQDN** serwera i kliknąć przycisk **Zapisz**.
    - W przypadku wybrania opcji **Dodaj wiele elementów** można dodać wiele rekordów jednocześnie, określając **adres IP/nazwę FQDN** serwera z przyjazną nazwą poświadczenia w polu tekstowym. Sprawdź, czy dodano * * dodane rekordy, i kliknij przycisk **Zapisz**.
    - W przypadku wybrania opcji **Importuj woluminy CSV** _(wybrane domyślnie)_ można pobrać plik szablonu CSV, wypełnić plik **adresem IP serwera/nazwą FQDN** i przyjazną nazwą poświadczenia. Następnie zaimportuj plik do urządzenia, **Sprawdź** rekordy w pliku i kliknij przycisk **Zapisz**.

1. Po kliknięciu przycisku Zapisz Urządzenie spróbuje sprawdzić poprawność połączenia z dodanymi serwerami i wyświetlić **stan sprawdzania poprawności** w tabeli na każdym serwerze.
    - Jeśli walidacja nie powiedzie się dla serwera, przejrzyj błąd, klikając opcję **Walidacja nie powiodła się** w kolumnie Stan tabeli. Usuń problem i ponownie sprawdź poprawność.
    - Aby usunąć serwer, kliknij przycisk **Usuń**.
1. Możesz ponownie **sprawdzić poprawność** łączności z serwerami przed rozpoczęciem odnajdywania.
1. Kliknij przycisk **Rozpocznij odnajdywanie**, aby uruchomić odnajdywanie pomyślnie zweryfikowanych serwerów. Po pomyślnym zainicjowaniu odnajdywania można sprawdzić stan odnajdywania dla każdego serwera w tabeli.


Spowoduje to uruchomienie odnajdywania. Aby metadane wykrytego serwera pojawiły się w Azure Portal, zajmie około 2 minut na serwer.

## <a name="verify-servers-in-the-portal"></a>Weryfikowanie serwerów w portalu

Po zakończeniu odnajdywania możesz sprawdzić, czy serwery są wyświetlane w portalu.

1. Otwórz pulpit nawigacyjny usługi Azure Migrate.
2. W **Azure Migrate — Windows, Linux i SQL Server**  >  **Azure Migrate: Strona odnajdywania i oceny** kliknij ikonę, która wyświetla liczbę **odnalezionych serwerów**.

## <a name="next-steps"></a>Następne kroki

- [Oceń serwery fizyczne](tutorial-migrate-aws-virtual-machines.md) do migracji na maszyny wirtualne platformy Azure.
- [Przejrzyj dane](migrate-appliance.md#collected-data---physical) zbierane przez urządzenie podczas odnajdywania.
