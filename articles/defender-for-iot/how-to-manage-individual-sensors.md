---
title: Zarządzanie pojedynczymi czujnikami
description: Dowiedz się, jak zarządzać indywidualnymi czujnikami, w tym zarządzaniem plikami aktywacji, wykonywaniem kopii zapasowych i aktualizowaniem czujników autonomicznych.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 02/02/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: ba98eb7e87ba277dcd5279ecf17373a8276b1cb1
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523978"
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

- **W przypadku czujników połączonych z chmurą**: czujnik nie może połączyć się z Internetem. Sprawdź konfigurację sieci czujnika. Jeśli czujnik musi nawiązać połączenie za pomocą serwera proxy sieci Web w celu uzyskania dostępu do Internetu, sprawdź, czy serwer proxy jest prawidłowo skonfigurowany na ekranie **konfiguracji sieci czujnika** . Sprawdź, czy \* w zaporze i/lub serwerze proxy jest dozwolony Azure-Devices.NET:443. Jeśli symbole wieloznaczne nie są obsługiwane lub chcesz mieć większą kontrolę, nazwa FQDN określonej usługi Defender for IoT Hub powinna być otwarta w zaporze i/lub serwerze proxy. Aby uzyskać szczegółowe informacje, zobacz [punkty końcowe IoT Hub referencyjnych](../iot-hub/iot-hub-devguide-endpoints.md).  

- **W przypadku czujników połączonych z chmurą**: plik aktywacji jest prawidłowy, ale usługa Defender dla usługi IoT odrzuciła ją. Jeśli nie możesz rozwiązać tego problemu, możesz pobrać kolejną aktywację ze strony witryny i czujniki w portalu Defender for IoT. Jeśli to nie zadziała, skontaktuj się z pomoc techniczna firmy Microsoft.

## <a name="manage-certificates"></a>Zarządzanie certyfikatami

Po zainstalowaniu czujnika zostanie wygenerowany lokalny certyfikat z podpisem własnym, który będzie używany do uzyskiwania dostępu do aplikacji sieci Web czujnika. Po pierwszym zalogowaniu się do czujnika użytkownicy będą monitowani o podanie certyfikatu SSL/TLS.  Aby uzyskać więcej informacji na temat konfiguracji pierwszego czasu, zobacz [Logowanie i aktywowanie czujnika](how-to-activate-and-set-up-your-sensor.md).

Ten artykuł zawiera informacje na temat aktualizowania certyfikatów, pracy z poleceniami interfejsu CLI certyfikatu oraz obsługiwanych certyfikatów i parametrów certyfikatów.

### <a name="about-certificates"></a>Informacje o certyfikatach

Usługa Azure Defender for IoT używa certyfikatów SSL/TLS, aby:

- Zapoznaj się z określonymi wymaganiami dotyczącymi certyfikatów i szyfrowania, przekazując certyfikat podpisany przez urząd certyfikacji.

- Zezwalaj na sprawdzanie poprawności między konsolą zarządzania i połączonymi czujnikami oraz między konsolą zarządzania i konsolą zarządzania o wysokiej dostępności. Walidacje są oceniane względem listy odwołania certyfikatów i daty wygaśnięcia certyfikatu. *Jeśli walidacja nie powiedzie się, komunikacja między konsolą zarządzania i czujnikiem zostanie zatrzymana, a w konsoli zostanie wyświetlony błąd sprawdzania poprawności*. Ta opcja jest domyślnie włączona po instalacji.

 Reguły przekazywania innych firm, na przykład informacje o alercie wysyłane do dziennika systemowego, Splunk lub usługi ServiceNow; lub komunikacja z Active Directory nie są weryfikowane.

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

Urządzenia mogą korzystać z unikatowych plików certyfikatów. Jeśli zachodzi potrzeba zastąpienia certyfikatu, został przekazany;

- W wersji 10,0 certyfikat można zastąpić w menu Ustawienia systemu.

- W przypadku wersji wcześniejszych niż 10,0 certyfikat SSL można zastąpić za pomocą narzędzia wiersza polecenia.

### <a name="update-certificates"></a>Aktualizowanie certyfikatów

Administratorzy czujnika mogą aktualizować certyfikaty.

Aby zaktualizować certyfikat:  

1. Wybierz pozycję **Ustawienia systemowe**.

