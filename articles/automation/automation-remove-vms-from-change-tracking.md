---
title: Usuwanie maszyn wirtualnych z Azure Automation Change Tracking i spisu
description: W tym artykule opisano sposób usuwania maszyn wirtualnych z Change Tracking i spisu.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 8e9b80c60c098e4daf247db07fa48baa322aa7f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84169458"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Usuwanie maszyn wirtualnych ze śledzenia zmian i spisu

Po zakończeniu wdrażania zmian w maszynach wirtualnych w środowisku można je usunąć z funkcji [Change Tracking i spisu](change-tracking.md) .

## <a name="to-remove-your-vms"></a>Aby usunąć maszyny wirtualne

1. Z poziomu konta usługi Automation wybierz pozycję **śledzenie zmian** lub **spis** w obszarze **Zarządzanie konfiguracją**.

2. Użyj następującego polecenia, aby zidentyfikować identyfikator UUID maszyny wirtualnej, która ma zostać usunięta z zarządzania.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. W obszarze roboczym Log Analytics w obszarze **Ogólne**uzyskaj dostęp do zapisanych wyszukiwań dla konfiguracji zakresu `MicrosoftDefaultScopeConfig-ChangeTracking` .

4. Dla zapisanego wyszukiwania `MicrosoftDefaultComputerGroup` kliknij wielokropek z prawej strony i wybierz pozycję **Edytuj**. 

5. Usuń identyfikator UUID dla maszyny wirtualnej.

6. Powtórz kroki dla innych maszyn wirtualnych do usunięcia.

7. Zapisz zapisane wyszukiwanie po zakończeniu edycji. 

## <a name="next-steps"></a>Następne kroki

* Aby kontynuować pracę z Change Tracking i spisem, zobacz [zarządzanie Change Tracking i spisem](change-tracking-file-contents.md).
* Aby rozwiązać ogólne problemy z funkcjami, zobacz [Rozwiązywanie problemów dotyczących Change Tracking i spisu](troubleshoot/change-tracking.md).