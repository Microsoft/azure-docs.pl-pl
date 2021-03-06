---
author: baanders
description: plik dołączany do czyszczenia wystąpienia usługi Azure Digital bliźniaczych reprezentacji
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 9a02c4f5c5699b4a6308bfaa519fa9eb776414d6
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244578"
---
* **Jeśli nie potrzebujesz żadnych zasobów utworzonych w tym samouczku**, możesz usunąć wystąpienie usługi Azure Digital bliźniaczych reprezentacji i wszystkie inne zasoby z tego artykułu za pomocą polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) . Spowoduje to usunięcie wszystkich zasobów platformy Azure w grupie zasobów, a także samej grupy zasobów.
    
    > [!IMPORTANT]
    > Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub niewłaściwej grupy zasobów.
    
    Otwórz [Azure Cloud Shell](https://shell.azure.com)i uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie elementy, które zawiera.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```