1. Wybierz opcję **Certyfikaty SSL/TLS.**
1. Usuń lub Edytuj certyfikat i Dodaj nowy.

    - Dodaj nazwę certyfikatu.
    
    - Przekaż plik CRT i plik klucza, a następnie wprowadź hasło.
    - W razie potrzeby Przekaż plik PEM.

Aby zmienić ustawienie walidacji:

1. Włącza lub wyłącza przełącznik **Włącz weryfikację certyfikatu** .

1. Wybierz pozycję **Zapisz**.

Jeśli opcja jest włączona, a Walidacja nie powiedzie się, komunikacja między konsolą zarządzania a czujnikiem zostanie zatrzymana, a w konsoli zostanie wyświetlony błąd sprawdzania poprawności.

### <a name="certificate-support"></a>Obsługa certyfikatów

Obsługiwane są następujące certyfikaty:

- Infrastruktura kluczy prywatnych i korporacyjnych (prywatna infrastruktura PKI)

- Infrastruktura kluczy publicznych (publiczna infrastruktura PKI) 

- Wygenerowane lokalnie na urządzeniu (lokalnie z podpisem własnym). 

> [!IMPORTANT]
> Nie zalecamy używania certyfikatów z podpisem własnym. Ten typ połączenia nie jest bezpieczny i powinien być używany tylko w środowiskach testowych. Od tego czasu nie można sprawdzić poprawności właściciela certyfikatu i nie można utrzymywać zabezpieczeń systemu, dlatego certyfikaty z podpisem własnym nigdy nie powinny być używane dla sieci produkcyjnych.

### <a name="supported-ssl-certificates"></a>Obsługiwane certyfikaty SSL 

Obsługiwane są następujące parametry. 

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
- Jednostka organizacyjna podmiotu (OU) = zdefiniowana, na przykład contoso Labs
- Podmiot (O) rganizacji = zdefiniowany, na przykład contoso Inc.

**Plik klucza**

- Plik klucza wygenerowany podczas tworzenia CSR.

- RSA 2048 bitów (minimum) lub 4096 bitów.

 > [!Note]
 > Użycie klucza o długości 4096bits:
 > - Uzgadnianie protokołu SSL na początku każdego połączenia będzie wolniejsze.  
 > - Podczas uzgadniania występuje wzrost użycia procesora CPU. 

**Łańcuch certyfikatów**

- Pośredni plik certyfikatu (jeśli istnieje), który został dostarczony przez urząd certyfikacji

- Certyfikat urzędu certyfikacji, który wystawił certyfikat serwera, powinien znajdować się najpierw w pliku, a po nim wszystkie inne osoby w katalogu głównym. 
- Może zawierać atrybuty zbioru.

**Danym**

- Jeden klucz jest obsługiwany.

- Konfiguruje się podczas importowania certyfikatu.

Certyfikaty z innymi parametrami mogą funkcjonować, ale firma Microsoft nie obsługuje ich.

#### <a name="encryption-key-artifacts"></a>Artefakty klucza szyfrowania

**pem — plik kontenera certyfikatów**

Pliki Privacy Enhanced Mail (PEM) były ogólnym typem pliku używanym do zabezpieczania wiadomości e-mail. Obecnie pliki PEM są używane z certyfikatami i używają kluczy x509 ASN1.  

Plik kontenera jest zdefiniowany w RFC 1421 do 1424, format kontenera, który może zawierać tylko certyfikat publiczny. Na przykład program Apache instaluje certyfikat urzędu certyfikacji, pliki itp., SSL lub certyfikaty. Może to obejmować cały łańcuch certyfikatów, w tym klucz publiczny, klucz prywatny i certyfikaty główne.  

Może również zakodować CSR jako format PKCS10, który można przetłumaczyć na PEM.

**. Certificate. cer. CRT — plik kontenera certyfikatów**

`.pem` `.der` Plik lub sformatowany przy użyciu innego rozszerzenia. Plik jest rozpoznawany przez Eksploratora Windows jako certyfikat. `.pem`   Plik nie jest rozpoznawany przez Eksploratora Windows.

**klucz — plik klucza prywatnego**

Plik klucza jest w tym samym formacie co plik PEM, ale ma inne rozszerzenie.

#### <a name="additional-commonly-available-key-artifacts"></a>Dodatkowe często dostępne artefakty kluczy

**. CSR-żądanie podpisania certyfikatu**.  

