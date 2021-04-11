---
title: Skonfiguruj urządzenie Azure Migrate dla serwerów fizycznych
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate na potrzeby wykrywania i oceny serwera fizycznego.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 9052cbd3dc728b50b62c33f3a11a5e36a7504f29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771570"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Konfigurowanie urządzenia dla serwerów fizycznych

W tym artykule opisano sposób konfigurowania urządzenia Azure Migrate w przypadku oceniania serwerów fizycznych za pomocą narzędzia Azure Migrate do odnajdywania i oceny.

Urządzenie Azure Migrate jest lekkim urządzeniem używanym przez Azure Migrate: odnajdywanie i ocenianie, aby wykonać następujące czynności:

- Odnajdywanie serwerów lokalnych.
- Wysyłanie metadanych i danych wydajności dla odnalezionych serwerów do Azure Migrate: odnajdywania i oceny.

[Dowiedz się więcej](migrate-appliance.md) o urządzeniu Azure Migrate.


## <a name="appliance-deployment-steps"></a>Kroki wdrażania urządzenia

Aby skonfigurować urządzenie:

- Podaj nazwę urządzenia i Wygeneruj klucz projektu w portalu.
- Pobierz spakowany plik ze skryptem Instalatora Azure Migrate z Azure Portal.
- Wyodrębnij zawartość z pliku spakowanego. Uruchom konsolę programu PowerShell z uprawnieniami administracyjnymi.
- Wykonaj skrypt programu PowerShell, aby uruchomić aplikację sieci Web urządzenia.
- Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie przy użyciu klucza projektu.

### <a name="generate-the-project-key"></a>Generowanie klucza projektu

1. W obszarze **cele migracji**  >  **systemy Windows, Linux i SQL Server**  >  **Azure Migrate: odnajdywanie i Ocena** wybierz pozycję **odkryj**.
2. W obszarze **odnajdowanie serwerów**  >  **są zwirtualizowane serwery?** wybierz opcję **fizyczne lub inne (AWS, GCP, Xen itp.)**.
3. W obszarze **1: generowanie klucza projektu** Podaj nazwę urządzenia Azure Migrate, które zostanie skonfigurowane do odnajdywania serwerów fizycznych lub wirtualnych. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
1. Kliknij pozycję **Generuj klucz** , aby rozpocząć tworzenie wymaganych zasobów platformy Azure. Nie zamykaj strony odnajdywanie serwerów podczas tworzenia zasobów.
1. Po pomyślnym utworzeniu zasobów platformy Azure zostanie wygenerowany **klucz projektu** .
1. Skopiuj klucz, ponieważ będzie on potrzebny do ukończenia rejestracji urządzenia podczas jego konfiguracji.

### <a name="download-the-installer-script"></a>Pobierz skrypt Instalatora

W **2: Pobierz urządzenie Azure Migrate**, kliknij pozycję **Pobierz**.

   ![Wybory dla maszyn odnajdywania](./media/tutorial-assess-physical/servers-discover.png)


   ![Wybory dla klucza generowania](./media/tutorial-assess-physical/generate-key-physical.png)

### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na serwerach, do których pobrano plik, Otwórz okno polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla pliku spakowanego:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykładowe użycie chmury publicznej: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Przykładowe użycie w chmurze dla instytucji rządowych: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Sprawdź najnowszą wersję urządzenia i ustawienia [wartości skrótu](tutorial-discover-physical.md#verify-security) .
 

## <a name="run-the-azure-migrate-installer-script"></a>Uruchom skrypt Instalatora Azure Migrate
Skrypt Instalatora wykonuje następujące czynności:

- Instaluje agentów i aplikację sieci Web na potrzeby odnajdywania i oceny serwera fizycznego.
- Zainstaluj role systemu Windows, w tym usługi aktywacji systemu Windows, usług IIS i programu PowerShell ISE.
- Pobierz i zainstaluj moduł, który ma zostać przezapisywalny usług IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM) z trwałymi ustawieniami ustawień dla Azure Migrate.
- Tworzy następujące pliki w ścieżce:
    - **Pliki konfiguracji**:%ProgramData%\Microsoft Azure\Config
    - **Pliki dziennika**:%ProgramData%\Microsoft Azure\Logs

Uruchom skrypt w następujący sposób:

1. Wyodrębnij spakowany plik do folderu na serwerze, który będzie hostować urządzenie.  Upewnij się, że skrypt nie jest uruchamiany na serwerze z istniejącym urządzeniem Azure Migrate.
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

### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

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

    - Azure Migrate obsługuje klucz prywatny SSH generowany przez polecenie ssh-keygen przy użyciu algorytmów RSA, DSA, ECDSA i ed25519.
    - Obecnie Azure Migrate nie obsługuje klucza SSH opartego na hasłach. Użyj klucza SSH bez hasła.
    - Obecnie Azure Migrate nie obsługuje pliku klucza prywatnego SSH wygenerowanego przez program.
    - Azure Migrate obsługuje format OpenSSH pliku prywatnego klucza SSH, jak pokazano poniżej:
    
    ![Format obsługiwanego klucza prywatnego SSH](./media/tutorial-discover-physical/key-format.png)
1. Jeśli chcesz dodać jednocześnie wiele poświadczeń, kliknij pozycję **Dodaj więcej** , aby zapisać i dodać więcej poświadczeń. W przypadku odnajdywania serwerów fizycznych obsługiwane są wiele poświadczeń.
1. W **kroku 2: Podaj informacje o serwerze fizycznym lub wirtualnym**, kliknij pozycję **Dodaj źródło odnajdowania** , aby określić **adres IP/nazwę FQDN** serwera i przyjazną nazwę dla poświadczeń do nawiązania połączenia z serwerem.
1. Możesz **dodać pojedynczy element** naraz lub **dodać wiele elementów** w jednym miejscu. Istnieje również możliwość zapewnienia informacji o serwerze za poorednictwem **importowania woluminu CSV**.

    ![Opcje dodawania źródła odnajdywania](./media/tutorial-assess-physical/add-discovery-source-physical.png)

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

Wypróbuj [ocenę serwerów fizycznych](tutorial-assess-physical.md) za pomocą Azure Migrate: odnajdywania i oceny.