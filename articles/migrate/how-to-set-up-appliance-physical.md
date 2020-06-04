---
title: Skonfiguruj urządzenie Azure Migrate dla serwerów fizycznych
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate na potrzeby oceny serwera fizycznego.
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: 6d9cc071ad5d81a09a14b12fe2acdf564c2ea6c8
ms.sourcegitcommit: 79508e58c1f5c58554378497150ffd757d183f30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84331784"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Konfigurowanie urządzenia dla serwerów fizycznych

W tym artykule opisano sposób konfigurowania urządzenia Azure Migrate w przypadku oceniania serwerów fizycznych za pomocą narzędzia do oceny serwera Azure Migrate:.

Urządzenie Azure Migrate jest lekkim urządzeniem używanym do oceny Azure Migrate Server, aby wykonać następujące czynności:

- Odnajdywanie serwerów lokalnych.
- Wysyłanie metadanych i danych wydajności dla odnalezionych serwerów w celu Azure Migrate oceny serwera.

[Dowiedz się więcej](migrate-appliance.md) o urządzeniu Azure Migrate.


## <a name="appliance-deployment-steps"></a>Kroki wdrażania urządzenia

Aby skonfigurować urządzenie:
- Pobierz spakowany plik ze skryptem Instalatora Azure Migrate z Azure Portal.
- Wyodrębnij zawartość z pliku spakowanego. Uruchom konsolę programu PowerShell z uprawnieniami administracyjnymi.
- Wykonaj skrypt programu PowerShell, aby uruchomić aplikację sieci Web urządzenia.
- Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate.

## <a name="download-the-installer-script"></a>Pobierz skrypt Instalatora

Pobierz spakowany plik dla urządzenia.

1. W obszarze serwery **celów migracji**  >  **Servers**  >  **Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2. W obszarze **odnajdywanie**maszyn  >  **są zwirtualizowane maszyny?** kliknij pozycję **niezwirtualizowane/inne**.
3. Kliknij pozycję **Pobierz** , aby pobrać plik zip.

    ![Pobierz maszynę wirtualną](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla pliku spakowanego:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykładowe użycie chmury publicznej:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Przykładowe użycie w chmurze dla instytucji rządowych:```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Sprawdź najnowszą wersję urządzenia i wartości skrótu:
 
    - W przypadku chmury publicznej:

        **Scenariusz** | **Przesłać*** | **Wartość skrótu**
        --- | --- | ---
        Fizyczne (63,1 MB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0

    - Dla Azure Government:

        **Scenariusz** | **Przesłać*** | **Wartość skrótu**
        --- | --- | ---
        Fizyczne (63,1 MB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1


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

1. Wyodrębnij spakowany plik do folderu na serwerze, który będzie hostować urządzenie.  Upewnij się, że skrypt nie jest uruchamiany na komputerze na istniejącym urządzeniu Azure Migrate.
2. Uruchom program PowerShell na powyższym serwerze z uprawnieniami administracyjnymi (z podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell do folderu, w którym zawartość została wyodrębniona z pobranego pliku spakowanego.
4. Uruchom skrypt o nazwie **AzureMigrateInstaller. ps1** , uruchamiając następujące polecenie:

    - W przypadku chmury publicznej:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Dla Azure Government:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Po pomyślnym zakończeniu działania skryptu zostanie uruchomiona aplikacja sieci Web urządzenia.

Jeśli występują problemy, możesz uzyskać dostęp do dzienników skryptów w witrynie C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log w celu rozwiązywania problemów.



### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może połączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i dla [instytucji rządowych](migrate-appliance.md#government-cloud-urls) .

## <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

1. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną wirtualną, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację z poziomu pulpitu, klikając skrót do aplikacji.
2. W aplikacji internetowej > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
    - **Licencja**: zaakceptuj postanowienia licencyjne i przeczytaj informacje o innych firmach.
    - **Łączność**: aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
        - Kliknij pozycję **Ustawienia serwera proxy**i określ adres serwera proxy i port nasłuchujący w formularzu http://ProxyIPAddress lub http://ProxyFQDN .
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Synchronizacja czasu**: godzina została zweryfikowana. Czas na urządzeniu powinien być zsynchronizowany z czasem Internetu, aby funkcja odnajdywania maszyn wirtualnych działała prawidłowo.
    - **Instalowanie aktualizacji**: ocena serwera Azure Migrate sprawdza, czy na urządzeniu zainstalowano najnowsze aktualizacje.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Kliknij przycisk **Zaloguj**. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
2. Na nowej karcie Zaloguj się przy użyciu poświadczeń platformy Azure.
    - Zaloguj się przy użyciu nazwy użytkownika i hasła.
    - Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu Wróć do aplikacji sieci Web.
4. Wybierz subskrypcję, w której został utworzony projekt Azure Migrate. Następnie wybierz projekt.
5. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
6. Kliknij pozycję **Zarejestruj**.


## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Nawiąż połączenie z urządzeniem z serwerami fizycznymi, a następnie Uruchom odnajdywanie.

1. Kliknij pozycję **Dodaj poświadczenia** , aby określić poświadczenia konta, które będą używane przez urządzenie do odnajdywania serwerów.  
2. Określ **system operacyjny**, przyjazną nazwę dla poświadczeń oraz nazwę użytkownika i hasło. Następnie kliknij przycisk **Dodaj**.
Można dodać jeden zestaw poświadczeń dla serwerów z systemami Windows i Linux.
4. Kliknij przycisk **Dodaj serwer**i określ szczegóły serwera — nazwa FQDN/adres IP i przyjazna nazwa poświadczenia (jeden wpis na wiersz), aby połączyć się z serwerem.
3. Kliknij pozycję **Validate** (Waliduj). Po sprawdzeniu poprawności zostanie wyświetlona lista serwerów, które mogą zostać odnalezione.
    - Jeśli walidacja nie powiedzie się dla serwera, przejrzyj błąd, umieszczając kursor nad ikoną w kolumnie **stan** . Usuń problemy i ponownie sprawdź poprawność.
    - Aby usunąć serwer, wybierz pozycję > **Usuń**.
4. Po sprawdzeniu poprawności kliknij przycisk **Zapisz i Rozpocznij odnajdywanie** , aby rozpocząć proces odnajdywania.

Spowoduje to uruchomienie odnajdywania. Metadane wykrytych maszyn wirtualnych mogą pojawić się w Azure Portal około 15 minut.

## <a name="verify-servers-in-the-portal"></a>Weryfikowanie serwerów w portalu

Po zakończeniu odnajdywania możesz sprawdzić, czy serwery są wyświetlane w portalu.

1. Otwórz pulpit nawigacyjny Azure Migrate.
2. W **Azure Migrate serwery**  >  **Azure Migrate: Strona Ocena serwera** kliknij ikonę, która wyświetla liczbę **odnalezionych serwerów**.


## <a name="next-steps"></a>Następne kroki

Wypróbuj [ocenę serwerów fizycznych](tutorial-assess-physical.md) za pomocą oceny Azure Migrate Server.
