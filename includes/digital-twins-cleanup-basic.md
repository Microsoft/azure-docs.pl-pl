---
author: baanders
description: plik dołączany do czyszczenia podstawowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 4c03ef942896dda63f678018cdd257024cfbb6d4
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556340"
---
Jeśli zasoby utworzone w tym samouczku nie są już potrzebne, wykonaj następujące kroki, aby je usunąć.

Za pomocą [Azure Cloud Shell](https://shell.azure.com)można usunąć wszystkie zasoby platformy Azure w grupie zasobów za pomocą polecenia [AZ Group Delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) . To polecenie usuwa grupę zasobów i wystąpienie usługi Azure Digital bliźniaczych reprezentacji.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub niewłaściwej grupy zasobów.

Otwórz Azure Cloud Shell i uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie elementy, które zawiera.

```azurecli-interactive
az group delete --name <your-resource-group>
```