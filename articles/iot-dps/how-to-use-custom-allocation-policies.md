---
title: Niestandardowe zasady alokacji z Azure IoT Hub Device Provisioning Service
description: Jak używać niestandardowych zasad alokacji z Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/26/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 1432aee341509d8a5bdc9fffe89dd9bad33fc7de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766501"
---
# <a name="how-to-use-custom-allocation-policies"></a>Jak używać niestandardowych zasad alokacji

Niestandardowe zasady alokacji dają większą kontrolę nad przypisywaniem urządzeń do centrum IoT. Jest to realizowane przy użyciu kodu niestandardowego w funkcji [platformy Azure w](../azure-functions/functions-overview.md) celu przypisania urządzeń do centrum IoT. Usługa device provisioning wywołuje kod funkcji platformy Azure, podając wszystkie istotne informacje o urządzeniu i rejestracji. Kod funkcji jest wykonywany i zwraca informacje o centrum IoT używane do aprowizowania urządzenia.

Za pomocą niestandardowych zasad alokacji można zdefiniować własne zasady alokacji, gdy zasady udostępniane przez usługę Device Provisioning Service nie spełniają wymagań scenariusza.

Na przykład możesz chcieć sprawdzić certyfikat, którego używa urządzenie podczas aprowizowania, i przypisać urządzenie do centrum IoT hub na podstawie właściwości certyfikatu. Możesz też mieć informacje przechowywane w bazie danych urządzeń i musisz odpytać bazę danych, aby określić, do którego centrum IoT powinno zostać przypisane urządzenie.

W tym artykule pokazano niestandardowe zasady alokacji korzystające z funkcji platformy Azure napisanej w języku C#. Tworzone są dwa nowe centra IoT reprezentujące dział *Toasters firmy Contoso* i dział pomp *cieplnych firmy Contoso.* Urządzenia żądające aprowizowania muszą mieć identyfikator rejestracji z jednym z następujących sufiksów do zaakceptowania podczas aprowizowania:

* **-contoso-tstrsd-007**: Contoso Toasters Division
* **-contoso-hpsd-088**: Dział pomp cieplnych firmy Contoso

