---
title: Zarządzanie pojedynczymi czujnikami
description: Dowiedz się, jak zarządzać indywidualnymi czujnikami, w tym zarządzaniem plikami aktywacji, wykonywaniem kopii zapasowych i aktualizowaniem czujników autonomicznych.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/22/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: daaca1d7c6cf43f69241e6a23f8bdfaf4015ba23
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976949"
---
# <a name="manage-individual-sensors"></a>Zarządzanie pojedynczymi czujnikami

W tym artykule opisano sposób zarządzania indywidualnymi czujnikami. Zadania obejmują zarządzanie plikami aktywacji, wykonywanie kopii zapasowych i aktualizowanie czujników autonomicznych.

W lokalnej konsoli zarządzania można także wykonywać pewne zadania zarządzania czujnikami, w których można jednocześnie zarządzać wieloma czujnikami.

Azure Portal służy do dołączania i rejestracji czujnika.

## <a name="manage-sensor-activation-files"></a>Zarządzaj plikami aktywacji czujników

Czujnik został dołączony do usługi Azure Defender dla IoT z Azure Portal. Każdy czujnik został dołączony do czujnika połączonego lokalnie lub czujnika połączonego z chmurą.

Unikatowy plik aktywacji jest przekazywany do każdego wdrażanego czujnika. Aby uzyskać więcej informacji o tym, kiedy i jak używać nowego pliku, zobacz [przekazywanie nowych plików aktywacji](#upload-new-activation-files). Jeśli nie można przekazać pliku, zobacz [Rozwiązywanie problemów z przekazywaniem pliku aktywacji](#troubleshoot-activation-file-upload).

### <a name="about-activation-files-for-locally-connected-sensors"></a>Informacje o plikach aktywacji dla połączonych lokalnie czujników

Czujniki połączone lokalnie są skojarzone z subskrypcją platformy Azure. Plik aktywacji dla czujników połączonych lokalnie zawiera datę wygaśnięcia. Jeden miesiąc przed tą datą w górnej części konsoli czujnika pojawia się komunikat ostrzegawczy. Ostrzeżenie pozostanie do momentu zaktualizowania pliku aktywacji.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="Zrzut ekranu ustawień systemowych.":::

Można nadal korzystać z funkcji Defender for IoT, nawet jeśli plik aktywacji wygasł. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>Informacje o plikach aktywacji dla czujników połączonych z chmurą

Czujniki połączone z chmurą są skojarzone z usługą Defender for IoT Hub. Te czujniki nie są ograniczone przez okresy czasu dla pliku aktywacji. Plik aktywacji dla czujników połączonych z chmurą służy do zapewnienia połączenia z usługą Defender dla usługi IoT Hub.

### <a name="upload-new-activation-files"></a>Przekaż nowe pliki aktywacji

Może być konieczne przekazanie nowego pliku aktywacji dla czujnika dołączanego, gdy:

- Plik aktywacji wygasa na czujniku podłączonym lokalnie. 

- Chcesz pracy w innym trybie zarządzania czujnikami. 

- Chcesz przypisać nowe usługi Defender dla usługi IoT Hub do czujnika połączonego z chmurą.

Aby dodać nowy plik aktywacji:

1. Przejdź do strony **Zarządzanie czujnikami** .

2. Wybierz czujnik, dla którego chcesz przekazać nowy plik aktywacji.

3. Usuń go.

4. Ponownie Dołącz czujnik **z strony** dołączania w trybie nowy lub z nową usługą Defender dla usługi IoT Hub.

5. Pobierz plik aktywacji ze strony **pobierania pliku aktywacji** .

6. Zapisz plik.

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Pobierz plik aktywacji z usługi Defender dla usługi IoT Hub.":::

7. Zaloguj się do konsoli czujnika usługi Defender for IoT.

8. W konsoli czujnika wybierz opcję   >  **ponowna aktywacja** ustawień systemowych.

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="Wybór ponownej aktywacji na ekranie Ustawienia systemu.":::

9. Wybierz pozycję **Przekaż** i wybierz zapisany plik.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="Przekaż zapisany plik.":::

10. Wybierz pozycję **Aktywuj**.

### <a name="troubleshoot-activation-file-upload"></a>Rozwiązywanie problemów z przekazywaniem pliku aktywacji

Jeśli nie można przekazać pliku aktywacji, zostanie wyświetlony komunikat o błędzie. Mogły wystąpić następujące zdarzenia:

- **W przypadku czujników połączonych lokalnie**: plik aktywacji jest nieprawidłowy. Jeśli plik jest nieprawidłowy, przejdź do portalu Defender for IoT. Na stronie **Zarządzanie czujnikami** wybierz czujnik z nieprawidłowym plikiem i Pobierz nowy plik aktywacji.

- **W przypadku czujników połączonych z chmurą**: czujnik nie może połączyć się z Internetem. Sprawdź konfigurację sieci czujnika. Jeśli czujnik musi nawiązać połączenie za pomocą serwera proxy sieci Web w celu uzyskania dostępu do Internetu, sprawdź, czy serwer proxy jest prawidłowo skonfigurowany na ekranie **konfiguracji sieci czujnika** . Sprawdź, czy \* w zaporze i/lub serwerze proxy jest dozwolony Azure-Devices.NET:443. Jeśli symbole wieloznaczne nie są obsługiwane lub chcesz mieć większą kontrolę, nazwa FQDN określonej usługi Defender for IoT Hub powinna być otwarta w zaporze i/lub serwerze proxy. Aby uzyskać szczegółowe informacje, zobacz [punkty końcowe IoT Hub referencyjnych](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints).  

- **W przypadku czujników połączonych z chmurą**: plik aktywacji jest prawidłowy, ale usługa Defender dla usługi IoT odrzuciła ją. Jeśli nie możesz rozwiązać tego problemu, możesz pobrać kolejną aktywację ze strony **zarządzania czujnikami** w portalu Defender for IoT. Jeśli to nie zadziała, skontaktuj się z pomoc techniczna firmy Microsoft.

## <a name="manage-certificates"></a>Zarządzanie certyfikatami

Po zainstalowaniu czujnika zostanie wygenerowany lokalny certyfikat z podpisem własnym, który będzie używany do uzyskiwania dostępu do aplikacji sieci Web czujnika. Po pierwszym zalogowaniu się do czujnika użytkownicy będą monitowani o podanie certyfikatu SSL/TLS.  Aby uzyskać więcej informacji na temat konfiguracji po raz pierwszy, zobacz [Logowanie i aktywowanie czujnika](how-to-activate-and-set-up-your-sensor.md).

Ten artykuł zawiera informacje na temat aktualizowania certyfikatów, pracy z poleceniami interfejsu CLI certyfikatu oraz obsługiwanych certyfikatów i parametrów certyfikatów.

### <a name="about-certificates"></a>Informacje o certyfikatach

Usługa Azure Defender for IoT używa certyfikatów SSL/TLS, aby:

1. Zapoznaj się z określonymi wymaganiami dotyczącymi certyfikatów i szyfrowania, przekazując certyfikat podpisany przez urząd certyfikacji.

1. Zezwalaj na sprawdzanie poprawności między konsolą zarządzania i połączonymi czujnikami oraz między konsolą zarządzania i konsolą zarządzania o wysokiej dostępności. Walidacje są oceniane względem listy odwołania certyfikatów i daty wygaśnięcia certyfikatu. **Jeśli walidacja nie powiedzie się, komunikacja między konsolą zarządzania i czujnikiem zostanie zatrzymana, a w konsoli zostanie wyświetlony błąd sprawdzania poprawności. Ta opcja jest domyślnie włączona po instalacji.**

 Reguły przekazywania innych firm, na przykład informacje o alercie wysyłane do dziennika systemowego, Splunk lub usługi ServiceNow; lub komunikacja z Active Directory nie są weryfikowane.

### <a name="update-certificates"></a>Aktualizowanie certyfikatów

Administratorzy czujnika mogą aktualizować certyfikaty.

Aby zaktualizować certyfikat:  

1. Wybierz pozycję **Ustawienia systemowe**.
1. Wybierz opcję **Certyfikaty SSL/TLS.**
1. Usuń lub Edytuj certyfikat i Dodaj nowy.
    - Dodaj nazwę certyfikatu.
    - Przekaż plik CRT i plik klucza, a następnie wprowadź hasło.
    - Przekaż plik PEM, jeśli jest to wymagane.

Aby zmienić ustawienie walidacji:

1. Włącza lub wyłącza przełącznik **Włącz weryfikację certyfikatu** .
1. Wybierz pozycję **Zapisz**.

Jeśli opcja jest włączona, a Walidacja nie powiedzie się, komunikacja między konsolą zarządzania a czujnikiem zostanie zatrzymana, a w konsoli zostanie wyświetlony błąd sprawdzania poprawności.

### <a name="certificate-support"></a>Obsługa certyfikatów

Obsługiwane są następujące certyfikaty:

- Infrastruktura kluczy prywatnych/przedsiębiorstwa (prywatna infrastruktura PKI) 
- Infrastruktura kluczy publicznych (publiczna infrastruktura PKI) 
- Wygenerowane lokalnie na urządzeniu (lokalnie z podpisem własnym). **Korzystanie z certyfikatów z podpisem własnym nie jest zalecane.** To połączenie jest *niezabezpieczone* i powinno być używane tylko w środowiskach testowych. Nie można zweryfikować właściciela certyfikatu, a zabezpieczenia systemu nie mogą być obsługiwane. Certyfikaty z podpisem własnym nigdy nie powinny być używane dla sieci produkcyjnych.  

Obsługiwane są następujące parametry. CRT certyfikat

- Podstawowy plik certyfikatu dla nazwy domeny
- Algorytm podpisu = SHA256RSA
- Algorytm wyznaczania wartości skrótu podpisu = SHA256
- Ważny od = prawidłowa data przeszła
- Ważny do = prawidłowa data przyszła
- Klucz publiczny = RSA 2048bits (minimum) lub 4096bits
- Punkt dystrybucji listy CRL = adres URL pliku listy CRL
- Nazwa podmiotu (CN = URL) może być certyfikatem wieloznacznym, np. example.contoso.com lub  *. contoso.com**
- Podmiot (C) ountry = zdefiniowany, np. US
- Subject (OU) — jednostka organizacyjna = zdefiniowana, np. contoso Labs
- Podmiot (O) rganizacji = zdefiniowany, np. contoso Inc.

Plik klucza

- Plik klucza wygenerowany podczas tworzenia CSR
- RSA 2048bits (minimum) lub 4096bits

Łańcuch certyfikatów

- Pośredni plik certyfikatu (jeśli istnieje), który został dostarczony przez urząd certyfikacji
- Certyfikat urzędu certyfikacji, który wystawił certyfikat serwera, powinien znajdować się najpierw w pliku, a po nim wszystkie inne osoby w katalogu głównym. 
- Może zawierać atrybuty zbioru.

Danym

- Obsługiwane są 1 klucze
- Instalator podczas importowania certyfikatu

Certyfikaty z innymi parametrami mogą działać, ale nie mogą być obsługiwane przez firmę Microsoft.

#### <a name="encryption-key-artifacts"></a>Artefakty klucza szyfrowania

**pem — plik kontenera certyfikatów**

Nazwa pochodzi z Privacy Enhanced Mail (PEM), metody historycznej do bezpiecznej poczty e-mail, ale formatu kontenera, w którym wykorzystano, i jest to tłumaczenie Base64 kluczy ASN. 1.  

Zdefiniowane w dokumentach RFC 1421 do 1424: format kontenera, który może zawierać tylko certyfikat publiczny (na przykład z instalacją Apache, i pliki certyfikatów urzędu certyfikacji/etc/SSL/certs) lub może zawierać cały łańcuch certyfikatów, w tym klucz publiczny, klucz prywatny i certyfikaty główne.  

Może również zakodować CSR, ponieważ format PKCS10 można przetłumaczyć na PEM.

**. Certificate. cer. CRT — plik kontenera certyfikatów**

Plik o formacie PEM (lub rzadko. der) o innym rozszerzeniu. Jest on rozpoznawany przez Eksploratora Windows jako certyfikat. Plik PEM nie jest rozpoznawany przez Eksploratora Windows.

**klucz — plik klucza prywatnego**

Plik klucza ma taki sam format jak plik PEM, ale ma inne rozszerzenie.
##### <a name="use-cli-commands-to-deploy-certificates"></a>Wdrażanie certyfikatów przy użyciu poleceń interfejsu wiersza polecenia

Użyj polecenia *cyberx-xsense-Certificate-import* w celu zaimportowania certyfikatów. Aby użyć tego narzędzia, należy przekazać pliki certyfikatów do urządzenia (za pomocą narzędzi, takich jak WinSCP lub Wget).

Polecenie obsługuje następujące flagi wejściowe:

-h Pokaż składnię pomocy wiersza polecenia

--Ścieżka CRT do pliku certyfikatu (rozszerzenie CRT)

--Key *. plik klucza, Długość klucza powinna wynosić co najmniej 2048 bitów.

--Ścieżka łańcucha do pliku łańcucha certyfikatów (opcjonalnie)

--Przekaż hasło używane do szyfrowania certyfikatu (opcjonalnie)

--hasło — ustawia domyślnie = false, nieużywane. Ustaw wartość TRUE, aby użyć poprzedniego hasła dostarczonego z poprzednim certyfikatem (opcjonalnie)

W przypadku korzystania z interfejsu wiersza polecenia:

- Sprawdź, czy pliki certyfikatów są odczytywane na urządzeniu.

- Sprawdź, czy nazwa domeny i adres IP w certyfikacie są zgodne z konfiguracją zaplanowaną przez dział IT.


## <a name="connect-a-sensor-to-the-management-console"></a>Łączenie czujnika z konsolą zarządzania

W tej sekcji opisano sposób zapewnienia połączenia między czujnikiem i lokalną konsolą zarządzania. Należy to zrobić, jeśli pracujesz w sieci gapped i chcesz wysyłać informacje o zasobach i alertach do konsoli zarządzania z czujnika. To połączenie umożliwia również konsoli zarządzania wypychanie ustawień systemowych do czujnika i wykonywanie innych zadań zarządzania na czujniku.

Aby nawiązać połączenie:

1. Zaloguj się do lokalnej konsoli zarządzania.

2. Wybierz pozycję **Ustawienia systemowe**.

3. W sekcji **Konfiguracja czujnika — parametry połączenia** Skopiuj automatycznie wygenerowane parametry połączenia.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="Skopiuj parametry połączenia z tego ekranu."::: 

4. Zaloguj się do konsoli czujnika.

5. W okienku po lewej stronie wybierz pozycję **Ustawienia systemowe**.

6. Wybierz pozycję **połączenie konsoli zarządzania**.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe połączenie konsoli zarządzania.":::

7. Wklej parametry połączenia w polu **Parametry połączenia** i wybierz pozycję **Połącz**.

8. W lokalnej konsoli zarządzania w oknie **Zarządzanie lokacją** Przypisz czujnik do strefy.

## <a name="change-the-name-of-a-sensor"></a>Zmień nazwę czujnika

Można zmienić nazwę konsoli czujnika. Nowa nazwa zostanie wyświetlona w przeglądarce sieci Web w konsoli programu, w różnych oknach konsoli i w dziennikach rozwiązywania problemów.

Proces zmieniania nazw czujników różni się w zależności od połączonych lokalnie czujników i czujników połączonych z chmurą. Nazwa domyślna to **sensor**.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>Zmień nazwę czujnika połączonego lokalnie

Aby zmienić nazwę:

1. W dolnej części okienka po lewej stronie wybierz bieżącą etykietę czujnika.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="Zrzut ekranu pokazujący etykietę czujnika.":::

1. W oknie dialogowym **Edytowanie nazwy czujnika** wprowadź nazwę.

1. Wybierz pozycję **Zapisz**. Nowa nazwa zostanie zastosowana.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>Zmień nazwę czujnika połączonego z chmurą

Jeśli Czujnik został zarejestrowany jako czujnik połączony z chmurą, nazwa czujnika jest definiowana przez nazwę przypisaną podczas rejestracji. Nazwa jest uwzględniona w pliku aktywacji, który został przekazany podczas pierwszego logowania. Aby zmienić nazwę czujnika, należy przekazać nowy plik aktywacji.

Aby zmienić nazwę:

1. W portalu usługi Azure Defender dla IoT przejdź do strony **zarządzanie czujnikiem** .

1. Usuń czujnik z okna **Zarządzanie czujnikami** .

1. Zarejestruj się przy użyciu nowej nazwy.

1. Pobierz i nowy plik aktywacji.

1. Zaloguj się do czujnika i przekaż nowy plik aktywacji.

## <a name="update-the-sensor-network-configuration"></a>Aktualizowanie konfiguracji sieci czujnika

Konfiguracja sieci czujnika została zdefiniowana podczas instalacji czujnika. Można zmienić parametry konfiguracji. Można również skonfigurować konfigurację serwera proxy.

Jeśli utworzysz nowy adres IP, może być konieczne ponowne zalogowanie.

Aby zmienić konfigurację:

1. W menu po stronie wybierz pozycję **Ustawienia systemowe**.

2. W oknie **Ustawienia systemu** wybierz pozycję **Sieć**.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Skonfiguruj ustawienia sieci.":::

3. Ustaw parametry w następujący sposób:

    | Parametr | Opis |
    |--|--|
    | Adres IP | Adres IP czujnika |
    | Maska podsieci | Adres maski |
    | Brama domyślna | Domyślny adres bramy |
    | DNS | Adres serwera DNS |
    | Hostname (Nazwa hosta) | Nazwa hosta czujnika |
    | Serwer proxy | Nazwa hosta i portu serwera proxy |

4. Wybierz pozycję **Zapisz**.

## <a name="synchronize-time-zones-on-the-sensor"></a>Synchronizuj strefy czasowe na czujniku

Można skonfigurować czas i region czujnika, aby wszyscy użytkownicy widzieli ten sam czas i region.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="Skonfiguruj czas i region.":::

| Parametr | Opis |
|--|--|
| Strefa czasowa | Definicja strefy czasowej dla:<br />-Alerty<br />— Elementy widget trendów i statystyk<br />— Raporty wyszukiwania danych<br />   -Raporty oceny ryzyka<br />— Wektory ataków |
| Format daty | Wybierz jedną z następujących opcji formatu:<br />-DD/MM/RRRR HH: mm: SS<br />-MM/DD/RRRR HH: mm: SS<br />-RRRR/MM/DD HH: mm: SS |
| Data i godzina | Wyświetla bieżącą datę i godzinę lokalną w wybranym formacie.<br />Jeśli na przykład rzeczywista lokalizacja to America i Nowy Jork, ale strefa czasowa jest ustawiona na Europa i Berlin, czas jest wyświetlany zgodnie z czasem lokalnym w Berlinie. |

Aby skonfigurować czas czujnika:

1. W menu po stronie wybierz pozycję **Ustawienia systemowe**.

2. W oknie **Ustawienia systemu** wybierz pozycję **czas & regionalne**.

3. Ustaw parametry i wybierz pozycję **Zapisz**.

## <a name="set-up-backup-and-restore-files"></a>Konfigurowanie plików kopii zapasowej i przywracania

Kopia zapasowa systemu jest przeprowadzana automatycznie o godzinie 3:00 dziennie. Dane są zapisywane na innym dysku w czujniku. Domyślna lokalizacja to `/var/cyberx/backups`.

Ten plik można automatycznie przenieść do sieci wewnętrznej.

> [!NOTE]
> - Procedurę tworzenia kopii zapasowej i przywracania można wykonać tylko w tych samych wersjach.
> - W niektórych architekturach kopia zapasowa jest wyłączona. Możesz włączyć ją w `/var/cyberx/properties/backup.properties` pliku.

W przypadku kontrolowania czujnika przy użyciu lokalnej konsoli zarządzania można użyć harmonogramu tworzenia kopii zapasowych czujnika, aby zebrać te kopie zapasowe i zapisać je w konsoli zarządzania lub na zewnętrznym serwerze kopii zapasowych.

**Co to jest kopia zapasowa:** Konfiguracje i dane.

**Nie wykonano kopii zapasowej:** PCAP pliki i dzienniki. Można ręcznie utworzyć kopię zapasową i przywrócić PCAPs i dzienniki.

Pliki kopii zapasowej czujnika są automatycznie nazwane przy użyciu następującego formatu: `<sensor name>-backup-version-<version>-<date>.tar` . Może to być na przykład `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`.

Aby skonfigurować kopię zapasową:

- Zaloguj się do konta administracyjnego i wprowadź `$ sudo cyberx-xsense-system-backup` .

Aby przywrócić najnowszy plik kopii zapasowej:

- Zaloguj się do konta administracyjnego i wprowadź `$ sudo cyberx-xsense-system-restore` .

Aby zapisać kopię zapasową na zewnętrznym serwerze SMB:

1. Utwórz folder udostępniony na zewnętrznym serwerze SMB.

    Pobierz ścieżkę do folderu, nazwę użytkownika i hasło wymagane do uzyskania dostępu do serwera SMB.

2. W czujniku Utwórz katalog kopii zapasowych:

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. Edytuj `fstab` : 

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. Edytuj i Utwórz poświadczenia do udostępniania dla serwera SMB:

    `sudo nano /etc/samba/user` 

5. Dodaj:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. Zainstaluj katalog:

    `sudo mount -a`

7. Skonfiguruj katalog kopii zapasowej w folderze udostępnionym w usłudze Defender dla czujnika IoT:  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>Przywracanie czujników

Można przywrócić kopie zapasowe z konsoli czujnika i przy użyciu interfejsu wiersza polecenia.

**Aby przywrócić z konsoli programu:**

- Wybierz pozycję **Przywróć obraz** z okna **Ustawienia systemu** czujnika.

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="Przywróć obraz, wybierając przycisk.":::

Konsola wyświetli błędy przywracania.

**Aby przywrócić za pomocą interfejsu wiersza polecenia:**

- Zaloguj się do konta administracyjnego i wprowadź `$ sudo cyberx-management-system-restore` .

## <a name="update-a-standalone-sensor-version"></a>Aktualizowanie wersji czujnika autonomicznego

Poniższa procedura opisuje sposób aktualizowania czujnika autonomicznego za pomocą konsoli czujnika. Proces aktualizacji trwa około 30 minut.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/).

