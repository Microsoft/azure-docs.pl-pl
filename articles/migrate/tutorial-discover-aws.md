---
title: Odkryj wystąpienia AWS z oceną serwera Azure Migrate
description: Dowiedz się, jak odnajdywać wystąpienia AWS za pomocą oceny serwera Azure Migrate.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 6924182e6425b0927583e8c6498c7073c4ff1806
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064408"
---
# <a name="tutorial-discover-aws-instances-with-server-assessment"></a>Samouczek: odnajdywanie wystąpień AWS z oceną serwera

W ramach przeprowadzonej migracji na platformę Azure można odnaleźć serwery do oceny i migracji.

W tym samouczku pokazano, jak odnajdywać wystąpienia Amazon Web Services (AWS) za pomocą narzędzia do oceny serwera Azure Migrate:, korzystając z uproszczonego urządzenia Azure Migrate. Urządzenie jest wdrażane jako serwer fizyczny w celu ciągłego odnajdywania metadanych maszyn i wydajności.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj konto platformy Azure.
> * Przygotuj wystąpienia AWS do odnajdywania.
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
**Wprowadzony** | Wymagana jest maszyna wirtualna EC2, na której będzie uruchamiane urządzenie Azure Migrate. Maszyna powinna mieć następujące:<br/><br/> — Zainstalowano system Windows Server 2016. Uruchamianie urządzenia na komputerze z systemem Windows Server 2019 nie jest obsługiwane.<br/><br/> -16 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB miejsca na dysku i zewnętrznym przełączniku wirtualnym.<br/><br/> — Statyczny lub dynamiczny adres IP, z dostępem do Internetu, bezpośrednio lub za pomocą serwera proxy.
**Wystąpienia systemu Windows** | Zezwalaj na połączenia przychodzące na porcie WinRM 5985 (HTTP), aby urządzenie mogły ściągnąć konfigurację i metadane wydajności.
**Wystąpienia systemu Linux** | Zezwalaj na połączenia przychodzące na porcie 22 (TCP).

## <a name="prepare-an-azure-user-account"></a>Przygotowywanie konta użytkownika platformy Azure

Aby utworzyć projekt Azure Migrate i zarejestrować urządzenie Azure Migrate, musisz mieć konto z:
- Uprawnienia współautora lub właściciela w ramach subskrypcji platformy Azure.
- Uprawnienia do rejestrowania aplikacji Azure Active Directory.

Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji. Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać uprawnienia w następujący sposób:

1. W Azure Portal Wyszukaj "subskrypcje", a w obszarze **usługi**wybierz pozycję **subskrypcje**.

    ![Wyszukaj w polu wyszukiwania subskrypcję platformy Azure](./media/tutorial-discover-aws/search-subscription.png)

2. Na stronie **subskrypcje** wybierz subskrypcję, w której chcesz utworzyć projekt Azure Migrate. 
3. W subskrypcji wybierz pozycję **Kontrola dostępu (IAM)**  >  **sprawdzanie dostępu**.
4. W obszarze **Sprawdź dostęp**Wyszukaj odpowiednie konto użytkownika.
5. W obszarze **Dodaj przypisanie roli**kliknij pozycję **Dodaj**.

    ![Wyszukaj konto użytkownika, aby sprawdzić dostęp i przypisać rolę](./media/tutorial-discover-aws/azure-account-access.png)

6. W obszarze **Dodaj przypisanie roli**wybierz rolę współautor lub właściciela, a następnie wybierz konto (azmigrateuser w naszym przykładzie). Następnie kliknij przycisk **Zapisz**.

    ![Otwiera stronę Dodawanie przypisania roli w celu przypisania roli do konta](./media/tutorial-discover-aws/assign-role.png)

7. W portalu Wyszukaj użytkowników, a w obszarze **usługi**wybierz pozycję **Użytkownicy**.
8. W obszarze **Ustawienia użytkownika**Sprawdź, czy użytkownicy usługi Azure AD mogą rejestrować aplikacje (domyślnie ustawione na **wartość tak** ).

    ![Sprawdź ustawienia użytkownika, które użytkownicy mogą rejestrować Active Directory aplikacje](./media/tutorial-discover-aws/register-apps.png)


## <a name="prepare-aws-instances"></a>Przygotuj wystąpienia AWS

