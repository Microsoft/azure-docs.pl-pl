---
title: Tworzenie IoT Hub platformy Azure przy użyciu szablonu (PowerShell) | Microsoft Docs
description: Jak utworzyć IoT Hub z Azure PowerShell przy użyciu szablonu Azure Resource Manager.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 1fedadfa7e5b4ec3d7de30d0ad3ef1b1bfa0e0ec
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144390"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Tworzenie Centrum IoT Hub przy użyciu szablonu Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Dowiedz się, jak utworzyć IoT Hub i grupę odbiorców przy użyciu szablonu Azure Resource Manager. Szablony usługi Resource Manager są plikami JSON definiującymi zasoby, które należy wdrożyć dla danego rozwiązania. Aby uzyskać więcej informacji na temat opracowywania szablonów Menedżer zasobów, zobacz [dokumentację usługi Azure Resource Manager](../azure-resource-manager/index.yml).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Szablon Menedżer zasobów używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Oto kopia szablonu:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Szablon tworzy usługę Azure IoT Hub z trzema punktami końcowymi (eventhub, Cloud-to-Device i Messaging) oraz z grupą konsumentów. Aby uzyskać więcej przykładów dotyczących szablonów, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Schemat szablonu w usłudze IoT Hub można znaleźć  [tutaj](/azure/templates/microsoft.devices/iothub-allversions).

Istnieje kilka metod wdrażania szablonu.  Używasz Azure PowerShell w tym samouczku.

Aby uruchomić skrypt programu PowerShell, wybierz pozycję **Wypróbuj** , aby otworzyć usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie Wklej:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Jak widać w skrypcie programu PowerShell, używany szablon pochodzi z szablonów szybkiego startu platformy Azure. Aby korzystać z własnych potrzeb, należy najpierw przekazać plik szablonu do usługi Cloud Shell, a następnie użyć `-TemplateFile` przełącznika, aby określić nazwę pliku.  Aby zapoznać się z przykładem, zobacz [Wdrażanie szablonu](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu Centrum IoT Hub przy użyciu szablonu Azure Resource Manager warto poznać dalsze informacje:

* Przeczytaj o możliwościach [interfejsu API REST dostawcy zasobów IoT Hub][lnk-rest-api].
* Przeczytaj [Azure Resource Manager przegląd][lnk-azure-rm-overview] , aby dowiedzieć się więcej o możliwościach Azure Resource Manager.
* Aby poznać składnię i właściwości JSON do użycia w szablonach, zobacz [typy zasobów Microsoft. Devices](/azure/templates/microsoft.devices/iothub-allversions).

Aby dowiedzieć się więcej na temat opracowywania IoT Hub, zobacz następujące artykuły:

* [Wprowadzenie do zestawu SDK języka C][lnk-c-sdk]
* [Zestawy SDK usługi Azure IoT][lnk-sdks]

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: /rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/quickstart-linux.md