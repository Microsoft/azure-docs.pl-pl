---
title: Konfigurowanie urządzenia Azure Migrate dla funkcji Hyper-V
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate do oceniania i migrowania maszyn wirtualnych funkcji Hyper-V.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: a12785ea3791366badb907498796a73c2c993623
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314168"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Konfigurowanie urządzenia dla maszyn wirtualnych funkcji Hyper-V

Postępuj zgodnie z tym artykułem, aby skonfigurować urządzenie Azure Migrate do oceny maszyn wirtualnych funkcji Hyper-V za pomocą narzędzia do [oceny serwera Azure Migrate:](migrate-services-overview.md#azure-migrate-server-assessment-tool) .

[Urządzenie Azure Migrate](migrate-appliance.md) jest lekkim urządzeniem używanym przez Azure Migrate: Ocena serwera/migracja, aby odnajdywać lokalne maszyny wirtualne funkcji Hyper-V i wysyłać dane dotyczące metadanych/wydajności maszyny wirtualnej do platformy Azure.

Urządzenie można wdrożyć przy użyciu kilku metod:

- Skonfiguruj maszynę wirtualną funkcji Hyper-V przy użyciu pobranego wirtualnego dysku twardego. Jest to metoda opisana w tym artykule.
- Skonfiguruj na maszynie wirtualnej funkcji Hyper-V lub na komputerze fizycznym za pomocą skryptu Instalatora programu PowerShell. [Tej metody](deploy-appliance-script.md) należy użyć, jeśli nie można skonfigurować maszyny wirtualnej przy użyciu dysku VHD lub jeśli jesteś w Azure Government.

Po utworzeniu urządzenia sprawdź, czy może nawiązać połączenie z Azure Migrate: Ocena serwera, skonfiguruj ją po raz pierwszy i zarejestruj ją za pomocą projektu Azure Migrate.

## <a name="appliance-deployment-vhd"></a>Wdrażanie urządzenia (VHD)

Aby skonfigurować urządzenie przy użyciu szablonu wirtualnego dysku twardego:

- Podaj nazwę urządzenia i Wygeneruj klucz projektu Azure Migrate w portalu.
- Pobierz skompresowany wirtualny dysk twardy funkcji Hyper-V z Azure Portal.
- Utwórz urządzenie i sprawdź, czy może nawiązać połączenie z oceną serwera Azure Migrate.
- Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate przy użyciu klucza projektu Azure Migrate.

### <a name="generate-the-azure-migrate-project-key"></a>Generowanie klucza projektu Azure Migrate

1. W obszarze **Cele migracji** > **Serwery** > **Azure Migrate: Server Assessment** wybierz pozycję **Odnajdź**.
2. W obszarze **odnajdywanie**maszyn  >  **są zwirtualizowane maszyny?** wybierz pozycję **tak, używając funkcji Hyper-V**.
3. W **1: Wygeneruj klucz projektu Azure Migrate**, podaj nazwę urządzenia Azure Migrate, które zostanie skonfigurowane do odnajdywania maszyn wirtualnych funkcji Hyper-V. nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
1. Kliknij pozycję **Generuj klucz** , aby rozpocząć tworzenie wymaganych zasobów platformy Azure. Nie zamykaj strony odnajdywanie maszyn podczas tworzenia zasobów.
1. Po pomyślnym utworzeniu zasobów platformy Azure zostanie wygenerowany **klucz projektu Azure Migrate** .
1. Skopiuj klucz, ponieważ będzie on potrzebny do ukończenia rejestracji urządzenia podczas jego konfiguracji.

### <a name="download-the-vhd"></a>Pobierz dysk VHD

W **2: Pobierz urządzenie Azure Migrate**, wybierz opcję. Plik VHD i kliknij pozycję **Pobierz**. 

   ![Wybory dla maszyn odnajdywania](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Wybory dla klucza generowania](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla wirtualnego dysku twardego
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```





## <a name="create-the-appliance-vm"></a>Tworzenie maszyny wirtualnej urządzenia

Zaimportuj pobrany plik i Utwórz maszynę wirtualną.

1. Wyodrębnij plik skompresowanego dysku VHD do folderu na hoście funkcji Hyper-V, który będzie obsługiwał maszynę wirtualną urządzenia. Trzy foldery są wyodrębniane.
2. Otwórz Menedżera funkcji Hyper-V. W obszarze **Akcje**kliknij pozycję **Importuj maszynę wirtualną**.

    ![Wdrażanie wirtualnego dysku twardego](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Po **rozpoczęciu pracy**Kreatora importu maszyny wirtualnej > kliknij przycisk **dalej**.
3. W obszarze **lokalizowanie folderu**określ folder zawierający wyodrębniony wirtualny dysk twardy. Następnie kliknij przycisk **Dalej**.
1. W obszarze **Wybierz maszynę wirtualną**kliknij przycisk **dalej**.
2. W obszarze **Wybierz typ importu**kliknij pozycję **Kopiuj maszynę wirtualną (Utwórz nowy unikatowy identyfikator)**. Następnie kliknij przycisk **Dalej**.
3. W obszarze **Wybierz lokalizację docelową**pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
4. W obszarze **foldery magazynu**pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
5. W obszarze **Wybierz sieć**Określ przełącznik wirtualny, który będzie używany przez maszynę wirtualną. Przełącznik wymaga połączenia z Internetem, aby wysyłać dane do platformy Azure.
6. W obszarze **Podsumowanie**przejrzyj ustawienia. Następnie kliknij przycisk **Zakończ**.
7. W Menedżerze funkcji Hyper-V > **Virtual Machines**Uruchom maszynę wirtualną.


### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może połączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i dla [instytucji rządowych](migrate-appliance.md#government-cloud-urls) .

### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

> [!NOTE]
> Jeśli urządzenie zostanie skonfigurowane przy użyciu [skryptu programu PowerShell](deploy-appliance-script.md) zamiast pobranego wirtualnego dysku twardego, pierwsze dwa kroki tej procedury nie są istotne.

1. W Menedżerze funkcji Hyper-V > **Virtual Machines**kliknij prawym przyciskiem myszy maszynę wirtualną > **Połącz**.
2. Podaj język, strefę czasową i hasło dla urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną wirtualną, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację na pulpicie urządzenia, klikając skrót do aplikacji.
1. Zaakceptuj **postanowienia licencyjne**i przeczytaj informacje o innych firmach.
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
1. Kliknij pozycję **Zaloguj się**. Spowoduje to otwarcie monitu logowania platformy Azure na nowej karcie przeglądarki. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
1. Na nowej karcie Zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure.
   
   Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu Wróć do aplikacji sieci Web. 
4. Jeśli konto użytkownika platformy Azure używane do rejestrowania ma odpowiednie [uprawnienia](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) do zasobów platformy Azure utworzonych podczas generowania klucza, Rejestracja urządzenia zostanie zainicjowana.
1. Po pomyślnym zarejestrowaniu urządzenia można wyświetlić szczegóły rejestracji, klikając pozycję **Wyświetl szczegóły**.



### <a name="delegate-credentials-for-smb-vhds"></a>Delegowanie poświadczeń dla wirtualnych dysków twardych SMB

Jeśli używasz dysków VHD w systemie technologii, musisz włączyć delegowanie poświadczeń z urządzenia do hostów funkcji Hyper-V. W tym celu z urządzenia:

1. Na maszynie wirtualnej urządzenia Uruchom to polecenie. HyperVHost1/HyperVHost2 są przykładowymi nazwami hostów.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Alternatywnie należy to zrobić w Edytor lokalnych zasad grupy na urządzeniu:
    - W obszarze Konfiguracja komputera **zasad komputera lokalnego**  >  **Computer Configuration**kliknij pozycję **Szablony administracyjne**  >  **System**  >  **delegowania poświadczeń**systemowych.
    - Kliknij dwukrotnie pozycję **Zezwól na delegowanie świeżych poświadczeń**i wybierz pozycję **włączone**.
    - W obszarze **Opcje**kliknij pozycję **Pokaż**, a następnie na liście Dodaj każdego hosta funkcji Hyper-V, który ma zostać odnajdowany, przy użyciu **usługi WSMAN/** jako prefiksu.
    - W obszarze  **Delegowanie poświadczeń**kliknij dwukrotnie pozycję **Zezwól na delegowanie świeżych poświadczeń z uwierzytelnianiem serwera tylko NTLM**. Ponownie Dodaj każdy host funkcji Hyper-V, który ma zostać odnalezienie do listy, przy użyciu **usługi WSMAN/** jako prefiksu.

## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Połącz się z urządzeniem z hostami lub klastrami funkcji Hyper-V, a następnie Uruchom odnajdywanie maszyn wirtualnych.

1. W **kroku 1: podaj poświadczenia hosta funkcji Hyper-V**, kliknij pozycję **Dodaj poświadczenia** , aby określić przyjazną nazwę dla poświadczeń, Dodaj **nazwę użytkownika** i **hasło** dla hosta lub klastra funkcji Hyper-v, którego urządzenie będzie używało do odnajdywania maszyn wirtualnych. Kliknij pozycję **Zapisz**.
1. Jeśli chcesz dodać jednocześnie wiele poświadczeń, kliknij pozycję **Dodaj więcej** , aby zapisać i dodać więcej poświadczeń. Funkcja odnajdywania maszyn wirtualnych funkcji Hyper-V obsługuje wiele poświadczeń.
1. W **kroku 2: Podaj szczegóły dotyczące hosta lub klastra funkcji Hyper-v**, kliknij pozycję **Dodaj źródło odnajdowania** , aby określić hosta funkcji Hyper-v/ **adres IP/FQDN** klastra i przyjazną nazwę dla poświadczeń do nawiązania połączenia z hostem/klastrem.
1. Możesz **dodać pojedynczy element** naraz lub **dodać wiele elementów** w jednym miejscu. Istnieje również możliwość zapewnienia szczegółowych informacji o hoście lub klastrze funkcji Hyper-V za poorednictwem **importowania woluminu CSV**.

    ![Opcje dodawania źródła odnajdywania](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - W przypadku wybrania opcji **Dodaj pojedynczy element**należy określić przyjazną nazwę dla poświadczeń i hosta funkcji Hyper-V/ **adresu IP klastra/nazwy FQDN** , a następnie kliknąć przycisk **Zapisz**.
    - W przypadku wybrania opcji **Dodaj wiele elementów** _(wybrane domyślnie)_ można dodać wiele rekordów jednocześnie, określając w polu tekstowym opcję Host/klaster **/nazwa FQDN** klastra funkcji Hyper-V z przyjazną nazwą. **Sprawdź** dodane rekordy i kliknij pozycję **Zapisz**.
    - W przypadku wybrania opcji **Importuj woluminy CSV**można pobrać plik szablonu CSV, wypełnić go hostem/ **adresem IP klastra/nazwą FQDN** i przyjazną nazwą dla poświadczeń. Następnie zaimportuj plik do urządzenia, **Sprawdź** rekordy w pliku i kliknij przycisk **Zapisz**.

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

Wypróbuj [ocenę funkcji Hyper-V](tutorial-assess-hyper-v.md) dzięki ocenie serwera Azure Migrate.