2. Przejdź do usługi Defender for IoT.

3. Przejdź do strony **aktualizacje** .

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Zrzut ekranu strony aktualizacje usługi Defender for IoT.":::

4. Wybierz pozycję **Pobierz** z sekcji **czujniki** i Zapisz plik.

5. Na pasku bocznym konsoli czujnika wybierz pozycję **Ustawienia systemowe**.

6. W okienku **uaktualnienie wersji** wybierz pozycję **Uaktualnij**.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="Zrzut ekranu okienka uaktualniania.":::

7. Wybierz plik pobrany ze strony **aktualizacje** usługi Defender for IoT.

8. Rozpocznie się proces aktualizacji, w trakcie którego system zostanie dwukrotnie uruchomiony ponownie. Po pierwszym ponownym uruchomieniu (przed ukończeniem procesu aktualizacji) system zostanie otwarty przy użyciu okna logowania. Po zalogowaniu wersja uaktualnienia zostanie wyświetlona w lewym dolnym rogu paska bocznego.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="Zrzut ekranu przedstawiający wersję uaktualnienia, która pojawia się po zalogowaniu się.":::

## <a name="forward-sensor-failure-alerts"></a>Alerty błędów czujnika do przodu 

Możesz przesłać dalej alerty do stron trzecich, aby przedstawić szczegóły:

