---
title: Szybki start — tworzenie usługi Azure IoT Hub Device Provisioning Service (DPS) przy użyciu Azure Resource Manager szablonu (szablonu USŁUGI ARM)
description: Przewodnik Szybki start platformy Azure — dowiedz się, jak utworzyć usługę Azure IoT Hub Device Provisioning Service (DPS) przy użyciu szablonu Azure Resource Manager (szablonu USŁUGI ARM).
author: wesmc7777
ms.author: wesmc
ms.date: 01/27/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.openlocfilehash: bcb37c624f53d961020de022569a621ca1dfaba1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788998"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-dps-with-an-arm-template"></a>Szybki start: konfigurowanie aplikacji IoT Hub Device Provisioning Service (DPS) przy użyciu szablonu usługi ARM

Za pomocą szablonu [Azure Resource Manager](../azure-resource-manager/management/overview.md) arm (arm) można programowo skonfigurować zasoby w chmurze platformy Azure niezbędne do aprowiowania urządzeń. Te kroki pokazują, jak utworzyć centrum IoT Hub i nową IoT Hub Device Provisioning Service za pomocą szablonu usługi ARM. Centrum IoT Hub jest również połączone z zasobem usługi DPS przy użyciu szablonu. To połączenie umożliwia zasobowi usługi DPS przypisywanie urządzeń do centrum na podstawie skonfigurowanych zasad alokacji.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ten przewodnik Szybki start używa programu [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)i interfejsu wiersza polecenia platformy [Azure](../azure-resource-manager/templates/deploy-cli.md) do wykonania kroków programowych niezbędnych do utworzenia grupy zasobów i wdrożenia szablonu, ale możesz łatwo użyć programu [PowerShell,](../azure-resource-manager/templates/deploy-powershell.md)platformy .NET, języka Ruby lub innych języków programowania, aby wykonać te kroki i wdrożyć szablon. 

Jeśli Środowisko spełnia wymagania wstępne i znasz już szablony usługi ARM, wybranie poniższego przycisku Wdowy na platformie **Azure** spowoduje otwarcie szablonu do wdrożenia w Azure Portal.

