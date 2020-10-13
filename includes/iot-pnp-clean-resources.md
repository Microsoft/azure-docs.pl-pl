---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 6cd983b6421fc821853aa22d4dc2c297f672d292
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87336903"
---
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuowanie pracy z dodatkowymi artykułami Plug and Play IoT, możesz zachować i ponownie użyć zasobów użytych w tym artykule. W przeciwnym razie można usunąć zasoby utworzone w tym artykule, aby uniknąć dodatkowych opłat.

Jednocześnie można usunąć zarówno centrum, jak i zarejestrowane urządzenie, usuwając całą grupę zasobów przy użyciu poniższego polecenia interfejsu CLI platformy Azure. Nie używaj tego polecenia, jeśli te zasoby współużytkują grupę zasobów z innymi zasobami, które chcesz zachować.

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```

Aby usunąć tylko usługę IoT Hub, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Aby usunąć tylko tożsamość urządzenia zarejestrowana w usłudze IoT Hub, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Można również usunąć sklonowane pliki przykładowe z komputera deweloperskiego.
