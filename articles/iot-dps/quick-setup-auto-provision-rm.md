---
title: Szybki Start — Tworzenie usługi Azure IoT Hub Device Provisioning Service (DPS) przy użyciu szablonu Azure Resource Manager (szablon ARM)
description: Przewodnik Szybki Start platformy Azure — informacje na temat tworzenia usługi Azure IoT Hub Device Provisioning Service (DPS) przy użyciu szablonu Azure Resource Manager (szablon ARM).
author: wesmc7777
ms.author: wesmc
ms.date: 01/27/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.openlocfilehash: cc55d1569c85dd0873d0132833acc99bd5d06136
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928933"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-dps-with-an-arm-template"></a>Szybki Start: Konfigurowanie IoT Hub Device Provisioning Service (DPS) przy użyciu szablonu ARM

Za pomocą szablonu [Azure Resource Manager](../azure-resource-manager/management/overview.md) (szablonu ARM) można programowo skonfigurować zasoby chmury platformy Azure niezbędne do aprowizacji urządzeń. W tych krokach pokazano, jak utworzyć Centrum IoT Hub i nowe IoT Hub Device Provisioning Service przy użyciu szablonu ARM. Centrum IoT jest również połączone z zasobem DPS przy użyciu szablonu. To połączenie umożliwia zasobowi DPS przypisanie urządzeń do centrum na podstawie skonfigurowanych zasad alokacji.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ten przewodnik Szybki Start używa [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)i [interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md) do wykonywania kroków programistycznych niezbędnych do utworzenia grupy zasobów i wdrożenia szablonu, ale w celu wykonania tych kroków można łatwo użyć programu [PowerShell](../azure-resource-manager/templates/deploy-powershell.md), platformy .NET, języka Ruby lub innych języków programowania. 

Jeśli środowisko spełnia wymagania wstępne i masz już doświadczenie w korzystaniu z szablonów usługi ARM, wybierz poniższy przycisk **Wdróż na platformie Azure** spowoduje otwarcie szablonu wdrożenia w Azure Portal.

