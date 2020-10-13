---
title: Samouczek dotyczący korzystania z niestandardowych zasad alokacji przy użyciu usługi Azure IoT Hub Device Provisioning Service (DPS)
description: Samouczek dotyczący korzystania z niestandardowych zasad alokacji przy użyciu usługi Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: e20183356655668750cb1450338d4c8af1ee2d8c
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951710"
---
# <a name="tutorial-use-custom-allocation-policies-with-device-provisioning-service-dps"></a>Samouczek: używanie niestandardowych zasad alokacji z usługą Device Provisioning Service (DPS)

Niestandardowe zasady alokacji dają większą kontrolę nad sposobem przypisywania urządzeń do centrum IoT Hub. Jest to realizowane przy użyciu niestandardowego kodu w [funkcji platformy Azure](../azure-functions/functions-overview.md) , która jest uruchamiana podczas aprowizacji, aby przypisać urządzenia do centrum IoT. Usługa Device Provisioning wywołuje kod funkcji platformy Azure, dostarczając wszystkie istotne informacje o urządzeniu i rejestracji. Kod funkcji jest wykonywany i zwraca informacje o usłudze IoT Hub używane do aprowizacji urządzenia.

Korzystając z niestandardowych zasad alokacji, należy zdefiniować własne zasady alokacji, jeśli zasady udostępniane przez usługę Device Provisioning nie spełniają wymagań danego scenariusza.

Na przykład może być konieczne przeanalizowanie certyfikatu używanego przez urządzenie podczas aprowizacji i przypisanie urządzenia do usługi IoT Hub na podstawie właściwości certyfikatu. Możesz też mieć informacje przechowywane w bazie danych dla urządzeń i muszą wysyłać zapytania do bazy danych, aby określić, które Centrum IoT ma być przypisane do urządzenia.

W tym artykule przedstawiono grupę rejestracji z niestandardowymi zasadami alokacji korzystającą z funkcji platformy Azure zapisaną w języku C# w celu aprowizacji urządzeń wyskakujących przy użyciu kluczy symetrycznych. Wszystkie urządzenia nierozpoznane jako urządzenie wyskakujące nie będą obsługiwane w centrum IoT Hub.

Urządzenia będą żądały aprowizacji przy użyciu przykładowego kodu aprowizacji zawartego w [zestawie SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c).


W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie nowego aplikacja funkcji platformy Azure do obsługi niestandardowej funkcji alokacji
> * Tworzenie nowej rejestracji grupowej przy użyciu funkcji platformy Azure dla niestandardowych zasad alokacji
> * Utwórz klucze urządzeń dla dwóch urządzeń
> * Konfigurowanie środowiska deweloperskiego na przykład kodu urządzenia z [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c)
> * Uruchom urządzenia i sprawdź, czy są one obsługiwane zgodnie z niestandardowymi zasadami alokacji


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* W tym artykule przyjęto założenie, że wykonano kroki opisane w temacie [konfigurowanie IoT Hub Device Provisioning Service z Azure Portal,](./quick-setup-auto-provision.md) aby utworzyć wystąpienie IoT Hub i DPS.

* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

