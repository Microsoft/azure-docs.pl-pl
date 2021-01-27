---
title: Niestandardowe zasady alokacji przy użyciu usługi Azure IoT Hub Device Provisioning Service
description: Jak używać niestandardowych zasad alokacji przy użyciu usługi Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/26/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 14a405dbab0460f841a5e9104dbfeff101568f44
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919211"
---
# <a name="how-to-use-custom-allocation-policies"></a>Jak używać niestandardowych zasad alokacji

Niestandardowe zasady alokacji dają większą kontrolę nad sposobem przypisywania urządzeń do centrum IoT Hub. Jest to realizowane za pomocą niestandardowego kodu w [funkcji platformy Azure](../azure-functions/functions-overview.md) w celu przypisywania urządzeń do centrum IoT. Usługa Device Provisioning wywołuje kod funkcji platformy Azure, dostarczając wszystkie istotne informacje o urządzeniu i rejestracji. Kod funkcji jest wykonywany i zwraca informacje o usłudze IoT Hub używane do aprowizacji urządzenia.

Korzystając z niestandardowych zasad alokacji, należy zdefiniować własne zasady alokacji, jeśli zasady udostępniane przez usługę Device Provisioning nie spełniają wymagań danego scenariusza.

Na przykład może być konieczne przeanalizowanie certyfikatu używanego przez urządzenie podczas aprowizacji i przypisanie urządzenia do usługi IoT Hub na podstawie właściwości certyfikatu. Możesz też mieć informacje przechowywane w bazie danych dla urządzeń i muszą wysyłać zapytania do bazy danych, aby określić, które Centrum IoT ma być przypisane do urządzenia.

W tym artykule przedstawiono niestandardowe zasady alokacji przy użyciu funkcji platformy Azure, która została zapisywana w języku C#. Tworzone są dwa nowe centra IoT przedstawiające *Wydział wyskakujących powiadomień firmy Contoso* oraz *dział pomp cieplnych firmy Contoso*. Urządzenia żądające aprowizacji muszą mieć identyfikator rejestracji z jednym z następujących sufiksów, które mają zostać zaakceptowane do aprowizacji:

* **-contoso-tstrsd-007**: Wydział wyskakujących powiadomień contoso
* **-contoso-hpsd-088**: dział pomp cieplnych contoso

Urządzenia będą obsługiwane na podstawie jednego z tych wymaganych sufiksów w IDENTYFIKATORze rejestracji. Te urządzenia będą symulowane przy użyciu przykładu aprowizacji dołączonego do [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c).

W tym artykule wykonaj następujące czynności:

