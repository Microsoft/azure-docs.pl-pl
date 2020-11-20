---
title: Zarządzanie IoT Hub Device Provisioning Service przy użyciu interfejsu wiersza polecenia platformy Azure & usługi IoT
description: Dowiedz się, jak zarządzać IoT Hub Device Provisioning Service (DPS) przy użyciu interfejsu wiersza polecenia platformy Azure i rozszerzenia IoT
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: devx-track-azurecli
services: iot-dps
ms.openlocfilehash: 020b0dbc937885923b26c115f91147437b7a0f9b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964732"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Jak zarządzać IoT Hub Device Provisioning Service przy użyciu interfejsu wiersza polecenia platformy Azure i rozszerzenia IoT

[Interfejs wiersza polecenia platformy Azure](/cli/azure?view=azure-cli-latest) to narzędzie wielodostępnej do obsługi wielu platform i zarządzania zasobami platformy Azure, takimi jak IoT Edge. Interfejs wiersza polecenia platformy Azure jest dostępny w systemach Windows, Linux i macOS. Interfejs wiersza polecenia platformy Azure umożliwia zarządzanie zasobami IoT Hub platformy Azure, wystąpieniami usługi Device Provisioning i połączonymi centrami poza urządzeniem.

Rozszerzenie IoT wzbogaca interfejs wiersza polecenia platformy Azure z funkcjami takimi jak zarządzanie urządzeniami i pełna IoT Edge.

W tym samouczku należy najpierw wykonać kroki konfigurowania interfejsu wiersza polecenia platformy Azure i rozszerzenia IoT. Następnie nauczysz się, jak uruchamiać polecenia interfejsu CLI w celu wykonywania podstawowych operacji usługi Device Provisioning. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="installation"></a>Instalacja 

### <a name="install-python"></a>Instalowanie języka Python

Wymagany jest język [Python 2.7x lub Python 3.x](https://www.python.org/downloads/).

### <a name="install-the-azure-cli"></a>Instalowanie interfejsu wiersza polecenia platformy Azure

Postępuj zgodnie z [instrukcjami instalacji](/cli/azure/install-azure-cli?view=azure-cli-latest) , aby skonfigurować interfejs wiersza polecenia platformy Azure w Twoim środowisku. Minimalna wersja interfejsu wiersza polecenia platformy Azure musi być 2.0.70 lub nowsza. Użyj polecenia `az –version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack. Prostym sposobem instalowania w systemie Windows jest pobranie i zainstalowanie instalatora [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="install-iot-extension"></a>Zainstaluj rozszerzenie IoT

[Plik readme rozszerzenia IoT](https://github.com/Azure/azure-iot-cli-extension) opisuje kilka sposobów instalowania rozszerzenia. Najprostszym sposobem jest uruchomienie polecenia `az extension add --name azure-iot`. Po zakończeniu instalacji można użyć polecenia `az extension list` w celu zweryfikowania aktualnie zainstalowanych rozszerzeń lub polecenia `az extension show --name azure-iot` w celu wyświetlenia szczegółów rozszerzenia IoT. Aby usunąć rozszerzenie, można użyć polecenia `az extension remove --name azure-iot`.


## <a name="basic-device-provisioning-service-operations"></a>Podstawowe operacje usługi Device Provisioning

W przykładzie pokazano, jak zalogować się do konta platformy Azure, utworzyć grupę zasobów platformy Azure (kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure), utworzyć IoT Hub, utworzyć usługę Device Provisioning, wyświetlić istniejące usługi Device Provisioning i utworzyć połączone centrum IoT przy użyciu poleceń interfejsu wiersza polecenia. 

Przed rozpoczęciem wykonaj opisane wcześniej kroki instalacji. Jeśli nie masz jeszcze konta platformy Azure, możesz dzisiaj [utworzyć bezpłatne konto](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Zaloguj się do konta platformy Azure
  
```azurecli
az login
```

![Zrzut ekranu przedstawia okno wiersza polecenia, w którym jest uruchamiane polecenie AZ login.](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Utwórz grupę zasobów IoTHubBlogDemo na wschodnim Wschodzie

```azurecli
az group create -l eastus -n IoTHubBlogDemo
```

![Tworzenie grupy zasobów](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. Utwórz dwie usługi Device Provisioning

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps
```

![Tworzenie usługi Device Provisioning](./media/how-to-manage-dps-with-cli/create-dps.jpg)

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps2
```

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Utwórz listę wszystkich istniejących usług Device Provisioning w ramach tej grupy zasobów

```azurecli
az iot dps list --resource-group IoTHubBlogDemo
```

![Wyświetlanie listy usług aprowizacji urządzeń](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Utwórz IoT Hub blogDemoHub w ramach nowo utworzonej grupy zasobów

```azurecli
az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo
```

![Tworzenie centrum IoT Hub](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Połącz jeden istniejący IoT Hub z usługą Device Provisioning

```azurecli
az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus
```

![Łączenie centrum Hub](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie urządzenia
> * Uruchamianie urządzenia
> * Sprawdzanie, czy urządzenie zostało zarejestrowane

Przejdź do następnego samouczka, aby dowiedzieć się, jak aprowizować wiele urządzeń w obrębie centrów ze zrównoważonym obciążeniem. 

> [!div class="nextstepaction"]
> [Aprowizuj urządzenia w centrach IoT Hub ze zrównoważonym obciążeniem](./tutorial-provision-multiple-hubs.md)