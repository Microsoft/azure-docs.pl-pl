---
title: Konfigurowanie urządzenia Azure Migrate dla funkcji Hyper-V
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate, aby ocenić i zmigrować serwery w funkcji Hyper-V.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 8f0e7ec2566928897d2b84357b599506520e8d95
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612028"
---
# <a name="set-up-an-appliance-for-servers-on-hyper-v"></a>Konfigurowanie urządzenia dla serwerów w funkcji Hyper-V

Postępuj zgodnie z tym artykułem, aby skonfigurować urządzenie Azure Migrate na potrzeby odnajdywania i oceny serwerów w funkcji Hyper-V za pomocą narzędzia [Azure Migrate do odnajdywania i oceny](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) .

[Urządzenie Azure Migrate](migrate-appliance.md) jest lekkim urządzeniem używanym przez Azure Migrate: odnajdywanie i ocenianie/Migrowanie w celu odnajdywania serwerów lokalnych w funkcji Hyper-V oraz wysyłania danych metadanych/wydajności serwera do platformy Azure.

Urządzenie można wdrożyć przy użyciu kilku metod:

- Skonfiguruj serwer na serwerze funkcji Hyper-V przy użyciu pobranego wirtualnego dysku twardego. Ta metoda została opisana w tym artykule.
- Konfiguracja na serwerze na serwerze funkcji Hyper-V lub serwera fizycznego przy użyciu skryptu Instalatora programu PowerShell. [Tej metody](deploy-appliance-script.md) należy użyć, jeśli nie można skonfigurować serwera przy użyciu dysku VHD lub jeśli jesteś w Azure Government.

Po utworzeniu urządzenia sprawdź, czy może nawiązać połączenie z Azure Migrate: odnajdywanie i ocenianie, Konfigurowanie po raz pierwszy i rejestrowanie go w projekcie.

## <a name="appliance-deployment-vhd"></a>Wdrażanie urządzenia (VHD)

Aby skonfigurować urządzenie przy użyciu szablonu wirtualnego dysku twardego:

- Podaj nazwę urządzenia i Wygeneruj klucz projektu w portalu.
- Pobierz skompresowany wirtualny dysk twardy funkcji Hyper-V z Azure Portal.
- Utwórz urządzenie i sprawdź, czy może nawiązać połączenie z Azure Migrate: odnajdywania i oceny.
- Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie przy użyciu klucza projektu.

### <a name="generate-the-project-key"></a>Generowanie klucza projektu

1. W obszarze **cele migracji**  >  **systemy Windows, Linux i SQL Server**  >  **Azure Migrate: odnajdywanie i Ocena** wybierz pozycję **odkryj**.
2. W obszarze **odnajdowanie serwerów**  >  **są zwirtualizowane serwery?** wybierz opcję **tak, używając funkcji Hyper-V**.
3. W obszarze **1: generowanie klucza projektu** Podaj nazwę urządzenia Azure Migrate, które zostanie skonfigurowane na potrzeby odnajdywania serwerów w funkcji Hyper-V. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
1. Kliknij pozycję **Generuj klucz** , aby rozpocząć tworzenie wymaganych zasobów platformy Azure. Nie zamykaj strony odnajdywanie serwerów podczas tworzenia zasobów.
1. Po pomyślnym utworzeniu zasobów platformy Azure zostanie wygenerowany **klucz projektu** .
1. Skopiuj klucz, ponieważ będzie on potrzebny do ukończenia rejestracji urządzenia podczas jego konfiguracji.

### <a name="download-the-vhd"></a>Pobierz dysk VHD

W **2: Pobierz urządzenie Azure Migrate**, wybierz opcję. Plik VHD i kliknij pozycję **Pobierz**.

   ![Wybory dla serwerów odnajdowania](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Wybory dla klucza generowania](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)

### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na serwerze, do którego pobrano plik, Otwórz okno polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla wirtualnego dysku twardego
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