- Czujniki rozłączone

- Błędy zdalnej kopii zapasowej

Te informacje są wysyłane podczas tworzenia reguły przekazywania dla powiadomień systemowych.

> [!NOTE]
> Administratorzy mogą wysyłać powiadomienia systemowe.

Aby wysłać powiadomienia:

1. Zaloguj się do lokalnej konsoli zarządzania.
1. Wybierz pozycję **przesyłanie dalej** z menu po stronie.
1. Utwórz regułę przekazywania.
1. Wybierz opcję **powiadomienia systemu raportów**.

Aby uzyskać więcej informacji na temat reguł przekazywania, zobacz [przekazywanie informacji o alercie](how-to-forward-alert-information-to-partners.md).

## <a name="adjust-system-properties"></a>Dostosuj właściwości systemu

Właściwości systemu kontrolują różne operacje i ustawienia w czujniku. Edytowanie lub modyfikowanie ich może spowodować uszkodzenie działania konsoli czujnika.

Przed zmianą ustawień zapoznaj się z tematem [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/) .

Aby uzyskać dostęp do właściwości systemu:

1. Zaloguj się do lokalnej konsoli zarządzania lub czujnika.

2. Wybierz pozycję **Ustawienia systemowe**.

3. Wybierz pozycję **Właściwości systemu** z sekcji **Ogólne** .

## <a name="see-also"></a>Zobacz też

[Badania i pakiety analizy zagrożeń](how-to-work-with-threat-intelligence-packages.md)

[Zarządzanie czujnikami z poziomu konsoli zarządzania](how-to-manage-sensors-from-the-on-premises-management-console.md)
