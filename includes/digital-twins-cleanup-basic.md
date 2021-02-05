---
author: baanders
description: plik dołączany do czyszczenia wystąpienia usługi Azure Digital bliźniaczych reprezentacji
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: c3c1b814b357a2e4b724590261657e485852f99c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575705"
---
* **Jeśli nie potrzebujesz żadnych zasobów utworzonych w tym samouczku**, możesz usunąć wystąpienie usługi Azure Digital bliźniaczych reprezentacji i wszystkie inne zasoby z tego artykułu za pomocą polecenia [AZ Group Delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) . Spowoduje to usunięcie wszystkich zasobów platformy Azure w grupie zasobów, a także samej grupy zasobów.
    
    > [!IMPORTANT]
    > Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub niewłaściwej grupy zasobów.
    
    Otwórz [Azure Cloud Shell](https://shell.azure.com)i uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie elementy, które zawiera.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```