Sprawdź najnowszą wartość skrótu, porównując wynik powyższego polecenia z wartością udokumentowaną [tutaj](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v#verify-security) .

## <a name="create-the-appliance"></a>Utwórz urządzenie

Zaimportuj pobrany plik i Utwórz urządzenie.

1. Wyodrębnij plik skompresowanego dysku VHD do folderu na hoście funkcji Hyper-V, który będzie hostować urządzenie. Trzy foldery są wyodrębniane.
2. Otwórz Menedżera funkcji Hyper-V. W obszarze **Akcje** kliknij pozycję **Importuj maszynę wirtualną**.

    ![Wdrażanie wirtualnego dysku twardego](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Po **rozpoczęciu pracy** Kreatora importu maszyny wirtualnej > kliknij przycisk **dalej**.
3. W obszarze **lokalizowanie folderu** określ folder zawierający wyodrębniony wirtualny dysk twardy. Następnie kliknij przycisk **Dalej**.
1. W obszarze **Wybierz maszynę wirtualną** kliknij przycisk **dalej**.
2. W obszarze **Wybierz typ importu** kliknij pozycję **Kopiuj maszynę wirtualną (Utwórz nowy unikatowy identyfikator)**. Następnie kliknij przycisk **Dalej**.
3. W obszarze **Wybierz lokalizację docelową** pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
4. W obszarze **foldery magazynu** pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
5. W obszarze **Wybierz sieć** Określ przełącznik wirtualny, który będzie używany przez serwer. Przełącznik wymaga połączenia z Internetem, aby wysyłać dane do platformy Azure.
6. W obszarze **Podsumowanie** przejrzyj ustawienia. Następnie kliknij przycisk **Zakończ**.
7. W Menedżerze funkcji Hyper-V > **Virtual Machines** Uruchom maszynę wirtualną.

### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że urządzenie może połączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i dla [instytucji rządowych](migrate-appliance.md#government-cloud-urls) .

### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

> [!NOTE]
> Jeśli urządzenie zostanie skonfigurowane przy użyciu [skryptu programu PowerShell](deploy-appliance-script.md) zamiast pobranego wirtualnego dysku twardego, pierwsze dwa kroki tej procedury nie są istotne.

1. W Menedżerze funkcji Hyper-V > **Virtual Machines**, kliknij prawym przyciskiem myszy serwer > **Połącz**.
2. Podaj język, strefę czasową i hasło dla urządzenia.
3. Otwórz przeglądarkę w dowolnym systemie, który może nawiązać połączenie z urządzeniem, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację na pulpicie urządzenia, klikając skrót do aplikacji.
1. Zaakceptuj **postanowienia licencyjne** i przeczytaj informacje o innych firmach.
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
4. Jeśli konto użytkownika platformy Azure używane do rejestrowania ma odpowiednie [uprawnienia](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) do zasobów platformy Azure utworzonych podczas generowania klucza, Rejestracja urządzenia zostanie zainicjowana.
1. Po pomyślnym zarejestrowaniu urządzenia można wyświetlić szczegóły rejestracji, klikając pozycję **Wyświetl szczegóły**.

### <a name="delegate-credentials-for-smb-vhds"></a>Delegowanie poświadczeń dla wirtualnych dysków twardych SMB

Jeśli używasz dysków VHD w systemie technologii, musisz włączyć delegowanie poświadczeń z urządzenia do hostów funkcji Hyper-V. W tym celu z urządzenia:

1. Na urządzeniu Uruchom to polecenie. HyperVHost1/HyperVHost2 są przykładowymi nazwami hostów.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Alternatywnie należy to zrobić w Edytor lokalnych zasad grupy na urządzeniu:
    - W obszarze Konfiguracja komputera **zasad komputera lokalnego**  >  kliknij pozycję **Szablony administracyjne**  >    >  **delegowania poświadczeń** systemowych.
    - Kliknij dwukrotnie pozycję **Zezwól na delegowanie świeżych poświadczeń** i wybierz pozycję **włączone**.
    - W obszarze **Opcje** kliknij pozycję **Pokaż**, a następnie na liście Dodaj każdego hosta funkcji Hyper-V, który ma zostać odnajdowany, przy użyciu **usługi WSMAN/** jako prefiksu.
    - W obszarze  **Delegowanie poświadczeń** kliknij dwukrotnie pozycję **Zezwól na delegowanie świeżych poświadczeń z uwierzytelnianiem serwera tylko NTLM**. Ponownie Dodaj każdy host funkcji Hyper-V, który ma zostać odnalezienie do listy, przy użyciu **usługi WSMAN/** jako prefiksu.

## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Połącz się z urządzeniem z hostami lub klastrami funkcji Hyper-V i Rozpocznij odnajdywanie.

1. W **kroku 1: podaj poświadczenia hosta funkcji Hyper-V**, kliknij pozycję **Dodaj poświadczenia** , aby określić przyjazną nazwę dla poświadczeń, Dodaj **nazwę użytkownika** i **hasło** dla hosta lub klastra funkcji Hyper-v, którego urządzenie będzie używało do odnajdywania serwerów. Kliknij pozycję **Zapisz**.
1. Jeśli chcesz dodać jednocześnie wiele poświadczeń, kliknij pozycję **Dodaj więcej** , aby zapisać i dodać więcej poświadczeń. Do odnajdywania serwerów w funkcji Hyper-V są obsługiwane wiele poświadczeń.
1. W **kroku 2: Podaj szczegóły dotyczące hosta lub klastra funkcji Hyper-v**, kliknij pozycję **Dodaj źródło odnajdowania** , aby określić hosta funkcji Hyper-v/ **adres IP/FQDN** klastra i przyjazną nazwę dla poświadczeń do nawiązania połączenia z hostem/klastrem.
1. Możesz **dodać pojedynczy element** naraz lub **dodać wiele elementów** w jednym miejscu. Istnieje również możliwość zapewnienia szczegółowych informacji o hoście lub klastrze funkcji Hyper-V za poorednictwem **importowania woluminu CSV**.

    ![Opcje dodawania źródła odnajdywania](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - W przypadku wybrania opcji **Dodaj pojedynczy element** należy określić przyjazną nazwę dla poświadczeń i hosta funkcji Hyper-V/ **adresu IP klastra/nazwy FQDN** , a następnie kliknąć przycisk **Zapisz**.
    - W przypadku wybrania opcji **Dodaj wiele elementów** _(wybrane domyślnie)_ można dodać wiele rekordów jednocześnie, określając w polu tekstowym opcję Host/klaster **/nazwa FQDN** klastra funkcji Hyper-V z przyjazną nazwą. Sprawdź, czy dodano * * dodane rekordy, i kliknij przycisk **Zapisz**.
    - W przypadku wybrania opcji **Importuj woluminy CSV** można pobrać plik szablonu CSV, wypełnić go hostem/ **adresem IP klastra/nazwą FQDN** i przyjazną nazwą dla poświadczeń. Następnie zaimportuj plik do urządzenia, **Sprawdź** rekordy w pliku i kliknij przycisk **Zapisz**.

1. Po kliknięciu przycisku Zapisz Urządzenie spróbuje sprawdzić poprawność połączenia z hostami/klastrami funkcji Hyper-V, które zostały dodane, i wyświetlić **stan sprawdzania poprawności** w tabeli dla każdego hosta/klastra.
    - Aby sprawdzić poprawność hostów/klastrów, można wyświetlić więcej szczegółów, klikając ich adres IP/nazwę FQDN.
    - Jeśli walidacja nie powiedzie się dla hosta, przejrzyj błąd, klikając opcję **Walidacja nie powiodła się** w kolumnie Stan tabeli. Usuń problem i ponownie sprawdź poprawność.
    - Aby usunąć hosty lub klastry, kliknij pozycję **Usuń**.
    - Nie można usunąć określonego hosta z klastra. Można usunąć tylko cały klaster.
    - Można dodać klaster, nawet jeśli występują problemy z konkretnymi hostami w klastrze.
1. Możesz ponownie **sprawdzić poprawność** łączności z hostami/klastrami przed rozpoczęciem odnajdywania.
1. Kliknij przycisk **Rozpocznij odnajdywanie**, aby uruchomić Odnajdywanie serwera od pomyślnie zweryfikowanych hostów/klastrów. Po pomyślnym zainicjowaniu odnajdywania można sprawdzić stan odnajdywania dla każdego hosta/klastra w tabeli.

Spowoduje to uruchomienie odnajdywania. Aby metadane odnalezionych serwerów były wyświetlane w Azure Portal, zajmie około 2 minut na każdy host.

## <a name="verify-servers-in-the-portal"></a>Weryfikowanie serwerów w portalu

Po zakończeniu odnajdywania możesz sprawdzić, czy serwery są wyświetlane w portalu.

1. Otwórz pulpit nawigacyjny usługi Azure Migrate.
2. W **Azure Migrate — Windows, Linux i SQL Server**  >  **Azure Migrate: Strona odnajdywania i oceny** kliknij ikonę, która wyświetla liczbę **odnalezionych serwerów**.

## <a name="next-steps"></a>Następne kroki

Wypróbuj [ocenę funkcji Hyper-V](tutorial-assess-hyper-v.md) za pomocą Azure Migrate: odnajdywania i oceny.