---
title: Usuwanie maszyn wirtualnych z Azure Automation Change Tracking i spisu
description: W tym artykule opisano sposób usuwania maszyn wirtualnych z Change Tracking i spisu.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 22cb49c414e21e5c47330f2c67fc2cf30e3364b2
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836672"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Usuwanie maszyn wirtualnych ze śledzenia zmian i spisu

Po zakończeniu wdrażania zmian w maszynach wirtualnych w środowisku można je usunąć z funkcji [Change Tracking i spisu](change-tracking.md) .

1. Z poziomu konta usługi Automation wybierz pozycję **śledzenie zmian** lub **spis** w obszarze **Zarządzanie konfiguracją**.

2. Użyj następującego polecenia, aby zidentyfikować identyfikator UUID maszyny wirtualnej, która ma zostać usunięta z zarządzania.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. W obszarze roboczym Log Analytics w obszarze **Ogólne**uzyskaj dostęp do zapisanych wyszukiwań.

4. Dla zapisanego wyszukiwania `MicrosoftDefaultComputerGroup` kliknij wielokropek z prawej strony i wybierz pozycję **Edytuj**. 

5. Usuń identyfikator UUID dla maszyny wirtualnej.

6. Powtórz kroki dla innych maszyn wirtualnych do usunięcia.

7. Zapisz zapisane wyszukiwanie po zakończeniu edycji. 

## <a name="next-steps"></a>Następne kroki

* Aby kontynuować pracę z Change Tracking i spisem, zobacz [zarządzanie Change Tracking i spisem](change-tracking-file-contents.md).
* Aby rozwiązać ogólne problemy z funkcjami, zobacz [Rozwiązywanie problemów dotyczących Change Tracking i spisu](troubleshoot/change-tracking.md).