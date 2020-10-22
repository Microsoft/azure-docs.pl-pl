---
author: baanders
description: plik dołączany do czyszczenia podstawowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 2e095a7b286a8860535d6b58fa93098735b30c87
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372325"
---
Jeśli zasoby utworzone w tym samouczku nie są już potrzebne, wykonaj następujące kroki, aby je usunąć.

Za pomocą [Azure Cloud Shell](https://shell.azure.com)można usunąć wszystkie zasoby platformy Azure w grupie zasobów za pomocą polecenia [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) . Spowoduje to usunięcie grupy zasobów i wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

Otwórz Azure Cloud Shell i uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie elementy, które zawiera.

```azurecli
az group delete --name <your-resource-group>
```