---
title: Zarządzanie lokalną konsolą zarządzania
description: Dowiedz się więcej na temat opcji lokalnej konsoli zarządzania, takich jak tworzenie kopii zapasowych i przywracanie, Definiowanie nazwy hosta i Konfigurowanie serwera proxy do czujników.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: d76db6830839902a46aaf6515f816fdcc36d0df5
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523944"
---
# <a name="manage-the-on-premises-management-console"></a>Zarządzanie lokalną konsolą zarządzania

W tym artykule opisano opcje lokalnej konsoli zarządzania, takie jak tworzenie kopii zapasowych i przywracanie, pobieranie pliku aktywacji urządzeń z Komitetem, aktualizowanie certyfikatów i Konfigurowanie serwera proxy do czujników.

Możesz dołączyć lokalną konsolę zarządzania do Azure Portal.

## <a name="upload-an-activation-file"></a>Przekaż plik aktywacji

Po pierwszym zalogowaniu plik aktywacji dla lokalnej konsoli zarządzania zostanie pobrany. Ten plik zawiera zagregowane urządzenia zatwierdzone, które są zdefiniowane podczas procesu dołączania. Lista zawiera czujniki skojarzone z wieloma subskrypcjami.

Po początkowej aktywacji liczba monitorowanych urządzeń może przekroczyć liczbę zatwierdzonych urządzeń zdefiniowanych podczas dołączania. Może się to zdarzyć na przykład wtedy, gdy połączysz więcej czujników z konsolą zarządzania. W przypadku niezgodności między liczbą monitorowanych urządzeń i liczbą zatwierdzonych urządzeń w konsoli zarządzania zostanie wyświetlone ostrzeżenie. Jeśli wystąpi to zdarzenie, należy przekazać nowy plik aktywacji.

Aby przekazać plik aktywacji:

1. Przejdź do strony **cennika** usługi Azure Defender for IoT.
1. Wybierz pozycję **Pobierz plik aktywacji na karcie Konsola zarządzania** . Plik aktywacji zostanie pobrany.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Pobierz plik aktywacji.":::

1. Wybierz pozycję **Ustawienia systemowe** z konsoli zarządzania.
1. Wybierz pozycję **Aktywacja**.
1. Wybierz pozycję **Wybierz plik** i wybierz zapisany plik.

## <a name="manage-certificates"></a>Zarządzanie certyfikatami

Po zainstalowaniu lokalnej konsoli zarządzania zostanie wygenerowany lokalny certyfikat z podpisem własnym, który będzie używany do uzyskiwania dostępu do aplikacji sieci Web konsoli zarządzania. Gdy użytkownicy administrator logują się po raz pierwszy, są monitowani o podanie certyfikatu SSL/TLS. Aby uzyskać więcej informacji na temat konfiguracji pierwszego czasu, zobacz [Aktywacja i Konfigurowanie lokalnej konsoli zarządzania](how-to-activate-and-set-up-your-on-premises-management-console.md).

W poniższych sekcjach znajdują się informacje dotyczące aktualizowania certyfikatów, pracy z poleceniem interfejsu wiersza polecenia certyfikatu oraz obsługiwanych certyfikatów i parametrów certyfikatów.

### <a name="about-certificates"></a>Informacje o certyfikatach

Usługa Azure Defender for IoT używa certyfikatów SSL i TLS, aby:

- Zapoznaj się z określonymi wymaganiami dotyczącymi certyfikatów i szyfrowania, przekazując certyfikat podpisany przez urząd certyfikacji.

- Zezwalaj na sprawdzanie poprawności między konsolą zarządzania i połączonymi czujnikami oraz między konsolą zarządzania i konsolą zarządzania o wysokiej dostępności. Sprawdzanie poprawności jest oceniane względem listy odwołania certyfikatów i daty wygaśnięcia certyfikatu. *Jeśli walidacja nie powiedzie się, komunikacja między konsolą zarządzania i czujnikiem zostanie zatrzymana, a w konsoli zostanie wyświetlony komunikat o błędzie walidacji*. Ta opcja jest domyślnie włączona po instalacji.

