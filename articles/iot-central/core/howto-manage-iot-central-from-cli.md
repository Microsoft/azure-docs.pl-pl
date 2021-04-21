---
title: Zarządzanie IoT Central z interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: W tym artykule opisano sposób tworzenia aplikacji IoT Central przy użyciu interfejsu wiersza polecenia i zarządzania nimi. Aplikację można wyświetlać, modyfikować i usuwać przy użyciu interfejsu wiersza polecenia.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: c30781cb83436e15a217a1d43c0e39facae9f52d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770421"
---
# <a name="manage-iot-central-from-azure-cli"></a>Zarządzanie IoT Central z interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Zamiast tworzyć aplikacje IoT Central i zarządzać nimi w witrynie internetowej Azure IoT Central [Application Manager,](https://aka.ms/iotcentral) możesz zarządzać aplikacjami za pomocą interfejsu wiersza polecenia platformy [Azure.](/cli/azure/)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Jeśli musisz uruchamiać polecenia interfejsu wiersza polecenia w innej subskrypcji platformy Azure, zobacz [Zmienianie aktywnej subskrypcji.](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)

## <a name="create-an-application"></a>Tworzenie aplikacji

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Użyj polecenia [az iot central app create,](/cli/azure/iot/central/app#az_iot_central_app_create) aby utworzyć IoT Central aplikacji w subskrypcji platformy Azure. Na przykład:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Te polecenia najpierw tworzą grupę zasobów w regionie Wschodnie zasoby USA dla aplikacji. W poniższej tabeli opisano parametry używane z **poleceniem az iot central app create:**

| Parametr         | Opis |
| ----------------- | ----------- |
| resource-group    | Grupa zasobów zawierająca aplikację. Ta grupa zasobów musi już istnieć w subskrypcji. |
| location          | Domyślnie to polecenie używa lokalizacji z grupy zasobów. Obecnie można utworzyć aplikację w IoT Central Australii, Azja i Pacyfik,  **Europie,** **Stany Zjednoczone,** Zjednoczonym **Królestwie** i  Japonii.  |
| name              | Nazwa aplikacji w Azure Portal. |
| Poddomeny         | Poddomena w adresie URL aplikacji. W tym przykładzie adres URL aplikacji to `https://mysubdomain.azureiotcentral.com` . |
| sku               | Obecnie można użyć **st1** lub **ST2.** Zobacz [Azure IoT Central cennika.](https://azure.microsoft.com/pricing/details/iot-central/) |
| szablon          | Szablon aplikacji do użycia. Aby uzyskać więcej informacji, zobacz poniższą tabelę. |
| nazwa wyświetlana      | Nazwa aplikacji wyświetlana w interfejsie użytkownika. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Wyświetl swoje aplikacje

Użyj polecenia [az iot central app list,](/cli/azure/iot/central/app#az_iot_central_app_list) aby wyświetlić listę aplikacji IoT Central i wyświetlić metadane.

## <a name="modify-an-application"></a>Modyfikowanie aplikacji

Użyj polecenia [az iot central app update,](/cli/azure/iot/central/app#az_iot_central_app_update) aby zaktualizować metadane IoT Central aplikacji. Aby na przykład zmienić nazwę wyświetlaną aplikacji:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Usuwanie aplikacji

Użyj polecenia [az iot central app delete,](/cli/azure/iot/central/app#az_iot_central_app_delete) aby usunąć IoT Central aplikację. Na przykład:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak zarządzać aplikacjami Azure IoT Central z interfejsu wiersza polecenia platformy Azure, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Administrowanie aplikacją](howto-administer.md)
