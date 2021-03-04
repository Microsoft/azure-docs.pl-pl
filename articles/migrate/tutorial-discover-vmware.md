---
title: 'Odnajdź serwery działające w środowisku VMware za pomocą Azure Migrate: Ocena serwera'
description: Informacje na temat odnajdywania lokalnych maszyn wirtualnych VMware za pomocą narzędzia do oceny Azure Migrate Server
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 9/14/2020
ms.custom: mvc
ms.openlocfilehash: 4d2b0fbb377beacdb75a1a5552855936bee2b205
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041315"
---
# <a name="tutorial-discover-servers-running-in-vmware-environment-with-azure-migrate-server-assessment"></a>Samouczek: odnajdywanie serwerów działających w środowisku VMware z Azure Migrate: Ocena serwera

W ramach kursu migracji na platformę Azure można wykryć spisy i obciążenia lokalne.

W tym samouczku pokazano, jak odnajdywać serwery działające w środowisku VMware przy użyciu Azure Migrate: narzędzia do oceny serwera przy użyciu urządzenia z uproszczonym Azure Migrate. Urządzenie jest wdrażane jako serwer działający w vCenter Server, aby w sposób ciągły wykrywać serwery i ich metadane wydajności, aplikacje działające na serwerach, zależności serwera SQL Server i wystąpieniach i bazach danych.

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
**Host vCenter Server/ESXi** | Potrzebujesz vCenter Server w wersji 5,5, 6,0, 6,5 lub 6,7.<br/><br/> Serwery muszą być hostowane na hoście ESXi z systemem w wersji 5,5 lub nowszej.<br/><br/> Na vCenter Server Zezwalaj na połączenia przychodzące na porcie TCP 443, aby urządzenie mogły zbierać metadane konfiguracji i wydajności.<br/><br/> Urządzenie domyślnie łączy się z vCenter Server na porcie 443. Jeśli vCenter Server nasłuchuje na innym porcie, można zmodyfikować port, gdy podajesz vCenter Server szczegóły w Menedżerze konfiguracji urządzeń.<br/><br/> Na hostach ESXi upewnij się, że dostęp przychodzący jest dozwolony na porcie TCP 443 w celu przeprowadzenia odnajdywania zainstalowanych aplikacji i analizy zależności bez agenta na serwerach.
**Wprowadzony** | vCenter Server wymaga zasobów do przydzielenia serwera dla urządzenia Azure Migrate:<br/><br/> -32 GB pamięci RAM, 8 procesorów wirtualnych vCPU i około 80 GB miejsca na dysku.<br/><br/> — Zewnętrzny przełącznik wirtualny i dostęp do Internetu na serwerze urządzeń, bezpośrednio lub za pośrednictwem serwera proxy.
**Serwery** | Wszystkie wersje systemów operacyjnych Windows i Linux są obsługiwane na potrzeby odnajdywania metadanych konfiguracji i wydajności. <br/><br/> Aby przeprowadzić odnajdywanie aplikacji na serwerach, obsługiwane są wszystkie wersje systemów operacyjnych Windows i Linux. Sprawdź wersje systemu operacyjnego obsługiwane [w przypadku analizy](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) zależności bez agentów.<br/><br/> Aby przeprowadzić odnajdywanie zainstalowanych aplikacji i analizy zależności bez agenta, należy zainstalować i uruchomić narzędzia VMware (nowsze niż 10.2.0) na serwerach. Na serwerach z systemem Windows musi być zainstalowany program PowerShell w wersji 2,0 lub nowszej.<br/><br/> Aby odnaleźć SQL Server wystąpienia i bazy danych, [zapoznaj się](migrate-support-matrix-vmware.md#requirements-for-discovery-of-sql-server-instances-and-databases) z obsługiwanymi SQL Server wersjami i wydaniami, obsługiwane wersje systemu operacyjnego Windows i mechanizmy uwierzytelniania.

> [!Note]
> Odnajdywanie i Ocena SQL Server wystąpień i baz danych działających w środowisku VMware jest teraz w wersji zapoznawczej. Aby wypróbować tę funkcję, użyj [**tego linku**](https://aka.ms/AzureMigrate/SQL) , aby utworzyć projekt w regionie **Australia Wschodnia** . Jeśli masz już projekt w Australii wschodniej i chcesz wypróbować tę funkcję, upewnij się, że zostały spełnione [**wymagania wstępne**](how-to-discover-sql-existing-project.md) w portalu.

## <a name="prepare-an-azure-user-account"></a>Przygotowywanie konta użytkownika platformy Azure

Aby utworzyć projekt Azure Migrate i zarejestrować urządzenie Azure Migrate, musisz mieć konto z:
- Uprawnienia współautora lub właściciela w ramach subskrypcji platformy Azure
- Uprawnienia do rejestrowania aplikacji Azure Active Directory (AAD)
- Właściciel lub współautor oraz uprawnienia administratora dostępu użytkowników do subskrypcji platformy Azure w celu utworzenia Key Vault używane podczas migracji serwera bez agenta

Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś właścicielem subskrypcji. Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać uprawnienia w następujący sposób:

1. W Azure Portal Wyszukaj "subskrypcje", a w obszarze **usługi** wybierz pozycję **subskrypcje**.

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="Wyszukaj w polu wyszukiwania subskrypcję platformy Azure":::


2. Na stronie **subskrypcje** wybierz subskrypcję, w której chcesz utworzyć projekt Azure Migrate.
3. W subskrypcji wybierz pozycję **Kontrola dostępu (IAM)**  >  **sprawdzanie dostępu**.
4. W obszarze **Sprawdź dostęp** Wyszukaj odpowiednie konto użytkownika.
5. W obszarze **Dodaj przypisanie roli** kliknij pozycję **Dodaj**.
:::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="Wyszukaj konto użytkownika, aby sprawdzić dostęp i przypisać rolę":::
    
6. W obszarze **Dodaj przypisanie roli** wybierz rolę współautor lub właściciela, a następnie wybierz konto (azmigrateuser w naszym przykładzie). Następnie kliknij przycisk **Zapisz**.

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="Otwiera stronę Dodawanie przypisania roli w celu przypisania roli do konta":::

1. Aby zarejestrować urządzenie, konto platformy Azure musi mieć **uprawnienia do rejestrowania aplikacji usługi AAD.**
1. W Azure Portal przejdź do **Azure Active Directory**  >  **użytkowników**  >  **Ustawienia użytkownika**.
1. W obszarze **Ustawienia użytkownika** Sprawdź, czy użytkownicy usługi Azure AD mogą rejestrować aplikacje (domyślnie ustawione na **wartość tak** ).

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="Sprawdź ustawienia użytkownika, które użytkownicy mogą rejestrować Active Directory aplikacje":::

9. Jeśli ustawienia "Rejestracje aplikacji" są ustawione na wartość "nie", zażądaj dzierżawy/administratora globalnego, aby przypisał wymagane uprawnienie. Alternatywnie, dzierżawa/Administrator globalny może przypisać rolę **dewelopera aplikacji** do konta, aby umożliwić rejestrację aplikacji usługi AAD. [Dowiedz się więcej](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>Przygotuj oprogramowanie VMware

Na vCenter Server sprawdź, czy Twoje konto ma uprawnienia do tworzenia maszyny wirtualnej przy użyciu pliku komórek jajowych. Jest to konieczne w przypadku wdrożenia urządzenia Azure Migrate jako maszyny wirtualnej VMware przy użyciu pliku komórki jajowe.

Azure Migrate wymaga vCenter Server konta tylko do odczytu na potrzeby odnajdywania i oceny serwerów działających w środowisku programu VMware. Jeśli chcesz również przeprowadzić odnajdywanie zainstalowanych aplikacji i analizy zależności bez agenta, konto wymaga uprawnień włączonych do **Virtual Machines > operacji gościa**.

### <a name="create-an-account-to-access-vcenter"></a>Utwórz konto, aby uzyskać dostęp do programu vCenter

W programie klient sieci Web vSphere Skonfiguruj konto w następujący sposób:

1. Korzystając z konta z uprawnieniami administratora, w > klienta sieci Web programu vSphere wybierz pozycję **Administracja**.
2. **Dostęp**, wybierz pozycję **Użytkownicy i grupy logowania jednokrotnego**.
3. W obszarze **Użytkownicy** Dodaj nowego użytkownika.
4. W obszarze **nowy użytkownik** wpisz szczegóły konta. Następnie kliknij przycisk **OK**.
5. W obszarze **uprawnienia globalne** wybierz konto użytkownika i przypisz rolę tylko do **odczytu** do konta. Następnie kliknij przycisk **OK**.
6.  Jeśli chcesz również przeprowadzić odnajdywanie zainstalowanych aplikacji i analizy zależności bez agenta, przejdź do **ról** > wybierz rolę tylko do **odczytu** , a w obszarze **uprawnienia** wybierz pozycję **operacje gościa**. Można propagować uprawnienia do wszystkich obiektów w vCenter Server, zaznaczając pole wyboru Propaguj do elementów podrzędnych.

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="Pole wyboru zezwalające na operacje gościa w roli tylko do odczytu":::


> [!NOTE]
> Można ograniczyć odnajdywanie do określonych vCenter Server centrów danych, klastrów, folderu klastrów, hostów, folderu hostów lub poszczególnych serwerów przez określanie zakresu vCenter Server konta. [**Dowiedz się więcej**](set-discovery-scope.md) na temat określania zakresu vCenter Servergo konta użytkownika.


### <a name="create-an-account-to-access-servers"></a>Utwórz konto, aby uzyskać dostęp do serwerów

Do przeprowadzenia odnajdywania zainstalowanych aplikacji, analizy zależności bez agenta i odnajdywania wystąpień SQL Server i baz danych potrzebne jest konto użytkownika z niezbędnymi uprawnieniami na serwerach. Konto użytkownika można podać w Menedżerze konfiguracji urządzenia. Urządzenie nie instaluje żadnych agentów na serwerach.

1. W przypadku serwerów z systemem Windows należy utworzyć konto (lokalne lub domeny) z uprawnieniami administracyjnymi na serwerach. Aby odnaleźć SQL Server wystąpienia i bazy danych, musisz mieć konto systemu Windows lub SQL Server być członkiem roli serwera sysadmin. [Dowiedz się więcej](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) na temat sposobu przypisywania wymaganej roli do konta użytkownika.
2. W przypadku serwerów z systemem Linux Utwórz konto z uprawnieniami głównymi. Alternatywnie można utworzyć konto z tymi uprawnieniami dla plików/bin/netstat i/bin/ls: CAP_DAC_READ_SEARCH i CAP_SYS_PTRACE.

> [!NOTE]
> Teraz można dodać wiele poświadczeń serwera w programie Configuration Manager, aby przeprowadzić odnajdywanie zainstalowanych aplikacji, analizę zależności bez agenta oraz odnajdywanie wystąpień SQL Server i baz danych. Można dodać wiele poświadczeń uwierzytelniania domen/okien (nienależących do domeny)/Linux (nienależących do domeny) i/lub SQL Server. [**Dowiedz się więcej**](add-server-credentials.md)

## <a name="set-up-a-project"></a>Konfigurowanie projektu

Skonfiguruj nowy projekt Azure Migrate.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Przegląd** wybierz pozycję **Utwórz projekt**.
5. W obszarze **Utwórz projekt** wybierz swoją subskrypcję platformy Azure i grupę zasobów. Utwórz grupę zasobów, jeśli jej nie masz.
6. W obszarze **szczegóły projektu** Określ nazwę projektu i geografię, w której chcesz utworzyć projekt. Przejrzyj obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="Pola nazwy i regionu projektu":::

7. Wybierz przycisk **Utwórz**.
8. Zaczekaj kilka minut, aż projekt Azure Migrate zostanie wdrożony. **Azure Migrate: Narzędzie do oceny serwera** jest domyślnie dodawane do nowego projektu.

> [!NOTE]
> Jeśli projekt został już utworzony, możesz użyć tego samego projektu do zarejestrowania dodatkowych urządzeń, aby odnaleźć i ocenić więcej. serwerów. [ **Dowiedz się więcej**](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Konfigurowanie urządzenia

Azure Migrate: Ocena serwera używa urządzenia uproszczonego Azure Migrate. Urządzenie wykonuje odnajdywanie serwera i wysyła metadane dotyczące konfiguracji serwera i wydajności do Azure Migrate. Urządzenie można skonfigurować przez wdrożenie szablonu komórki jajowe, który można pobrać z projektu.

> [!NOTE]
> Jeśli z jakiegoś powodu nie można skonfigurować urządzenia przy użyciu szablonu, można skonfigurować go za pomocą skryptu programu PowerShell na istniejącym serwerze z systemem Windows Server 2016. [**Dowiedz się więcej**](deploy-appliance-script.md#set-up-the-appliance-for-vmware).

### <a name="deploy-with-ova"></a>Wdrażanie przy użyciu komórek jajowych

Aby skonfigurować urządzenie przy użyciu szablonu komórki jajowe:
1. Podaj nazwę urządzenia i Wygeneruj klucz projektu Azure Migrate w portalu.
1. Pobierz plik szablonu komórki jajowe i zaimportuj go do vCenter Server. Sprawdź, czy komórki jajowe są bezpieczne.
1. Utwórz maszynę wirtualną z urządzeniem z pliku JAJOWE i sprawdź, czy może ona połączyć się z Azure Migrate.
1. Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie przy użyciu klucza projektu Azure Migrate.

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Wygeneruj klucz projektu Azure Migrate

1. W obszarze **Cele migracji** > **Serwery** > **Azure Migrate: Server Assessment** wybierz pozycję **Odnajdź**.
2. W obszarze **odnajdywanie** maszyn  >  **są zwirtualizowane maszyny?** wybierz pozycję **tak, aby uzyskać VMware vSphere funkcji hypervisor**.
3. W obszarze **1: generowanie klucza projektu Azure Migrate** Podaj nazwę urządzenia Azure Migrate, które zostanie skonfigurowane do odnajdywania serwerów w środowisku programu VMware. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
1. Kliknij pozycję **Generuj klucz** , aby rozpocząć tworzenie wymaganych zasobów platformy Azure. Nie zamykaj strony odnajdywania podczas tworzenia zasobów.
1. Po pomyślnym utworzeniu zasobów platformy Azure zostanie wygenerowany **klucz projektu Azure Migrate** .
1. Skopiuj klucz, ponieważ będzie on potrzebny do ukończenia rejestracji urządzenia podczas jego konfiguracji.

### <a name="2-download-the-ova-template"></a>2. Pobierz szablon komórki jajowe

W **2: Pobierz urządzenie Azure Migrate**, wybierz opcję. Plik komórki jajowe i kliknij pozycję **Pobierz**.

### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy plik komórki jajowe jest bezpieczny:

1. Na serwerze, do którego pobrano plik, Otwórz okno polecenia administratora.
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

### <a name="3-create-the-appliance-server"></a>3. Utwórz serwer urządzenia

Zaimportuj pobrany plik i Utwórz serwer w środowisku VMware

1. W konsoli klienta vSphere kliknij pozycję **plik**  >  **Wdróż OVF szablon**.
2. W Kreatorze wdrażania szablonu OVF > **Źródło** Określ lokalizację pliku komórek jajowych.
3. W polu **Nazwa** i **Lokalizacja** Określ przyjazną nazwę serwera. Wybierz obiekt spisu, w którym będzie hostowany serwer.
5. W obszarze **host/klaster** Określ hosta lub klaster, na którym będzie uruchamiany serwer programu.
6. W obszarze **Magazyn** określ miejsce docelowe magazynu dla serwera programu.
7. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
8. W polu **mapowanie sieci** Określ sieć, z którą zostanie nawiązane połączenie serwer. Sieć wymaga łączności z Internetem, aby można było wysyłać metadane do Azure Migrate.
9. Sprawdź poprawność ustawień, a następnie kliknij pozycję **Finish** (Zakończ).


### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że serwer urządzenia może połączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i dla [instytucji rządowych](migrate-appliance.md#government-cloud-urls) .


### <a name="4-configure-the-appliance"></a>4. Skonfiguruj urządzenie

Skonfiguruj urządzenie po raz pierwszy.

> [!NOTE]
> Jeśli urządzenie zostanie skonfigurowane przy użyciu [**skryptu programu PowerShell**](deploy-appliance-script.md) zamiast pobranych komórek jajowych, pierwsze dwa kroki tej procedury nie są istotne.

1. W konsoli klienta vSphere kliknij prawym przyciskiem myszy serwer, a następnie wybierz polecenie **Otwórz konsolę**.
2. Podaj język, strefę czasową i hasło dla urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z serwerem urządzeń, a następnie otwórz adres URL menedżera konfiguracji urządzenia: `https://appliance name or IP address: 44368` .

   Alternatywnie można otworzyć program Configuration Manager z poziomu pulpitu serwera urządzeń, wybierając skrót do programu Configuration Manager.
1. Zaakceptuj **postanowienia licencyjne** i przeczytaj informacje o innych firmach.
1. W programie Configuration Manager > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
   - **Łączność**: Urządzenie sprawdza, czy serwer ma dostęp do Internetu. Jeśli serwer używa serwera proxy:
     - Kliknij pozycję **Skonfiguruj serwer proxy** , aby określić adres serwera proxy `http://ProxyIPAddress` lub `http://ProxyFQDN` port nasłuchujący.
     - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
     - Obsługiwane są tylko serwery proxy HTTP.
     - Jeśli dodano szczegóły serwera proxy lub wyłączono serwer proxy i/lub uwierzytelnianie, kliknij przycisk **Zapisz** , aby ponownie uruchomić sprawdzanie łączności.
   - **Synchronizacja czasu**: czas na urządzeniu powinien być zsynchronizowany z czasem Internetu, aby odnajdywanie działało prawidłowo.
   - **Zainstaluj aktualizacje**: urządzenie zapewnia zainstalowanie najnowszych aktualizacji. Po zakończeniu sprawdzania można kliknąć pozycję **Wyświetl usługi urządzenia** , aby zobaczyć stan i wersje usług uruchomionych na serwerze urządzeń.
   - **Zainstaluj VDDK**: Urządzenie sprawdza, czy zainstalowano pakiet VMware vSphere Virtual Disk Development Kit (VDDK). Jeśli nie jest zainstalowany, Pobierz VDDK 6,7 z programu VMware i Wyodrębnij zawartość pliku zip do określonej lokalizacji na urządzeniu, zgodnie z **instrukcjami dotyczącymi instalacji**.

     Migracja serwera Azure Migrate przy użyciu programu VDDK do replikowania serwerów podczas migracji na platformę Azure. 
1. Jeśli chcesz, możesz **ponownie uruchomić wymagania wstępne** w dowolnym momencie podczas konfigurowania urządzenia, aby sprawdzić, czy urządzenie spełnia wszystkie wymagania wstępne.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Panel 1 w Menedżerze konfiguracji urządzenia":::


### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Wklej **klucz projektu Azure Migrate** skopiowany z portalu. Jeśli nie masz klucza, przejdź do pozycji **Ocena serwera> odkryj> zarządzanie istniejącymi urządzeniami**, wybierz nazwę urządzenia podaną w momencie generowania klucza i skopiuj odpowiedni klucz.
1. Do uwierzytelnienia w systemie Azure potrzebny będzie kod urządzenia. Kliknięcie przycisku **Zaloguj** spowoduje otwarcie modalnego kodu urządzenia, jak pokazano poniżej.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Modalne wyświetlanie kodu urządzenia":::

1. Kliknij pozycję **Kopiuj kod & login** , aby skopiować kod urządzenia i otworzyć monit logowania platformy Azure na nowej karcie przeglądarki. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
1. Na nowej karcie Wklej kod urządzenia i zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure.
   
   Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. W przypadku nagłego zamknięcia karty logowania bez logowania, należy odświeżyć kartę przeglądarki w Menedżerze konfiguracji urządzenia, aby ponownie włączyć przycisk Zaloguj.
1. Po pomyślnym zalogowaniu Wróć do poprzedniej karty przy użyciu Menedżera konfiguracji urządzeń.
1. Jeśli konto użytkownika platformy Azure używane do rejestrowania ma odpowiednie uprawnienia do zasobów platformy Azure utworzonych podczas generowania klucza, Rejestracja urządzenia zostanie zainicjowana.
1. Po pomyślnym zarejestrowaniu urządzenia można wyświetlić szczegóły rejestracji, klikając pozycję **Wyświetl szczegóły**.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Panel 2 w Menedżerze konfiguracji urządzenia":::

## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

### <a name="provide-vcenter-server-details"></a>Podaj szczegóły vCenter Server

Urządzenie musi połączyć się z vCenter Server, aby odnaleźć dane konfiguracji i wydajności serwerów.

1. W **kroku 1: podaj poświadczenia vCenter Server**, kliknij pozycję **Dodaj poświadczenia** , aby określić przyjazną nazwę dla poświadczeń, Dodaj **nazwę użytkownika** i **hasło** do konta vCenter Server, które będzie używane przez urządzenie do odnajdywania serwerów z systemem w vCenter Server.
    - Należy skonfigurować konto z wymaganymi uprawnieniami, które opisano w tym artykule.
    - Jeśli chcesz przeznaczyć zakres odnajdywania do określonych obiektów VMware (vCenter Server centrach danych, klastrów, folderu klastrów, hostów, folderu hostów lub poszczególnych maszyn wirtualnych), zapoznaj się z instrukcjami w [tym artykule](set-discovery-scope.md) , aby ograniczyć konto używane przez Azure Migrate.
1. W **kroku 2: podaj vCenter Server Szczegóły**, kliknij pozycję **Dodaj źródło odnajdywania** , aby wybrać przyjazną nazwę dla poświadczeń z listy rozwijanej, określ **adres IP/nazwę FQDN** vCenter Server. Możesz pozostawić **port** domyślny (443) lub określić port niestandardowy, dla którego vCenter Server nasłuchiwanie i kliknąć przycisk **Zapisz**.
1. Po kliknięciu przycisku **Zapisz** Urządzenie spróbuje sprawdzić poprawność połączenia z vCenter Server przy użyciu podanych poświadczeń i wyświetlić **stan weryfikacji** w tabeli dotyczącej vCenter Server adres IP/nazwa FQDN.
1. Przed rozpoczęciem odnajdywania można ponownie **sprawdzić poprawność** łączności, aby vCenter Server w dowolnym momencie.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panel 3 w Menedżerze konfiguracji urządzeń dla vCenter Server Szczegóły":::

### <a name="provide-server-credentials"></a>Podaj poświadczenia serwera

W **kroku 3: zapewnianie poświadczeń serwera do wykonywania spisu oprogramowania, analizy zależności bez agentów i odnajdywania SQL Server wystąpień i baz danych**, można wybrać opcję udostępnienia wielu poświadczeń serwera lub jeśli nie chcesz korzystać z tych funkcji, możesz pominąć ten krok i kontynuować odnajdywanie vCenter Server. Zamiar można zmienić w dowolnym momencie później.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Panel 3 w Menedżerze konfiguracji urządzenia dla szczegółów serwera":::

> [!Note]
> Odnajdywanie i Ocena SQL Server wystąpień i baz danych działających w środowisku VMware jest teraz w wersji zapoznawczej. Aby wypróbować tę funkcję, użyj [**tego linku**](https://aka.ms/AzureMigrate/SQL) , aby utworzyć projekt w regionie **Australia Wschodnia** . Jeśli masz już projekt w Australii wschodniej i chcesz wypróbować tę funkcję, upewnij się, że zostały spełnione [**wymagania wstępne**](how-to-discover-sql-existing-project.md) w portalu.

Jeśli chcesz korzystać z tych funkcji, możesz podać poświadczenia serwera, wykonując poniższe kroki. Urządzenie podejmie próbę automatycznego mapowania poświadczeń do serwerów w celu wykonania funkcji odnajdywania.

- Możesz dodać poświadczenia serwera, klikając przycisk **Dodaj poświadczenia** . Spowoduje to otwarcie modalnego, w którym można wybrać **Typ poświadczeń** z listy rozwijanej.
- Możesz podać domenę/system Windows (nienależące do domeny)/Linux (niedomenowe)/SQL Server poświadczenia uwierzytelniania. [Dowiedz się więcej](add-server-credentials.md) na temat podania poświadczeń i sposobu ich obsługi.
- Dla każdego typu poświadczeń należy określić przyjazną nazwę dla poświadczeń, dodać **nazwę użytkownika** i **hasło** , a następnie kliknąć przycisk **Zapisz**.
- W przypadku wybrania poświadczeń domeny należy również określić nazwę FQDN domeny. Nazwa FQDN jest wymagana do zweryfikowania autentyczności poświadczeń z Active Directory tej domeny.
- Zapoznaj się z [uprawnieniami wymaganymi](add-server-credentials.md#required-permissions) na koncie w celu odnalezienia zainstalowanych aplikacji, analizy zależności bez wykorzystania agentów lub odnajdywania wystąpień SQL Server i baz danych.
- Jeśli chcesz dodać jednocześnie wiele poświadczeń, kliknij pozycję **Dodaj więcej** , aby zapisać i dodać więcej poświadczeń.
- Po kliknięciu przycisku **Zapisz** lub **Dodaj więcej** urządzenie sprawdza poprawność poświadczeń domeny z Active Directory domeny w celu ich autentyczności. W tym celu należy unikać blokowania kont, gdy urządzenie wykonuje wiele iteracji w celu mapowania poświadczeń na odpowiednie serwery.
- W tabeli poświadczeń można zobaczyć **stan sprawdzania poprawności** dla wszystkich poświadczeń domeny. Zweryfikowane zostaną tylko poświadczenia domeny.
- Jeśli sprawdzanie poprawności zakończy się niepowodzeniem, można kliknąć pozycję stan **niepowodzenia** , aby zobaczyć błąd napotkany, a następnie kliknąć przycisk ponownie **Sprawdź poprawność poświadczeń** po rozwiązaniu problemu w celu ponownego zweryfikowania nieudanych poświadczeń domeny.


### <a name="start-discovery"></a>Rozpocznij odnajdywanie

1. Kliknij pozycję **Rozpocznij odnajdywanie**, aby uruchomić odnajdywanie vCenter Server. Po pomyślnym zainicjowaniu odnajdywania można sprawdzić stan odnajdywania dla vCenter Server adres IP/nazwa FQDN w tabeli sources.
1. Jeśli podano poświadczenia serwera, Spis oprogramowania (Odnajdywanie zainstalowanych aplikacji) zostanie automatycznie zainicjowany po zakończeniu odnajdywania vCenter Server. Spis oprogramowania jest wykonywany co 12 godzin.
1. [Spis oprogramowania](how-to-discover-applications.md) służy do identyfikowania wystąpień SQL Server uruchomionych na serwerach i korzystania z tych informacji, urządzenia próbuje nawiązać połączenie z wystąpieniami za pośrednictwem uwierzytelniania systemu Windows lub poświadczeń uwierzytelniania SQL Server dostarczonych na urządzeniu oraz zbierać dane w SQL Server bazach danych i ich właściwościach. Funkcja odnajdywania SQL jest przeprowadzana co 24 godziny.
1. Podczas tworzenia spisu oprogramowania poświadczenia dodanych serwerów będą powtarzane względem serwerów i sprawdzane pod kątem analizy zależności bez agenta. Można włączyć analizę zależności bez agenta dla serwerów z poziomu portalu. Tylko serwery, na których Walidacja zakończyła się powodzeniem, można wybrać, aby włączyć analizę zależności bez agenta.

> [!Note]
>Azure Migrate zaszyfruje komunikację między urządzeniem Azure Migrate i SQL Server wystąpieniami źródła (z właściwością Szyfruj połączenie ustawioną na wartość TRUE). Te połączenia są szyfrowane za pomocą [**TrustServerCertificate**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (wartość true). Warstwa transportu będzie używać protokołu SSL do szyfrowania kanału i pomijania łańcucha certyfikatów w celu zweryfikowania zaufania. Serwer urządzenia musi być skonfigurowany do [**zaufania głównego urzędu certyfikacji**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Jeśli podczas uruchamiania nie zainicjowano obsługi administracyjnej certyfikatu na serwerze, SQL Server generuje certyfikat z podpisem własnym, który jest używany do szyfrowania pakietów logowania. [**Dowiedz się więcej**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Odnajdywanie działa w następujący sposób:
- Aby spis odnalezionych serwerów pojawił się w portalu, zajmie około 15 minut.
- Odnajdywanie zainstalowanych aplikacji może zająć trochę czasu. Czas trwania zależy od liczby odnalezionych serwerów. W przypadku serwerów z 500 na wykrytym magazynie w portalu Azure Migrate trwa około godziny.
- Po zakończeniu odnajdywania serwerów można włączyć analizę zależności bez agenta na serwerach z poziomu portalu.
- SQL Server wystąpienia i bazy danych zostaną uruchomione w portalu w ciągu 24 godzin od inicjacji odnajdywania.

## <a name="next-steps"></a>Następne kroki

- [Oceniaj serwery](./tutorial-assess-vmware-azure-vm.md) do migracji na maszyny wirtualne platformy Azure.
- [Ocenianie serwerów SQL](./tutorial-assess-sql.md) na potrzeby migracji do usługi Azure SQL.
- [Przejrzyj dane](migrate-appliance.md#collected-data---vmware) zbierane przez urządzenie podczas odnajdywania.