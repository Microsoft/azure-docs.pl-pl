---
title: Samouczek — Inicjowanie obsługi administracyjnej urządzeń X. 509 na platformie Azure IoT Hub przy użyciu niestandardowego sprzętowego modułu zabezpieczeń (HSM)
description: W tym samouczku są stosowane grupy rejestracji. W tym samouczku dowiesz się, jak udostępnić urządzenia X. 509 za pomocą niestandardowego sprzętowego modułu zabezpieczeń (HSM) oraz zestawu SDK urządzeń C dla platformy Azure IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 01/28/2021
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: b178aa4a524cb7fcc85c7fc68ac5f772747787a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99052366"
---
# <a name="tutorial-provision-multiple-x509-devices-using-enrollment-groups"></a>Samouczek: Inicjowanie obsługi wielu urządzeń X. 509 przy użyciu grup rejestracji

W tym samouczku dowiesz się, jak udostępnić grupom urządzeń IoT, które używają certyfikatów X. 509 do uwierzytelniania. Przykładowy kod urządzenia z [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) zostanie wykonany na komputerze deweloperskim w celu zasymulowania aprowizacji urządzeń X. 509. Na rzeczywistych urządzeniach kod urządzenia zostałby wdrożony i uruchomiony z urządzenia IoT.

Przed kontynuowaniem pracy z tym samouczkiem upewnij się, że zostały wykonane czynności opisane w sekcji [konfigurowanie IoT Hub Device Provisioning Service z Azure Portal](quick-setup-auto-provision.md) . Ponadto, jeśli nie znasz procesu autozastrzegania, zapoznaj się z omówieniem [aprowizacji](about-iot-dps.md#provisioning-process) . 

Usługa Azure IoT Device Provisioning obsługuje dwa typy rejestracji dla urządzeń aprowizacji:

* [Grupy rejestracji](concepts-service.md#enrollment-group): służą do rejestrowania wielu pokrewnych urządzeń.
* [Rejestracje indywidualne](concepts-service.md#individual-enrollment): służą do rejestrowania pojedynczych urządzeń.

Ten samouczek jest podobny do poprzednich samouczków demonstrujących sposób używania grup rejestracji do udostępniania zestawów urządzeń. Jednak w tym samouczku zostaną użyte certyfikaty X. 509 zamiast kluczy symetrycznych. Przejrzyj poprzednie samouczki w tej sekcji, aby zapoznać się z prostym podejściem przy użyciu [kluczy symetrycznych](./concepts-symmetric-key-attestation.md).

W tym samouczku przedstawiono [niestandardowy przykład modułu HSM](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/custom_hsm_example) , który zapewnia implementację zastępczą w połączeniu z bezpiecznym magazynem opartym na sprzęcie. [Sprzętowy moduł zabezpieczeń (HSM)](./concepts-service.md#hardware-security-module) jest używany do bezpiecznego, sprzętowego przechowywania wpisów tajnych urządzeń. Moduł HSM może być używany z kluczem symetrycznym, certyfikatem X. 509 lub zaświadczeniem modułu TPM w celu zapewnienia bezpiecznego magazynu dla wpisów tajnych. Sprzętowy Magazyn kluczy tajnych urządzeń nie jest wymagany, ale zdecydowanie zaleca się ochronę poufnych informacji, takich jak klucz prywatny certyfikatu urządzenia.


W tym samouczku wykonasz następujące cele:

> [!div class="checklist"]
> * Utwórz łańcuch certyfikatów zaufania, aby zorganizować zbiór urządzeń za pomocą certyfikatów X. 509.
> * Uzupełnij dowód posiadania certyfikatu podpisywania używanego w łańcuchu certyfikatów.
> * Tworzenie nowej rejestracji grupy korzystającej z łańcucha certyfikatów
> * Konfigurowanie środowiska programistycznego na potrzeby aprowizacji urządzenia przy użyciu kodu z [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c)
> * Zapewnij urządzeniu za pomocą łańcucha certyfikatów z przykładem niestandardowego sprzętu sprzętowego modułu zabezpieczeń (HSM) w zestawie SDK.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe wymagania wstępne dotyczą środowiska deweloperskiego systemu Windows używanego do symulowania urządzeń. W systemie Linux lub macOS zapoznaj się z odpowiednią sekcją w sekcji [Przygotowywanie środowiska deweloperskiego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w dokumentacji zestawu SDK.

* [Program Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 z włączonym obciążeniem ["Programowanie aplikacji klasycznych w języku C++"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) . Obsługiwane są również programy Visual Studio 2015 i Visual Studio 2017. 

    Program Visual Studio jest używany w tym artykule do tworzenia przykładowego kodu urządzenia, który zostanie wdrożony na urządzeniach IoT.  Nie oznacza to, że program Visual Studio jest wymagany na urządzeniu.

* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Przygotuj środowisko deweloperskie zestawu SDK usługi Azure IoT C

W tej sekcji przygotujesz środowisko deweloperskie używane do opracowania [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c). Zestaw SDK zawiera przykładowy kod i narzędzia używane przez funkcję aprowizacji urządzeń X. 509 w usłudze DPS.

1. Pobierz [system kompilacji CMAKE](https://cmake.org/download/).

    **Przed** rozpoczęciem instalacji należy pamiętać, że wymagania wstępne programu Visual Studio ([Visual Studio](https://visualstudio.microsoft.com/vs/) i ["Programowanie aplikacji klasycznych w języku C++"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) ) są zainstalowane na komputerze `CMake` . Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

2. Znajdź nazwę tagu dla [najnowszej wersji](https://github.com/Azure/azure-iot-sdk-c/releases/latest) zestawu SDK języka C usługi Azure IoT.

3. Otwórz wiersz polecenia lub powłokę Git Bash. Uruchom następujące polecenia, aby sklonować najnowszą wersję repozytorium [usługi Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) w witrynie GitHub. Użyj znacznika znalezionego w poprzednim kroku jako wartości `-b` parametru:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

4. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu. 

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. `cmake`Utworzony katalog będzie zawierać niestandardowy przykład modułu HSM i przykładowy kod aprowizacji urządzenia, który używa niestandardowego modułu HSM do udostępniania uwierzytelniania X. 509. 

    Uruchom następujące polecenie w `cmake` katalogu, aby skompilować wersję zestawu SDK specyficzną dla platformy deweloperskiej. Kompilacja będzie zawierać odwołanie do niestandardowego przykładowego modułu HSM. 

    Podczas określania ścieżki używanej w `-Dhsm_custom_lib` poniższym polu upewnij się, że ścieżka została użyta względem `cmake` utworzonego wcześniej katalogu. Ścieżka względna pokazana poniżej jest tylko przykładem.

    ```cmd
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    ```

    Jeśli program `cmake` nie znajdzie kompilatora języka C++, mogą występować błędy kompilacji podczas uruchamiania powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs).

    Po pomyślnym zakończeniu kompilacji w katalogu zostanie wygenerowane rozwiązanie programu Visual Studio `cmake` . Ostatnie linie wyjściowe wyglądają podobnie do następujących danych wyjściowych:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: D:/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-x509-certificate-chain"></a>Tworzenie łańcucha certyfikatów X. 509

W tej sekcji przedstawiono generowanie łańcucha certyfikatów X. 509 trzech certyfikatów do testowania poszczególnych urządzeń za pomocą tego samouczka. Certyfikaty będą mieć następującą hierarchię.

![Samouczek dotyczący łańcucha certyfikatów urządzeń](./media/tutorial-custom-hsm-enrollment-group-x509/example-device-cert-chain.png#lightbox)

[Certyfikat główny](concepts-x509-attestation.md#root-certificate): zostanie ukończony [dowód posiadania](how-to-verify-certificates.md) , aby zweryfikować certyfikat główny. Ta weryfikacja umożliwi zaufać temu certyfikatowi i zweryfikowanie certyfikatów podpisanych przez usługę DPS.

[Certyfikat pośredni](concepts-x509-attestation.md#intermediate-certificate): typowy dla certyfikatów pośrednich, które mają być używane do grupowania urządzeń logicznie według wierszy produktów, działów firmy lub innych kryteriów. W tym samouczku zostanie użyty łańcuch certyfikatów składający się z jednego certyfikatu pośredniego. Certyfikat pośredni zostanie podpisany przez certyfikat główny. Ten certyfikat będzie również używany w grupie rejestracji utworzonej w usłudze DPS do logicznego grupowania zestawu urządzeń. Ta konfiguracja umożliwia zarządzanie całą grupą urządzeń, które mają certyfikaty urządzeń podpisane przez ten sam certyfikat pośredni. Można utworzyć grupy rejestracji do włączania lub wyłączania grupy urządzeń. Aby uzyskać więcej informacji na temat wyłączania grupy urządzeń, zobacz [niezezwalanie na certyfikat pośredniego lub głównego urzędu certyfikacji X. 509 przy użyciu grupy rejestracji](how-to-revoke-device-access-portal.md#disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group)

[Certyfikaty urządzeń](concepts-x509-attestation.md#end-entity-leaf-certificate): certyfikaty urządzeń (liści) będą podpisane przez certyfikat pośredni i przechowywane na urządzeniu wraz z jego kluczem prywatnym. W idealnym przypadku te poufne elementy byłyby bezpiecznie przechowywane przy użyciu modułu HSM. Każde urządzenie będzie przedstawiać swój certyfikat i klucz prywatny wraz z łańcuchem certyfikatów podczas próby aprowizacji. 

#### <a name="create-root-and-intermediate-certificates"></a>Tworzenie certyfikatów głównych i pośrednich

Aby utworzyć główne i pośrednie fragmenty łańcucha certyfikatów:

1. Otwórz wiersz polecenia narzędzia Git bash. Wykonaj kroki 1 i 2, korzystając z instrukcji powłoki bash, które znajdują się w temacie [Zarządzanie certyfikatami testów urzędu certyfikacji dla przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md#managing-test-ca-certificates-for-samples-and-tutorials).

    Spowoduje to utworzenie katalogu roboczego dla skryptów certyfikatów i wygenerowanie przykładu głównego i pośredniego certyfikatu dla łańcucha certyfikatów przy użyciu OpenSSL. 
    
2. Zwróć uwagę na dane wyjściowe pokazujące lokalizację certyfikatu głównego z podpisem własnym. Ten certyfikat przejdzie przez [dowód posiadania](how-to-verify-certificates.md) , aby później zweryfikować własność.

    ```output
    Creating the Root CA Certificate
    CA Root Certificate Generated At:
    ---------------------------------
        ./certs/azure-iot-test-only.root.ca.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                fc:cc:6b:ab:3b:9a:3e:fe
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:30 2020 GMT
                Not After : Nov 22 21:30:30 2020 GMT
            Subject: CN=Azure IoT Hub CA Cert Test Only
    ```        
    
3. Zwróć uwagę na dane wyjściowe pokazujące lokalizację certyfikatu pośredniego, który jest podpisany/wystawiony przez certyfikat główny. Ten certyfikat będzie używany z grupą rejestracji, która zostanie utworzona później.

    ```output
    Intermediate CA Certificate Generated At:
    -----------------------------------------
        ./certs/azure-iot-test-only.intermediate.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 1 (0x1)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:33 2020 GMT
                Not After : Nov 22 21:30:33 2020 GMT
            Subject: CN=Azure IoT Hub Intermediate Cert Test Only
    ```    
    
#### <a name="create-device-certificates"></a>Tworzenie certyfikatów urządzeń

Aby utworzyć certyfikaty urządzeń podpisane przez certyfikat pośredni w łańcuchu:

1. Uruchom następujące polecenie, aby utworzyć nowy certyfikat urządzenia/liścia z nazwą podmiotu nadaną jako parametr. Użyj przykładowej nazwy podmiotu podanej dla tego samouczka `custom-hsm-device-01` . Ta nazwa podmiotu będzie IDENTYFIKATORem urządzenia dla urządzenia IoT. 

    > [!WARNING]
    > Nie używaj nazwy podmiotu z spacjami. Ta nazwa podmiotu jest IDENTYFIKATORem urządzenia dla aprowizacji urządzenia IoT. Musi być zgodna z regułami dotyczącymi identyfikatora urządzenia. Aby uzyskać więcej informacji, zobacz [właściwości tożsamości urządzenia](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).

    ```cmd
    ./certGen.sh create_device_certificate_from_intermediate "custom-hsm-device-01"
    ```

    Zwróć uwagę na następujące dane wyjściowe pokazujące, gdzie znajduje się nowy certyfikat urządzenia. Certyfikat urządzenia jest podpisany (wystawiony) przez certyfikat pośredni.

    ```output
    -----------------------------------
    ./certs/new-device.cert.pem: OK
    Leaf Device Certificate Generated At:
    ----------------------------------------
        ./certs/new-device.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 9 (0x9)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub Intermediate Cert Test Only
            Validity
                Not Before: Nov 10 09:20:33 2020 GMT
                Not After : Dec 10 09:20:33 2020 GMT
            Subject: CN=custom-hsm-device-01
    ```    
    
2. Uruchom następujące polecenie, aby utworzyć pełny plik łańcucha certyfikatów zawierający nowy certyfikat urządzenia dla programu `custom-hsm-device-01` .

    ```Bash
    cd ./certs && cat new-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-device-01-full-chain.cert.pem && cd ..
    ```

    Użyj edytora tekstów i Otwórz plik łańcucha certyfikatów, *./certs/New-Device-01-Full-Chain.CERT.pem*. Tekst łańcucha certyfikatów zawiera pełny łańcuch wszystkich trzech certyfikatów. Ten tekst będzie używany jako łańcuch certyfikatów w ramach niestandardowego kodu urządzenia modułu HSM w dalszej części tego samouczka dla programu `custom-hsm-device-01` .

    Pełny tekst łańcucha ma następujący format:
 
    ```output 
    -----BEGIN CERTIFICATE-----
        <Text for the device certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the intermediate certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the root certificate includes public key>
    -----END CERTIFICATE-----
    ```

3. Zwróć uwagę na to, że klucz prywatny nowego certyfikatu urządzenia jest zapisywana w pliku *./Private/New-Device.Key.pem*. Zmień nazwę pliku klucza *./Private/New-Device-01.Key.pem* dla `custom-hsm-device-01` urządzenia. Tekst dla tego klucza będzie wymagany przez urządzenie podczas aprowizacji. Tekst zostanie dodany do przykładowego modułu HSM w późniejszym czasie.

    ```bash
    $ mv private/new-device.key.pem private/new-device-01.key.pem
    ```

    > [!WARNING]
    > Tekst certyfikatów zawiera tylko informacje o kluczu publicznym. 
    >
    > Jednak urządzenie musi mieć również dostęp do klucza prywatnego dla certyfikatu urządzenia. Jest to konieczne, ponieważ urządzenie musi przeprowadzić weryfikację przy użyciu tego klucza w czasie wykonywania podczas próby aprowizacji. Czułość tego klucza jest jednym z głównych powodów, dla których zaleca się używanie magazynu sprzętowego w rzeczywistym module HSM w celu zapewnienia bezpieczeństwa kluczy prywatnych.

4. Powtórz kroki 1-3 dla drugiego urządzenia z IDENTYFIKATORem urządzenia `custom-hsm-device-02` . Na tym urządzeniu należy używać następujących wartości:

    |   Opis                 |  Wartość  |
    | :---------------------------- | :--------- |
    | Nazwa podmiotu                  | `custom-hsm-device-02` |
    | Pełny plik łańcucha certyfikatów   | *./certs/new-device-02-full-chain.cert.pem* |
    | Nazwa pliku klucza prywatnego          | *Private/New-Device-02. Key. pem* |
    

## <a name="verify-ownership-of-the-root-certificate"></a>Weryfikuj własność certyfikatu głównego

1. Korzystając z wskazówek [zarejestrowano publiczną część certyfikatu X. 509 i uzyskaj kod weryfikacyjny](how-to-verify-certificates.md#register-the-public-part-of-an-x509-certificate-and-get-a-verification-code), Przekaż certyfikat główny ( `./certs/azure-iot-test-only.root.ca.cert.pem` ) i Pobierz kod weryfikacyjny z usługi DPS.

2. Gdy masz kod weryfikacyjny z usługi DPS dla certyfikatu głównego, uruchom następujące polecenie w katalogu roboczym skryptu certyfikatu w celu wygenerowania certyfikatu weryfikacji.
 
    Kod weryfikacyjny określony tutaj jest tylko przykładem. Użyj kodu wygenerowanego z usługi DPS.    

    ```Bash
    ./certGen.sh create_verification_certificate 1B1F84DE79B9BD5F16D71E92709917C2A1CA19D5A156CB9F    
    ```    

    Ten skrypt tworzy certyfikat podpisany przez certyfikat główny z nazwą podmiotu ustawioną na kod weryfikacyjny. Ten certyfikat umożliwia usłudze DPS zweryfikowanie, czy masz dostęp do klucza prywatnego certyfikatu głównego. Zwróć uwagę na lokalizację certyfikatu weryfikacji w danych wyjściowych skryptu. Ten certyfikat jest generowany w `.pfx` formacie.

    ```output
    Leaf Device PFX Certificate Generated At:
    --------------------------------------------
        ./certs/verification-code.cert.pfx
    ```

3. Jak wspomniano w polu [Przekaż podpisany certyfikat weryfikacyjny](how-to-verify-certificates.md#upload-the-signed-verification-certificate), Przekaż certyfikat weryfikacji, a następnie kliknij przycisk **Weryfikuj** w usłudze DPS, aby zakończyć potwierdzenie posiadania certyfikatu głównego.


## <a name="update-the-certificate-store-on-windows-based-devices"></a>Aktualizowanie magazynu certyfikatów na urządzeniach z systemem Windows

Na urządzeniach z systemem innym niż Windows można przekazać łańcuch certyfikatów z kodu jako magazyn certyfikatów.

Na urządzeniach z systemem Windows należy dodać certyfikaty podpisywania (główne i pośrednie) do [magazynu certyfikatów](/windows/win32/secauthn/certificate-stores)systemu Windows. W przeciwnym razie certyfikaty podpisywania nie będą transportowane do platformy DPS za pośrednictwem bezpiecznego kanału z usługą Transport Layer Security (TLS).

> [!TIP]
> Istnieje również możliwość użycia OpenSSL zamiast bezpiecznego kanału (Schannel) z zestawem C SDK. Aby uzyskać więcej informacji na temat korzystania z programu OpenSSL, zobacz [Używanie OpenSSL w zestawie SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#using-openssl-in-the-sdk).

Aby dodać certyfikaty podpisywania do magazynu certyfikatów na urządzeniach z systemem Windows:

1. W wierszu polecenia usługi git bash przejdź do `certs` podkatalogu zawierającego certyfikaty podpisywania i Przekształć je w `.pfx` następujący sposób.

    certyfikat główny:

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.root.ca.key.pem -in ./azure-iot-test-only.root.ca.cert.pem -export -out ./root.pfx
    ```
    
    certyfikat pośredni:   

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.intermediate.key.pem -in ./azure-iot-test-only.intermediate.cert.pem -export -out ./intermediate.pfx
    ```

2. Kliknij prawym przyciskiem myszy przycisk **Start** systemu Windows. Następnie kliknij przycisk **Uruchom**. Wprowadź *certmgr. MCS* i kliknij przycisk **OK** , aby uruchomić przystawkę MMC Menedżera certyfikatów.

3. W Menedżerze certyfikatów w obszarze **Certyfikaty — bieżący użytkownik** kliknij pozycję **Zaufane główne** urzędy certyfikacji. Następnie w menu kliknij kolejno pozycje **Akcja**  >  **wszystkie zadania**  >  **Importuj** do zaimportowania `root.pfx` .

    * Upewnij się, że usługa **wymiany informacji osobistych (pfx)** jest wyszukiwana
    * Użyj `1234` jako hasła.
    * Umieść certyfikat w magazynie certyfikatów **Zaufane główne urzędy certyfikacji** .

4. W Menedżerze certyfikatów w obszarze **Certyfikaty — bieżący użytkownik** kliknij pozycję **pośrednich urzędów certyfikacji**. Następnie w menu kliknij kolejno pozycje **Akcja**  >  **wszystkie zadania**  >  **Importuj** do zaimportowania `intermediate.pfx` .

    * Upewnij się, że usługa **wymiany informacji osobistych (pfx)** jest wyszukiwana
    * Użyj `1234` jako hasła.
    * Umieść certyfikat w magazynie certyfikatów **pośrednich urzędów certyfikacji** .

Certyfikaty podpisywania są teraz zaufane na urządzeniu z systemem Windows, a pełny łańcuch można przewieźć do punktu dystrybucji.



## <a name="create-an-enrollment-group"></a>Tworzenie grupy rejestracji

1. Zaloguj się do Azure Portal, wybierz przycisk **wszystkie zasoby** w menu po lewej stronie i Otwórz swoją usługę Device Provisioning.

2. Wybierz kartę **Zarządzanie rejestracjami** , a następnie wybierz przycisk **Dodaj grupę rejestracji** u góry.

3. W panelu **Dodaj grupę rejestracji** Wprowadź poniższe informacje, a następnie naciśnij przycisk **Zapisz** .

      ![Dodawanie grupy rejestracji dla zaświadczania X. 509 w portalu](./media/tutorial-custom-hsm-enrollment-group-x509/custom-hsm-enrollment-group-x509.png#lightbox)

    | Pole        | Wartość           |
    | :----------- | :-------------- |
    | **Nazwa grupy** | Na potrzeby tego samouczka wprowadź **Custom-HSM-x509-Devices** |
    | **Typ zaświadczania** | Wybierz **certyfikat** |
    | **Urządzenie usługi IoT Edge** | Wybierz **wartość FAŁSZ** |
    | **Typ certyfikatu** | Wybierz **certyfikat pośredni** |
    | **Plik PEM lub CER certyfikatu podstawowego** | Przejdź do utworzonego wcześniej pośrednika (*./certs/Azure-IoT-test-Only.Intermediate.CERT.pem*) |


## <a name="configure-the-provisioning-device-code"></a>Skonfiguruj kod urządzenia aprowizacji

W tej sekcji należy zaktualizować przykładowy kod przy użyciu informacji o wystąpieniu usługi Device Provisioning Service. Jeśli urządzenie zostanie uwierzytelnione, zostanie przypisane do centrum IoT połączonego z wystąpieniem usługi Device Provisioning skonfigurowanym w tej sekcji.

1. W Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning i zanotuj wartość **_Identyfikator zakresu_** .

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Uruchom program Visual Studio i Otwórz nowy plik rozwiązania, który został utworzony w `cmake` katalogu głównym repozytorium git Azure-IoT-SDK-c. Plik rozwiązania ma nazwę `azure_iot_sdks.sln` .

3. W Eksplorator rozwiązań dla programu Visual Studio przejdź do **Provisioning_Samples > prov_dev_client_sample > pliki źródłowe** i otwórz plik *prov_dev_client_sample. c*.

4. Znajdź stałą `id_scope` i zastąp jej wartość wcześniej skopiowaną wartością **Zakres identyfikatorów**. 

    ```c
    static const char* id_scope = "0ne00000A0A";
    ```

5. Znajdź definicję funkcji `main()` w tym samym pliku. Upewnij się, że `hsm_type` zmienna jest ustawiona na `SECURE_DEVICE_TYPE_X509` tak, jak pokazano poniżej.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    //hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Kliknij prawym przyciskiem myszy projekt **prov\_dev\_client\_sample**, a następnie wybierz pozycję **Ustaw jako projekt startowy**.


## <a name="configure-the-custom-hsm-stub-code"></a>Konfigurowanie niestandardowego kodu szczątkowego modułu HSM

Informacje dotyczące współpracy z rzeczywistym bezpiecznym magazynem opartym na sprzęcie różnią się w zależności od sprzętu. W efekcie łańcuchy certyfikatów używane przez symulowane urządzenia w tym samouczku będą stałe w niestandardowym kodzie stub modułu HSM. W rzeczywistym scenariuszu łańcuch certyfikatów będzie przechowywany w rzeczywistym sprzęcie HSM, aby zapewnić lepsze zabezpieczenia informacji poufnych. Metody podobne do metod zastępczych użytych w tym przykładzie zostałyby następnie wdrożone w celu odczytania wpisów tajnych z tego magazynu sprzętowego. 

Mimo że sprzęt modułu HSM nie jest wymagany, zaleca się ochronę poufnych informacji, takich jak klucz prywatny certyfikatu. Jeśli rzeczywisty moduł HSM został wywołany przez przykład, klucz prywatny nie będzie obecny w kodzie źródłowym. Klucz w kodzie źródłowym ujawnia klucz osobie, która może wyświetlić kod. Ta czynność jest wykonywana tylko w tym artykule, aby ułatwić uczenie się.

Aby zaktualizować niestandardowy kod stub modułu HSM w celu symulowania tożsamości urządzenia z IDENTYFIKATORem `custom-hsm-device-01` , wykonaj następujące czynności:

1. W Eksplorator rozwiązań dla programu Visual Studio przejdź do **Provisioning_Samples > custom_hsm_example > pliki źródłowe** i otwórz plik *custom_hsm_example. c*.

2. Zaktualizuj wartość ciągu `COMMON_NAME` stałej ciągu przy użyciu nazwy pospolitej użytej podczas generowania certyfikatu urządzenia.

    ```c
    static const char* const COMMON_NAME = "custom-hsm-device-01";
    ```

3. W tym samym pliku należy zaktualizować wartość ciągu `CERTIFICATE` stałego ciągu przy użyciu tekstu łańcucha certyfikatu zapisanego w *./certs/New-Device-01-Full-Chain.CERT.pem* po wygenerowaniu certyfikatów.

    Składnia tekstu certyfikatu musi być zgodna z wzorcem poniżej bez dodatkowych spacji ani analizy wykonywanej przez program Visual Studio.

    ```c
    // <Device/leaf cert>
    // <intermediates>
    // <root>
    static const char* const CERTIFICATE = "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFPDCCAySgAwIBAgIBATANBgkqhkiG9w0BAQsFADAqMSgwJgYDVQQDDB9BenVy\n"
        ...
    "MTEyMjIxMzAzM1owNDEyMDAGA1UEAwwpQXp1cmUgSW9UIEh1YiBJbnRlcm1lZGlh\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----";        
    ```

    Poprawne aktualizowanie tej wartości ciągu w tym kroku może być bardzo żmudnyme i podlega błędu. Aby wygenerować poprawną składnię w wierszu polecenia narzędzia Git bash, skopiuj i wklej następujące polecenia powłoki bash do wiersza poleceń narzędzia Git bash i naciśnij klawisz **Enter**. Te polecenia generują składnię dla `CERTIFICATE` stałej wartości ciągu.

    ```Bash
    input="./certs/new-device-01-full-chain.cert.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    Skopiuj i wklej tekst certyfikatu wyjściowego dla nowej wartości stałej. 


4. W tym samym pliku wartość ciągu `PRIVATE_KEY` stałej musi również być aktualizowana przy użyciu klucza prywatnego certyfikatu urządzenia.

    Składnia tekstu klucza prywatnego musi być zgodna ze wzorcem, bez dodatkowych spacji ani analizy wykonywanej przez program Visual Studio.

    ```c
    static const char* const PRIVATE_KEY = "-----BEGIN RSA PRIVATE KEY-----\n"
    "MIIJJwIBAAKCAgEAtjvKQjIhp0EE1PoADL1rfF/W6v4vlAzOSifKSQsaPeebqg8U\n"
        ...
    "X7fi9OZ26QpnkS5QjjPTYI/wwn0J9YAwNfKSlNeXTJDfJ+KpjXBcvaLxeBQbQhij\n"
    "-----END RSA PRIVATE KEY-----";
    ```

    Poprawne aktualizowanie tej wartości ciągu w tym kroku może być również bardzo żmudnym i podlega błędu. Aby wygenerować poprawną składnię w wierszu polecenia narzędzia Git bash, skopiuj i wklej następujące polecenie powłoki bash, a następnie naciśnij klawisz **Enter**. Te polecenia generują składnię dla `PRIVATE_KEY` stałej wartości ciągu.

    ```Bash
    input="./private/new-device-01.key.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    Skopiuj i wklej tekst danych wyjściowych klucza prywatnego dla nowej wartości stałej. 

5. Zapisz *custom_hsm_example. c*.

6. W menu programu Visual Studio wybierz kolejno opcje **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić rozwiązanie. Po wyświetleniu monitu o ponowne skompilowanie projektu wybierz pozycję **tak** , aby ponownie skompilować projekt przed uruchomieniem.

    Poniżej przedstawiono przykładowe symulowane urządzenie `custom-hsm-device-01` , które zostało pomyślnie uruchomione, i nawiązanie połączenia z usługą aprowizacji. Urządzenie zostało przypisane do centrum IoT Hub i zarejestrowano:

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-01
    Press enter key to exit:
    ```

7. W portalu przejdź do centrum IoT Hub połączonego z usługą aprowizacji, a następnie wybierz kartę **urządzenia IoT** . Po pomyślnej aprowizacji urządzenia X. 509 w centrum identyfikator urządzenia jest wyświetlany w bloku **urządzenia IoT** z opcją *stan* jako **włączone**. Może być konieczne naciśnięcie przycisku **Odśwież** w górnej części ekranu. 

    ![Niestandardowe urządzenie HSM jest zarejestrowane w usłudze IoT Hub](./media/tutorial-custom-hsm-enrollment-group-x509/hub-provisioned-custom-hsm-x509-device.png) 

8. Powtórz kroki 1-7 dla drugiego urządzenia z IDENTYFIKATORem urządzenia `custom-hsm-device-02` . Na tym urządzeniu należy używać następujących wartości:

    |   Opis                 |  Wartość  |
    | :---------------------------- | :--------- |
    | `COMMON_NAME`                 | `"custom-hsm-device-02"` |
    | Pełny łańcuch certyfikatów        | Generuj tekst przy użyciu `input="./certs/new-device-02-full-chain.cert.pem"` |
    | Klucz prywatny                   | Generuj tekst przy użyciu `input="./private/new-device-02.key.pem"` |

    Poniżej przedstawiono przykładowe symulowane urządzenie `custom-hsm-device-02` , które zostało pomyślnie uruchomione, i nawiązanie połączenia z usługą aprowizacji. Urządzenie zostało przypisane do centrum IoT Hub i zarejestrowano:

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-02
    Press enter key to exit:
    ```


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu testowania i eksplorowaniu tego przykładowego klienta urządzenia wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego samouczka.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz usługę Device Provisioning. Otwórz przystawkę **Zarządzanie rejestracjami** dla usługi, a następnie wybierz kartę **grupy rejestracji** . Zaznacz pole wyboru obok *nazwy grupy* urządzeń utworzonej w tym samouczku i naciśnij przycisk **Usuń** w górnej części okienka. 
1. Kliknij pozycję **Certyfikaty** w usłudze DPS. Dla każdego przekazanego certyfikatu i zweryfikowanego w tym samouczku kliknij certyfikat, a następnie kliknij przycisk **Usuń** , aby go usunąć.
1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz swoje centrum IoT Hub. Otwórz **urządzenia IoT** dla swojego centrum. Zaznacz pole wyboru obok *identyfikatora urządzenia* urządzenia zarejestrowanego w tym samouczku. Kliknij przycisk **Usuń** w górnej części okienka.

## <a name="next-steps"></a>Następne kroki

W tym samouczku poznasz urządzenie X. 509 z niestandardowym modułem HSM do centrum IoT Hub. Aby dowiedzieć się, jak udostępnić urządzenia IoT w wielu centrach, przejdź do następnego samouczka. 

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie urządzeń w centrach IoT o zrównoważonym obciążeniu](tutorial-provision-multiple-hubs.md)