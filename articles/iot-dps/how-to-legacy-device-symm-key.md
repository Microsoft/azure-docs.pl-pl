---
title: Inicjowanie obsługi administracyjnej urządzeń przy użyciu kluczy symetrycznych — platforma Azure IoT Hub Device Provisioning Service
description: Jak używać kluczy symetrycznych do udostępniania urządzeń za pomocą wystąpienia usługi Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.openlocfilehash: 5d193d30428d24ccf65c3f70885192acad2fdc9f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228333"
---
# <a name="how-to-provision-devices-using-symmetric-key-enrollment-groups"></a>Jak udostępnić urządzenia przy użyciu grup rejestracji kluczy symetrycznych

W tym artykule pokazano, jak bezpiecznie zainicjować obsługę wielu urządzeń z kluczami symetrycznymi w jednym IoT Hub przy użyciu grupy rejestracji.

Niektóre urządzenia mogą nie mieć certyfikatu, modułu TPM ani żadnej innej funkcji zabezpieczeń, której można użyć do bezpiecznego zidentyfikowania urządzenia. Usługa Device Provisioning obejmuje [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md). Zaświadczenie klucza symetrycznego może służyć do identyfikowania urządzenia na podstawie unikatowych informacji, takich jak adres MAC lub numer seryjny.

