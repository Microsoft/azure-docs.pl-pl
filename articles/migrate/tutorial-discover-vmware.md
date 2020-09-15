---
title: Odnajdywanie maszyn wirtualnych VMware za pomocą oceny serwera Azure Migrate
description: Informacje na temat odnajdywania lokalnych maszyn wirtualnych VMware za pomocą narzędzia do oceny Azure Migrate Server
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 55a7f15233d32f5ee60a57ad34e8b0177ca29ff3
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064456"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>Samouczek: odnajdywanie maszyn wirtualnych VMware z oceną serwera

W ramach kursu migracji na platformę Azure można wykryć spisy i obciążenia lokalne. 

W tym samouczku przedstawiono sposób odnajdywania lokalnych maszyn wirtualnych VMware przy użyciu Azure Migrate: narzędzia do oceny serwera za pomocą urządzenia uproszczonego Azure Migrate. Urządzenie można wdrożyć jako maszynę wirtualną VMware, aby ciągle wykrywać metadane maszyn wirtualnych i wydajności, aplikacje działające na maszynach wirtualnych oraz zależności maszyn wirtualnych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

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
**Host vCenter Server/ESXi** | Potrzebujesz vCenter Server w wersji 5,5, 6,0, 6,5 lub 6,7.<br/><br/> Maszyny wirtualne muszą być hostowane na hoście ESXi z systemem w wersji 5,5 lub nowszej.<br/><br/> Na vCenter Server Zezwalaj na połączenia przychodzące na porcie TCP 443, aby urządzenie mogły zbierać dane oceny.<br/><br/> Urządzenie domyślnie łączy się z programem vCenter na porcie 443. Jeśli serwer vCenter nasłuchuje na innym porcie, można zmodyfikować port po nawiązaniu połączenia z urządzeniem na serwerze w celu uruchomienia odnajdywania.<br/><br/> Na serwerze EXSi, który hostuje maszyny wirtualne, upewnij się, że na porcie TCP 443 jest dozwolony dostęp przychodzący na potrzeby odnajdywania aplikacji.
**Wprowadzony** | vCenter Server wymaga zasobów do przydzielenia maszyny wirtualnej dla urządzenia Azure Migrate:<br/><br/> — Windows Server 2016<br/><br/> -32 GB pamięci RAM, osiem procesorów wirtualnych vCPU i około 80 GB miejsca na dysku.<br/><br/> — Zewnętrzny przełącznik wirtualny i dostęp do Internetu dla maszyny wirtualnej, bezpośrednio lub za pośrednictwem serwera proxy.
**Maszyny wirtualne** | Aby skorzystać z tego samouczka, maszyny wirtualne z systemem Windows muszą działać pod kontrolą systemu Windows Server 2016, 2012 R2, 2012 lub 2008 R2.<br/><br/> Na maszynach wirtualnych z systemem Linux musi działać Red Hat Enterprise Linux 7/6/5, Ubuntu Linux 14.04/16.04, Debian 7/8, Oracle Linux 6/7 lub CentOS 5/6/7.<br/><br/> Maszyny wirtualne potrzebują zainstalowanych i uruchomionych narzędzi VMware (wersji nowszej niż 10.2.0).<br/><br/> Na maszynach wirtualnych z systemem Windows należy zainstalować program Windows PowerShell w wersji 2,0 lub nowszej.


## <a name="prepare-an-azure-user-account"></a>Przygotowywanie konta użytkownika platformy Azure

Aby utworzyć projekt Azure Migrate i zarejestrować urządzenie Azure Migrate, musisz mieć konto z:
- Uprawnienia współautora lub właściciela w ramach subskrypcji platformy Azure.
- Uprawnienia do rejestrowania aplikacji Azure Active Directory.

Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji. Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać uprawnienia w następujący sposób:

1. W Azure Portal Wyszukaj "subskrypcje", a w obszarze **usługi**wybierz pozycję **subskrypcje**.

    ![Wyszukaj w polu wyszukiwania subskrypcję platformy Azure](./media/tutorial-discover-vmware/search-subscription.png)

2. Na stronie **subskrypcje** wybierz subskrypcję, w której chcesz utworzyć projekt Azure Migrate. 
3. W subskrypcji wybierz pozycję **Kontrola dostępu (IAM)**  >  **sprawdzanie dostępu**.
4. W obszarze **Sprawdź dostęp**Wyszukaj odpowiednie konto użytkownika.
5. W obszarze **Dodaj przypisanie roli**kliknij pozycję **Dodaj**.

    ![Wyszukaj konto użytkownika, aby sprawdzić dostęp i przypisać rolę](./media/tutorial-discover-vmware/azure-account-access.png)

