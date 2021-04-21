---
author: baanders
description: dołączanie pliku w celu oczyszczenia Azure Digital Twins danych
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0d8cc30c0511098caf7b6c47d7f7bd400dc32f1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799828"
---
* **Jeśli nie potrzebujesz żadnych** zasobów utworzonych w tym samouczku, możesz usunąć wystąpienie usługi Azure Digital Twins i wszystkie inne zasoby z tego artykułu za pomocą polecenia [az group delete.](/cli/azure/group#az_group_delete) Spowoduje to usunięcie wszystkich zasobów platformy Azure w grupie zasobów, a także samej grupy zasobów.
    
    > [!IMPORTANT]
    > Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub niewłaściwej grupy zasobów.
    
    Otwórz [Azure Cloud Shell](https://shell.azure.com), a następnie uruchom następujące polecenie, aby usunąć grupę zasobów i wszystko, co zawiera.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```