[![Wdrażanie na platformie Azure — omówienie](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-iothub-device-provisioning/).

> [!NOTE]
> Obecnie tworzenie rejestracji przy użyciu nowych zasobów usługi DPS nie obsługuje szablonów usługi ARM. Jest to typowe i zrozumiałe żądanie, które jest rozważane do implementacji.

:::code language="json" source="~/quickstart-templates/101-iothub-device-provisioning/azuredeploy.json":::

W powyższym szablonie zdefiniowano dwa zasoby platformy Azure:

* [**Microsoft.Devices/iothubs:**](/azure/templates/microsoft.devices/iothubs)tworzy nową Azure IoT Hub.
* [**Microsoft.Devices/provisioningservices:**](/azure/templates/microsoft.devices/provisioningservices)tworzy nową Azure IoT Hub Device Provisioning Service z nową IoT Hub już z nim połączone.


## <a name="deploy-the-template"></a>Wdrożenie szablonu

#### <a name="deploy-with-the-portal"></a>Wdrażanie przy użyciu portalu

1. Wybierz poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon do wdrożenia. Szablon tworzy nowy zasób usług IoT Hub i DPS. Koncentrator zostanie połączony w zasobie usługi DPS.

    [![Kroki wdrażania na platformie Azure w portalu](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości, a następnie kliknij **pozycję Przejrzyj i utwórz.**

    ![Parametry wdrażania szablonu usługi ARM w portalu](./media/quick-setup-auto-provision-rm/arm-template-deployment-parameters-portal.png)    

    Jeśli nie określono tego poniżej, użyj wartości domyślnej, aby utworzyć zasób usługi IoT Hub i usługi DPS.

    | Pole | Opis |
    | :---- | :---------- |
    | **Subskrypcja** | Wybierz swoją subskrypcję platformy Azure. |
    | **Grupa zasobów** | Kliknij **pozycję Utwórz nową,** wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK.** |
    | **Region** | Wybierz region dla zasobów. Na przykład **Wschodnie usa**. |
    | **Nazwa centrum IoT** | Wprowadź nazwę aplikacji, która IoT Hub globalnie unikatowa w przestrzeni nazw *.azure-devices.net.* Nazwa centrum będzie potrzebna w następnej sekcji podczas weryfikowania wdrożenia. |
    | **Nazwa usługi aprowingu** | Wprowadź nazwę nowego zasobu usługi Device Provisioning Service (DPS). Nazwa musi być globalnie unikatowa w obrębie *przestrzeni nazw .azure-devices-provisioning.net* . Nazwa usługi DPS będzie potrzebna w następnej sekcji podczas weryfikowania wdrożenia. |
    
3. Na następnym ekranie przeczytaj warunki. Jeśli akceptujesz wszystkie warunki, kliknij pozycję **Utwórz.** 

    Wdrożenie potrwa kilka minut. 

    Oprócz interfejsu Azure Portal można również użyć interfejsu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).


#### <a name="deploy-with-the-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Korzystanie z interfejsu wiersza polecenia platformy Azure wymaga wersji 2.6 lub nowszej. Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, sprawdź swoją wersję, uruchamiając: `az --version`

Zaloguj się do konta platformy Azure i wybierz subskrypcję.

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, a nie w portalu, musisz się zalogować. Aby zalogować się w wierszu polecenia, uruchom [polecenie logowania](/cli/azure/get-started-with-az-cli2):
    
    ```azurecli
    az login
    ```

    Postępuj zgodnie z instrukcjami w celu uwierzytelnienia przy użyciu kodu i zaloguj się na konto platformy Azure za pośrednictwem przeglądarki internetowej.

2. Jeśli masz wiele subskrypcji platformy Azure, zalogowanie się do platformy Azure zapewnia dostęp do wszystkich kont platformy Azure skojarzonych z poświadczeniami użytkownika. Użyj następującego [polecenia, aby wyświetlić listę kont platformy Azure](/cli/azure/account) dostępnych do użycia:
    
    ```azurecli
    az account list -o table
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, której chcesz użyć do uruchamiania poleceń w celu utworzenia centrum IoT i zasobów usługi DPS. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Skopiuj i wklej następujące polecenia do wiersza polecenia. Następnie wykonaj polecenia, naciskając klawisz **ENTER**.
   
    > [!TIP]
    > Polecenia będą monitować o lokalizację grupy zasobów. Aby wyświetlić listę dostępnych lokalizacji, najpierw uruchamiając polecenie :
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

4. Polecenia te wyświetlą monit o następujące informacje. Podaj każdą wartość i naciśnij klawisz **ENTER**.

    | Parametr | Opis |
    | :-------- | :---------- |
    | **Nazwa projektu** | Wartość tego parametru zostanie użyta do utworzenia grupy zasobów do przechowywania wszystkich zasobów. Ciąg `rg` zostanie dodany na końcu wartości nazwy grupy zasobów. |
    | **Lokalizacji** | Ta wartość to region, w którym będą znajdować się wszystkie zasoby. |
    | **iotHubName** | Wprowadź nazwę aplikacji, która IoT Hub globalnie unikatowa w przestrzeni nazw *.azure-devices.net.* Nazwa centrum będzie potrzebna w następnej sekcji podczas weryfikowania wdrożenia. |
    | **provisioningServiceName** | Wprowadź nazwę nowego zasobu usługi Device Provisioning Service (DPS). Nazwa musi być globalnie unikatowa w obrębie *przestrzeni nazw .azure-devices-provisioning.net* . Nazwa usługi DPS będzie potrzebna w następnej sekcji podczas weryfikowania wdrożenia. |

    Do wdrożenia szablonu służy azureCLI. Oprócz interfejsu wiersza polecenia platformy Azure można również używać interfejsu API Azure PowerShell, Azure Portal i REST. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).


## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

1. Aby zweryfikować wdrożenie, [](/cli/azure/resource#az_resource_list) uruchom następujące polecenie, aby wyświetlić listę zasobów i poszukać nowej usługi aprowizowania oraz centrum IoT Hub w danych wyjściowych:

    ```azurecli
     az resource list -g "${projectName}rg"
    ```

2. Aby sprawdzić, czy koncentrator jest już połączony z zasobem usługi DPS, uruchom następujące polecenie [dps extension show.](/cli/azure/iot/dps#az_iot_dps_show)

    ```azurecli
     az iot dps show --name <Your provisioningServiceName>
    ```

    Zwróć uwagę na koncentratory połączone z `iotHubs` członkiem.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, możesz usunąć grupę zasobów i wszystkie jej zasoby Azure Portal interfejsie wiersza polecenia platformy Azure.

Aby usunąć grupę zasobów i wszystkie jej zasoby z Azure Portal, po prostu otwórz grupę zasobów i kliknij pozycję Usuń **grupę** zasobów u góry.

Aby usunąć grupę zasobów wdrożoną przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az group delete --name "${projectName}rg"
```

Grupy zasobów i poszczególne zasoby można również usuwać przy użyciu interfejsów API Azure Portal, PowerShell lub REST, a także z obsługiwanymi zestawami SDK platformy opublikowanymi dla Azure Resource Manager lub IoT Hub Device Provisioning Service.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono centrum IoT i wystąpienie usługi Device Provisioning Service oraz powiązyno te dwa zasoby. Aby dowiedzieć się, jak za pomocą tej konfiguracji aprowizować urządzenie, przejdź do przewodnika Szybki start tworzenia urządzenia.

> [!div class="nextstepaction"]
> [Przewodnik Szybki start dotyczący aprowizować urządzenie](./quick-create-simulated-device-symm-key.md)