6. W obszarze **Dodaj przypisanie roli**wybierz rolę współautor lub właściciela, a następnie wybierz konto (azmigrateuser w naszym przykładzie). Następnie kliknij przycisk **Zapisz**.

    ![Otwiera stronę Dodawanie przypisania roli w celu przypisania roli do konta](./media/tutorial-discover-vmware/assign-role.png)

7. W portalu Wyszukaj użytkowników, a w obszarze **usługi**wybierz pozycję **Użytkownicy**.
8. W obszarze **Ustawienia użytkownika**Sprawdź, czy użytkownicy usługi Azure AD mogą rejestrować aplikacje (domyślnie ustawione na **wartość tak** ).

    ![Sprawdź ustawienia użytkownika, które użytkownicy mogą rejestrować Active Directory aplikacje](./media/tutorial-discover-vmware/register-apps.png)


## <a name="prepare-vmware"></a>Przygotuj oprogramowanie VMware

Na vCenter Server Utwórz konto, za pomocą którego urządzenie może uzyskać dostęp do vCenter Server i sprawdź, czy wymagane porty są otwarte. Potrzebujesz również konta, które może być używane przez urządzenie do uzyskiwania dostępu do maszyn wirtualnych. 

### <a name="create-an-account-to-access-vcenter"></a>Utwórz konto, aby uzyskać dostęp do programu vCenter

W programie klient sieci Web vSphere Skonfiguruj konto w następujący sposób:

1. Korzystając z konta z uprawnieniami administratora, w > klienta sieci Web programu vSphere wybierz pozycję **Administracja**.
2. **Dostęp**, wybierz pozycję **Użytkownicy i grupy logowania jednokrotnego**.
3. W obszarze **Użytkownicy**Dodaj nowego użytkownika.
4. W obszarze **nowy użytkownik**wpisz szczegóły konta. Następnie kliknij przycisk **OK**.
5. W obszarze **uprawnienia globalne**wybierz konto użytkownika i przypisz rolę tylko do **odczytu** do konta. Następnie kliknij przycisk **OK**.
6. W obszarze **role** > wybierz rolę **tylko do odczytu** , a w obszarze **uprawnienia**wybierz pozycję **operacje gościa**. Te uprawnienia są potrzebne do odnajdywania aplikacji uruchomionych na maszynach wirtualnych oraz do analizowania zależności maszyn wirtualnych.
 
    ![Pole wyboru zezwalające na operacje gościa w roli tylko do odczytu](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>Utwórz konto, aby uzyskać dostęp do maszyn wirtualnych

Urządzenie uzyskuje dostęp do maszyn wirtualnych w celu odnajdywania aplikacji i analizowania zależności maszyn wirtualnych. Urządzenie nie instaluje żadnych agentów na maszynach wirtualnych.

1. Utwórz konto administratora lokalnego, za pomocą którego urządzenie może wykrywać aplikacje i zależności na maszynach wirtualnych z systemem Windows.
2. W przypadku maszyn z systemem Linux Utwórz konto użytkownika z uprawnieniami głównymi lub konto użytkownika z tymi uprawnieniami w plikach/bin/netstat i/bin/ls: CAP_DAC_READ_SEARCH i CAP_SYS_PTRACE.

> [!NOTE]
> Azure Migrate obsługuje jedno poświadczenie na potrzeby odnajdywania aplikacji na wszystkich serwerach z systemem Windows oraz jedno poświadczenie na potrzeby odnajdywania aplikacji na wszystkich komputerach z systemem Linux.


## <a name="set-up-a-project"></a>Konfigurowanie projektu

Skonfiguruj nowy projekt Azure Migrate.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Przegląd**wybierz pozycję **Utwórz projekt**.
5. W obszarze **Utwórz projekt**wybierz swoją subskrypcję platformy Azure i grupę zasobów. Utwórz grupę zasobów, jeśli jej nie masz.
6. W obszarze **szczegóły projektu**Określ nazwę projektu i geografię, w której chcesz utworzyć projekt. Przejrzyj obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pola nazwy i regionu projektu](./media/tutorial-discover-vmware/new-project.png)

7. Wybierz przycisk **Utwórz**.
8. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony.

**Azure Migrate: Narzędzie do oceny serwera** jest domyślnie dodawane do nowego projektu.

![Zostanie wyświetlona strona narzędzia do oceny serwera, która jest domyślnie dodawana](./media/tutorial-discover-vmware/added-tool.png)


## <a name="set-up-the-appliance"></a>Konfigurowanie urządzenia

Ten samouczek konfiguruje urządzenie na maszynie wirtualnej VMware.
- Pobierz szablon urządzenia i zaimportuj go do vCenter Server, aby utworzyć maszynę wirtualną z urządzeniem.
- Po utworzeniu urządzenia należy je skonfigurować po raz pierwszy i zarejestrować w projekcie Azure Migrate.