Jeśli można łatwo zainstalować [sprzętowy moduł zabezpieczeń (HSM)](concepts-service.md#hardware-security-module) i certyfikat, to może być lepszym rozwiązaniem do identyfikowania i aprowizacji urządzeń. Użycie modułu HSM umożliwi obejście aktualizacji kodu wdrożonego na wszystkich urządzeniach i nie ma klucza tajnego osadzonego w obrazach urządzeń. W tym artykule założono, że żaden moduł HSM lub certyfikat nie jest wykonalną opcją. Jednak zakłada się, że masz pewną metodę aktualizowania kodu urządzenia, aby używać usługi Device Provisioning do udostępniania tych urządzeń. 

W tym artykule przyjęto również założenie, że aktualizacja urządzenia odbywa się w bezpiecznym środowisku, aby zapobiec nieautoryzowanemu dostępowi do klucza grupy głównej lub klucza urządzenia pochodnego.

Ten artykuł został opracowany z myślą o stacjach roboczych z systemem Windows. Jednak opisane procedury można wykonać także w systemie Linux. Aby uzyskać przykład dla systemu Linux, zobacz [Aprowizowanie pod kątem wielu dzierżaw](how-to-provision-multitenant.md).

> [!NOTE]
> Przykład użyty w tym artykule został zapisany w C. Istnieje również dostępny [przykład klucza symetrycznego inicjowania obsługi urządzenia w języku C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) . Aby użyć tego przykładu, Pobierz lub Sklonuj repozytorium [Azure-IoT-Samples-CSharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) i postępuj zgodnie z instrukcjami w wierszu w przykładowym kodzie. Korzystając z instrukcji przedstawionych w tym artykule, można utworzyć grupę rejestracji klucza symetrycznego przy użyciu portalu i znaleźć zakres identyfikatorów oraz klucze podstawowe i pomocnicze, które są potrzebne do uruchomienia przykładu. Możesz również utworzyć rejestracje indywidualne przy użyciu przykładu.

## <a name="prerequisites"></a>Wymagania wstępne

* Zakończenie [konfigurowania IoT Hub Device Provisioning Service przy użyciu Azure Portal](./quick-setup-auto-provision.md) przewodnika Szybki Start.

Poniższe wymagania wstępne dotyczą środowiska projektowego systemu Windows. W systemie Linux lub macOS zapoznaj się z odpowiednią sekcją w sekcji [Przygotowywanie środowiska deweloperskiego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w dokumentacji zestawu SDK.

* [Program Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 z włączonym obciążeniem ["Programowanie aplikacji klasycznych w języku C++"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) . Obsługiwane są również programy Visual Studio 2015 i Visual Studio 2017.

* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

## <a name="overview"></a>Omówienie

Unikatowy identyfikator rejestracji zostanie zdefiniowany dla każdego urządzenia na podstawie informacji identyfikujących to urządzenie. Na przykład adres MAC lub numer seryjny.

Grupa rejestracyjna korzystająca z [zaświadczania klucza symetrycznego](concepts-symmetric-key-attestation.md) zostanie utworzona przy użyciu usługi Device Provisioning. Grupa rejestracji będzie zawierać klucz główny grupy. Ten klucz główny będzie używany do mieszania każdego unikatowego identyfikatora rejestracji w celu utworzenia unikatowego klucza urządzenia dla każdego urządzenia. Urządzenie użyje tego pochodnego klucza urządzenia ze swoim unikatowym IDENTYFIKATORem rejestracji w celu zaświadczenia usługi Device Provisioning i przypisania jej do centrum IoT Hub.

Kod urządzenia opisany w tym artykule będzie postępować zgodnie z tym samym wzorcem, co [Przewodnik Szybki Start: Inicjowanie symulowanego urządzenia przy użyciu kluczy symetrycznych](quick-create-simulated-device-symm-key.md). Kod symuluje urządzenie przy użyciu przykładu z [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c). Symulowane urządzenie zostanie zatwierdzona z grupą rejestracji zamiast rejestracji indywidualnej, jak pokazano w przewodniku Szybki Start.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Przygotowywanie środowiska deweloperskiego dla zestawu SDK języka C usługi Azure IoT

W tej sekcji przygotujesz środowisko deweloperskie używane do opracowania [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c). 

Zestaw SDK zawiera przykładowy kod dla symulowanego urządzenia. To urządzenie symulowane podejmie próbę aprowizacji podczas sekwencji rozruchu urządzenia.

1. Pobierz [system kompilacji CMAKE](https://cmake.org/download/).

    Ważne jest, aby wstępnie wymagane składniki (program Visual Studio oraz pakiet roboczy „Programowanie aplikacji klasycznych w języku C++”) były zainstalowane na tym komputerze **przed** uruchomieniem `CMake` instalacji. Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

2. Znajdź nazwę tagu dla [najnowszej wersji](https://github.com/Azure/azure-iot-sdk-c/releases/latest) zestawu SDK.

3. Otwórz wiersz polecenia lub powłokę Git Bash. Uruchom następujące polecenia, aby sklonować najnowszą wersję repozytorium [usługi Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) w witrynie GitHub. Użyj znacznika znalezionego w poprzednim kroku jako wartości `-b` parametru:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

4. Utwórz `cmake` podkatalog w katalogu głównym repozytorium git i przejdź do tego folderu. Uruchom następujące polecenia z `azure-iot-sdk-c` katalogu:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Uruchom następujące polecenie, które utworzy wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego. Rozwiązanie programu Visual Studio dla symulowanego urządzenia zostanie wygenerowane w katalogu `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Jeśli program `cmake` nie znajdzie kompilatora języka C++, mogą występować błędy kompilacji podczas uruchamiania powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Gdy kompilacja zakończy się powodzeniem, kilka ostatnich wierszy danych wyjściowych będzie wyglądać podobnie do następujących danych wyjściowych:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-a-symmetric-key-enrollment-group"></a>Tworzenie grupy rejestracji kluczy symetrycznych

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz wystąpienie usługi Device Provisioning Service.

2. Wybierz kartę **Zarządzanie rejestracjami** , a następnie kliknij przycisk **Dodaj grupę rejestracji** w górnej części strony. 

3. W obszarze **Dodaj grupę rejestracji** Wprowadź poniższe informacje, a następnie kliknij przycisk **Zapisz** .

   - **Nazwa grupy**: wprowadź **mylegacydevices**.

   - **Typ zaświadczania**: Wybierz **klucz symetryczny**.

   - **Automatycznie generuj klucze**: zaznacz to pole wyboru.

   - **Wybierz sposób przypisywania urządzeń do centrów**: wybierz opcję **Konfiguracja statyczna** , aby przypisać do określonego centrum.

   - **Wybierz centra IoT, do których ta grupa może być przypisana**: wybierz jedno z centrów.

     ![Dodaj grupę rejestracji na potrzeby zaświadczania klucza symetrycznego](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Po zapisaniu rejestracji zostanie wygenerowany **klucz podstawowy** i **klucz pomocniczy**. Zostaną one dodane do wpisu rejestracji. Twoja grupa rejestracji kluczy symetrycznych jest wyświetlana jako **mylegacydevices** w kolumnie *Nazwa grupy* na karcie *grupy rejestracji* . 

    Otwórz rejestrację i skopiuj wartość wygenerowanego **klucza podstawowego**. Ten klucz jest kluczem grupy głównej.


## <a name="choose-a-unique-registration-id-for-the-device&quot;></a>Wybierz unikatowy identyfikator rejestracji dla urządzenia

Unikatowy identyfikator rejestracji musi być zdefiniowany, aby można było zidentyfikować każde urządzenie. Możesz użyć adresu MAC, numeru seryjnego lub wszelkich unikatowych informacji z urządzenia. 

W tym przykładzie używamy kombinacji adresu MAC i numeru seryjnego tworzącego następujący ciąg dla identyfikatora rejestracji.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Utwórz unikatowe identyfikatory rejestracji dla każdego urządzenia. Prawidłowe znaki to małe litery alfanumeryczne i myślnik (&quot;-").


## <a name="derive-a-device-key"></a>Utwórz klucz urządzenia 

Aby wygenerować klucze urządzeń, użyj klucza głównego grupy rejestracji, aby obliczyć [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) identyfikatora rejestracji dla każdego urządzenia. Następnie wynik zostanie przekonwertowany do formatu Base64 dla każdego urządzenia.

> [!WARNING]
> Kod urządzenia dla każdego urządzenia powinien zawierać tylko odpowiedni pochodny klucz urządzenia dla tego urządzenia. Nie dodawaj klucza głównego grupy do kodu urządzenia. Złamany klucz główny ma możliwość naruszenia zabezpieczeń wszystkich uwierzytelnianych urządzeń.


# <a name="windows"></a>[Windows](#tab/windows)

Jeśli używasz stacji roboczej z systemem Windows, możesz użyć programu PowerShell, aby wygenerować pochodny klucz urządzenia, jak pokazano w poniższym przykładzie.

Zastąp wartość **klucza kluczem** **podstawowym** zanotowanym wcześniej.

Zastąp wartość **REG_ID** identyfikatorem rejestracji.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

# <a name="linux"></a>[Linux](#tab/linux)

Jeśli używasz stacji roboczej z systemem Linux, możesz użyć OpenSSL, aby wygenerować pochodny klucz urządzenia, jak pokazano w poniższym przykładzie.

Zastąp wartość **klucza kluczem** **podstawowym** zanotowanym wcześniej.

Zastąp wartość **REG_ID** identyfikatorem rejestracji.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---

Każde urządzenie używa pochodnego klucza urządzenia i unikatowego identyfikatora rejestracji do wykonywania zaświadczania klucza symetrycznego z grupą rejestracji podczas aprowizacji.



## <a name="create-a-device-image-to-provision"></a>Tworzenie obrazu urządzenia do aprowizacji

W tej sekcji zostanie zaktualizowany przykład aprowizacji o nazwie **Prov \_ dev \_ Client \_ Sample** znajdujący się w wcześniej skonfigurowanym zestawie SDK języka C usługi Azure IoT. 

Ten przykładowy kod symuluje sekwencję rozruchu urządzenia, która wysyła żądanie aprowizacji do wystąpienia usługi Device Provisioning. Sekwencja rozruchu spowoduje, że urządzenie zostanie rozpoznane i przypisane do centrum IoT, które zostało skonfigurowane w grupie rejestracji. Ta wartość zostanie wykonana dla każdego urządzenia, które będzie obsługiwane przy użyciu grupy rejestracji.

1. W witrynie Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning Service, a następnie zapisz wartość **_Zakres identyfikatorów_**.

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. W programie Visual Studio Otwórz plik rozwiązania **azure_iot_sdks. sln** , który został wygenerowany przez uruchomienie CMAKE wcześniej. Plik rozwiązania powinien znajdować się w następującej lokalizacji:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. W oknie *Eksplorator rozwiązań* programu Visual Studio przejdź do folderu **Provision\_Samples**. Rozwiń przykładowy projekt o nazwie **prov\_dev\_client\_sample**. Rozwiń pozycję **Pliki źródłowe**, a następnie otwórz plik **prov\_dev\_client\_sample.c**.

4. Znajdź stałą `id_scope` i zastąp jej wartość wcześniej skopiowaną wartością **Zakres identyfikatorów**. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Znajdź definicję funkcji `main()` w tym samym pliku. Upewnij się, że zmienna `hsm_type` jest ustawiona na `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, jak pokazano poniżej:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Znajdź wywołanie funkcji `prov_dev_set_symmetric_key_info()` w pliku **prov\_dev\_client\_sample.c**, które jest ujęte w komentarz.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Usuń komentarz wywołania funkcji i Zastąp wartości symboli zastępczych (łącznie z nawiasami ostrymi) unikatowym IDENTYFIKATORem rejestracji urządzenia i wygenerowanym kluczem urządzenia pochodnego.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Zapisz plik.

7. Kliknij prawym przyciskiem myszy projekt **prov\_dev\_client\_sample**, a następnie wybierz pozycję **Ustaw jako projekt startowy**. 

8. W menu programu Visual Studio wybierz kolejno opcje **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić rozwiązanie. W monicie o ponowne skompilowanie projektu kliknij przycisk **Tak**, aby ponownie skompilować projekt przed uruchomieniem.

    Następujące dane wyjściowe to przykład pomyślnego uruchomienia urządzenia symulowanego i połączenia z wystąpieniem usługi aprowizowania w celu przypisania do centrum IoT:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. W portalu przejdź do centrum IoT Hub, do którego zostało przypisane symulowane urządzenie, a następnie kliknij kartę **urządzenia IoT** . Po pomyślnej aprowizacji symulowanego centrum jego identyfikator urządzenia jest wyświetlany w bloku **urządzenia IoT** z opcją *stan* jako **włączone**. Być może trzeba będzie kliknąć przycisk **Odśwież** u góry strony. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Zagadnienia dotyczące zabezpieczeń

Należy pamiętać, że to pozostawia pochodny klucz urządzenia dołączony jako część obrazu dla każdego urządzenia, co nie jest zalecanym najlepszym rozwiązaniem w zakresie zabezpieczeń. Jest to jedną z przyczyn, dlaczego zabezpieczenia i łatwość użycia często są kompromisami. Należy w pełni sprawdzić zabezpieczenia urządzeń w oparciu o własne wymagania.


## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o ponownej aprowizacji, zobacz

> [!div class="nextstepaction"]
> [Pojęcia dotyczące ponownego inicjowania obsługi administracyjnej urządzenia IoT Hub](concepts-device-reprovision.md)

> [!div class="nextstepaction"]
> [Szybki start: aprowizowanie urządzenia symulowanego przy użyciu kluczy symetrycznych](quick-create-simulated-device-symm-key.md)

* Aby dowiedzieć się więcej na temat anulowania aprowizacji, zobacz

> [!div class="nextstepaction"]
> [Jak anulować obsługę administracyjną urządzeń, które były wcześniej inicjowane samoobsługowo](how-to-unprovision-devices.md)