Ten plik jest używany do przesłania do urzędów certyfikacji. Rzeczywisty format to PKCS10, który jest zdefiniowany w dokumencie RFC 2986 i może zawierać niektóre lub wszystkie szczegóły klucza żądanego certyfikatu. Na przykład temat, organizacja i stan. Jest to klucz publiczny certyfikatu, który jest podpisywany przez urząd certyfikacji i odbiera certyfikat w programie Return.  

Zwrócony certyfikat jest certyfikatem publicznym, który zawiera klucz publiczny, ale nie klucz prywatny. 

**. PKCS12. pfx. p12 — kontener hasła**. 

Pierwotnie zdefiniowane przez firmę RSA w Public-Key standardach kryptograficznych (PKCS), 12-wariantowe zostały pierwotnie ulepszone przez firmę Microsoft i przesłane dalej jako RFC 7292.  

Ten format kontenera wymaga hasła, które zawiera pary certyfikatów Public i Private. W przeciwieństwie do `.pem`   plików, ten kontener jest w pełni szyfrowany.  

Możesz użyć OpenSSL, aby przekształcić ten plik w `.pem`   pliku z kluczami publicznymi i prywatnymi: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**algorytm der — szyfrowany binarnie PEM**.

Sposób kodowania składni ASN. 1 w formacie binarnym polega na `.pem`   pliku, który jest tylko zakodowanym algorytmem Base64 `.der` . 

OpenSSL może przekonwertować te pliki na `.pem` :  `openssl x509 -inform der -in to-convert.der -out converted.pem` .  

System Windows będzie rozpoznawał te pliki jako pliki certyfikatów. Domyślnie system Windows będzie eksportować certyfikaty jako `.der` sformatowane pliki o innym rozszerzeniu.  

**. CRL — lista odwołania certyfikatów**.  
Urzędy certyfikacji tworzą te pliki jako sposób cofania autoryzacji certyfikatów przed ich wygaśnięciem.
 
##### <a name="cli-commands"></a>Polecenia interfejsu wiersza polecenia

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

## <a name="connect-a-sensor-to-the-management-console"></a>Łączenie czujnika z konsolą zarządzania

W tej sekcji opisano sposób zapewnienia połączenia między czujnikiem i lokalną konsolą zarządzania. Należy to zrobić, jeśli pracujesz w sieci gapped i chcesz wysyłać informacje o urządzeniu i alercie do konsoli zarządzania z czujnika. To połączenie umożliwia również konsoli zarządzania wypychanie ustawień systemowych do czujnika i wykonywanie innych zadań zarządzania na czujniku.

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

1. W portalu usługi Azure Defender dla IoT przejdź do strony witryny i czujniki.

1. Usuń czujnik ze strony Lokacje i czujniki.

1. Zarejestruj się przy użyciu nowej nazwy, wybierając opcję **czujnik** dołączania ze strony wprowadzenie.

1. Pobierz nowy plik aktywacji.

1. Zaloguj się do konsoli czujnika usługi Defender for IoT.

1. W konsoli czujnika wybierz pozycję **Ustawienia systemu** , a następnie wybierz pozycję **ponowna aktywacja**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Przekaż plik aktywacji, aby ponownie aktywować czujnik.":::

1. Wybierz pozycję **Przekaż** i wybierz zapisany plik.

1. Wybierz pozycję **Aktywuj**.

## <a name="update-the-sensor-network-configuration"></a>Aktualizowanie konfiguracji sieci czujnika

Konfiguracja sieci czujnika została zdefiniowana podczas instalacji czujnika. Można zmienić parametry konfiguracji. Można również skonfigurować konfigurację serwera proxy.

Jeśli utworzysz nowy adres IP, może być konieczne ponowne zalogowanie.

Aby zmienić konfigurację:

1. W menu po stronie wybierz pozycję **Ustawienia systemowe**.

2. W oknie **Ustawienia systemu** wybierz pozycję **Sieć**.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Skonfiguruj ustawienia sieci.":::

3. Ustaw parametry:

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

## <a name="next-steps"></a>Następne kroki

[Badania i pakiety analizy zagrożeń](how-to-work-with-threat-intelligence-packages.md)

[Zarządzanie czujnikami z poziomu konsoli zarządzania](how-to-manage-sensors-from-the-on-premises-management-console.md)