Skonfiguruj konto, za pomocą którego urządzenie może uzyskiwać dostęp do wystąpień AWS.

- W przypadku serwerów z systemem Windows należy skonfigurować konto użytkownika lokalnego na wszystkich serwerach z systemem Windows, które mają zostać uwzględnione w odnajdywaniu. Dodaj konto użytkownika do następujących grup: — Użytkownicy zarządzania zdalnego — użytkownicy monitora wydajności — Użytkownicy dzienników wydajności.
 - W przypadku serwerów systemu Linux potrzebujesz konta głównego na serwerach systemu Linux, które mają być odnajdywane.


## <a name="set-up-a-project"></a>Konfigurowanie projektu

Skonfiguruj nowy projekt Azure Migrate.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Przegląd**wybierz pozycję **Utwórz projekt**.
5. W obszarze **Utwórz projekt**wybierz swoją subskrypcję platformy Azure i grupę zasobów. Utwórz grupę zasobów, jeśli jej nie masz.
6. W obszarze **szczegóły projektu**Określ nazwę projektu i geografię, w której chcesz utworzyć projekt. Przejrzyj obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pola nazwy i regionu projektu](./media/tutorial-discover-aws/new-project.png)

7. Wybierz przycisk **Utwórz**.
8. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony.

**Azure Migrate: Narzędzie do oceny serwera** jest domyślnie dodawane do nowego projektu.

![Zostanie wyświetlona strona narzędzia do oceny serwera, która jest domyślnie dodawana](./media/tutorial-discover-aws/added-tool.png)

## <a name="set-up-the-appliance"></a>Konfigurowanie urządzenia

