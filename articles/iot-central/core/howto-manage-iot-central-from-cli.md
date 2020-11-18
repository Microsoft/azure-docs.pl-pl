---
title: Zarządzanie IoT Central z poziomu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: W tym artykule opisano sposób tworzenia aplikacji IoT Central i zarządzania nią przy użyciu interfejsu wiersza polecenia. Można wyświetlać, modyfikować i usuwać aplikację przy użyciu interfejsu wiersza polecenia.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: 201318a5a5680f248b831bb480888f106286fbe1
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660036"
---
# <a name="manage-iot-central-from-azure-cli"></a>Zarządzanie IoT Central z poziomu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Zamiast tworzyć aplikacje IoT Central i zarządzać nimi w witrynie sieci Web programu [azure IoT Central Application Manager](https://aka.ms/iotcentral) , możesz użyć [interfejsu wiersza polecenia platformy Azure](/cli/azure/) do zarządzania aplikacjami.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Jeśli musisz uruchomić polecenia interfejsu CLI w innej subskrypcji platformy Azure, zobacz [Zmiana aktywnej subskrypcji](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription).

## <a name="create-an-application"></a>Tworzenie aplikacji

Użyj polecenia [AZ IoT Central App Create](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-create) , aby utworzyć aplikację IoT Central w ramach subskrypcji platformy Azure. Na przykład:

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

Te polecenia najpierw tworzą grupę zasobów w regionie Wschodnie stany USA dla aplikacji. W poniższej tabeli opisano parametry używane z poleceniem **AZ IoT Central App Create** :

| Parametr         | Opis |
| ----------------- | ----------- |
| resource-group    | Grupa zasobów zawierająca aplikację. Ta grupa zasobów musi już istnieć w Twojej subskrypcji. |
| location          | Domyślnie to polecenie używa lokalizacji z grupy zasobów. Obecnie można utworzyć aplikację IoT Central w **Australii**, **Azja i Pacyfik**, **Europie**, **Stany Zjednoczone**, **Zjednoczonym Królestwie** i **Japonii** lokalizacje geograficzne. |
| name              | Nazwa aplikacji w Azure Portal. |
| poddomeny         | Poddomena w adresie URL aplikacji. W tym przykładzie adres URL aplikacji to `https://mysubdomain.azureiotcentral.com` . |
| sku               | Obecnie można użyć opcji **ST1** lub **ST2**. Zobacz [Cennik usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| szablon          | Szablon aplikacji do użycia. Aby uzyskać więcej informacji, zobacz poniższą tabelę. |
| Nazwa wyświetlana      | Nazwa aplikacji wyświetlana w interfejsie użytkownika. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Wyświetl swoje aplikacje

Użyj polecenia [AZ IoT Central App list](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-list) , aby wyświetlić listę aplikacji IoT Central i wyświetlić metadane.

## <a name="modify-an-application"></a>Modyfikowanie aplikacji

Użyj polecenia [AZ IoT Central App Update](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-update) , aby zaktualizować metadane aplikacji IoT Central. Na przykład, aby zmienić nazwę wyświetlaną aplikacji:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Usuwanie aplikacji

Aby usunąć aplikację IoT Central, użyj polecenia [AZ IoT Central App Delete](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-delete) . Na przykład:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać aplikacjami IoT Central platformy Azure z poziomu interfejsu wiersza polecenia platformy Azure, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Administrowanie aplikacją](howto-administer.md)
