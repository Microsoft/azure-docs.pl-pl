---
author: baanders
description: plik dołączany do czyszczenia podstawowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji i rejestracji aplikacji
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 45d6a806e94ceca9574b0ed5f73c2b87ef438438
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88891503"
---
Jeśli zasoby utworzone w tym samouczku nie są już potrzebne, wykonaj następujące kroki, aby je usunąć.

Za pomocą [Azure Cloud Shell](https://shell.azure.com)można usunąć wszystkie zasoby platformy Azure w grupie zasobów za pomocą polecenia [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) . Spowoduje to usunięcie grupy zasobów i wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

Otwórz Azure Cloud Shell i uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie elementy, które zawiera.

```azurecli-interactive
az group delete --name <your-resource-group>
```

Następnie usuń Azure Active Directory rejestrację aplikacji utworzoną dla aplikacji klienckiej przy użyciu tego polecenia:

```azurecli-interactive
az ad app delete --id <your-application-ID>
```