Reguły przekazujące inne firmy nie są weryfikowane. Przykłady to informacje o alercie wysyłane do dziennika systemowego, Splunk lub usługi ServiceNow; i komunikacja z Active Directory.

#### <a name="ssl-certificates"></a>Certyfikaty SSL

Usługa Defender dla czujnika IoT oraz lokalna Konsola zarządzania używają protokołu SSL i certyfikatów TLS dla następujących funkcji: 

 - Bezpieczna komunikacja między użytkownikami i konsolą sieci Web urządzenia. 
 
 - Zabezpieczanie komunikacji z interfejsem API REST na czujniku i lokalnej konsoli zarządzania.
 
 - Bezpieczna komunikacja między czujnikami i lokalną konsolą zarządzania. 

Po zainstalowaniu urządzenie generuje lokalny certyfikat z podpisem własnym, aby umożliwić wstępny dostęp do konsoli sieci Web. Za pomocą narzędzia wiersza polecenia można zainstalować certyfikaty SSL przedsiębiorstwa i TLS [`cyberx-xsense-certificate-import`](#cli-commands) . 

 > [!NOTE]
 > W przypadku integracji i reguł przekazywania, gdy urządzenie jest klientem i inicjatorem sesji, używane są określone certyfikaty i nie są związane z certyfikatami systemu.  
 >
 >W takich przypadkach certyfikaty są zwykle odbierane z serwera lub używają szyfrowania asymetrycznego, w którym zostanie dostarczony określony certyfikat w celu skonfigurowania integracji. 

### <a name="update-certificates"></a>Aktualizowanie certyfikatów

Użytkownicy będący administratorami lokalnej konsoli zarządzania mogą aktualizować certyfikaty.

Aby zaktualizować certyfikat:  

1. Wybierz pozycję **Ustawienia systemowe**.

1. Wybierz opcję **Certyfikaty SSL/TLS**.
1. Usuń lub Edytuj certyfikat i Dodaj nowy.
   
   - Dodaj nazwę certyfikatu.
   
   - Przekaż plik CRT i plik klucza, a następnie wprowadź hasło.
   - W razie potrzeby Przekaż plik PEM.

Aby zmienić ustawienie walidacji:

1. Włącz lub wyłącz przełącznik **Włącz weryfikację certyfikatu** .

1. Wybierz pozycję **Zapisz**.

Jeśli opcja jest włączona, a Walidacja nie powiedzie się, komunikacja między konsolą zarządzania a czujnikiem zostanie zatrzymana, a w konsoli zostanie wyświetlony komunikat o błędzie walidacji.

### <a name="certificate-support"></a>Obsługa certyfikatów

Obsługiwane są następujące certyfikaty:

- Infrastruktura kluczy prywatnych i korporacyjnych (prywatna infrastruktura PKI)
 
- Infrastruktura kluczy publicznych (publiczna infrastruktura PKI) 

- Wygenerowane lokalnie na urządzeniu (lokalnie z podpisem własnym) 

  > [!IMPORTANT]
  > Nie zalecamy używania certyfikatów z podpisem własnym. Ten typ połączenia nie jest bezpieczny i powinien być używany tylko w środowiskach testowych. Od tego czasu nie można sprawdzić poprawności właściciela certyfikatu i nie można utrzymywać zabezpieczeń systemu, dlatego certyfikaty z podpisem własnym nigdy nie powinny być używane dla sieci produkcyjnych.

### <a name="supported-ssl-certificates"></a>Obsługiwane certyfikaty SSL 

Obsługiwane są następujące parametry: 

**CRT certyfikat**

- Podstawowy plik certyfikatu dla nazwy domeny

- Algorytm podpisu = SHA256RSA
- Algorytm wyznaczania wartości skrótu podpisu = SHA256
- Ważny od = prawidłowa data przeszła
- Ważny do = prawidłowa data przyszła
- Klucz publiczny = RSA 2048 bitów (minimum) lub 4096 bitów
- Punkt dystrybucji listy CRL = adres URL pliku listy CRL
- Nazwa podmiotu (CN = URL) może być certyfikatem z symbolem wieloznacznym; na przykład czujnik. contoso. <span> com lub *. contoso. <span> Dodatki
- Podmiot (C) ountry = zdefiniowany, na przykład US
- Podmiot (OU) — jednostka organizacyjna = zdefiniowana; Przykładowo firma Contoso Labs
- Podmiot (O) rganizacji = zdefiniowany; na przykład contoso Inc

**Plik klucza**

- Plik klucza wygenerowany podczas tworzenia CSR

- RSA 2048 bitów (minimum) lub 4096 bitów

 > [!Note]
 > Użycie klucza o długości 4096bits:
 > - Uzgadnianie protokołu SSL na początku każdego połączenia będzie wolniejsze.  
 > - Podczas uzgadniania występuje wzrost użycia procesora CPU. 

**Łańcuch certyfikatów**

- Pośredni plik certyfikatu (jeśli istnieje), który został dostarczony przez urząd certyfikacji.

- Certyfikat urzędu certyfikacji, który wystawił certyfikat serwera, powinien znajdować się najpierw w pliku, a po nim wszystkie inne osoby w katalogu głównym. 
- Łańcuch może zawierać atrybuty zbioru.

**Danym**

- Jeden klucz jest obsługiwany.

- Konfiguruje się podczas importowania certyfikatu.

Certyfikaty z innymi parametrami mogą funkcjonować, ale firma Microsoft nie obsługuje ich.

#### <a name="encryption-key-artifacts"></a>Artefakty klucza szyfrowania

**PEM: plik kontenera certyfikatów**

Pliki Privacy Enhanced Mail (PEM) były ogólnym typem pliku używanym do zabezpieczania wiadomości e-mail. Obecnie pliki PEM są używane z certyfikatami i używają kluczy x509 ASN1.  

Plik kontenera jest zdefiniowany w RFC 1421 do 1424, format kontenera, który może zawierać tylko certyfikat publiczny. Na przykład program Apache instaluje certyfikat urzędu certyfikacji, pliki itp., SSL lub certyfikaty. Może to obejmować cały łańcuch certyfikatów, w tym klucz publiczny, klucz prywatny i certyfikaty główne.  

Może również zakodować CSR jako format PKCS10, który można przetłumaczyć na PEM.

**. Certificate. cer. CRT: plik kontenera certyfikatów**

`.pem` `.der` Plik lub sformatowany przy użyciu innego rozszerzenia. Plik jest rozpoznawany przez Eksploratora Windows jako certyfikat. `.pem`   Plik nie jest rozpoznawany przez Eksploratora Windows.

**. klucz: plik klucza prywatnego**

Plik klucza jest w tym samym formacie co plik PEM, ale ma inne rozszerzenie. 

#### <a name="additional-commonly-available-key-artifacts"></a>Dodatkowe często dostępne artefakty kluczy

**. CSR-żądanie podpisania certyfikatu**.  

Ten plik jest używany do przesłania do urzędów certyfikacji. Rzeczywisty format to PKCS10, który jest zdefiniowany w dokumencie RFC 2986 i może zawierać niektóre lub wszystkie szczegóły klucza żądanego certyfikatu. Na przykład temat, organizacja i stan. Jest to klucz publiczny certyfikatu, który jest podpisywany przez urząd certyfikacji i odbiera certyfikat w programie Return.  

Zwrócony certyfikat jest certyfikatem publicznym, który zawiera klucz publiczny, ale nie klucz prywatny. 

**. PKCS12. pfx. p12 — kontener hasła**. 

Pierwotnie zdefiniowane przez firmę RSA w Public-Key standardach kryptograficznych (PKCS), 12-wariantowe zostały pierwotnie ulepszone przez firmę Microsoft i przesłane dalej jako RFC 7292.  

Ten format kontenera wymaga hasła, które zawiera pary certyfikatów Public i Private. W przeciwieństwie do `.pem`   plików, ten kontener jest w pełni szyfrowany.  

Możesz użyć OpenSSL, aby przekształcić plik w `.pem`   plik z kluczami publicznymi i prywatnymi: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**algorytm der — szyfrowany binarnie PEM**.

Sposób kodowania składni ASN. 1 w formacie binarnym polega na `.pem`   pliku, który jest tylko zakodowanym algorytmem Base64 `.der` . 

OpenSSL może przekonwertować te pliki na `.pem` :  `openssl x509 -inform der -in to-convert.der -out converted.pem` .  

System Windows będzie rozpoznawał te pliki jako pliki certyfikatów. Domyślnie system Windows będzie eksportować certyfikaty jako `.der` sformatowane pliki o innym rozszerzeniu.

**. CRL — lista odwołania certyfikatów**.  

Urzędy certyfikacji tworzą te produkty jako sposób cofania autoryzacji certyfikatów przed ich wygaśnięciem. 

#### <a name="cli-commands"></a>Polecenia interfejsu wiersza polecenia

Użyj `cyberx-xsense-certificate-import` polecenia CLI, aby zaimportować certyfikaty. Aby użyć tego narzędzia, należy przekazać pliki certyfikatów do urządzenia za pomocą narzędzi, takich jak WinSCP lub Wget.

Polecenie obsługuje następujące flagi wejściowe:

- `-h`: Pokazuje składnię pomocy wiersza polecenia.

- `--crt`: Ścieżka do pliku certyfikatu (rozszerzenie CRT).

- `--key`:  \* . Key — plik. Długość klucza powinna wynosić co najmniej 2 048 bitów.

- `--chain`: Ścieżka do pliku łańcucha certyfikatów (opcjonalnie).

- `--pass`: Hasło używane do szyfrowania certyfikatu (opcjonalne).

- `--passphrase-set`: Domyślnie = `False` , nieużywane. Ustaw, aby `True` użyć poprzedniego hasła dostarczonego z poprzednim certyfikatem (opcjonalnie).

Gdy korzystasz z polecenia CLI:

- Sprawdź, czy pliki certyfikatów są odczytywane na urządzeniu.

- Sprawdź, czy nazwa domeny i adres IP w certyfikacie są zgodne z konfiguracją zaplanowaną przez dział IT.

### <a name="use-openssl-to-manage-certificates"></a>Zarządzanie certyfikatami za pomocą OpenSSL

Zarządzaj certyfikatami za pomocą następujących poleceń:

| Opis | Interfejs wiersza polecenia |
|--|--|
| Generowanie nowego klucza prywatnego i żądania podpisania certyfikatu | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| Generowanie certyfikatu z podpisem własnym | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| Generuj żądanie podpisania certyfikatu (CSR) dla istniejącego klucza prywatnego | `openssl req -out CSR.csr -key privateKey.key -new` |
| Generowanie żądania podpisania certyfikatu opartego na istniejącym certyfikacie | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| Usuń hasło z klucza prywatnego | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

Jeśli musisz sprawdzić informacje w ramach certyfikatu, CSR lub klucza prywatnego, Użyj tych poleceń;

| Opis | Interfejs wiersza polecenia |
|--|--|
| Sprawdź żądanie podpisania certyfikatu (CSR) | `openssl req -text -noout -verify -in CSR.csr` |
| Sprawdź klucz prywatny | `openssl rsa -in privateKey.key -check` |
| Sprawdź certyfikat | `openssl x509 -in certificate.crt -text -noout`  |

Jeśli zostanie wyświetlony komunikat o błędzie, że klucz prywatny nie jest zgodny z certyfikatem lub że certyfikat zainstalowany w lokacji nie jest zaufany, Użyj tych poleceń, aby naprawić błąd.

| Opis | Interfejs wiersza polecenia |
|--|--|
| Sprawdź skrót MD5 klucza publicznego, aby upewnić się, że jest on zgodny z tym, co znajduje się w CSR lub kluczu prywatnym | jedno. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> dwóch. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> r.3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

Aby przekonwertować certyfikaty i klucze na różne formaty, aby były zgodne z określonymi typami serwerów lub oprogramowania, Użyj tych poleceń;

| Opis | Interfejs wiersza polecenia |
|--|--|
| Konwertuj plik algorytmu DER (. CRT. cer. der) na PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| Konwertuj plik PEM na algorytm DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| Konwertowanie pliku PKCS # 12 (pfx. p12) zawierającego klucz prywatny i certyfikaty do PEM | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />Można dodać `-nocerts` tylko do danych wyjściowych klucza prywatnego lub dodać `-nokeys` tylko do danych wyjściowych. |
| Konwertowanie pliku certyfikatu PEM i klucza prywatnego na PKCS # 12 (. pfx. p12) | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="define-backup-and-restore-settings"></a>Zdefiniuj ustawienia kopii zapasowej i przywracania

Kopia zapasowa systemu lokalnej konsoli zarządzania jest wykonywana automatycznie, codziennie. Dane są zapisywane na innym dysku. Domyślna lokalizacja to `/var/cyberx/backups`. 

Ten plik można automatycznie przenieść do sieci wewnętrznej. 

> [!NOTE]
> Procedurę tworzenia kopii zapasowej i przywracania można wykonać tylko w tej samej wersji. 

Aby utworzyć kopię zapasową maszyny lokalnej konsoli zarządzania: 

- Zaloguj się do konta administracyjnego i wprowadź `sudo cyberx-management-backup -full` .

Aby przywrócić najnowszy plik kopii zapasowej:

- Zaloguj się do konta administracyjnego i wprowadź `$ sudo cyberx-management-system-restore` .

Aby zapisać kopię zapasową na zewnętrznym serwerze SMB:

1. Utwórz folder udostępniony na zewnętrznym serwerze SMB.  

   Pobierz ścieżkę do folderu, nazwę użytkownika i hasło wymagane do uzyskania dostępu do serwera SMB. 

2. W usłudze Defender for IoT Utwórz katalog kopii zapasowych:

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Edytuj fstab:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. Edytuj lub Utwórz poświadczenia dla serwera SMB do udostępnienia: 

   - `sudo nano /etc/samba/user` 

5. Dodaj: 

   - `username=<user name>`

   - `password=<password>` 

6. Zainstaluj katalog: 

   - `sudo mount -a` 

7. Skonfiguruj katalog kopii zapasowej w folderze udostępnionym w usłudze Defender dla lokalnej konsoli zarządzania usługi IoT:  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>Edytuj nazwę hosta

Aby edytować nazwę hosta konsoli zarządzania skonfigurowaną na serwerze DNS organizacji:

1. W okienku po lewej stronie konsoli zarządzania wybierz pozycję **Ustawienia systemowe**.  

2. W sekcji Sieć konsoli wybierz pozycję **Sieć**. 

3. Wprowadź nazwę hosta skonfigurowaną na serwerze DNS organizacji. 

4. Wybierz pozycję **Zapisz**.

## <a name="define-vlan-names"></a>Definiowanie nazw sieci VLAN

Nazwy sieci VLAN nie są zsynchronizowane między czujnikiem i konsolą zarządzania. Należy zdefiniować identyczne nazwy w składnikach.

W obszarze Sieć wybierz pozycję **Sieć VLAN** i Dodaj nazwy do odnalezionych identyfikatorów sieci VLAN. Następnie wybierz pozycję **Zapisz**.

## <a name="define-a-proxy-to-sensors"></a>Zdefiniuj serwer proxy do czujników

Podnieś poziom zabezpieczeń systemu, uniemożliwiając Logowanie użytkownika bezpośrednio do czujnika. Zamiast tego należy użyć tunelowania proxy, aby umożliwić użytkownikom dostęp do czujnika z lokalnej konsoli zarządzania z pojedynczą regułą zapory. To ulepszenie ogranicza możliwość nieautoryzowanego dostępu do środowiska sieciowego poza czujnikiem.

Użyj serwera proxy w środowiskach, w których nie ma bezpośredniej łączności z czujnikami.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="Użytkownicy.":::

Poniższa procedura umożliwia połączenie czujnika z lokalną konsolą zarządzania i włączenie tunelowania w tej konsoli:

1. Zaloguj się do lokalnego interfejsu wiersza polecenia konsoli zarządzania przy użyciu poświadczeń administracyjnych.

2. Wpisz `sudo cyberx-management-tunnel-enable` i wybierz **Enter**.

4. Wpisz `--port 10000` i wybierz **Enter**.

## <a name="adjust-system-properties"></a>Dostosuj właściwości systemu

Właściwości systemu kontrolują różne operacje i ustawienia w konsoli zarządzania programu. Edytowanie lub modyfikowanie ich może spowodować uszkodzenie operacji konsoli zarządzania. Przed zmianą ustawień zapoznaj się z tematem [Pomoc techniczna firmy Microsoft](https://support.microsoft.com) .

Aby uzyskać dostęp do właściwości systemu: 

1. Zaloguj się do lokalnej konsoli zarządzania lub czujnika.

2. Wybierz pozycję **Ustawienia systemowe**.

3. Wybierz pozycję **Właściwości systemu** z sekcji **Ogólne** .

## <a name="change-the-name-of-the-on-premises-management-console"></a>Zmień nazwę lokalnej konsoli zarządzania

Można zmienić nazwę lokalnej konsoli zarządzania. Nowe nazwy są wyświetlane w przeglądarce internetowej konsoli programu, w różnych oknach konsoli i w dziennikach rozwiązywania problemów. Nazwa domyślna to **Konsola zarządzania** programu.

Aby zmienić nazwę:

1. W dolnej części okienka po lewej stronie wybierz bieżącą nazwę.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="Zrzut ekranu przedstawiający wersję lokalnej konsoli zarządzania.":::

2. W oknie dialogowym **Edytowanie konfiguracji konsoli zarządzania** wprowadź nową nazwę. Nazwa nie może być dłuższa niż 25 znaków.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Zrzut ekranu przedstawiający edytowanie konfiguracji platformy Defender for IoT.":::

3. Wybierz pozycję **Zapisz**. Nowa nazwa zostanie zastosowana.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="Zrzut ekranu pokazujący zmianę nazwy konsoli.":::

## <a name="password-recovery"></a>Odzyskiwanie hasła

Odzyskiwanie hasła dla lokalnej konsoli zarządzania jest powiązane z subskrypcją, z którą urządzenie jest podłączone. Nie można odzyskać hasła, jeśli nie wiesz, z którą subskrypcją jest dołączone urządzenie.

Aby zresetować hasło:

1. Przejdź do strony logowania lokalnej konsoli zarządzania.
1. Wybierz pozycję **Odzyskiwanie hasła**.
1. Skopiuj unikatowy identyfikator.
1. Przejdź do strony usługi Defender dla **witryn i czujników** , a następnie wybierz kartę **Odzyskaj hasło** .
1. Wprowadź unikatowy identyfikator i wybierz pozycję **Odzyskaj**. Plik aktywacji zostanie pobrany.
1. Przejdź do strony **Odzyskiwanie hasła** i Przekaż plik aktywacji.
1. Wybierz opcję **Dalej**.
 
   Otrzymasz teraz nazwę użytkownika i nowe hasło generowane przez system.

> [!NOTE]
> Czujnik jest połączony z subskrypcją, z którą został pierwotnie połączony. Hasło można odzyskać tylko przy użyciu tej samej subskrypcji, do której jest dołączona.

## <a name="update-the-software-version"></a>Zaktualizuj wersję oprogramowania

Poniższa procedura zawiera opis sposobu aktualizowania wersji oprogramowania lokalnej konsoli zarządzania. Proces aktualizacji trwa około 30 minut.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/).

1. Przejdź do usługi Defender for IoT.

1. Przejdź do strony **aktualizacje** .

1. Wybierz wersję z sekcji lokalna Konsola zarządzania.

1. Wybierz pozycję **Pobierz** i Zapisz plik.

1. Zaloguj się do lokalnej konsoli zarządzania i wybierz pozycję **Ustawienia systemowe** z menu po stronie.

1. W okienku **Aktualizacja wersji** wybierz pozycję **Aktualizuj**.

1. Wybierz plik pobrany ze strony **aktualizacje** usługi Defender for IoT.

## <a name="next-steps"></a>Następne kroki

[Zarządzanie czujnikami z poziomu konsoli zarządzania](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Zarządzanie pojedynczymi czujnikami](how-to-manage-individual-sensors.md)
