---
title: Odnajdywanie maszyn wirtualnych funkcji Hyper-V za pomocą oceny serwera Azure Migrate
description: Dowiedz się, jak odnajdywać lokalne maszyny wirtualne funkcji Hyper-V za pomocą narzędzia do oceny Azure Migrate Server.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: fdf96b5767b461953fa88923aaa5050aff4613bc
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064371"
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
**Wdrażanie urządzenia** | vCenter Server wymaga zasobów do przydzielenia maszyny wirtualnej dla urządzenia:<br/><br/> — Windows Server 2016<br/><br/> -32 GB pamięci RAM<br/><br/> -Osiem procesorów wirtualnych vCPU<br/><br/> -Około 80 GB miejsca na dysku.<br/><br/> — Zewnętrzny przełącznik wirtualny.<br/><br/> — Dostęp do Internetu dla maszyny wirtualnej, bezpośrednio lub za pośrednictwem serwera proxy.
**Maszyny wirtualne** | Na maszynach wirtualnych może działać dowolny system operacyjny Windows lub Linux. 

Przed rozpoczęciem możesz [przejrzeć dane](migrate-appliance.md#collected-data---hyper-v) zbierane przez urządzenie podczas odnajdywania.

## <a name="prepare-an-azure-user-account"></a>Przygotowywanie konta użytkownika platformy Azure

Aby utworzyć projekt Azure Migrate i zarejestrować urządzenie Azure Migrate, musisz mieć konto z:
- Uprawnienia współautora lub właściciela w ramach subskrypcji platformy Azure.
- Uprawnienia do rejestrowania aplikacji Azure Active Directory.

Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji. Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać uprawnienia w następujący sposób:


1. W Azure Portal Wyszukaj "subskrypcje", a w obszarze **usługi**wybierz pozycję **subskrypcje**.

    ![Wyszukaj w polu wyszukiwania subskrypcję platformy Azure](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Na stronie **subskrypcje** wybierz subskrypcję, w której chcesz utworzyć projekt Azure Migrate. 
3. W subskrypcji wybierz pozycję **Kontrola dostępu (IAM)**  >  **sprawdzanie dostępu**.
4. W obszarze **Sprawdź dostęp**Wyszukaj odpowiednie konto użytkownika.
5. W obszarze **Dodaj przypisanie roli**kliknij pozycję **Dodaj**.

    ![Wyszukaj konto użytkownika, aby sprawdzić dostęp i przypisać rolę](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. W obszarze **Dodaj przypisanie roli**wybierz rolę współautor lub właściciela, a następnie wybierz konto (azmigrateuser w naszym przykładzie). Następnie kliknij przycisk **Zapisz**.

    ![Otwiera stronę Dodawanie przypisania roli w celu przypisania roli do konta](./media/tutorial-discover-hyper-v/assign-role.png)

7. W portalu Wyszukaj użytkowników, a w obszarze **usługi**wybierz pozycję **Użytkownicy**.
8. W obszarze **Ustawienia użytkownika**Sprawdź, czy użytkownicy usługi Azure AD mogą rejestrować aplikacje (domyślnie ustawione na **wartość tak** ).

    ![Sprawdź ustawienia użytkownika, które użytkownicy mogą rejestrować Active Directory aplikacje](./media/tutorial-discover-hyper-v/register-apps.png)

## <a name="prepare-hyper-v-hosts"></a>Przygotuj hosty funkcji Hyper-V

Skonfiguruj konto z dostępem administratora na hostach funkcji Hyper-V. Urządzenie używa tego konta do odnajdowania.

- Opcja 1: Przygotuj konto z dostępem administratora do komputera hosta funkcji Hyper-V.
- Opcja 2: przygotowywanie konta administratora lokalnego lub konta administratora domeny i Dodawanie konta do tych grup: Użytkownicy zarządzania zdalnego, Administratorzy funkcji Hyper-V i użytkownicy monitora wydajności.


## <a name="set-up-a-project"></a>Konfigurowanie projektu

konfiguruje nowy projekt Azure Migrate.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Przegląd**wybierz pozycję **Utwórz projekt**.
5. W obszarze **Utwórz projekt**wybierz swoją subskrypcję platformy Azure i grupę zasobów. Utwórz grupę zasobów, jeśli jej nie masz.
6. W obszarze **szczegóły projektu**Określ nazwę projektu i geografię, w której chcesz utworzyć projekt. Przejrzyj obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pola nazwy i regionu projektu](./media/tutorial-discover-hyper-v/new-project.png)

7. Wybierz przycisk **Utwórz**.
8. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony.

**Azure Migrate: Narzędzie do oceny serwera** jest domyślnie dodawane do nowego projektu.

![Zostanie wyświetlona strona narzędzia do oceny serwera, która jest domyślnie dodawana](./media/tutorial-discover-hyper-v/added-tool.png)


## <a name="set-up-the-appliance"></a>Konfigurowanie urządzenia

Ten samouczek konfiguruje urządzenie na maszynie wirtualnej funkcji Hyper-V.
- Pobierz szablon urządzenia i zaimportuj go do vCenter Server, aby utworzyć maszynę wirtualną z urządzeniem.
- Po utworzeniu urządzenia należy skonfigurować je po raz pierwszy i zarejestrować w projekcie Azure Migrate.

> [!NOTE]
> Jeśli z jakiegoś powodu nie można skonfigurować urządzenia przy użyciu szablonu, można skonfigurować go za pomocą skryptu programu PowerShell. [Dowiedz się więcej](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).


### <a name="download-the-vhd"></a>Pobierz dysk VHD

1. W obszarze serwery **celów migracji**  >  **Servers**  >  **Azure Migrate: Ocena serwera**, wybierz pozycję **odkryj**.
2. W obszarze **odnajdywanie**maszyn  >  **są zwirtualizowane maszyny?** wybierz pozycję **tak, używając funkcji Hyper-V**.
3. Wybierz pozycję **Pobierz** , aby pobrać plik VHD.

   ![Wybrane do pobrania plik komórki jajowe](./media/tutorial-discover-hyper-v/download-vhd.png)



### <a name="deploy-the-appliance-vm"></a>Wdróż maszynę wirtualną urządzenia

Zaimportuj pobrany plik i Utwórz maszynę wirtualną:

1. Pobierz skompresowany plik VHD na hosta funkcji Hyper-V, na którym zostanie umieszczona maszyna wirtualna.
2. Wyodrębnij spakowany plik.

    - W wyodrębnionej lokalizacji plik rozpakuje się do folderu o nazwie AzureMigrateAppliance_VersionNumber.
    - Ten folder zawiera podfolder, nazywany również AzureMigrateAppliance_VersionNumber.
    - Ten podfolder zawiera trzy dalsze migawki podfolderów, wirtualne dyski twarde i Virtual Machines.

3. Otwórz Menedżera funkcji Hyper-V. W obszarze **Akcje**kliknij pozycję **Importuj maszynę wirtualną**.

    ![Element menu w Menedżerze funkcji Hyper-V w celu zaimportowania dysku VHD](./media/tutorial-discover-hyper-v/deploy-vhd.png)

4. Po **rozpoczęciu pracy**Kreatora importu maszyny wirtualnej > kliknij przycisk **dalej**.
5. W obszarze **lokalizowanie folderu**wybierz folder **Virtual Machines** . Następnie kliknij przycisk **Dalej**.
6. W obszarze **Wybierz maszynę wirtualną**kliknij przycisk **dalej**.
7. W obszarze **Wybierz typ importu**kliknij pozycję **Kopiuj maszynę wirtualną (Utwórz nowy unikatowy identyfikator)**. Następnie kliknij przycisk **Dalej**.
8. W obszarze **Wybierz lokalizację docelową**pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
9. W obszarze **foldery magazynu**pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
10. W obszarze **Wybierz sieć**Określ przełącznik wirtualny, który będzie używany przez maszynę wirtualną. Przełącznik wymaga połączenia z Internetem, aby wysyłać dane do platformy Azure. [Dowiedz się więcej](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) na temat tworzenia przełącznika wirtualnego.
11. W obszarze **Podsumowanie**przejrzyj ustawienia. Następnie kliknij przycisk **Zakończ**.
12. W Menedżerze funkcji Hyper-V > **Virtual Machines**Uruchom maszynę wirtualną.


### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Sprawdź dostęp do urządzenia w następujący sposób:

1. Sprawdź, czy maszyna wirtualna może nawiązać połączenie z platformą Azure.
    - W chmurze publicznej komputer z urządzeniem powinien mieć możliwość nawiązywania połączeń z tymi [adresami URL](migrate-appliance.md#public-cloud-urls).
    - W chmurze dla instytucji rządowych urządzenie powinno mieć możliwość łączenia się z tymi [adresami URL instytucji rządowych](migrate-appliance.md#government-cloud-urls).
2. Upewnij się, że te porty są otwarte na komputerze urządzenia:

    - Zezwalaj na połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.
    - Zezwalaj na połączenia przychodzące na porcie 44368, aby zdalnie uzyskiwać dostęp do aplikacji sieci Web na urządzeniu przy użyciu adresu URL: https:// \<appliance-ip-or-name> : 44368.
    - Zezwalaj na połączenia wychodzące na porcie 443 (HTTPS), aby wysyłać metadane odnajdywania i wydajności do Azure Migrate.


### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

> [!NOTE]
> Jeśli urządzenie zostanie skonfigurowane przy użyciu [skryptu programu PowerShell](deploy-appliance-script.md) zamiast pobranego wirtualnego dysku twardego, pierwsze dwa kroki tej procedury nie są istotne.

1. W Menedżerze funkcji Hyper-V > **Virtual Machines**kliknij prawym przyciskiem myszy maszynę wirtualną > **Połącz**.
2. Podaj język, strefę czasową i hasło dla urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną wirtualną, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację na pulpicie urządzenia, klikając skrót do aplikacji.
1. W aplikacji internetowej > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
    - **Licencja**: zaakceptuj postanowienia licencyjne i przeczytaj informacje o innych firmach.
    - **Łączność**: aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
      - Kliknij pozycję **Ustawienia serwera proxy**i określ adres serwera proxy i port nasłuchujący w formularzu http://ProxyIPAddress lub http://ProxyFQDN .
      - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
      - Obsługiwane są tylko serwery proxy HTTP.
    - **Synchronizacja czasu**: godzina została zweryfikowana. Czas na urządzeniu powinien być zsynchronizowany z czasem Internetu, aby funkcja odnajdywania maszyn wirtualnych działała prawidłowo.
    - **Instalowanie aktualizacji**: ocena serwera Azure Migrate sprawdza, czy na urządzeniu zainstalowano najnowsze aktualizacje.

### <a name="register-the-appliance"></a>Rejestrowanie urządzenia 

1. W obszarze **zarejestruj się w Azure Migrate**wybierz pozycję **Zaloguj**. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.

    ![Kliknij pozycję Zaloguj, aby rozpocząć rejestrowanie urządzenia](./media/tutorial-discover-hyper-v/register.png)

1. Na stronie **logowania** Zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure. Logowanie przy użyciu numeru PIN nie jest obsługiwane.

    ![Przycisk Zaloguj, aby zarejestrować urządzenie](./media/tutorial-discover-hyper-v/sign-in.png)
1. Po pomyślnym zalogowaniu Wróć do aplikacji.
1. W obszarze **zarejestruj się w Azure Migrate**wybierz subskrypcję, w której Azure Migrate projekt został utworzony, a następnie wybierz projekt.
1. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
3. Wybierz pozycję **Zarejestruj**. Następnie kliknij przycisk **Kontynuuj**. Komunikat zawiera rejestrację jako pomyślne.

    ![Wypełnij pola subskrypcja, projekt i nazwa urządzenia i Zarejestruj urządzenie](./media/tutorial-discover-hyper-v/success-register.png)


### <a name="delegate-credentials-for-smb-vhds"></a>Delegowanie poświadczeń dla wirtualnych dysków twardych SMB

Jeśli używasz dysków VHD w systemie technologii, musisz włączyć delegowanie poświadczeń z urządzenia do hostów funkcji Hyper-V. W tym celu należy umożliwić każdemu hostowi działanie jako delegata urządzenia. Jeśli samouczki zostały wykonane w powyższej części samouczka, podczas przygotowywania funkcji Hyper-V do oceny i migracji należy wykonać te instrukcje. Należy skonfigurować protokół CredSSP dla hostów [ręcznie](tutorial-prepare-hyper-v.md#enable-credssp-to-delegate-credentials)lub przez [uruchomienie skryptu](tutorial-prepare-hyper-v.md#run-the-script) , który to robi.

Włącz na urządzeniu w następujący sposób:

#### <a name="option-1"></a>Opcja 1

Na maszynie wirtualnej urządzenia Uruchom to polecenie. HyperVHost1/HyperVHost2 są przykładowymi nazwami hostów.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
```

Przykład: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Opcja 2

Alternatywnie należy to zrobić w Edytor lokalnych zasad grupy na urządzeniu:

1. W obszarze Konfiguracja komputera **zasad komputera lokalnego**  >  **Computer Configuration**kliknij pozycję **Szablony administracyjne**  >  **System**  >  **delegowania poświadczeń**systemowych.
2. Kliknij dwukrotnie pozycję **Zezwól na delegowanie świeżych poświadczeń**i wybierz pozycję **włączone**.
3. W obszarze **Opcje**kliknij pozycję **Pokaż**, a następnie na liście Dodaj każdego hosta funkcji Hyper-V, który ma zostać odnajdowany, przy użyciu **usługi WSMAN/** jako prefiksu.
4. Następnie w obszarze **Delegowanie poświadczeń**kliknij dwukrotnie pozycję **Zezwól na delegowanie świeżych poświadczeń z uwierzytelnianiem serwera tylko NTLM**. Ponownie Dodaj każdy host funkcji Hyper-V, który ma zostać odnalezienie do listy, przy użyciu **usługi WSMAN/** jako prefiksu.



## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Urządzenie musi połączyć się z hostami funkcji Hyper-V w celu odnalezienia maszyn wirtualnych.


### <a name="connect-to-hyper-v-hosts"></a>Nawiązywanie połączenia z hostami funkcji Hyper-V

1. W polu **Nazwa użytkownika** i **hasło**określ poświadczenia konta, które będą używane przez urządzenie do odnajdywania maszyn wirtualnych. Określ przyjazną nazwę dla poświadczeń, a następnie kliknij pozycję **Zapisz szczegóły**.
2. Kliknij pozycję **Dodaj hosta**i określ szczegóły hosta/klastra funkcji Hyper-V.
3. Kliknij pozycję **Validate** (Waliduj). Po walidacji wyświetlana jest liczba maszyn wirtualnych, które mogą zostać odnalezione na każdym hoście/klastrze.
    - Jeśli walidacja nie powiedzie się dla hosta, przejrzyj błąd, umieszczając kursor nad ikoną w kolumnie **stan** . Usuń problemy i ponownie sprawdź poprawność.
    - Aby usunąć hosty lub klastry, wybierz pozycję > **Usuń**.
    - Nie można usunąć określonego hosta z klastra. Można usunąć tylko cały klaster.
    - Można dodać klaster, nawet jeśli występują problemy z konkretnymi hostami w klastrze.
4. Po sprawdzeniu poprawności kliknij przycisk **Zapisz i Rozpocznij odnajdywanie** , aby rozpocząć proces odnajdywania.

Spowoduje to uruchomienie odnajdywania. Dla każdego hosta pozostanie około 1,5 minut na obsługę metadanych odnalezionych serwerów w Azure Portal.


### <a name="verify-discovered-vms-in-the-portal"></a>Weryfikowanie odnalezionych maszyn wirtualnych w portalu

Po przeprowadzeniu odnajdywania możesz sprawdzić, czy maszyny wirtualne są wyświetlane w Azure Portal:

1. Otwórz pulpit nawigacyjny Azure Migrate.
2. W **Azure Migrate serwery**  >  **Azure Migrate: Ocena serwera**, wybierz ikonę, która wyświetla liczbę **odnalezionych serwerów**.

## <a name="next-steps"></a>Następne kroki

- [Oceń maszyny wirtualne funkcji Hyper-V](tutorial-assess-hyper-v.md) do migracji na maszyny wirtualne platformy Azure.
- [Przejrzyj dane](migrate-appliance.md#collected-data---hyper-v) zbierane przez urządzenie podczas odnajdywania.