> [!NOTE]
> Jeśli z jakiegoś powodu nie można skonfigurować urządzenia przy użyciu szablonu, można skonfigurować go za pomocą skryptu programu PowerShell. [Dowiedz się więcej](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="download-the-ova-template"></a>Pobierz szablon komórki jajowe

1. W obszarze serwery **celów migracji**  >  **Servers**  >  **Azure Migrate: Ocena serwera**, wybierz pozycję **odkryj**.
2. W obszarze **odnajdywanie**maszyn  >  **są zwirtualizowane maszyny?** wybierz pozycję **tak, używając funkcji hypervisor programu VMware vSphere**.
3. Wybierz pozycję **Pobierz** , aby pobrać plik szablonu komórki jajowe.

   ![Wybór do pobierania pliku komórki jajowe](./media/tutorial-discover-vmware/download-ova.png)


### <a name="deploy-the-appliance-vm"></a>Wdróż maszynę wirtualną urządzenia

Zaimportuj pobrany plik i Utwórz maszynę wirtualną:

1. Zaloguj się do konsoli klienta vSphere przy użyciu utworzonego konta vCenter.
2. Na karcie **Virtual Machines** menu **Akcje** > wybierz pozycję **Wdróż szablon OVF**, aby otworzyć Kreatora wdrażania szablonu OVF.
3. W obszarze **Wybierz szablon OVF**wybierz pozycję **plik lokalny**i przejdź do szablonu pobierania. 
   ![vSphere polecenie menu do wdrażania szablonu OVF](./media/tutorial-discover-vmware/deploy-ovf.png)
3. Określ resztę Kreatora ustawień kreatora, w tym lokalizację wdrożenia, Host/klaster, na którym zostanie uruchomiona maszyna wirtualna, oraz ustawienia magazynu/sieci.
4. W obszarze **gotowe do ukończenia**kliknij przycisk **Zakończ**. System importuje i wdraża szablon. 
5. Po wdrożeniu maszyna wirtualna urządzenia zostanie wyświetlona na karcie **Virtual Machines** .
6. Wybierz maszynę wirtualną **, > włączyć**.
7. Zapoznaj się z postanowieniami **licencyjnymi**w > konsoli zdalnej programu VMware i zaakceptowanie postanowień licencyjnych.
8. W obszarze **Dostosowywanie ustawień**Skonfiguruj hasło dla konta użytkownika.


### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Sprawdź dostęp do maszyny wirtualnej.

1. Sprawdź, czy maszyna wirtualna może nawiązać połączenie z platformą Azure.
    - W chmurze publicznej komputer z urządzeniem powinien mieć możliwość nawiązywania połączeń z tymi [adresami URL](migrate-appliance.md#public-cloud-urls).
    - W chmurze dla instytucji rządowych maszyna wirtualna powinna mieć możliwość łączenia się z tymi [adresami URL instytucji rządowych](migrate-appliance.md#government-cloud-urls).
2. Upewnij się, że te porty są otwarte na komputerze urządzenia:

    - Zezwalaj na połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.
    - Zezwalaj na połączenia przychodzące na porcie 44368, aby zdalnie uzyskiwać dostęp do aplikacji sieci Web na urządzeniu przy użyciu adresu URL: https://<urządzenie-IP-lub-Name>:44368.
    - Zezwalaj na połączenia wychodzące na porcie 443 (HTTPS), aby wysyłać metadane odnajdywania i wydajności do Azure Migrate.

### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

1. Zaloguj się do maszyny wirtualnej urządzenia. 
    - Aplikacja sieci Web urządzenia otwiera się automatycznie w przeglądarce.
    - Alternatywnie możesz otworzyć aplikację z pulpitu urządzenia za pomocą skrótu aplikacji.
2. W aplikacji sieci Web Azure Migrate urządzenia > **skonfigurować wymagania wstępne**, zapoznaj się z postanowieniami licencyjnymi i zapoznaj się z informacjami innych firm.
3. Urządzenie sprawdza, czy maszyna wirtualna ma dostęp do Internetu i czy czas na maszynie wirtualnej jest zsynchronizowany z czasem Internetu.
    - Jeśli używasz serwera proxy, kliknij pozycję **Skonfiguruj serwer proxy** i określ adres i port serwera proxy (w formacie http://ProxyIPAddress lub http://ProxyFQDN) . 
    - Określ poświadczenia, jeśli serwer proxy wymaga uwierzytelnienia. Obsługiwane są tylko serwery proxy HTTP.
4. Urządzenie instaluje najnowsze aktualizacje Azure Migrate i sprawdza, czy jest zainstalowany pakiet VMWare vSphere Virtual Disk Development Kit (VDDK).
5. Aby zainstalować VDDK 6,7, kliknij pozycję **Pobierz** , aby pobrać ją z programu VMware, a następnie wyodrębnij zawartość pliku zip do określonej lokalizacji na urządzeniu. Następnie kliknij przycisk **Weryfikuj i zainstaluj**.

    ![Strona wymagania wstępne dotyczące weryfikowania dostępu do Internetu i ustawień na potrzeby wdrażania urządzeń](./media/tutorial-discover-vmware/prerequisites.png)
  
3. Po zainstalowaniu VDDK Sprawdź ustawienia, a następnie kliknij przycisk **Kontynuuj**.

### <a name="register-the-appliance"></a>Rejestrowanie urządzenia 

1. W obszarze **zarejestruj się w Azure Migrate**wybierz pozycję **Zaloguj**. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.

    ![Kliknij pozycję Zaloguj, aby rozpocząć rejestrowanie urządzenia](./media/tutorial-discover-vmware/register.png)

1. Na stronie **logowania** Zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure. Logowanie przy użyciu numeru PIN nie jest obsługiwane.

    ![Przycisk Zaloguj, aby zarejestrować urządzenie](./media/tutorial-discover-vmware/sign-in.png)
1. Po pomyślnym zalogowaniu Wróć do aplikacji.
1. W obszarze **zarejestruj się w Azure Migrate**wybierz subskrypcję, w której Azure Migrate projekt został utworzony, a następnie wybierz projekt.
1. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
3. Wybierz pozycję **Zarejestruj**. Następnie kliknij przycisk **Kontynuuj**. Komunikat zawiera rejestrację jako pomyślne.

    ![Wypełnij pola subskrypcja, projekt i nazwa urządzenia i Zarejestruj urządzenie](./media/tutorial-discover-vmware/success-register.png)

## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Urządzenie musi nawiązać połączenie z usługą vCenter Server w celu odnalezienia maszyn wirtualnych.

### <a name="connect-to-vcenter-server"></a>Połącz z vCenter Server

Urządzenie musi nawiązać połączenie z usługą vCenter Server w celu odnalezienia maszyn wirtualnych.

1. W obszarze **określ vCenter Server**Określ nazwę (FQDN) lub adres IP vCenter Server.
2. Pozostaw domyślny port lub określ port niestandardowy, dla którego vCenter Server nasłuchuje.
3. W polu **Nazwa użytkownika** i **hasło**określ poświadczenia konta vCenter Server, które będą używane przez urządzenie do odnajdywania maszyn wirtualnych na vCenter Server.
3. Wybierz pozycję **Weryfikuj połączenie**, aby upewnić się, że urządzenie może połączyć się z vCenter Server. 

    ![Szczegóły programu vCenter Server i przycisk służący do sprawdzania poprawności połączenia z vCenter Server](./media/tutorial-discover-vmware/vcenter.png)

1. W obszarze **Znajdź aplikacje i zależności na maszynach wirtualnych**kliknij pozycję **Dodaj poświadczenia**.
1. Określ poświadczenia systemu Windows/Linux, które są używane na potrzeby odnajdywania aplikacji i analizy zależności.
1. Kliknij przycisk **Zapisz i Rozpocznij odnajdywanie**, aby uruchomić proces odnajdywania.

    ![Dodaj poświadczenia odnajdywania aplikacji i analizy zależności i Rozpocznij odnajdywanie ciągłe. ](./media/tutorial-discover-vmware/start-discovery.png)


Po rozpoczęciu odnajdywania:

- Odnalezienie metadanych maszyny wirtualnej w portalu może potrwać około 15 minut.
- Odnajdywanie aplikacji zajmuje trochę czasu. Czas zależy od liczby wykrytych maszyn wirtualnych. W przypadku maszyn wirtualnych 500 ilość spisu aplikacji do wyświetlenia w portalu Azure Migrate trwa około godzinę.



## <a name="verify-discovered-vms-in-the-portal"></a>Weryfikowanie odnalezionych maszyn wirtualnych w portalu

Po przeprowadzeniu odnajdywania możesz sprawdzić, czy maszyny wirtualne są wyświetlane w Azure Portal:

1. Otwórz pulpit nawigacyjny Azure Migrate.
2. W **Azure Migrate serwery**  >  **Azure Migrate: Ocena serwera**, wybierz ikonę, która wyświetla liczbę **odnalezionych serwerów**.

## <a name="next-steps"></a>Następne kroki

- [Ocenianie maszyn wirtualnych VMware](tutorial-assess-vmware.md) na potrzeby migracji do maszyn wirtualnych platformy Azure.
- [Przejrzyj dane](migrate-appliance.md#collected-data---vmware) zbierane przez urządzenie podczas odnajdywania.
