---
title: Tworzenie aplikacji IoT Hub użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Dowiedz się, jak za pomocą poleceń interfejsu wiersza polecenia platformy Azure utworzyć grupę zasobów, a następnie utworzyć centrum IoT w grupie zasobów. Dowiedz się również, jak usunąć koncentrator.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: eff0085a4a739e0831b25b1bd28cd234fdbcde3d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766465"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Tworzenie centrum IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

W tym artykule pokazano, jak utworzyć centrum IoT hub przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć grupę zasobów, a następnie dodać centrum IoT.

1. Podczas tworzenia centrum IoT Należy utworzyć je w grupie zasobów. Użyj istniejącej grupy zasobów lub uruchom następujące [polecenie, aby utworzyć grupę zasobów](/cli/azure/resource):
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > W poprzednim przykładzie tworzona jest grupa zasobów w lokalizacji Zachodnie stany USA. Aby wyświetlić listę dostępnych lokalizacji, należy użyć tego polecenia: 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. Uruchom następujące [polecenie, aby utworzyć centrum IoT](/cli/azure/iot/hub#az_iot_hub_create) w grupie zasobów, używając globalnie unikatowej nazwy dla centrum IoT:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Poprzednie polecenie tworzy centrum IoT w warstwie cenowej S1, za którą są naliczane opłaty. Aby uzyskać więcej informacji, zobacz [Azure IoT Hub cennika.](https://azure.microsoft.com/pricing/details/iot-hub/)

## <a name="remove-an-iot-hub"></a>Usuwanie IoT Hub

Za pomocą interfejsu [](/cli/azure/resource)wiersza polecenia platformy Azure można usunąć pojedynczy zasób, taki jak centrum IoT, lub usunąć grupę zasobów i wszystkie jej zasoby, w tym wszystkie centra IoT.

Aby [usunąć centrum IoT Hub,](/cli/azure/iot/hub#az_iot_hub_delete)uruchom następujące polecenie:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Aby [usunąć grupę zasobów](/cli/azure/group#az_group_delete) i wszystkie jej zasoby, uruchom następujące polecenie:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z centrum IoT Hub, zobacz następujące artykuły:

* [IoT Hub dla deweloperów](iot-hub-devguide.md)
* [Używanie Azure Portal do zarządzania IoT Hub](iot-hub-create-through-portal.md)