Aby skonfigurować urządzenie w wystąpieniu AWS, należy pobrać i uruchomić skrypt Instalatora w [odpowiednim wystąpieniu](#prerequisites). Po utworzeniu urządzenia można sprawdzić, czy może nawiązać połączenie z platformą Azure, skonfigurować go po raz pierwszy i zarejestrować w projekcie Azure Migrate.

### <a name="download-the-installer-script"></a>Pobierz skrypt Instalatora

Pobierz spakowany plik dla urządzenia.

1. W obszarze serwery **celów migracji**  >  **Servers**  >  **Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2. W obszarze **odnajdywanie**maszyn  >  **są zwirtualizowane maszyny?** kliknij pozycję **niezwirtualizowane/inne**.
3. Kliknij pozycję **Pobierz** , aby pobrać plik zip.



### <a name="install-the-appliance"></a>Instalowanie urządzenia

Skrypt Instalatora wykonuje następujące czynności:

- Instaluje agentów i aplikację sieci Web na potrzeby odnajdywania i oceny AWS.
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

    - W przypadku chmury publicznej: ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Dla Azure Government: ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Po pomyślnym zakończeniu działania skryptu zostanie uruchomiona aplikacja sieci Web urządzenia.

Jeśli występują problemy, możesz uzyskać dostęp do dzienników skryptów w witrynie C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log w celu rozwiązywania problemów.


### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Sprawdź dostęp do urządzenia w następujący sposób:

1. Upewnij się, że komputer urządzenia może połączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i dla [instytucji rządowych](migrate-appliance.md#government-cloud-urls) .
2. Upewnij się, że te porty są otwarte na komputerze urządzenia:

    - Zezwalaj na połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.
    - Zezwalaj na połączenia przychodzące na porcie 44368, aby zdalnie uzyskiwać dostęp do aplikacji sieci Web na urządzeniu przy użyciu adresu URL: https://<urządzenie-IP-lub-Name>:44368.
    - Zezwalaj na połączenia wychodzące na porcie 443 (HTTPS), aby wysyłać metadane odnajdywania i wydajności do Azure Migrate.


### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

1. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną wirtualną, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację na pulpicie urządzenia, wybierając skrót do aplikacji.
2. W aplikacji sieci Web Azure Migrate urządzenia > **skonfigurować wymagania wstępne**, zapoznaj się z postanowieniami licencyjnymi i zapoznaj się z informacjami innych firm.
3. Urządzenie sprawdza, czy maszyna wirtualna ma dostęp do Internetu i czy czas na maszynie wirtualnej jest zsynchronizowany z czasem Internetu.
    - Jeśli używasz serwera proxy, kliknij pozycję **Skonfiguruj serwer proxy** i określ adres i port serwera proxy (w formacie http://ProxyIPAddress lub http://ProxyFQDN) . 
    - Określ poświadczenia, jeśli serwer proxy wymaga uwierzytelnienia. Obsługiwane są tylko serwery proxy HTTP.
4. Urządzenie instaluje najnowsze aktualizacje Azure Migrate
5. Przejrzyj ustawienia, a następnie kliknij przycisk **Kontynuuj**.

       

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. W obszarze **zarejestruj się w Azure Migrate**wybierz pozycję **Zaloguj**. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.

    ![Kliknij pozycję Zaloguj, aby rozpocząć rejestrowanie urządzenia](./media/tutorial-discover-aws/register.png)

1. Na stronie **logowania** Zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure. Logowanie przy użyciu numeru PIN nie jest obsługiwane.

    ![Przycisk Zaloguj, aby zarejestrować urządzenie](./media/tutorial-discover-aws/sign-in.png)
1. Po pomyślnym zalogowaniu Wróć do aplikacji.
1. W obszarze **zarejestruj się w Azure Migrate**wybierz subskrypcję, w której Azure Migrate projekt został utworzony, a następnie wybierz projekt.
1. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
3. Wybierz pozycję **Zarejestruj**. Następnie kliknij przycisk **Kontynuuj**. Komunikat zawiera rejestrację jako pomyślne.

    ![Wypełnij pola subskrypcja, projekt i nazwa urządzenia i Zarejestruj urządzenie](./media/tutorial-discover-aws/success-register.png)


## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Urządzenie nawiązuje połączenie z wystąpieniem usługi AWS na potrzeby odnajdywania.

1. Kliknij pozycję **Dodaj poświadczenia** , aby określić poświadczenia konta, które będą używane przez urządzenie do odnajdywania serwerów.  
2. Zaloguj się przy użyciu nazwy użytkownika i hasła. Logowanie za pomocą klucza nie jest obsługiwane. Ponadto użytkownik musi być administratorem głównym lub częścią lokalnej grupy administratorów.
3. Określ **system operacyjny**, przyjazną nazwę dla poświadczeń oraz nazwę użytkownika i hasło. Następnie kliknij przycisk **Dodaj**.
Można dodać wiele poświadczeń dla serwerów z systemami Windows i Linux.
4. Kliknij przycisk **Dodaj serwer**i określ szczegóły serwera — nazwa FQDN/adres IP i przyjazna nazwa poświadczenia (jeden wpis na wiersz), aby połączyć się z serwerem.
5. Kliknij pozycję **Validate** (Waliduj). Po sprawdzeniu poprawności zostanie wyświetlona lista serwerów, które mogą zostać odnalezione.
    - Jeśli walidacja nie powiedzie się dla serwera, przejrzyj błąd, umieszczając kursor nad ikoną w kolumnie **stan** . Usuń problemy i ponownie sprawdź poprawność.
    - Aby usunąć serwer, wybierz pozycję > **Usuń**.
6. Po sprawdzeniu poprawności kliknij przycisk **Zapisz i Rozpocznij odnajdywanie** , aby rozpocząć proces odnajdywania.

Spowoduje to uruchomienie odnajdywania. W celu wyświetlenia metadanych w Azure Portal zwykle trwa mniej niż dwie minuty na serwer.


## <a name="verify-discovered-vms-in-the-portal"></a>Weryfikowanie odnalezionych maszyn wirtualnych w portalu

Po przeprowadzeniu odnajdywania możesz sprawdzić, czy maszyny wirtualne są wyświetlane w Azure Portal:

1. Otwórz pulpit nawigacyjny Azure Migrate.
2. W **Azure Migrate serwery**  >  **Azure Migrate: Ocena serwera**, wybierz ikonę, która wyświetla liczbę **odnalezionych serwerów**.

## <a name="next-steps"></a>Następne kroki

- [Oceń serwery fizyczne](tutorial-migrate-aws-virtual-machines.md) do migracji na maszyny wirtualne platformy Azure.
- [Przejrzyj dane](migrate-appliance.md#collected-data---physical) zbierane przez urządzenie podczas odnajdywania.