[![Wdrażanie na platformie Azure — omówienie](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-iothub-device-provisioning/).

> [!NOTE]
> Obecnie nie ma obsługi szablonów ARM do tworzenia rejestracji z nowymi zasobami usługi DPS. Jest to typowy i zrozumiały wniosek, który jest brany pod uwagę w przypadku implementacji.

:::code language="json" source="~/quickstart-templates/101-iothub-device-provisioning/azuredeploy.json":::

Dwa zasoby platformy Azure są zdefiniowane w powyższym szablonie:

* [**Microsoft. Devices/iothubs**](/azure/templates/microsoft.devices/iothubs): tworzy nowy IoT Hub platformy Azure.
* [**Microsoft. Devices/provisioningservices**](/azure/templates/microsoft.devices/provisioningservices): tworzy nowy IoT Hub Device Provisioning Service platformy Azure z nowym IoT Hub już z nim połączonym.


## <a name="deploy-the-template"></a>Wdrożenie szablonu

#### <a name="deploy-with-the-portal"></a>Wdrażanie przy użyciu portalu

1. Wybierz Poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon do wdrożenia. Ten szablon umożliwia utworzenie nowego zasobu Centrum IoT Hub i DPS. Centrum zostanie połączone z zasobem DPS.

    [![Wdrażanie na platformie Azure w ramach kroków portalu](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości, a następnie kliknij pozycję **Przegląd + Utwórz**.

    ![Parametry wdrożenia szablonu ARM w portalu](./media/quick-setup-auto-provision-rm/arm-template-deployment-parameters-portal.png)    

    O ile nie zostało to określone poniżej, użyj wartości domyślnej, aby utworzyć zasób Centrum IoT Hub i DPS.

    | Pole | Opis |
    | :---- | :---------- |
    | **Subskrypcja** | Wybierz swoją subskrypcję platformy Azure. |
    | **Grupa zasobów** | Kliknij przycisk **Utwórz nowy** i wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**. |
    | **Region** | Wybierz region dla zasobów. Na przykład **Wschodnie stany USA**. |
    | **Nazwa Centrum IoT Hub** | Wprowadź nazwę IoT Hub, która musi być globalnie unikatowa w przestrzeni nazw *. Azure-Devices.NET* . Podczas sprawdzania poprawności wdrożenia potrzebna jest nazwa centrum w następnej sekcji. |
    | **Nazwa usługi aprowizacji** | Wprowadź nazwę nowego zasobu usługi Device Provisioning (DPS). Nazwa musi być globalnie unikatowa w przestrzeni nazw *. Azure-Devices-Provisioning.NET* . Podczas sprawdzania poprawności wdrożenia potrzebna jest nazwa DPS w następnej sekcji. |
    
3. Na następnym ekranie Przeczytaj warunki. Jeśli akceptujesz wszystkie warunki, kliknij przycisk **Utwórz**. 

    Wdrożenie potrwa kilka chwil. 

    Oprócz Azure Portal można również użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).


#### <a name="deploy-with-the-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Korzystanie z interfejsu wiersza polecenia platformy Azure wymaga wersji 2,6 lub nowszej. Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, sprawdź swoją wersję, uruchamiając następujące polecenie: `az --version`

Zaloguj się do konta platformy Azure i wybierz subskrypcję.

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, a nie uruchamiasz go w portalu, musisz się zalogować. Aby zalogować się przy użyciu wiersza polecenia, uruchom [polecenie logowania](/cli/azure/get-started-with-az-cli2):
    
    ```azurecli
    az login
    ```

    Postępuj zgodnie z instrukcjami w celu uwierzytelnienia przy użyciu kodu i zaloguj się na konto platformy Azure za pośrednictwem przeglądarki internetowej.

2. Jeśli masz wiele subskrypcji platformy Azure, zalogowanie się do platformy Azure zapewnia dostęp do wszystkich kont platformy Azure skojarzonych z poświadczeniami użytkownika. Użyj następującego [polecenia, aby wyświetlić listę kont platformy Azure](/cli/azure/account) dostępnych do użycia:
    
    ```azurecli
    az account list -o table
    ```

    Użyj poniższego polecenia, aby wybrać subskrypcję, która ma być używana do uruchamiania poleceń w celu utworzenia zasobów Centrum IoT i usługi DPS. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Skopiuj i wklej następujące polecenia w wierszu polecenia. Następnie wykonaj polecenia, naciskając klawisz **Enter**.
   
    > [!TIP]
    > Polecenia będą monitował o lokalizację grupy zasobów. Listę dostępnych lokalizacji można wyświetlić, uruchamiając najpierw polecenie:
    >
    > `az account list-locations -o table`
    >
    >
    
    ```azurecli-interactive
    read -p "Enter a project name that is used for generating resource names:" projectName &&
    read -p "Enter the location (i.e. centralus):" location &&
    templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-device-provisioning/azuredeploy.json" &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
    echo "Press [ENTER] to continue ..." &&
    read
    ```

4. Polecenia wyświetlą monit o podanie następujących informacji. Podaj każdą wartość i naciśnij klawisz **Enter**.

    | Parametr | Opis |
    | :-------- | :---------- |
    | **Nazwa projektu** | Wartość tego parametru zostanie użyta do utworzenia grupy zasobów do przechowywania wszystkich zasobów. Ciąg `rg` zostanie dodany na końcu wartości nazwy grupy zasobów. |
    | **przeniesienie** | Ta wartość to region, w którym znajdują się wszystkie zasoby. |
    | **iotHubName** | Wprowadź nazwę IoT Hub, która musi być globalnie unikatowa w przestrzeni nazw *. Azure-Devices.NET* . Podczas sprawdzania poprawności wdrożenia potrzebna jest nazwa centrum w następnej sekcji. |
    | **provisioningServiceName** | Wprowadź nazwę nowego zasobu usługi Device Provisioning (DPS). Nazwa musi być globalnie unikatowa w przestrzeni nazw *. Azure-Devices-Provisioning.NET* . Podczas sprawdzania poprawności wdrożenia potrzebna jest nazwa DPS w następnej sekcji. |

    AzureCLI jest używany do wdrożenia szablonu. Oprócz interfejsu wiersza polecenia platformy Azure można również użyć Azure PowerShell, Azure Portal i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).


## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Aby zweryfikować wdrożenie, uruchom następujące polecenie, [Aby wyświetlić listę zasobów](/cli/azure/resource?view=azure-cli-latest#az-resource-list&preserve-view=true) i poszukać nowej usługi aprowizacji i centrum IoT w danych wyjściowych:

    ```azurecli
     az resource list -g "${projectName}rg"
    ```

2. Aby sprawdzić, czy centrum jest już połączone z zasobem DPS, uruchom następujące [polecenie DPS Extension show](/cli/azure/iot/dps?view=azure-cli-latest#az_iot_dps_show&preserve-view=true).

    ```azurecli
     az iot dps show --name <Your provisioningServiceName>
    ```

    Zwróć uwagę na centra, które są połączone z `iotHubs` elementem członkowskim.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, możesz użyć Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby usunąć grupę zasobów i wszystkie jej zasoby.

Aby usunąć grupę zasobów i wszystkie jej zasoby z Azure Portal, po prostu otwórz grupę zasobów i kliknij pozycję **Usuń grupę zasobów** i górną.

Aby usunąć grupę zasobów wdrożoną za pomocą interfejsu wiersza polecenia platformy Azure:

```azurecli
az group delete --name "${projectName}rg"
```

Grupy zasobów i poszczególne zasoby można także usuwać za pomocą Azure Portal, programu PowerShell lub interfejsów API REST, a także z obsługiwanymi zestawami SDK platformy opublikowanymi dla Azure Resource Manager lub IoT Hub Device Provisioning Service.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono Centrum IoT Hub i wystąpienie usługi Device Provisioning Service i połączono te dwa zasoby. Aby dowiedzieć się, jak za pomocą tego Instalatora zainicjować obsługę administracyjną urządzenia, przejdź do przewodnika Szybki Start dotyczącego tworzenia urządzenia.

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start dotyczący aprowizacji urządzenia](./quick-create-simulated-device-symm-key.md)

