---
title: 'Szybki Start: wdrażanie łącznika usługi Azure IoT for FHIR (wersja zapoznawcza) przy użyciu szablonu ARM'
description: W tym przewodniku szybki start dowiesz się, jak wdrożyć łącznik usługi Azure IoT for FHIR (wersja zapoznawcza) przy użyciu szablonu Azure Resource Manager (szablon ARM).
author: rbhaiya
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.author: rabhaiya
ms.date: 01/21/2021
ms.openlocfilehash: b45c63031596c96886a96a21bf693803088cc006
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745227"
---
# <a name="quickstart-use-an-azure-resource-manager-arm-template-to-deploy-azure-iot-connector-for-fhir-preview"></a>Szybki Start: używanie szablonu Azure Resource Manager (ARM) do wdrażania łącznika usługi Azure IoT dla FHIR (wersja zapoznawcza)

W tym przewodniku szybki start dowiesz się, jak używać szablonu Azure Resource Manager (szablon ARM) do wdrażania łącznika usługi Azure IoT dla szybkich zasobów współdziałania w ramach usług opieki zdrowotnej (FHIR&#174;) *, funkcji interfejsu API platformy Azure dla FHIR. Aby wdrożyć działające wystąpienie łącznika usługi Azure IoT dla FHIR, ten szablon wdraża także nadrzędny interfejs API platformy Azure dla usługi FHIR i aplikację IoT Central platformy Azure, która eksportuje dane telemetryczne z symulatora urządzeń do łącznika usługi Azure IoT dla FHIR. Szablon ARM można uruchomić w celu wdrożenia łącznika usługi Azure IoT dla FHIR za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w Azure Portal po zalogowaniu się.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdróż usługę Azure IoT Connector dla FHIR przy użyciu szablonu ARM w Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)

Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/).
* Jeśli chcesz uruchomić kod lokalnie, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/).
* Jeśli chcesz uruchomić kod lokalnie:
    * Powłoka bash (na przykład git bash, która jest zawarta w narzędziu [git dla systemu Windows](https://gitforwindows.org)).
    * [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Przegląd szablonu

[Szablon](https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json) definiuje następujące zasoby platformy Azure:

* **Microsoft. HealthcareApis/usługi**
* **Microsoft. HealthcareApis/Services/iomtconnectors**
* **Microsoft. IoTCentral/IoTApps**

## <a name="deploy-the-template"></a>Wdrożenie szablonu

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wybierz następujący link, aby wdrożyć łącznik usługi Azure IoT dla FHIR przy użyciu szablonu ARM w Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdróż usługę Azure IoT Connector for FHIR przy użyciu szablonu ARM w Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

Na stronie **wdrażanie interfejsu API platformy Azure dla FHIR** :

1. Jeśli chcesz, Zmień domyślną **subskrypcję** na inną subskrypcję.

2. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**, wprowadź nazwę nowej grupy zasobów, a następnie wybierz pozycję **OK**.

3. Jeśli utworzono nową grupę zasobów, wybierz **region** dla grupy zasobów.

4. Wprowadź nazwę nowego wystąpienia usługi Azure API for FHIR w **nazwie usługi FHIR**.

5. Wybierz **lokalizację** interfejsu API platformy Azure dla usługi FHIR. Lokalizacja może być taka sama jak lub różna od regionu grupy zasobów.

6. Podaj nazwę łącznika usługi Azure IoT dla wystąpienia FHIR w **nazwie łącznika IoT**.

7. Podaj nazwę połączenia utworzonego w ramach łącznika usługi Azure IoT dla FHIR w polu **Nazwa połączenia**. To połączenie jest używane przez aplikację Azure IoT Central do wypychania dystrybucji symulowanych danych telemetrycznych z urządzenia do usługi Azure IoT Connector dla FHIR.

8. Wprowadź nazwę nowej aplikacji platformy Azure IoT Central w usłudze **IoT Central Name**. Ta aplikacja będzie używać szablonu *monitorowania ciągłego pacjenta* w celu symulowania urządzenia.

9. Wybierz lokalizację aplikacji IoT Central z listy rozwijanej **lokalizacja IoT Central** . 

10. Wybierz pozycję **Przejrzyj i utwórz**.

11. Przeczytaj warunki i postanowienia, a następnie wybierz pozycję **Utwórz**.

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

> [!NOTE]
> Jeśli chcesz uruchomić skrypty programu PowerShell lokalnie, najpierw wprowadź, `Connect-AzAccount` Aby skonfigurować swoje poświadczenia platformy Azure.

Jeśli `Microsoft.HealthcareApis` dostawca zasobów nie jest już zarejestrowany dla Twojej subskrypcji, możesz zarejestrować go przy użyciu następującego kodu interaktywnego. Aby uruchomić kod w Azure Cloud Shell, wybierz opcję **Wypróbuj** w górnym rogu dowolnego bloku kodu.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Jeśli `Microsoft.IoTCentral` dostawca zasobów nie jest już zarejestrowany dla Twojej subskrypcji, możesz zarejestrować go przy użyciu następującego kodu interaktywnego. Aby uruchomić kod w Azure Cloud Shell, wybierz opcję **Wypróbuj** w górnym rogu dowolnego bloku kodu.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.IoTCentral
```

Użyj poniższego kodu, aby wdrożyć usługę Azure IoT Connector for FHIR przy użyciu szablonu ARM.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$fhirServiceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$location = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$iotConnectorName = Read-Host -Prompt "Enter a name for the new Azure IoT Connector for FHIR"
$connectionName = Read-Host -Prompt "Enter a name for the connection with Azure IoT Connector for FHIR"
$iotCentralName = Read-Host -Prompt "Enter a name for the new Azure IoT Central Application"
$iotCentralLocation = Read-Host -Prompt "Enter a location for the new Azure IoT Central Application"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure IoT Connector for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json `
    -fhirServiceName $fhirServiceName `
    -location $location
    -iotConnectorName $iotConnectorName
    -connectionName $connectionName
    -iotCentralName $iotCentralName
    -iotCentralLocation $iotCentralLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

Jeśli `Microsoft.HealthcareApis` dostawca zasobów nie jest już zarejestrowany dla Twojej subskrypcji, możesz zarejestrować go przy użyciu następującego kodu interaktywnego. Aby uruchomić kod w Azure Cloud Shell, wybierz opcję **Wypróbuj** w górnym rogu dowolnego bloku kodu.

```azurecli-interactive
az extension add --name healthcareapis
```

Jeśli `Microsoft.IoTCentral` dostawca zasobów nie jest już zarejestrowany dla Twojej subskrypcji, możesz zarejestrować go przy użyciu następującego kodu interaktywnego.

```azurecli-interactive
az extension add --name azure-iot
```

Użyj poniższego kodu, aby wdrożyć usługę Azure IoT Connector for FHIR przy użyciu szablonu ARM.

```azurecli-interactive
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter a name for the new Azure API for FHIR service: " fhirServiceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " location &&
read -p "Enter a name for the new Azure IoT Connector for FHIR: " iotConnectorName &&
read -p "Enter a name for the connection with Azure IoT Connector for FHIR: " connectionName &&
read -p "Enter a name for the new Azure IoT Central Application: " iotCentralName &&
read -p "Enter a location for the new Azure IoT Central Application: " iotCentralLocation &&

params='fhirServiceName='$fhirServiceName' location='$location' iotConnectorName='$iotConnectorName' connectionName='$connectionName' iotCentralName='$iotCentralName' iotCentralLocation='$iotCentralLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure IoT Connector for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Wdrożenie może potrwać kilka minut. Zwróć uwagę na nazwy usługi Azure API for FHIR, aplikacji platformy Azure IoT Central i grupy zasobów używanej w celu późniejszego przejrzenia wdrożonych zasobów.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj następujące kroki, aby zapoznać się z omówieniem nowego interfejsu API platformy Azure dla usługi FHIR:

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **Azure API for FHIR**.

2. Z listy FHIR wybierz nową usługę. Zostanie wyświetlona strona **Przegląd** nowej usługi Azure API for FHIR.

3. Aby sprawdzić, czy nowe konto interfejsu API FHIR jest inicjowane, wybierz link obok pozycji **punkt końcowy metadanych FHIR** , aby pobrać instrukcję obsługi interfejsu API FHIR. Link ma format `https://<service-name>.azurehealthcareapis.com/metadata` . Jeśli konto jest inicjowane, zostanie wyświetlony duży plik JSON.

4. Aby sprawdzić, czy nowy łącznik usługi Azure IoT for FHIR jest zainicjowany, wybierz pozycję **Łącznik IoT (wersja zapoznawcza)** z menu nawigacji po lewej stronie, aby otworzyć stronę **Łączniki usługi IoT** . Na stronie musi być widoczny łącznik aprowizacji usługi Azure IoT dla FHIR z wartością *status* jako *online*, wartość *Connections* AS *1*, a zarówno *Mapowanie urządzenia* , jak i *Mapowanie FHIR* zawierają ikonę *sukcesu* .

5. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **aplikacje IoT Central**.

6. Na liście aplikacji IoT Central wybierz nową usługę. Zostanie wyświetlona strona **Przegląd** nowej aplikacji IoT Central.

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

Uruchom następujący kod interaktywny, aby wyświetlić szczegółowe informacje na temat usługi Azure API for FHIR. Musisz wprowadzić nazwę nowej usługi FHIR i grupę zasobów.

```azurepowershell-interactive
$fhirServiceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new FHIR service has been provisioned"

$requestUri="https://" + $fhirServiceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

> [!NOTE]
> Łącznik usługi Azure IoT dla programu FHIR nie udostępnia teraz poleceń programu PowerShell. Aby sprawdzić, czy łącznik usługi Azure IoT dla programu FHIR został prawidłowo zainicjowany, Użyj procesu weryfikacji podanego na karcie **Portal** .

Uruchom następujący kod interaktywny, aby wyświetlić szczegółowe informacje o aplikacji IoT Central platformy Azure. Musisz wprowadzić nazwę nowej aplikacji IoT Central i grupy zasobów.

```azurepowershell-interactive
$iotCentralName = Read-Host -Prompt "Enter the name of your Azure IoT Central application"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName" -Verbose
Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

Uruchom następujący kod interaktywny, aby wyświetlić szczegółowe informacje na temat usługi Azure API for FHIR. Musisz wprowadzić nazwę nowej usługi FHIR i grupę zasobów.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " fhirServiceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$fhirServiceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

> [!NOTE]
> Łącznik usługi Azure IoT dla programu FHIR nie udostępnia w tym momencie poleceń interfejsu wiersza polecenia. Aby sprawdzić, czy łącznik usługi Azure IoT dla programu FHIR został prawidłowo zainicjowany, Użyj procesu weryfikacji podanego na karcie **Portal** .

Uruchom następujący kod interaktywny, aby wyświetlić szczegółowe informacje o aplikacji IoT Central platformy Azure. Musisz wprowadzić nazwę nowej aplikacji IoT Central i grupy zasobów.

```azurecli-interactive
read -p "Enter the name of your IoT Central application: " iotCentralName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az iot central app show -g $resourceGroupName -n $iotCentralName" &&
az iot central app show -g $resourceGroupName -n $iotCentralName &&
```

---

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Łączenie danych IoT z łącznikiem usługi Azure IoT dla FHIR (wersja zapoznawcza)
> [!IMPORTANT]
> Szablon mapowania urządzeń podany w tym przewodniku został zaprojektowany z założeniami, aby można było korzystać z eksportu danych (starsza wersja) w IoT Central.

Aplikacja IoT Central obecnie nie udostępnia szablonu ARM ani poleceń programu PowerShell i interfejsu wiersza polecenia, aby można było ustawić eksport danych. Tak więc postępuj zgodnie z poniższymi instrukcjami przy użyciu Azure Portal.  

Po wdrożeniu aplikacji IoT Central Twoje dwa wbudowane urządzenia symulowane rozpoczną generowanie danych telemetrycznych. Na potrzeby tego samouczka będziemy pozyskiwać dane telemetryczne z inteligentnych funkcji symulatora *poprawek* do FHIR za pośrednictwem łącznika usługi Azure IoT dla FHIR. Aby wyeksportować dane IoT do łącznika usługi Azure IoT dla programu FHIR, należy [skonfigurować eksport danych (starszą wersję) w ramach IoT Central](../iot-central/core/howto-export-data-legacy.md). Na stronie Eksport danych (starsza wersja):
- Wybierz pozycję *Azure Event Hubs* jako miejsce docelowe eksportowania.
- Zaznacz pole wyboru *Użyj wartości parametrów połączenia* dla **Event Hubs przestrzeni nazw** .
- Udostępnij łącznik usługi Azure IoT dla parametrów połączenia FHIR uzyskanych w poprzednim kroku dla pola **Parametry połączenia** .
- Zachowaj opcję **telemetrii** *dla* **danych do wyeksportowania** .

---

## <a name="view-device-data-in-azure-api-for-fhir"></a>Wyświetlanie danych urządzenia w usłudze Azure API for FHIR

Można wyświetlić FHIR widoczne zasoby utworzone przez łącznik usługi Azure IoT dla FHIR na serwerze FHIR przy użyciu programu Poster. Skonfiguruj swoje [ogłoszenie, aby uzyskać dostęp do interfejsu API platformy Azure dla usługi FHIR](access-fhir-postman-tutorial.md) , a następnie `GET` Wyślij żądanie, aby `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` wyświetlić obserwacje zasobów FHIR z wartością współczynnika serca.

> [!TIP]
> Upewnij się, że użytkownik ma odpowiednie uprawnienia dostępu do interfejsu API platformy Azure dla FHIRej płaszczyzny danych. Aby przypisać wymagane role płaszczyzny danych, użyj [kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure](configure-azure-rbac.md) .

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Usuń grupę zasobów, która spowoduje usunięcie zasobów z grupy zasobów.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **grupy zasobów**.

2. Na liście Grupa zasobów wybierz nazwę grupy zasobów.

3. Na stronie **Przegląd** w grupie zasobów wybierz pozycję **Usuń grupę zasobów**.

4. W oknie dialogowym potwierdzenia wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu ARM, zapoznaj się z [samouczkiem dotyczącym tworzenia i wdrażania pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono funkcję łącznika usługi Azure IoT for FHIR w interfejsie API platformy Azure dla zasobu FHIR. Wybierz spośród kolejnych kroków, aby dowiedzieć się więcej o łączniku usługi Azure IoT dla FHIR:

Poznaj różne etapy przepływu danych w ramach łącznika usługi Azure IoT dla FHIR.

>[!div class="nextstepaction"]
>[Łącznik usługi Azure IoT dla przepływu danych FHIR](iot-data-flow.md)

Dowiedz się, jak skonfigurować łącznik usługi IoT przy użyciu szablonów mapowania urządzeń i FHIR.

>[!div class="nextstepaction"]
>[Łącznik usługi Azure IoT dla szablonów mapowania FHIR](iot-mapping-templates.md)

* W Azure Portal łącznik usługi Azure IoT dla FHIR jest określany jako łącznik IoT (wersja zapoznawcza). FHIR jest zastrzeżonym znakiem towarowym HL7 i jest używany z uprawnieniem HL7.