Urządzenia zostaną aprowowane na podstawie jednego z tych wymaganych sufiksów w identyfikatorze rejestracji. Te urządzenia zostaną symulowane przy użyciu przykładu aprowizowania dołączonego do zestawu [SDK języka C usługi Azure IoT.](https://github.com/Azure/azure-iot-sdk-c)

W tym artykule wykonasz następujące kroki:

* Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć dwa centra IoT dzielenia firmy Contoso (dział **Toasters** firmy Contoso i Dział pomp **cieplnych firmy Contoso)**
* Tworzenie nowej rejestracji grupowej przy użyciu funkcji platformy Azure dla niestandardowych zasad alokacji
* Utwórz klucze urządzeń dla dwóch symulacji urządzeń.
* Konfigurowanie środowiska projektowego dla zestawu SDK języka C usługi Azure IoT
* Symulowanie urządzeń i sprawdzanie, czy są aprowowane zgodnie z przykładowym kodem w niestandardowych zasadach alokacji

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne są dla środowiska projektowego systemu Windows. W przypadku systemu Linux lub macOS zapoznaj się z odpowiednią sekcją w sekcji [Przygotowywanie środowiska projektowego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w dokumentacji zestawu SDK.

- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 r. z włączonym [obciążeniem "Tworzenie aplikacji klasycznych w języku C++".](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) Visual Studio 2015 i Visual Studio 2017 są również obsługiwane.

- Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Tworzenie usługi aprowizowanej i dwóch oddziałowych centrów IoT Hub

W tej sekcji utworzysz usługę Azure Cloud Shell i dwa centra IoT reprezentujące dział Contoso **Toasters i** dział pomp cieplnych **firmy Contoso.**

> [!TIP]
> Polecenia używane w tym artykule tworzą usługę aprowizowania i inne zasoby w lokalizacji Zachodnie stany USA. Zalecamy utworzenie zasobów w najbliższym regionie, który obsługuje usługę Device Provisioning Service. Można wyświetlić listę dostępnych lokalizacji, uruchamiając polecenie `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` lub przechodząc do strony [Stan platformy Azure](https://azure.microsoft.com/status/) i wyszukując usługę „Device Provisioning”. W poleceniach lokalizacje można określić w jednym wyrazie lub w formacie wielu słów; na przykład: westus, West US, WEST US itp. W wartości nie jest wróżniana wielkość liter. Jeśli do określenia lokalizacji używasz formatu wielu słów, ujmij wartość w cudzysłowy, na przykład `-- location "West US"`.
>

1. Użyj polecenia Azure Cloud Shell, aby utworzyć grupę zasobów za pomocą [polecenia az group create.](/cli/azure/group#az_group_create) Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

    Poniższy przykład tworzy grupę zasobów o *nazwie contoso-us-resource-group* w *regionie westus.* Zaleca się używanie tej grupy dla wszystkich zasobów utworzonych w tym artykule. Takie podejście ułatwi czyszczenie po zakończeniu pracy.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Użyj polecenia Azure Cloud Shell, aby utworzyć usługę device provisioning service (DPS) za pomocą [polecenia az iot dps create.](/cli/azure/iot/dps#az_iot_dps_create) Usługa aprowizowania zostanie dodana do *grupy zasobów contoso-us-resource..*

    Poniższy przykład tworzy usługę aprowizowania o *nazwie contoso-provisioning-service-1098* w *lokalizacji westus.* Należy użyć unikatowej nazwy usługi. Sufiks należy umieścić w nazwie usługi w miejsce **ciągu 1098**.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Ukończenie tego polecenia może potrwać kilka minut.

3. Użyj polecenia Azure Cloud Shell, aby utworzyć centrum IoT dzielenia Contoso **Toasters** za pomocą [polecenia az iot hub create.](/cli/azure/iot/hub#az_iot_hub_create) Centrum IoT zostanie dodane do *grupy zasobów contoso-us-resource-group.*

    Poniższy przykład tworzy centrum IoT o nazwie *contoso-toasters-hub-1098* w *lokalizacji westus.* Należy użyć unikatowej nazwy centrum. Sufiks należy umieścić w nazwie centrum w miejsce **ciągu 1098**. 

    > [!CAUTION]
    > Przykładowy kod funkcji platformy Azure dla niestandardowych zasad alokacji wymaga `-toasters-` podciągu w nazwie centrum. Upewnij się, że używasz nazwy zawierającej wymagany podciąg tosters.
    
    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Ukończenie tego polecenia może potrwać kilka minut.

4. Użyj polecenia Azure Cloud Shell, aby utworzyć centrum IoT Dzielenie pomp cieplnych **firmy Contoso** za pomocą polecenia [az iot hub create.](/cli/azure/iot/hub#az_iot_hub_create) To centrum IoT zostanie również dodane do *grupy zasobów contoso-us-resource-group.*

    Poniższy przykład tworzy centrum IoT o nazwie *contoso-heatpumps-hub-1098* w *lokalizacji westus.* Należy użyć unikatowej nazwy centrum. Sufiks należy umieścić w nazwie centrum w miejsce **ciągu 1098**. 

    > [!CAUTION]
    > Przykładowy kod funkcji platformy Azure dla niestandardowych zasad alokacji wymaga `-heatpumps-` podciągu w nazwie centrum. Upewnij się, że używasz nazwy zawierającej wymagany podciąg podciągu heatpumps.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Ukończenie tego polecenia może potrwać kilka minut.

5. Centra IoT Hub muszą być połączone z zasobem usługi DPS. 

    Uruchom następujące dwa polecenia, aby uzyskać parametry połączenia dla właśnie utworzonych centrów. Zastąp nazwy zasobów centrum nazwami, które zostały wybrane w każdym poleceniu:

    ```azurecli-interactive 
    hubToastersConnectionString=$(az iot hub connection-string show --hub-name contoso-toasters-hub-1098 --key primary --query connectionString -o tsv)
    hubHeatpumpsConnectionString=$(az iot hub connection-string show --hub-name contoso-heatpumps-hub-1098 --key primary --query connectionString -o tsv)
    ```

    Uruchom następujące polecenia, aby połączyć koncentratory z zasobem usługi DPS. Zastąp nazwę zasobu DPS nazwą wybraną w każdym poleceniu:

    ```azurecli-interactive 
    az iot dps linked-hub create --dps-name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --connection-string $hubToastersConnectionString --location westus
    az iot dps linked-hub create --dps-name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --connection-string $hubHeatpumpsConnectionString --location westus
    ```




## <a name="create-the-custom-allocation-function"></a>Tworzenie niestandardowej funkcji alokacji

W tej sekcji utworzysz funkcję platformy Azure, która implementuje niestandardowe zasady alokacji. Ta funkcja decyduje o tym, w którym centrum IoT Hub należy zarejestrować urządzenie, na podstawie tego, czy jego identyfikator rejestracji zawiera ciąg **-contoso-tstrsd-007** lub **-contoso-hpsd-088.** Ustawia również początkowy stan bliźniaczej reprezentacji urządzenia na podstawie tego, czy urządzenie jest toster, czy pompa cieplna.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Na stronie głównej wybierz pozycję **+ Utwórz zasób.**

2. W polu *wyszukiwania Wyszukaj w* witrynie Marketplace wpisz "Aplikacja funkcji". Z listy rozwijanej wybierz pozycję **Aplikacja funkcji,** a następnie wybierz pozycję **Utwórz.**

3. Na **stronie Tworzenie** aplikacji funkcji na karcie **Podstawowe** wprowadź następujące ustawienia dla nowej aplikacji funkcji i wybierz pozycję Przejrzyj **i utwórz:**

    **Grupa zasobów:** wybierz **grupę contoso-us-resource-group,** aby zachować wszystkie zasoby utworzone w tym artykule razem.

    **Nazwa aplikacji funkcji:** wprowadź unikatową nazwę aplikacji funkcji. W tym przykładzie **użyto contoso-function-app-1098**.

    **Publikuj:** sprawdź, czy **wybrano** opcję Kod.

    **Stos środowiska uruchomieniowego:** wybierz z listy rozwijanej pozycję **.NET** Core.

    **Wersja:** wybierz **pozycję 3.1** z listy rozwijanej.

    **Region:** wybierz ten sam region co grupa zasobów. W tym przykładzie użyto **zachodnie stany USA.**

    > [!NOTE]
    > Domyślnie funkcja Application Insights włączona. Application Insights tego artykułu nie jest konieczne, ale może pomóc w zrozumieniu i zbadaniu wszelkich problemów związanych z alokacją niestandardową. Jeśli wolisz, możesz wyłączyć usługę Application Insights, wybierając **kartę Monitorowanie,** a następnie wybierając pozycję **Nie** dla opcji Włącz **Application Insights**.

    ![Tworzenie aplikacji funkcji platformy Azure do hostowania niestandardowej funkcji alokacji](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. Na stronie **Podsumowanie** wybierz pozycję **Utwórz,** aby utworzyć aplikację funkcji. Wdrożenie może potrwać kilka minut. Po zakończeniu wybierz pozycję **Przejdź do zasobu**.

5. W lewym okienku strony Przegląd **aplikacji** funkcji kliknij pozycję **Funkcje,** a następnie **pozycję + Dodaj,** aby dodać nową funkcję.

6. Na stronie **Dodawanie funkcji** kliknij pozycję **Wyzwalacz HTTP,** a następnie kliknij **przycisk** Dodaj.

7. Na następnej stronie kliknij pozycję **Kod i testowanie.** Dzięki temu można edytować kod funkcji o nazwie **HttpTrigger1**. Plik **kodu run.csx** powinien zostać otwarty do edycji.

8. Odwołaj się do wymaganych pakietów NuGet. Aby utworzyć początkową bliźniacza reprezentacji urządzenia, funkcja alokacji niestandardowej używa klas zdefiniowanych w dwóch pakietach NuGet, które muszą zostać załadowane do środowiska hostingu. W Azure Functions się do pakietów NuGet przy użyciu *pliku function.proj.* W tym kroku zapiszemy i przekażemy plik *function.proj* dla wymaganych zestawów.  Aby uzyskać więcej informacji, zobacz [Using NuGet packages with Azure Functions](../azure-functions/functions-reference-csharp.md#using-nuget-packages)(Używanie pakietów NuGet z Azure Functions ).

    1. Skopiuj następujące wiersze do ulubionego edytora i zapisz plik na komputerze jako *function.proj.*

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

    2. Kliknij przycisk **Przekaż** znajdujący się nad edytorem kodu, aby przekazać *plik function.proj.* Po zakończeniu przekazywania wybierz plik w edytorze kodu przy użyciu pola listy rozwijanej, aby zweryfikować jego zawartość.

9. Upewnij się, że w edytorze kodu wybrano element *run.csx* dla aplikacji **HttpTrigger1.** Zastąp kod funkcji **HttpTrigger1** następującym kodem i wybierz pozycję **Zapisz:**

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

## <a name="create-the-enrollment"></a>Tworzenie rejestracji

W tej sekcji utworzysz nową grupę rejestracji, która używa niestandardowych zasad alokacji. Dla uproszczenia w tym artykule użyto [zaświadczenia klucza symetrycznego](concepts-symmetric-key-attestation.md) z rejestracją. Aby uzyskać bezpieczniejsze rozwiązanie, rozważ użycie zaświadczenia certyfikatu [X.509](concepts-x509-attestation.md) z łańcuchem zaufania.

1. Nadal w [Azure Portal](https://portal.azure.com)otwórz usługę aprowizowania.

2. Wybierz **pozycję Zarządzaj rejestracjami** w okienku po lewej stronie, a następnie wybierz przycisk **Dodaj grupę** rejestracji w górnej części strony.

3. Na **stronie Dodaj grupę rejestracji** wprowadź następujące informacje i wybierz **przycisk** Zapisz.

    **Nazwa grupy:** wprowadź **nazwę contoso-custom-allocated-devices.**

    **Typ zaświadczenia:** wybierz pozycję **Klucz symetryczny.**

    **Automatycznie generuj** klucze: to pole wyboru powinno być już zaznaczone.

    **Wybierz sposób przypisywania urządzeń do centrów:** wybierz pozycję **Niestandardowe (użyj funkcji platformy Azure).**

    **Subskrypcja:** wybierz subskrypcję, w której utworzono funkcję platformy Azure.

    **Aplikacja funkcji:** wybierz aplikację funkcji według nazwy. **w tym przykładzie została użyta aplikacja contoso-function-app-1098.**

    **Funkcja:** wybierz **funkcję HttpTrigger1.**

    ![Dodawanie niestandardowej grupy rejestracji alokacji dla zaświadczenia klucza symetrycznego](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Po zapisaniu rejestracji otwórz ją ponownie i zanotuj **klucz podstawowy**. Najpierw należy zapisać rejestrację, aby wygenerować klucze. Ten klucz będzie później używany do generowania unikatowych kluczy urządzeń dla urządzeń symulowanych.

## <a name="derive-unique-device-keys"></a>Wyprowadzaj unikatowe klucze urządzeń

W tej sekcji utworzysz dwa unikatowe klucze urządzenia. Jeden klucz będzie używany dla symulowanego urządzenia toster. Drugi klucz będzie używany dla symulowanego urządzenia pompy cieplnej.

Aby wygenerować klucz urządzenia,  należy użyć zanotego wcześniej klucza podstawowego, aby obliczyć [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) identyfikatora rejestracji urządzenia dla każdego urządzenia i przekonwertować wynik na format Base64. Aby uzyskać więcej informacji na temat tworzenia pochodnych kluczy urządzeń za pomocą grup rejestracji, zobacz sekcję rejestracje grup w sekcji Zaświadczenia klucza [symetrycznego](concepts-symmetric-key-attestation.md).

W przykładzie w tym artykule użyj następujących dwóch identyfikatorów rejestracji urządzeń i oblicz klucz urządzenia dla obu urządzeń. Oba identyfikatory rejestracji mają prawidłowy sufiks do pracy z przykładowym kodem dla niestandardowych zasad alokacji:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**


# <a name="windows"></a>[Windows](#tab/windows)

Jeśli używasz stacji roboczej z systemem Windows, możesz użyć programu PowerShell do wygenerowania pochodnego klucza urządzenia, jak pokazano w poniższym przykładzie.

Zamień wartość **KEY** na zanotiony wcześniej klucz podstawowy. 

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

Jeśli używasz stacji roboczej z systemem Linux, możesz użyć narzędzia openssl do wygenerowania pochodnych kluczy urządzeń, jak pokazano w poniższym przykładzie.

Zamień wartość **KEY** na zanotiony wcześniej klucz podstawowy. 

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

Symulowane urządzenia będą używać pochodnych kluczy urządzeń z każdym identyfikatorem rejestracji w celu przeprowadzenia zaświadczenia klucza symetrycznego.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Przygotowywanie środowiska deweloperskiego dla zestawu SDK języka C usługi Azure IoT

W tej sekcji przygotujemy środowisko projektowe używane do kompilowania zestawu [SDK języka C usługi Azure IoT.](https://github.com/Azure/azure-iot-sdk-c) Zestaw SDK zawiera przykładowy kod urządzenia symulowanego. To urządzenie symulowane podejmie próbę aprowizacji podczas sekwencji rozruchu urządzenia.

Ta sekcja jest ukierunkowana na stację roboczą z systemem Windows. Aby uzyskać przykład dla systemu Linux, zobacz konfigurowanie maszyn wirtualnych w te tematze Jak aprowizować [wielowątkową usługę](how-to-provision-multitenant.md).

1. Pobierz system [kompilacji CMake.](https://cmake.org/download/)

    Ważne jest, aby wstępnie wymagane składniki (program Visual Studio oraz pakiet roboczy „Programowanie aplikacji klasycznych w języku C++”) były zainstalowane na tym komputerze **przed** uruchomieniem `CMake` instalacji. Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

2. Znajdź nazwę tagu dla [najnowszej wersji](https://github.com/Azure/azure-iot-sdk-c/releases/latest) zestawu SDK.

3. Otwórz wiersz polecenia lub powłokę Git Bash. Uruchom następujące polecenia, aby sklonować najnowszą wersję repozytorium [GitHub zestawu SDK języka C usługi Azure IoT.](https://github.com/Azure/azure-iot-sdk-c) Użyj tagu znalezionego w poprzednim kroku jako wartości `-b` parametru:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

4. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu. Uruchom następujące polecenia z `azure-iot-sdk-c` katalogu :

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Uruchom następujące polecenie, które utworzy wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego. Rozwiązanie programu Visual Studio dla symulowanego urządzenia zostanie wygenerowane w katalogu `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Jeśli kompilator języka C++ nie zostanie uruchomiony, podczas uruchamiania polecenia mogą wystąpić `cmake` błędy kompilacji. W takim przypadku spróbuj uruchomić polecenie w [wierszu Visual Studio wiersza polecenia](/dotnet/framework/tools/developer-command-prompt-for-vs).

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

W tej sekcji zaktualizujemy przykład aprowizowania o nazwie **prov \_ dev client \_ \_ sample** znajdujący się w zestawie SDK języka C usługi Azure IoT, który został wcześniej ustawiony.

Ten przykładowy kod symuluje sekwencję rozruchu urządzenia, która wysyła żądanie aprowizowania do wystąpienia usługi Device Provisioning Service. Sekwencja rozruchu spowoduje, że urządzenie toster zostanie rozpoznane i przypisane do centrum IoT przy użyciu niestandardowych zasad alokacji.

1. W witrynie Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning Service, a następnie zapisz wartość **_Zakres identyfikatorów_**.

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. W Visual Studio otwórz plik **rozwiązania azure_iot_sdks.sln,** który został wygenerowany przez wcześniejsze uruchomienie CMake. Plik rozwiązania powinien znajdować się w następującej lokalizacji:

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

### <a name="simulate-the-contoso-toaster-device"></a>Symulowanie urządzenia toster firmy Contoso

1. Aby zasymulować urządzenie toaster, znajdź wywołanie metody w aplikacji `prov_dev_set_symmetric_key_info()` **prov \_ dev client \_ \_ sample.c,** która jest oznaczana jako komentarz.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Odkomentuj wywołanie funkcji i zastąp wartości symboli zastępczych (w tym nawiasy kątowe) identyfikatorem rejestracji tostera i wygenerowanym wcześniej pochodnym kluczem urządzenia. Wartość klucza **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** pokazana poniżej jest podana tylko jako przykład.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Zapisz plik.

2. W Visual Studio menu wybierz pozycję **Rozpocznij debugowanie** bez debugowania,  >   aby uruchomić rozwiązanie. W wierszu polecenia ponownego skompilowania projektu wybierz pozycję **Tak,** aby ponownie skompilować projekt przed uruchomieniem.

    Następujące dane wyjściowe to przykład pomyślnego uruchomienia symulowanego urządzenia tosterowego i połączenia się z wystąpieniem usługi aprowowania, które zostanie przypisane do centrum IoT Hub w tosterach za pomocą niestandardowych zasad alokacji:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Symulowanie urządzenia firmy Contoso z pompą cieplną

1. Aby zasymulować urządzenie pompy cieplnej, zaktualizuj ponownie wywołanie metody w aplikacji `prov_dev_set_symmetric_key_info()` **prov \_ dev client \_ \_ sample.c** przy użyciu identyfikatora rejestracji pompy cieplnej i wygenerowanego wcześniej klucza urządzenia pochodnego. Wartość klucza **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** pokazana poniżej jest również podana tylko jako przykład.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Zapisz plik.

2. W menu Visual Studio pozycję **Rozpocznij**  >  **debugowanie bez debugowania,** aby uruchomić rozwiązanie. W wierszu polecenia ponownego kompilowania projektu wybierz pozycję **Tak,** aby ponownie skompilować projekt przed uruchomieniem.

    Następujące dane wyjściowe to przykład symulowanego urządzenia pompy cieplnej, które zostało pomyślnie uruchomiony i połączone z wystąpieniem usługi aprowizowania, które zostanie przypisane do centrum IoT pomp cieplnych firmy Contoso przez niestandardowe zasady alokacji:

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

W poniższej tabeli przedstawiono oczekiwane scenariusze i kody błędów wyników, które mogą wystąpić. Ta tabela zawiera informacje pomocne w rozwiązywaniu problemów z błędami niestandardowych zasad alokacji Azure Functions.

| Scenariusz | Wynik rejestracji z usługi Provisioning Service | Wyniki aprowizowania zestawu SDK |
| -------- | --------------------------------------------- | ------------------------ |
| Ten obiekt webhook zwraca wartość 200 OK, a parametr "iotHubHostName" jest ustawiony na prawidłową nazwę hosta centrum IoT | Stan wyniku: Przypisane  | Zestaw SDK zwraca PROV_DEVICE_RESULT_OK wraz z informacjami o centrum |
| Element webhook zwraca wartość 200 OK z wartością "iotHubHostName" obecną w odpowiedzi, ale ustawioną na pusty ciąg lub wartość null | Stan wyniku: Niepowodzenie<br><br> Kod błędu: CustomAllocationIotHubNotSpecified (400208) | Zestaw SDK zwraca PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Ten webhook zwraca wartość 401 Brak autoryzacji | Stan wyniku: Niepowodzenie<br><br>Kod błędu: CustomAllocationUnauthorizedAccess (400209) | Zestaw SDK zwraca PROV_DEVICE_RESULT_UNAUTHORIZED |
| Utworzono rejestrację indywidualną w celu wyłączenia urządzenia | Stan wyniku: Wyłączone | Zestaw SDK zwraca PROV_DEVICE_RESULT_DISABLED |
| Webhook zwraca kod błędu >= 429 | Orkiestracji dps będzie ponowić próbę kilka razy. Zasady ponawiania są obecnie:<br><br>&nbsp;&nbsp;- Liczba ponownych prób: 10<br>&nbsp;&nbsp;- Początkowy interwał: 1 s<br>&nbsp;&nbsp;- Inkrementacja: 9 s | Zestaw SDK zignoruje błąd i przesła kolejny komunikat o stanie get w określonym czasie |
| Ten webhook zwraca dowolny inny kod stanu | Stan wyniku: Niepowodzenie<br><br>Kod błędu: CustomAllocationFailed (400207) | Zestaw SDK zwraca PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z zasobami utworzonymi w tym artykule, możesz je pozostawić. Jeśli nie planujesz nadal korzystać z zasobów, skorzystaj z poniższych kroków, aby usunąć wszystkie zasoby utworzone w tym artykule, aby uniknąć niepotrzebnych opłat.

W poniższych krokach założono, że w tym artykule utworzono wszystkie zasoby zgodnie z instrukcjami w tej samej grupie zasobów o nazwie **contoso-us-resource-group.**

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub niewłaściwej grupy zasobów. Jeśli usługa IoT Hub została utworzona wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, zamiast usuwać całą grupę zasobów, usuń tylko zasób usługi IoT Hub.
>

Aby usunąć grupę zasobów według nazwy:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.

2. W polu **tekstowym Filtruj** według nazwy... wpisz nazwę grupy zasobów zawierającej zasoby **contoso-us-resource-group**. 

3. Po prawej stronie grupy zasobów na liście wyników wybierz pozycję ... , **a** następnie **pozycję Usuń grupę zasobów.**

4. Zostanie poproszony o potwierdzenie usunięcia grupy zasobów. Wpisz ponownie nazwę grupy zasobów, aby potwierdzić, a następnie wybierz pozycję **Usuń.** Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat ponownego aprowizowania, [zobacz IoT Hub Device reprovisioning concepts (Pojęcia dotyczące ponownego aprowizowania urządzeń)](concepts-device-reprovision.md) 
* Aby dowiedzieć się więcej Na temat coania aprowizowania, zobacz [Jak coprowizować urządzenia, które były wcześniej automatycznie aprowizowane](how-to-unprovision-devices.md)