* Korzystanie z interfejsu wiersza polecenia platformy Azure do tworzenia dwóch centrów **IoT działu****firmy Contoso**
* Tworzenie nowej rejestracji grupowej przy użyciu funkcji platformy Azure dla niestandardowych zasad alokacji
* Utwórz klucze urządzeń dla dwóch symulacji urządzeń.
* Konfigurowanie środowiska deweloperskiego dla zestawu SDK usługi Azure IoT C
* Symuluj urządzenia i sprawdź, czy są one obsługiwane zgodnie z przykładowym kodem w niestandardowych zasadach alokacji

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe wymagania wstępne dotyczą środowiska projektowego systemu Windows. W systemie Linux lub macOS zapoznaj się z odpowiednią sekcją w sekcji [Przygotowywanie środowiska deweloperskiego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w dokumentacji zestawu SDK.

- [Program Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 z włączonym obciążeniem ["Programowanie aplikacji klasycznych w języku C++"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) . Obsługiwane są również programy Visual Studio 2015 i Visual Studio 2017.

- Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Tworzenie usługi aprowizacji i dwóch centrów obsługi IoT

W tej sekcji użyjesz Azure Cloud Shell, aby utworzyć usługę aprowizacji i dwa centra IoT przedstawiające **Wydział wyskakujących powiadomień contoso** oraz **dział pomp cieplnych firmy Contoso**.

> [!TIP]
> Polecenia używane w tym artykule umożliwiają utworzenie usługi aprowizacji i innych zasobów w lokalizacji zachodnie stany USA. Zalecamy utworzenie zasobów w najbliższym regionie, który obsługuje usługę Device Provisioning. Można wyświetlić listę dostępnych lokalizacji, uruchamiając polecenie `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` lub przechodząc do strony [Stan platformy Azure](https://azure.microsoft.com/status/) i wyszukując usługę „Device Provisioning”. W poleceniach lokalizacje można określić w jednym formacie Word lub wielowyrazowym; na przykład: Zachodnie, zachodnie stany USA, ZACHODNIe stany USA itd. W wartości nie jest rozróżniana wielkość liter. Jeśli do określenia lokalizacji używasz formatu wielu słów, ujmij wartość w cudzysłowy, na przykład `-- location "West US"`.
>

1. Użyj Azure Cloud Shell, aby utworzyć grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group#az-group-create) . Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

    Poniższy przykład tworzy grupę zasobów o nazwie *contoso-US-Resource-Group* w regionie *zachodnie* . Zaleca się użycie tej grupy dla wszystkich zasobów utworzonych w tym artykule. To podejście ułatwi oczyszczenie po zakończeniu.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Użyj Azure Cloud Shell, aby utworzyć usługę Device Provisioning (DPS) za pomocą polecenia [AZ IoT DPS Create](/cli/azure/iot/dps#az-iot-dps-create) . Usługa aprowizacji zostanie dodana do *grupy contoso-US-Resource-Group*.

    Poniższy przykład tworzy usługę aprowizacji o nazwie *contoso-Provisioning-Service-1098* w lokalizacji *zachodniej* . Musisz użyć unikatowej nazwy usługi. Utwórz własny sufiks w nazwie usługi zamiast **1098**.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Wykonanie tego polecenia może potrwać kilka minut.

3. Użyj Azure Cloud Shell, aby utworzyć **wyskakujące powiadomienia firmy Contoso działu** IoT Hub za pomocą polecenia [AZ IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) . Centrum IoT zostanie dodane do *grupy contoso-US-Resource-Group*.

    W poniższym przykładzie utworzono Centrum IoT o nazwie *contoso-wyskakujące-Hub-1098* w lokalizacji *zachodniej* . Musisz użyć unikatowej nazwy centrum. Utwórz własny sufiks w nazwie centrum zamiast **1098**. 

    > [!CAUTION]
    > Przykładowy kod funkcji platformy Azure dla niestandardowych zasad alokacji wymaga podciągu `-toasters-` w nazwie centrum. Upewnij się, że używasz nazwy zawierającej wymagane podciąg wyskakujących powiadomień.
    
    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Wykonanie tego polecenia może potrwać kilka minut.

4. Użyj Azure Cloud Shell, aby utworzyć **pompy firmy Contoso, działu** IoT Hub za pomocą polecenia [AZ IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) . To centrum IoT zostanie również dodane do *grupy contoso-US-Resource-Group*.

    W poniższym przykładzie utworzono Centrum IoT o nazwie *contoso-heatpumps-Hub-1098* w lokalizacji *zachodniej* . Musisz użyć unikatowej nazwy centrum. Utwórz własny sufiks w nazwie centrum zamiast **1098**. 

    > [!CAUTION]
    > Przykładowy kod funkcji platformy Azure dla niestandardowych zasad alokacji wymaga podciągu `-heatpumps-` w nazwie centrum. Upewnij się, że używasz nazwy zawierającej wymagany podciąg heatpumps.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Wykonanie tego polecenia może potrwać kilka minut.

5. Centra IoT muszą być połączone z zasobem DPS. 

    Uruchom następujące dwa polecenia, aby uzyskać parametry połączenia dla właśnie utworzonych centrów. Zastąp nazwy zasobów centrum nazwami wybranymi w każdym z poleceń:

    ```azurecli-interactive 
    hubToastersConnectionString=$(az iot hub connection-string show --hub-name contoso-toasters-hub-1098 --key primary --query connectionString -o tsv)
    hubHeatpumpsConnectionString=$(az iot hub connection-string show --hub-name contoso-heatpumps-hub-1098 --key primary --query connectionString -o tsv)
    ```

    Uruchom następujące polecenia, aby połączyć centra z zasobem DPS. Zastąp nazwę zasobu DPS nazwą wybraną w każdym z poleceń:

    ```azurecli-interactive 
    az iot dps linked-hub create --dps-name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --connection-string $hubToastersConnectionString --location westus
    az iot dps linked-hub create --dps-name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --connection-string $hubHeatpumpsConnectionString --location westus
    ```




## <a name="create-the-custom-allocation-function"></a>Utwórz niestandardową funkcję alokacji

W tej sekcji utworzysz funkcję platformy Azure, która implementuje niestandardowe zasady alokacji. Ta funkcja decyduje o tym, które Centrum IoT Hub powinno być zarejestrowane na podstawie tego, czy jego identyfikator rejestracji zawiera ciąg **-contoso-tstrsd-007** lub **-contoso-hpsd-088**. Ustawia również początkowy stan sznurka urządzenia w zależności od tego, czy urządzenie jest wyskakującym, czy pompą cieplną.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Na stronie głównej wybierz pozycję **+ Utwórz zasób**.

2. W polu Wyszukaj w *portalu Marketplace* wpisz "aplikacja funkcji". Z listy rozwijanej wybierz pozycję **aplikacja funkcji**, a następnie wybierz pozycję **Utwórz**.

3. Na stronie **aplikacja funkcji** Utwórz na karcie **podstawowe** wprowadź następujące ustawienia dla nowej aplikacji funkcji i wybierz pozycję **Przegląd + Utwórz**:

    **Grupa zasobów**: Wybierz **grupę contoso-US-Resource-Group** , aby zachować razem wszystkie zasoby utworzone w tym artykule.

    **Nazwa aplikacja funkcji**: Wprowadź unikatową nazwę aplikacji funkcji. W tym przykładzie zastosowano **contoso-Function-App-1098**.

    **Publikowanie**: Sprawdź, czy **kod** jest zaznaczony.

    **Stos środowiska uruchomieniowego**: wybierz z listy rozwijanej pozycję **.NET Core** .

    **Wersja**: wybierz pozycję **3,1** z listy rozwijanej.

    **Region**: Wybierz ten sam region, w którym znajduje się grupa zasobów. W tym przykładzie zastosowano **zachodnie stany USA**.

    > [!NOTE]
    > Domyślnie Application Insights jest włączona. Application Insights nie jest to konieczne w tym artykule, ale może pomóc w zrozumieniu i zbadaniu problemów występujących w ramach alokacji niestandardowej. Jeśli wolisz, możesz wyłączyć Application Insights, wybierając kartę **monitorowanie** , a następnie pozycję **nie** , aby **włączyć Application Insights**.

    ![Utwórz aplikacja funkcji platformy Azure, aby hostować funkcję niestandardowej alokacji](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. Na stronie **Podsumowanie** wybierz pozycję **Utwórz** , aby utworzyć aplikację funkcji. Wdrożenie może potrwać kilka minut. Po zakończeniu wybierz pozycję **Przejdź do zasobu**.

5. W lewym okienku strony **Przegląd** aplikacji funkcji kliknij pozycję **funkcje** , a następnie przycisk **Dodaj** , aby dodać nową funkcję.

6. Na stronie **Dodawanie funkcji** kliknij pozycję **wyzwalacz http**, a następnie kliknij przycisk **Dodaj** .

7. Na następnej stronie kliknij pozycję **Code + test**. Pozwala to na edycję kodu dla funkcji o nazwie **HttpTrigger1**. Plik kodu **Run. CSX** powinien zostać otwarty do edycji.

8. Odwołuje się do wymaganych pakietów NuGet. Aby utworzyć początkową sznurek urządzenia, funkcja niestandardowej alokacji używa klas, które są zdefiniowane w dwóch pakietach NuGet, które muszą zostać załadowane do środowiska hostingu. W Azure Functions pakiety NuGet są wywoływane przy użyciu pliku *Function. proj* . W tym kroku zostanie zapisany i przekazany plik *Function. proj* dla wymaganych zestawów.  Aby uzyskać więcej informacji, zobacz [Używanie pakietów NuGet z Azure Functions](../azure-functions/functions-reference-csharp.md#using-nuget-packages).

    1. Skopiuj następujące wiersze do ulubionego edytora i Zapisz plik na komputerze jako *Function. proj*.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.16.3" />
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.27.0" />
            </ItemGroup>  
        </Project>
        ```

    2. Kliknij przycisk **Przekaż** znajdujący się powyżej edytora kodu, aby przekazać plik *Function. proj* . Po przekazaniu wybierz plik w edytorze kodu przy użyciu pola rozwijanego, aby sprawdzić zawartość.

9. Upewnij się, że w edytorze kodu została wybrana wartość *Run. CSX* dla **HttpTrigger1** . Zastąp kod funkcji **HttpTrigger1** następującym kodem i wybierz pozycję **Zapisz**:

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

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
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
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
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>Utwórz rejestrację

W tej sekcji utworzysz nową grupę rejestracji, która używa niestandardowych zasad alokacji. Dla uproszczenia w tym artykule jest stosowane [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md) z rejestracją. Aby lepiej zabezpieczyć rozwiązanie, należy rozważyć użycie [zaświadczania certyfikatu X. 509](concepts-x509-attestation.md) z łańcuchem zaufania.

1. Nadal na [Azure Portal](https://portal.azure.com)Otwórz swoją usługę aprowizacji.

2. W lewym okienku wybierz pozycję **Zarządzaj rejestracjami** , a następnie wybierz przycisk **Dodaj grupę rejestracji** w górnej części strony.

3. W obszarze **Dodaj grupę rejestracji** Wprowadź poniższe informacje, a następnie wybierz przycisk **Zapisz** .

    **Nazwa grupy**: wprowadź **contoso-Custom-przydzieloną-Devices**.

    **Typ zaświadczania**: Wybierz **klucz symetryczny**.

    **Automatycznie Generuj klucze**: to pole wyboru powinno być już zaznaczone.

    **Wybierz sposób przypisywania urządzeń do centrów**: wybierz pozycję **niestandardowa (Użyj funkcji platformy Azure)**.

    **Subskrypcja**: wybierz subskrypcję, w której została utworzona funkcja platformy Azure.

    **Aplikacja funkcji**: Wybierz aplikację funkcji według nazwy. w tym przykładzie użyto **contoso-Function-App-1098** .

    **Funkcja**: wybierz funkcję **HttpTrigger1** .

    ![Dodaj grupę rejestracji niestandardowej alokacji na potrzeby zaświadczania klucza symetrycznego](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Po zapisaniu rejestracji Otwórz ją ponownie i zanotuj **klucz podstawowy**. Musisz najpierw zapisać rejestrację, aby generować klucze. Ten klucz zostanie użyty do wygenerowania unikatowych kluczy urządzeń dla symulowanych urządzeń.

## <a name="derive-unique-device-keys"></a>Utwórz unikatowe klucze urządzeń

W tej sekcji utworzysz dwa unikatowe klucze urządzeń. Jeden klucz zostanie użyty dla symulowanego urządzenia wyskakującego. Drugi klucz zostanie użyty dla symulowanego urządzenia pompy termicznej.

Aby wygenerować klucz urządzenia, należy użyć wcześniej zanotowanego **klucza podstawowego** do obliczenia wartości [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) identyfikatora rejestracji urządzenia dla każdego urządzenia i przekonwertowania wyniku na format Base64. Aby uzyskać więcej informacji na temat tworzenia pochodnych kluczy urządzeń przy użyciu grup rejestracji, zobacz sekcję rejestracje grupowe [zaświadczania klucza symetrycznego](concepts-symmetric-key-attestation.md).

W przykładzie w tym artykule należy użyć następujących dwóch identyfikatorów rejestracji urządzeń i obliczyć klucz urządzenia dla obu urządzeń. Oba identyfikatory rejestracji mają prawidłowy sufiks do pracy z przykładowym kodem dla niestandardowych zasad alokacji:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**


# <a name="windows"></a>[Windows](#tab/windows)

Jeśli używasz stacji roboczej z systemem Windows, możesz użyć programu PowerShell, aby wygenerować pochodny klucz urządzenia, jak pokazano w poniższym przykładzie.

Zastąp wartość **klucza kluczem** **podstawowym** zanotowanym wcześniej.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='breakroom499-contoso-tstrsd-007'
$REG_ID2='mainbuilding167-contoso-hpsd-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

# <a name="linux"></a>[Linux](#tab/linux)

Jeśli używasz stacji roboczej z systemem Linux, możesz użyć OpenSSL, aby wygenerować pochodne klucze urządzenia, jak pokazano w poniższym przykładzie.

Zastąp wartość **klucza kluczem** **podstawowym** zanotowanym wcześniej.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=breakroom499-contoso-tstrsd-007
REG_ID2=mainbuilding167-contoso-hpsd-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---

Symulowane urządzenia będą używały pochodnych kluczy urządzeń z każdym IDENTYFIKATORem rejestracji w celu przeprowadzenia zaświadczania klucza symetrycznego.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Przygotowywanie środowiska deweloperskiego dla zestawu SDK języka C usługi Azure IoT

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

    Jeśli `cmake` nie znajdziesz kompilatora języka C++, może wystąpić błąd kompilacji podczas wykonywania polecenia. Jeśli tak się stanie, spróbuj uruchomić polecenie w [wierszu polecenia programu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs).

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

6. Kliknij prawym przyciskiem myszy projekt **prov\_dev\_client\_sample**, a następnie wybierz pozycję **Ustaw jako projekt startowy**.

### <a name="simulate-the-contoso-toaster-device"></a>Symulowanie urządzenia wyskakującego firmy Contoso

1. Aby zasymulować urządzenie wyskakujące, Znajdź wywołanie `prov_dev_set_symmetric_key_info()` w **Prov \_ dev \_ Client \_ Sample. c** , które jest oznaczone jako komentarz.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Usuń komentarz wywołania funkcji i Zastąp wartości symboli zastępczych (włącznie z nawiasami ostrymi) IDENTYFIKATORem rejestracji wyskakującego powiadomienia i wygenerowanym wcześniej kluczem urządzenia. Wartość klucza **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** pokazano poniżej jest pokazana jako przykład.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Zapisz plik.

2. W menu programu Visual Studio wybierz kolejno opcje **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić rozwiązanie. W wierszu polecenia, aby ponownie skompilować projekt, wybierz opcję **tak**, aby ponownie skompilować projekt przed uruchomieniem.

    Poniższe dane wyjściowe to przykład symulowanego urządzenia wyskakującego pomyślnie, który przeprowadził rozruch i nawiązać połączenie z wystąpieniem usługi aprowizacji do przypisania do centrum IoT Hub przez niestandardowe zasady alokacji:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Symulowanie urządzenia pompy termicznej firmy Contoso

1. Aby zasymulować urządzenie pompy cieplnej, zaktualizuj wywołanie do `prov_dev_set_symmetric_key_info()` programu w **Prov \_ dev \_ Client \_ Sample. c** ponownie z identyfikatorem rejestracji pompy cieplnej i wygenerowanym wcześniej kluczem urządzenia. Wartość klucza **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** pokazana poniżej jest również podana jako przykład.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Zapisz plik.

2. W menu programu Visual Studio wybierz kolejno opcje **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić rozwiązanie. W wierszu polecenia, aby ponownie skompilować projekt, wybierz opcję **tak** , aby ponownie skompilować projekt przed uruchomieniem.

    Następujące dane wyjściowe są przykładem urządzenia symulowanej pompy cieplnej pomyślnie rozruchu i nawiązywania połączenia z wystąpieniem usługi aprowizacji, które ma zostać przypisane do centrum IoT firmy Contoso, w ramach niestandardowych zasad alokacji:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Rozwiązywanie problemów z niestandardowymi zasadami alokacji

W poniższej tabeli przedstawiono oczekiwane scenariusze i kody błędów wyników, które mogą zostać wyświetlone. Ta tabela ułatwia rozwiązywanie problemów z niestandardowymi błędami zasad alokacji przy użyciu Azure Functions.

| Scenariusz | Wynik rejestracji z usługi aprowizacji | Wyniki zestawu SDK aprowizacji |
| -------- | --------------------------------------------- | ------------------------ |
| Element webhook zwraca 200 OK z ustawieniem "iotHubHostName" prawidłową nazwą hosta Centrum IoT Hub | Stan wyniku: przypisano  | Zestaw SDK zwraca PROV_DEVICE_RESULT_OK wraz z informacjami o centrum |
| Element webhook zwraca 200 OK z elementem "iotHubHostName" znajdującym się w odpowiedzi, ale ustawia pusty ciąg lub wartość null | Stan wyniku: niepowodzenie<br><br> Kod błędu: CustomAllocationIotHubNotSpecified (400208) | Zestaw SDK zwraca PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Element webhook zwraca 401 bez autoryzacji | Stan wyniku: niepowodzenie<br><br>Kod błędu: CustomAllocationUnauthorizedAccess (400209) | Zestaw SDK zwraca PROV_DEVICE_RESULT_UNAUTHORIZED |
| Utworzono indywidualną rejestrację w celu wyłączenia urządzenia | Stan wyniku: wyłączone | Zestaw SDK zwraca PROV_DEVICE_RESULT_DISABLED |
| Element webhook zwraca kod błędu >= 429 | Trwa ponawianie przez aranżację DPS. Zasady ponawiania są obecnie następujące:<br><br>&nbsp;&nbsp;-Liczba ponownych prób: 10<br>&nbsp;&nbsp;-Początkowy interwał: 1<br>&nbsp;&nbsp;-Increment: 9 | Zestaw SDK zignoruje błąd i wyśle inny komunikat o stanie w określonym czasie |
| Element webhook zwraca każdy inny kod stanu | Stan wyniku: niepowodzenie<br><br>Kod błędu: CustomAllocationFailed (400207) | Zestaw SDK zwraca PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

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

* Aby dowiedzieć się więcej, zobacz temat [IoT Hub ponowne Inicjowanie obsługi administracyjnej urządzeń](concepts-device-reprovision.md) 
* Aby dowiedzieć się więcej, zobacz [Jak anulować obsługę administracyjną urządzeń, które były wcześniej inicjowane](how-to-unprovision-devices.md) .