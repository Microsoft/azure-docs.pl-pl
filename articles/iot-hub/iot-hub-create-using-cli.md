---
title: Tworzenie IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Dowiedz się, jak utworzyć grupę zasobów przy użyciu poleceń interfejsu wiersza polecenia platformy Azure, a następnie utworzyć Centrum IoT w grupie zasobów. Dowiedz się również, jak usunąć centrum.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e7253747d95917e34725cc7c6c034b1306f65e50
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84708080"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Tworzenie Centrum IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

W tym artykule opisano sposób tworzenia Centrum IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te czynności, musisz mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Zaloguj się i Skonfiguruj swoje konto platformy Azure

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, a nie przy użyciu Cloud Shell, musisz zalogować się do konta platformy Azure.

W wierszu polecenia uruchom [polecenie logowania](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

   ```azurecli
   az login
   ```

Postępuj zgodnie z instrukcjami w celu uwierzytelnienia przy użyciu kodu i zaloguj się na konto platformy Azure za pośrednictwem przeglądarki internetowej.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć grupę zasobów, a następnie dodać Centrum IoT Hub.

1. Podczas tworzenia Centrum IoT należy je utworzyć w grupie zasobów. Użyj istniejącej grupy zasobów lub uruchom następujące [polecenie, aby utworzyć grupę zasobów](https://docs.microsoft.com/cli/azure/resource):
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > W poprzednim przykładzie tworzona jest grupa zasobów w lokalizacji Zachodnie stany USA. Listę dostępnych lokalizacji można wyświetlić, uruchamiając następujące polecenie: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Uruchom następujące [polecenie, aby utworzyć Centrum IoT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) w grupie zasobów przy użyciu unikatowej nazwy globalnej dla Centrum IoT Hub:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Poprzednie polecenie tworzy Centrum IoT w warstwie cenowej S1, dla którego opłaty są naliczane. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Usuwanie IoT Hub

Możesz użyć interfejsu wiersza polecenia platformy Azure, aby [usunąć pojedynczy zasób](https://docs.microsoft.com/cli/azure/resource), taki jak centrum IoT, lub usunąć grupę zasobów i wszystkie jej zasoby, w tym wszystkie centra IoT.

Aby [usunąć Centrum IoT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete), uruchom następujące polecenie:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Aby [usunąć grupę zasobów](https://docs.microsoft.com/cli/azure/group#az-group-delete) i wszystkie jej zasoby, uruchom następujące polecenie:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o korzystaniu z usługi IoT Hub, zobacz następujące artykuły:

* [Przewodnik dla deweloperów IoT Hub](iot-hub-devguide.md)
* [Zarządzanie IoT Hub przy użyciu Azure Portal](iot-hub-create-through-portal.md)
