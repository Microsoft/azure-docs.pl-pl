---
title: Tworzenie IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Dowiedz się, jak utworzyć grupę zasobów przy użyciu poleceń interfejsu wiersza polecenia platformy Azure, a następnie utworzyć Centrum IoT w grupie zasobów. Dowiedz się również, jak usunąć centrum.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e9757b94330f889869080a0e961ab2728cee86f6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659934"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Tworzenie Centrum IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

W tym artykule opisano sposób tworzenia Centrum IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć grupę zasobów, a następnie dodać Centrum IoT Hub.

1. Podczas tworzenia Centrum IoT należy je utworzyć w grupie zasobów. Użyj istniejącej grupy zasobów lub uruchom następujące [polecenie, aby utworzyć grupę zasobów](/cli/azure/resource):
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > W poprzednim przykładzie tworzona jest grupa zasobów w lokalizacji Zachodnie stany USA. Listę dostępnych lokalizacji można wyświetlić, uruchamiając następujące polecenie: 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. Uruchom następujące [polecenie, aby utworzyć Centrum IoT](/cli/azure/iot/hub#az-iot-hub-create) w grupie zasobów przy użyciu unikatowej nazwy globalnej dla Centrum IoT Hub:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Poprzednie polecenie tworzy Centrum IoT w warstwie cenowej S1, dla którego opłaty są naliczane. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Usuwanie IoT Hub

Możesz użyć interfejsu wiersza polecenia platformy Azure, aby [usunąć pojedynczy zasób](/cli/azure/resource), taki jak centrum IoT, lub usunąć grupę zasobów i wszystkie jej zasoby, w tym wszystkie centra IoT.

Aby [usunąć Centrum IoT](/cli/azure/iot/hub#az-iot-hub-delete), uruchom następujące polecenie:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Aby [usunąć grupę zasobów](/cli/azure/group#az-group-delete) i wszystkie jej zasoby, uruchom następujące polecenie:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o korzystaniu z usługi IoT Hub, zobacz następujące artykuły:

* [Przewodnik dla deweloperów IoT Hub](iot-hub-devguide.md)
* [Zarządzanie IoT Hub przy użyciu Azure Portal](iot-hub-create-through-portal.md)