---
author: baanders
description: plik dołączany do czyszczenia podstawowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji i rejestracji aplikacji
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: e8f167555434338416381aaf8978a3d551686fb2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606656"
---
Jeśli zasoby utworzone w tym samouczku nie są już potrzebne, wykonaj następujące kroki, aby je usunąć.

Za pomocą [Azure Cloud Shell](https://shell.azure.com)można usunąć wszystkie zasoby platformy Azure w grupie zasobów za pomocą polecenia [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) . Spowoduje to usunięcie grupy zasobów i wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

Otwórz Azure Cloud Shell i uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie elementy, które zawiera.

```azurecli
az group delete --name <your-resource-group>
```

Następnie usuń Azure Active Directory rejestrację aplikacji utworzoną dla aplikacji klienckiej przy użyciu tego polecenia:

```azurecli
az ad app delete --id <your-application-ID>
```