* W środowisku deweloperskim systemu Windows [program Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 z włączonym obciążeniem ["Programowanie aplikacji klasycznych w języku C++"](https://docs.microsoft.com/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) jest wymagany. Obsługiwane są również programy Visual Studio 2015 i Visual Studio 2017.

* W systemie Linux lub macOS zapoznaj się z odpowiednią sekcją w sekcji [Przygotowywanie środowiska deweloperskiego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w dokumentacji [zestawu SDK usługi Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) .


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-custom-allocation-function"></a>Utwórz niestandardową funkcję alokacji

W tej sekcji utworzysz funkcję platformy Azure, która implementuje niestandardowe zasady alokacji. Ta funkcja decyduje o tym, czy urządzenie powinno być zarejestrowane na IoT Hub w zależności od tego, czy jego identyfikator rejestracji zawiera prefiks ciągu **contoso-wyskakujący**.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Na stronie głównej wybierz pozycję **+ Utwórz zasób**.

2. W polu Wyszukaj w *portalu Marketplace* wpisz "aplikacja funkcji". Z listy rozwijanej wybierz pozycję **aplikacja funkcji**, a następnie wybierz pozycję **Utwórz**.

3. Na stronie **aplikacja funkcji** Utwórz na karcie **podstawowe** wprowadź następujące ustawienia dla nowej aplikacji funkcji i wybierz pozycję **Przegląd + Utwórz**:

    **Subskrypcja**: Jeśli masz wiele subskrypcji i nie wybrano żądanej subskrypcji, wybierz subskrypcję, której chcesz użyć.

    **Grupa zasobów**: to pole pozwala utworzyć nową grupę zasobów lub wybrać istniejącą, aby zawierała aplikację funkcji. Wybierz tę samą grupę zasobów, która zawiera Centrum IoT utworzone do testowania wcześniej, na przykład **TestResources**. Dzięki umieszczeniu wszystkich powiązanych zasobów w jednej grupie można nimi zarządzać jednocześnie.

    **Nazwa aplikacja funkcji**: Wprowadź unikatową nazwę aplikacji funkcji. W tym przykładzie zastosowano **contoso-Function-App**.

    **Publikowanie**: Sprawdź, czy **kod** jest zaznaczony.

    **Stos środowiska uruchomieniowego**: wybierz z listy rozwijanej pozycję **.NET Core** .

    **Region**: Wybierz ten sam region, w którym znajduje się grupa zasobów. W tym przykładzie zastosowano **zachodnie stany USA**.

    > [!NOTE]
    > Domyślnie Application Insights jest włączona. Application Insights nie jest to konieczne w tym artykule, ale może pomóc w zrozumieniu i zbadaniu problemów występujących w ramach alokacji niestandardowej. Jeśli wolisz, możesz wyłączyć Application Insights, wybierając kartę **monitorowanie** , a następnie pozycję **nie** , aby **włączyć Application Insights**.

    ![Utwórz aplikacja funkcji platformy Azure, aby hostować funkcję niestandardowej alokacji](./media/tutorial-custom-allocation-policies/create-function-app.png)

4. Na stronie **Podsumowanie** wybierz pozycję **Utwórz** , aby utworzyć aplikację funkcji. Wdrożenie może potrwać kilka minut. Po zakończeniu wybierz pozycję **Przejdź do zasobu**.

5. W lewym okienku w obszarze **funkcje** kliknij pozycję **funkcje** , a następnie **+ Dodaj** , aby dodać nową funkcję.

6. Na stronie szablony wybierz kafelek **wyzwalacz http** , a następnie wybierz pozycję **Utwórz funkcję**. Zostanie utworzona funkcja o nazwie **HttpTrigger1** , a w portalu zostanie wyświetlona strona przegląd dla swojej funkcji.

7. Kliknij pozycję **Code + test** , aby uzyskać nową funkcję. W portalu jest wyświetlana zawartość pliku z kodem **Run. CSX** . 

8. Zastąp kod funkcji **HttpTrigger1** następującym kodem i wybierz pozycję **Zapisz**. Niestandardowy kod alokacji jest gotowy do użycia.

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster 
                if (regId.Contains("contoso-toaster"))
                {
                    //Log IoT hubs configured for the enrollment
                    foreach(string hubString in hubs)
                    {
                        log.LogInformation("linkedHub : " + hubString);
                    }

                    obj.iotHubHostName = hubs[0];
                    log.LogInformation("Selected hub : " + obj.iotHubHostName);
                }
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
    }
    ```

9. Po prostu poniżej dolnej części pliku z kodem **Run. CSX** kliknij pozycję **Logs (dzienniki** ), aby monitorować rejestrowanie z funkcji alokacji niestandardowej. 


## <a name="create-the-enrollment"></a>Utwórz rejestrację

W tej sekcji utworzysz nową grupę rejestracji, która używa niestandardowych zasad alokacji. Dla uproszczenia w tym artykule jest stosowane [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md) z rejestracją. Aby lepiej zabezpieczyć rozwiązanie, należy rozważyć użycie [zaświadczania certyfikatu X. 509](concepts-x509-attestation.md) z łańcuchem zaufania.

1. Nadal na [Azure Portal](https://portal.azure.com)Otwórz swoją usługę aprowizacji.

2. W lewym okienku wybierz pozycję **Zarządzaj rejestracjami** , a następnie wybierz przycisk **Dodaj grupę rejestracji** w górnej części strony.

3. W obszarze **Dodaj grupę rejestracji**wprowadź informacje w poniższej tabeli, a następnie kliknij przycisk **Zapisz** .

    | Pole | Opis i/lub Sugerowana wartość |
    | :---- | :----------------------------- |
    | **Nazwa grupy** | Wprowadź **contoso-Custom-przydzieloną-Devices** |
    | **Typ zaświadczania** | Wybierz **klucz symetryczny** |
    | **Automatycznie Generuj klucze** | To pole wyboru powinno być już zaznaczone. |
    | **Wybierz sposób przypisywania urządzeń do centrów** | Wybieranie **niestandardowych (Korzystanie z funkcji platformy Azure)** |
    | **Wybierz centra IoT, do których ta grupa może być przypisana** | Wybierz Centrum IoT utworzone wcześniej po zakończeniu przewodnika Szybki Start. |
    | **Wybierz funkcję platformy Azure** | Wybierz subskrypcję zawierającą utworzoną aplikację funkcji. Następnie wybierz pozycję **contoso-Function-App** i **HttpTrigger1** dla funkcji. |

    ![Dodaj grupę rejestracji niestandardowej alokacji na potrzeby zaświadczania klucza symetrycznego](./media/tutorial-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Po zapisaniu rejestracji Otwórz ją ponownie i zanotuj **klucz podstawowy**. Musisz najpierw zapisać rejestrację, aby generować klucze. Ten podstawowy klucz symetryczny zostanie użyty do wygenerowania unikatowych kluczy urządzeń dla urządzeń, które próbują później. 

## <a name="derive-unique-device-keys"></a>Utwórz unikatowe klucze urządzeń

Urządzenia nie używają podstawowego klucza symetrycznego bezpośrednio. Zamiast tego należy użyć klucza podstawowego do uzyskania klucza urządzenia dla każdego urządzenia. W tej sekcji utworzysz dwa unikatowe klucze urządzeń. Jeden klucz zostanie użyty dla symulowanego urządzenia wyskakującego. Drugi klucz zostanie użyty dla symulowanego urządzenia pompy termicznej. Wygenerowane klucze umożliwią obu urządzeniom próbę rejestracji. Tylko jeden identyfikator rejestracji urządzenia będzie miał prawidłowy sufiks, który zostanie zaakceptowany przez niestandardowy kod zasad alokacji. W związku z tym, zostanie zaakceptowany i inne odrzucone

Aby uzyskać klucz urządzenia, należy użyć klucza symetrycznego zanotowanego wcześniej w celu obliczenia wartości [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) identyfikatora rejestracji urządzenia dla każdego urządzenia i przekonwertowania wyniku na format Base64. Aby uzyskać więcej informacji na temat tworzenia pochodnych kluczy urządzeń przy użyciu grup rejestracji, zobacz sekcję rejestracje grupowe [zaświadczania klucza symetrycznego](concepts-symmetric-key-attestation.md).

Aby zapoznać się z przykładem w tym artykule, użyj następujących dwóch identyfikatorów rejestracji urządzeń z poniższym kodem, aby obliczyć klucz urządzenia dla obu urządzeń:

* **contoso-wyskakujący 007**
* **contoso-heatpump-088**

Zastąp wartość zmiennej **Key** **kluczem podstawowym** zanotowaną wcześniej po utworzeniu grupy rejestracyjnej. Wartość klucza i dane wyjściowe podane w poniższym kodzie jest tylko przykładem.

#### <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Jeśli używasz stacji roboczej z systemem Windows, możesz użyć programu PowerShell, aby wygenerować pochodny klucz urządzenia, jak pokazano w poniższym przykładzie.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='contoso-toaster-007'
$REG_ID2='contoso-heatpump-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($key)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

#### <a name="bash"></a>[Bash](#tab/bash)

Jeśli używasz stacji roboczej z systemem Linux, możesz użyć OpenSSL, aby wygenerować pochodne klucze urządzenia, jak pokazano w poniższym przykładzie bash.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=contoso-toaster-007
REG_ID2=contoso-heatpump-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Przygotowywanie środowiska deweloperskiego dla zestawu SDK języka C usługi Azure IoT

Urządzenia będą żądały aprowizacji przy użyciu przykładowego kodu aprowizacji zawartego w [zestawie SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c).

W tej sekcji przygotowano środowisko programistyczne używane do kompilowania [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c). Zestaw SDK zawiera przykładowy kod dla symulowanego urządzenia. To urządzenie symulowane podejmie próbę aprowizacji podczas sekwencji rozruchu urządzenia.

Ta sekcja jest ukierunkowana na stację roboczą opartą na systemie Windows. Aby zapoznać się z przykładem systemu Linux, zobacz Konfigurowanie maszyn wirtualnych w temacie [jak zapewnić obsługę wielodostępności](how-to-provision-multitenant.md).

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

4. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu. Uruchom następujące polecenia z `azure-iot-sdk-c` katalogu:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Uruchom następujące polecenie, które utworzy wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego. Rozwiązanie programu Visual Studio dla symulowanego urządzenia zostanie wygenerowane w katalogu `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Jeśli `cmake` nie znajdziesz kompilatora języka C++, może wystąpić błąd kompilacji podczas wykonywania polecenia. Jeśli tak się stanie, spróbuj uruchomić polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

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

## <a name="simulate-the-devices"></a>Symulowanie urządzeń

W tej sekcji zostanie zaktualizowany przykład aprowizacji o nazwie **Prov \_ dev \_ Client \_ Sample** znajdujący się w wcześniej skonfigurowanym zestawie SDK języka C usługi Azure IoT.

Ten przykładowy kod symuluje sekwencję rozruchu urządzenia, która wysyła żądanie aprowizacji do wystąpienia usługi Device Provisioning. Sekwencja rozruchu spowoduje, że urządzenie wyskakujące zostanie rozpoznane i przypisane do usługi IoT Hub przy użyciu niestandardowych zasad alokacji.

1. W witrynie Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning Service, a następnie zapisz wartość **_Zakres identyfikatorów_**.

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. W programie Visual Studio Otwórz plik rozwiązania **azure_iot_sdks. sln** , który został wygenerowany przez uruchomienie CMAKE wcześniej. Plik rozwiązania powinien znajdować się w następującej lokalizacji:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
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

6. W `main()` funkcji Znajdź wywołanie `Prov_Device_Register_Device()` . Tuż przed tym wywołaniem Dodaj następujące wiersze kodu, które używają [`Prov_Device_Set_Provisioning_Payload()`](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/prov-device-client-h/prov-device-set-provisioning-payload) do przekazywania niestandardowego ładunku JSON podczas aprowizacji. Może to służyć do zapewnienia dodatkowych informacji o niestandardowych funkcjach alokacji. Może to również służyć do przekazywania typu urządzenia zamiast badania identyfikatora rejestracji.

    ```c
    // An example custom payload
    const char* custom_json_payload = "{\"MyDeviceFirmwareVersion\":\"12.0.2.5\",\"MyDeviceProvisioningVersion\":\"1.0.0.0\"}";

    prov_device_result = Prov_Device_Set_Provisioning_Payload(prov_device_handle, custom_json_payload);
    if (prov_device_result != PROV_DEVICE_RESULT_OK)
    {
        (void)printf("\r\nFailure setting provisioning payload: %s\r\n", MU_ENUM_TO_STRING(PROV_DEVICE_RESULT, prov_device_result));
    }
    ```

7. Kliknij prawym przyciskiem myszy projekt **prov\_dev\_client\_sample**, a następnie wybierz pozycję **Ustaw jako projekt startowy**.

### <a name="simulate-the-contoso-toaster-device"></a>Symulowanie urządzenia wyskakującego firmy Contoso

1. Aby zasymulować urządzenie wyskakujące, Znajdź wywołanie `prov_dev_set_symmetric_key_info()` w **Prov \_ dev \_ Client \_ Sample. c** , które jest oznaczone jako komentarz.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Usuń komentarz wywołania funkcji i Zastąp wartości symboli zastępczych (włącznie z nawiasami ostrymi) IDENTYFIKATORem rejestracji wyskakującego powiadomienia i wygenerowanym wcześniej kluczem urządzenia. Wartość klucza **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** pokazano poniżej jest pokazana jako przykład.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-toaster-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Zapisz plik.

2. W menu programu Visual Studio wybierz kolejno opcje **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić rozwiązanie. W wierszu polecenia, aby ponownie skompilować projekt, wybierz opcję **tak**, aby ponownie skompilować projekt przed uruchomieniem.

    Następujący tekst jest przykładem rejestrowania danych wyjściowych z niestandardowym kodem funkcji alokacji uruchomionego dla wyskakującego urządzenia. Zwróć uwagę na to, że centrum zostało pomyślnie wybrane dla urządzenia wyskakującego. Zwróć również uwagę, `payload` że element członkowski zawiera niestandardową zawartość JSON dodaną do kodu. Jest to możliwe, aby Twój kod był używany w ramach programu `deviceRuntimeContext` .

    To rejestrowanie jest dostępne po kliknięciu pozycji **dzienniki** w obszarze kod funkcji w portalu:

    ```cmd
    2020-09-23T11:44:37.505 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=4596d45e-086f-4e86-929b-4a02814eee40)
    2020-09-23T11:44:41.380 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:44:41.381 [Information] Request.Body:...
    2020-09-23T11:44:41.381 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-toaster-007","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:44:41.687 [Information] linkedHub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Selected hub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Response
    2020-09-23T11:44:41.688 [Information] {"iotHubHostName":"contoso-toasters-hub-1098.azure-devices.net"}
    2020-09-23T11:44:41.689 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=4596d45e-086f-4e86-929b-4a02814eee40, Duration=4347ms)    
    ```

    Następujące przykładowe dane wyjściowe urządzenia pokazują, że symulowane urządzenie wyskakujące pomyślnie przeprowadził rozruch i nawiązać połączenie z wystąpieniem usługi aprowizacji do przypisania do centrum IoT Hub przez niestandardowe zasady alokacji:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: contoso-toaster-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Symulowanie urządzenia pompy termicznej firmy Contoso

1. Aby zasymulować urządzenie pompy cieplnej, zaktualizuj wywołanie do `prov_dev_set_symmetric_key_info()` programu w **Prov \_ dev \_ Client \_ Sample. c** ponownie z identyfikatorem rejestracji pompy cieplnej i wygenerowanym wcześniej kluczem urządzenia. Wartość klucza **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** pokazana poniżej jest również podana jako przykład.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-heatpump-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Zapisz plik.

2. W menu programu Visual Studio wybierz kolejno opcje **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić rozwiązanie. W wierszu polecenia, aby ponownie skompilować projekt, wybierz opcję **tak** , aby ponownie skompilować projekt przed uruchomieniem.

    Następujący tekst to przykład rejestrowania danych wyjściowych z niestandardowego kodu funkcji przydziału uruchomionego dla urządzenia pompy termicznej. Niestandardowe zasady alokacji odrzucają tę rejestrację z błędem HTTP 400 nieprawidłowym żądaniem. Zwróć uwagę, `payload` że element członkowski zawiera niestandardową zawartość JSON dodaną do kodu. Jest to możliwe, aby Twój kod był używany w ramach programu `deviceRuntimeContext` .

    To rejestrowanie jest dostępne po kliknięciu pozycji **dzienniki** w obszarze kod funkcji w portalu:

    ```cmd
    2020-09-23T11:50:23.652 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68)
    2020-09-23T11:50:23.653 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:50:23.654 [Information] Request.Body:...
    2020-09-23T11:50:23.654 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-heatpump-088","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:50:23.654 [Information] Unknown device registration
    2020-09-23T11:50:23.654 [Information] Response
    2020-09-23T11:50:23.654 [Information] Unrecognized device registration.
    2020-09-23T11:50:23.655 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68, Duration=11ms)
    ```

    Poniższe przykładowe dane wyjściowe urządzenia pokazują symulowane urządzenie pompy cieplnej, które umożliwia rozruch i nawiązywanie połączenia z wystąpieniem usługi aprowizacji w celu próby rejestracji w usłudze IoT Hub przy użyciu niestandardowych zasad alokacji. To nie powiodło się z powodu błędu ( `Custom allocation failed with status code: 400` ), ponieważ zasady alokacji niestandardowej zostały zaprojektowane tak, aby zezwalały na wyskakujące urządzenia:


    ```cmd
    Provisioning API Version: 1.3.7
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_prov_transport_process_json_reply Line:658 Provisioning Failure: OperationId: 4.eb89f3e8407a3711.2525bd34-02e9-4e91-a9c0-4dbc4ad5de66 - Date: 2020-09-23T17:05:58.2363145Z - Msg: Custom allocation failed with status code: 400
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_transport_mqtt_common.c Func:_prov_transport_common_mqtt_dowork Line:1014 Unable to process registration reply.
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_on_transport_registration_data Line:770 Failure retrieving data from the provisioning service
    
    Failure registering device: PROV_DEVICE_RESULT_DEV_AUTH_ERROR
    Press enter key to exit:    
    ```
    
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z zasobami utworzonymi w tym artykule, możesz je pozostawić. Jeśli nie planujesz kontynuowania korzystania z zasobów, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w tym artykule, aby uniknąć niepotrzebnych opłat.

W tym artykule przyjęto założenie, że utworzono wszystkie zasoby z tego artykułu zgodnie z instrukcjami w tej samej grupie zasobów o nazwie **contoso-US-Resource-Group**.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub niewłaściwej grupy zasobów. Jeśli usługa IoT Hub została utworzona wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, zamiast usuwać całą grupę zasobów, usuń tylko zasób usługi IoT Hub.
>

Aby usunąć grupę zasobów według nazwy:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.

2. W polu tekstowym **Filtruj według nazwy..** . Wpisz nazwę grupy zasobów zawierającej Twoje zasoby, **contoso-US-Resource-Group**. 

3. Z prawej strony grupy zasobów na liście wynik wybierz pozycję **...** , a następnie **Usuń grupę zasobów**.

4. Zostanie wyświetlony monit o potwierdzenie usunięcia grupy zasobów. Wpisz ponownie nazwę grupy zasobów, aby potwierdzić, a następnie wybierz pozycję **Usuń**. Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z bardziej szczegółowymi zasadami przydziału niestandardowego, zobacz [jak używać niestandardowych zasad alokacji](how-to-use-custom-allocation-policies.md).
* Aby dowiedzieć się więcej, zobacz temat [IoT Hub ponowne Inicjowanie obsługi administracyjnej urządzeń](concepts-device-reprovision.md).
* Aby dowiedzieć się więcej, zobacz Jak anulować obsługę [administracyjną urządzeń, które były wcześniej inicjowane](how-to-unprovision-devices.md).
