---
title: Tworzenie IoT Hub platformy Azure przy użyciu polecenia cmdlet programu PowerShell | Microsoft Docs
description: Dowiedz się, jak utworzyć grupę zasobów za pomocą poleceń cmdlet programu PowerShell, a następnie utworzyć Centrum IoT w grupie zasobów. Dowiedz się również, jak usunąć centrum.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 9c49f7ac744ee516aefc1571d50264132035ba8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73890607"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Tworzenie Centrum IoT Hub za pomocą polecenia cmdlet New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Wprowadzenie

Za pomocą poleceń cmdlet Azure PowerShell można tworzyć centra Azure IoT Hub i zarządzać nimi. W tym samouczku pokazano, jak utworzyć Centrum IoT przy użyciu programu PowerShell.

Aby wykonać te czynności, musisz mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Nawiązywanie połączenia z subskrypcją platformy Azure

Jeśli używasz Cloud Shell, użytkownik jest już zalogowany do subskrypcji. Jeśli używasz programu PowerShell lokalnie, wprowadź następujące polecenie, aby zalogować się do subskrypcji platformy Azure:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Do wdrożenia Centrum IoT potrzebna jest Grupa zasobów. Możesz użyć istniejącej grupy zasobów lub utworzyć nową.

Aby utworzyć grupę zasobów dla usługi IoT Hub, użyj polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) . Ten przykład tworzy grupę zasobów o nazwie **MyIoTRG1** w regionie **Wschodnie stany USA** :

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

Aby utworzyć Centrum IoT w grupie zasobów utworzonej w poprzednim kroku, użyj polecenia [New-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) . Ten przykład tworzy centrum **S1** o nazwie **MyTestIoTHub** w regionie **Wschodnie stany USA** :

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Nazwa Centrum IoT musi być globalnie unikatowa.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Wszystkie centra IoT można wyświetlić w ramach subskrypcji za pomocą polecenia [Get-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) :

```azurepowershell-interactive
Get-AzIotHub
```

Ten przykład pokazuje standardowe IoT Hub S1 utworzone w poprzednim kroku.

Centrum IoT można usunąć za pomocą polecenia [Remove-AzIotHub](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub) :

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Alternatywnie można usunąć grupę zasobów i wszystkie zawarte w niej zasoby za pomocą polecenia [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup) :

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu usługi IoT Hub za pomocą polecenia cmdlet programu PowerShell możesz zapoznać się z następującymi artykułami:

* [Polecenia cmdlet programu PowerShell umożliwiające pracę z Centrum IoT Hub](https://docs.microsoft.com/powershell/module/az.iothub/).

* [Interfejs API REST dostawcy zasobów IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Aby dowiedzieć się więcej na temat opracowywania IoT Hub, zobacz następujące artykuły:

* [Wprowadzenie do zestawu SDK języka C](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
